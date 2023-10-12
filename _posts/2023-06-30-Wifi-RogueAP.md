---
title: Rogue Access Point 
date: 2023-06-25 22:50:30 +/-TTTT
categories: [Wifi, Rogue Access Point]
tags: [Rogue Access Point] 

image:
  path: /assets/img/wifi/rogue/rogue-ap.png
#   alt: image alternative text
---
## Rogue access point
- We will create a rogue AP and look for devices trying to connect to a legitimate AP with the same name. In this attack scenario, our rogue AP is located near both the device and the legitimate AP. Ideally, the device we are targeting is far enough away from the intended AP that we can make our rogue AP more likely to be connected to. We'll use this attack to grab WPA pre-shared keys.

## The basic of rogue APs
- When a client connects to a wireless network, the device will save the network into a list called the Preferred Network List (PNL). A PNL allows devices to reconnect to a familiar network when it is detected again. To allow devices to be able to connect to the Internet from multiple locations in a large building, for example, large wireless networks will typically have multiple APs advertising the same ESSID.

- To illustrate this, let's imagine an office with two APs and a device, connected to the network and moving from one end of the office to the other. As a device moves out of range from one AP, it loses its connection. If the device then tries to reconnect and is closer to the second AP, it will recognize a familiar network ESSID and connect to the second AP.

- In this module, we will take advantage of this by advertising the same ESSID as an existing AP. If our rogue AP broadcasts a strong signal for the client, the client may attempt to connect to us instead of the existing AP. Even though we might not have the same pre-shared key (PSK) as the AP the client was expecting, we will be able to capture the first two messages of the 4-way handshake. This should give us just the right amount of information to crack the PSK.

There is an additional layer of complexity to this approach. Devices are increasingly sophisticated about which networks to connect to and may save the encryption details in the PNL when the network is saved. This means for a successful attack, our rogue AP will have to match the encryption details of the target.

With some of the basics out of the way, let's begin discovering the necessary information on our target.

## Discovery
- clients will connect to our rogue AP just based on the SSID being the same as the target
- We will use airodump-ng to gather information about our target
```shell
sudo airodump-ng -w outFile --output-format pcap wlan0
```
![image](/assets/img/wifi/rogue/airodump.png)
- The AP with the SSID "Mostar". The airodump-ng output shows us Mostar is a WPA2-PSK network with CCMP. The output also tells us that Mostar is running at 130 Mbit. Finally, we notice that Mostar is running on channel 1.
- Based on this information, we can deduce that the Mostar network is most likely 802.11n. When we create our rogue AP, we should match these settings as closely as possible to ensure that clients automatically connect to our rogue AP based on their Preferred Network List.
- Note that we are using a 2.4 GHz card to discover this information.
- Based on this information, we can deduce that the Mostar network is most likely 802.11n. When we create our rogue AP, we should match these settings as closely as possible to ensure that clients automatically connect to our rogue AP based on their Preferred Network List.

We shouldn't solely trust the output of airodump-ng since it only shows the highest encryption possible. If the Mostar target network also supports WPA1, that information will not be displayed in the table.

To get more information, let's open the output Pcap in Wireshark by running wireshark discovery-01.cap.

To find the beacon for the target AP, we can use a filter to limit the types of packets displayed. First, we can display only beacon packets by using the filter _wlan.fc.type_subtype == 0x08_. Management frames use "0" as the type and beacon frames are set to "8" as the subtype. We can also only target the Mostar SSID by adding _&&_ and using the filter _wlan.ssid == "Mostar"_.
![image](/assets/img/wifi/rogue/packet.png)
When we select the frame, we notice the detailed information about the beacon in the section below the list of frames.

By expanding the _Tagged Parameters_ tree under the _IEEEE 802.11 Wireless Management_ tree, we can review the _RSN Information_ and the _Vendor Specific: Microsoft Corp.: WPA Information Element_ trees. The latter section tells us that this AP supports WPA1, and the existence of _RSN Information_ tells us that it also supports WPA2.

Expanding these trees also shows that Mostar is configured with TKIP for Multicast and both TKIP and CCMP for Unicast/Pairwise, as can be determined by the existence of the _TKIP_ and _AES (CCM)_ parameters. In addition, we can confirm that the Mostar network does indeed authenticate with a PSK based on the _Auth Key Management_ configuration.

We also want to make note of other options available to us. For example, we could also create our rogue AP based on the client probes. Listing 1 shows that a client is probing for an SSID called "Yugoslavia". The encryption details, which are only sometimes provided, can be obtained using Wireshark as well.

In this case, we would have to search for probe requests instead of beacons. If no encryption is specified, we might still be able to target the client by creating an AP with both WPA1 and WPA2, each with TKIP and CCMP.
- At this point we know the following about our target AP:
	-   It has an ESSID of Mostar
	-   It has a BSSID of FC:7A:2B:88:63:EF
	-   It uses WPA (TKIP/CCMP) and WPA2 (TKIP/CCMP)
	-   It uses a PSK
	-   It runs on channel 1

With the information in hand, let's move on to creating the rogue AP and capturing WPA2 handshakes.
## Createing a Rogue AP
We will be using **_hostapd-mana** to create a rogue AP. This program is based on a modified version of hostapd, a daemon to create APs using Wi-Fi network adapters. In addition to creating APs, we could also use **hostapd-mana** to run a MANA attack.
We won't be specifically running a MANA attack in this module, but hostapd-mana will still be helpful because it provides a particularly stable method for creating an AP and capturing handshakes.

We can install hostapd-mana by running sudo apt install hostapd-mana.

In order to run hostapd-mana, we will need to build a configuration file. This file will instruct hostapd-mana how to configure the AP and where to save the captured handshake.

