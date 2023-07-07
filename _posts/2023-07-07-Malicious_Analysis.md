---
title: Malware Analysis in Malicious Document 
date: 2023-06-25 22:50:30 +/-TTTT
categories: [Malicious, Malware Analysic in Malicious]
tags: [Malware Analysis] 

image:
  path: /assets/img/malicious/yara.png
#   alt: image alternative text
---

# Rule detect yara
```ad-note
Download yara-rules: https://github.com/Yara-Rules/rules
command: yara -w /yara/rules/index.yar file_name.pdf

```
# Tool
```code
- yara
		yara -w /yara/rules/index.yar file.pdf
		
- zipdump
		zipdump.py -y /yara/rules/index.yar file.docm
- xorsearch
		xorsearch file.pdf http
		xorsearch -p file.pdf
		
- exiftool
		exiftool file.pdf | more
		
- peepdf (https://eternal-todo.com/tools/peepdf-pdf-analysis-tool )
		peepdf -i file.pdf
		
- pdfid -> show infor suspicious detail
		pdfid -i file.pdf
		
- js-didier
- pdf-parser (https://blog.didierstevens.com/programs/pdf-tools/ )
		pdf-parser --object [object_number] -f -w -d -[nameFile] fileAnalysis.pdf
		
- vipermonkey
		vmonkey -a file.vba
		
- olevba
		
		olevba file.doc
		olevba -c file.doc > outfile.vba
		Thuc hien decode: olevba --deobf --reveal file.vba > decodeobf.vba
		
		
- oletimes       
	   oletimes file.doc
	   
- oleid
	   oleid file.doc
	   
- lazy office analyzer: extract url, file modifications and executed program
		https://github.com/tehsyntx/loffice
		
		command:  loffice.py typeFile(word, excel, pdf) exit-on FileName
					loffice.py -p "c:\Program Files\Microsoft Office\Office15" word none file.docm
					
		some tool winDbg, WinAppDbg, Pefile, Capstone
```

## Metadata information
```code
    exiftool
```
## Signature detection
    zipdump + yara

## VBA
    olevba


--> Some document:

	http://virusshare.com

	http://contagio.blogspot.com