-> *Reference:*
   + https://github.com/sensepost/hostapd-mana/
   + https://w1.fi/hostapd/
   + https://sensepost.com/blog/2015/improvements-in-rogue-ap-attacks-mana-1%2F2/

### Building the hostapd-mana configuration
 The configuration file for hostapd-mana has many parameters that can be configured, but we won't need most of them for this module. Below is the simple configuration for hostapd-mana
```shell 
cat createFile_config a.conf
```
![The simplest configuration for hostapd-mama](/assets/img/wifi/rogue/config.png)

 Let's continue our configuration to more closely match the existing AP's settings and increase the likelihood of a successful attack.

 By default, hostapd-mana will run in 802.11b. In order to meet our target's settings, we need to change this to IEEE 802.11n. To accomplish this, we set the _ieee80211n_ parameter to "1" in order to enable 802.11n. Next, we need to specify the band to 2.4 GHz by setting the _hw_mode_ parameter to the letter "g". If the network was running on 5 GHz, we would set _hw_mode_ to "a".

![adding hw_mode to the config file](/assets/img/wifi/rogue/config2.png)

 Next, we can move on to the encryption and authentication settings. These will be important for us to configure in a way that mimics the target AP. First, we will set the _wpa_ parameter to the integer "3" to enable both WPA and WPA2 (setting this parameter to "1" enables only WPA and setting the value to "2" enables only WPA2).

We need to set the authentication to PSK and set the key as well. We can enable PSK authentication by setting the _wpa_key_mgmt_ parameter to "WPA-PSK". To set the key, we'll use the _wpa_passphrase_ parameter. The value we set the _wpa_passphrase_ parameter to is irrelevant, since we are only attempting to capture a handshake.

Next, to enable TKIP/CCMP encryption with WPA1, we set _wpa_pairwise_ to "TKIP CCMP". Finally, we set the _rsn_pairwise_ to "TKIP CCMP" as well in order to enable TKIP/CCMP with WPA2 encryption. If the target was using exclusively WPA or WPA2, we would only set _wpa_pairwise_ or _rsn_pairwise_. The cipher suite for multicast traffic is automatically set by hostapd-mana and we don't need to make any changes in the configuration for it.

![Adding security configuration](/assets/img/wifi/rogue/secu_config.png)

Finally, we need to configure the "mana" portion of hostapd-mana. The only configuration that we need to be concerned about is _mana_wpaout_, which will specify where to save the captured handshakes (in a Hashcat hccapx format). Each handshake that is captured will be appended to this file. We'll save our captured handshakes to the file named /home/kali/mostar.hccapx.

![Final Mostar-mama.conf](/assets/img/wifi/rogue/final_config.png)

***Note:***

*At the writing of this module, it is not possible to crack WPA3. However, when we encounter APs with WPA3, we may be able to trick clients into downgrading to a WPA2 connection, allowing us to capture a crackable handshake. We can accomplish this by creating an AP with only WPA2 and 802.11w set to "optional". WPA3 use the same algorithms as WPA2 CCMP, and requires 802.11w.
If 802.11w is disabled, a client may never try to connect, but WPA2 clients rarely use it (and sometimes don't handle it well). The combination of only using WPA2 and 802.11w set to "optional" will gives us the highest chance that a client will be willing to downgrade.
To achieve this in the hostapd configuration, the _wpa_ value should be set to "2", there shouldn't be a _wpa_pairwise_ parameter, and _rsn_pairwise_ should be set to "CCMP" only. To enable 802.11w, we would set _ieee80211w_ as a new parameter with the value of "1" (indicating it is optional). This also requires that we add "WPA-PSK-SHA256" to _wpa_key_mgmt_.*

### Capturing Handshakes
To start hostapd-mana, we will use the hostapd-mana command and provide the Mostar-mana.conf file as an argument. This will start hostapd-mana using the configuration we set earlier

![Running hostapd-mana](/assets/img/wifi/rogue/run_ap.png)

Some devices will automatically discover the new AP and, if the signal of our rogue AP is stronger than the target, the device will attempt to connect. While the device will ultimately fail to connect (since our configured PSK is incorrect), we can still see the connection attempts because hostapd-mana will log that a WPA1/WPA2 handshake was captured.

![Capturing handshakes](/assets/img/wifi/rogue/caphanshake.png)

Since the connection will fail (due the PSK mismatch), clients may attempt to connect multiple times. However, looking at Listing 7, we were able to capture a handshake. Excellent!

We might not always be so lucky. Some devices might hang on to a weak connection to the target AP. In these situations, we can "nudge" the stubborn clients in the right direction by deauthenticating all clients from the target AP. To accomplish this, we would use another wireless interface and use aireplay-ng to send deauthentication messages.

To deauthenticate clients, we first connect a new wireless card and start monitor mode on channel 1 by using airmon-ng. The channel should be set to "1" to match that of our target AP. This is all accomplished by running sudo airmon-ng start wlan1 1. Next, we can use aireplay-ng to run a deauthentication attack (-0), continuously (0), against all clients connected to our target AP (-a FC:7A:2B:88:63:EF).

![Deauthenticating client](/assets/img/wifi/rogue/deauthent.png)

At this point, clients connected to the target AP should begin disconnecting and looking for a new AP. If the clients find that our rogue AP has a stronger signal, they will attempt to connect and we will capture the WPA handshake.

![Capturing handshakes Deauthenticating client](/assets/img/wifi/rogue/cap_deauthent.png)

The captured handshakes are written to a hccapx file. This format is primarily meant to be used with Hashcat, but we can also crack it with aircrack-ng.

![Cracking the WPA](/assets/img/wifi/rogue/crack.png)
