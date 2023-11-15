+++
title = "SuperBASIC Internal Tokenisation"
author = "Norman Dunbar"
tags = ["SuperBASIC variables", "SuperBASIC Tokens"]
categories = ["SuperBASIC Tokens", "SuperBASIC Internals"]
description = "SuperBASIC Tokens"
date = "2018-12-28T11:10:00Z"
lastmod = "2018-12-28T11:10:00Z"
weight = 20
+++


A SuperBASIC program is held internally in a tokenised form. In this form each line is headed by a word whose value is the difference in length of the current line from the previous one. The value for the first line is its length (in bytes). If the next line were two bytes less in length than the first its header would be -2.

After the word header there follows a list of tokens each of which has a specific length associated with it as shown below.

|Token|Length (Bytes)|Byte 1|Byte 2|Word 2|Comments|
| --- | :---: | :---: | :---: | --- | :--- |
|space|2 |$80|nnn| - |Nnn is the number of spaces (1 - 127).|
|keyword|2 |$81|Keyword index| - | See keyword table below.|
|symbol|2 |$84|Symbol index| - | See symbol table below.|
|operator|2 |$85|Binary op index| - | See binary op table below.|
|monadic op|2 |$86|Monadic op index | - | See monadic op table below.|
|name|4 |$88| 0 |Number of entry in name table| |
|short integer|2 |$89|Short integer value -128 to 127 |-|Minerva.|
|long integer|4 |$8A|0|The two bytes of the integer's value -32,768 to 32767|Minerva.|
|string|4+ |$8B|' or "| nnn | Nnn characters follow, padded to even length. Single or double quotes.| 
|text|4+ |$8C|0|nnn| Nnn characters follow, padded to even length. No quotes.|
|line no|4 |$8D|0|Line number of the current line| |
|separator|2 |$8E|Separator index|-|see separator table below. |
|binary float|6 |$D8|Second byte of exponent|top 2 bytes of mantissa|the following two bytes hold the lower two bytes of the mantissa (SMSQ only).|
|hex float|6 |$E8|Second byte of exponent|top 2 bytes of mantissa|the following two bytes hold the lower two bytes of the mantissa (SMSQ only).|
|float|6 |$F0 to $FF|Second byte of exponent|top 2 bytes of mantissa|the following two bytes hold the lower two bytes of the mantissa.|


#### Keyword Table

|Value|Item| |Value|Item|  |Value|Item|  |Value|Item|
| ---: | :--- |-| ---: | :--- |-| ---: | :--- |-| ---: | :--- |
|$01|END      ||$08|PROCedure||$12|NEXT     ||$19|DIM    |
|£02|FOR      ||$09|FuNction ||$13|EXIT     ||$1A|LOCal  |
|$03|IF       ||$0A|GO       ||$14|ELSE     ||$1B|LET    |
|$04|REPeat   ||$0B|TO       ||$15|ON       ||$1C|THEN   |
|$05|SELect   ||$0C|SUB      ||$16|RETurn   ||$1D|STEP   |
|$06|WHEN     ||$0E|ERRor    ||$17|REMAINDER||$1E|REMark |
|$07|DEFine   ||$11|RESTORE  ||$18|DATA     ||$1F|MISTake|


#### Symbol Table 
|Value|Symbol| |Value|Symbol| |Value|Symbol|
| ---: | :---: |-| ---: | :---: |-| ---: | :---: |
|$01|=| |$05|(| |$08|}|
|$02|:| |$06|)| |$09|Forced space|
|$03|#| |$07|{| |$0A| Line feed|
|$04|,|


#### Binary Operator Table
|Value|Operator| |Value|Operator| |Value|Operator|
| ---: | :---: |-| ---: | :---: |-| ---: | :---: |
|$01|+ ||$09|<>  ||$10|&|
|$02|- ||$0A|<=  ||$11|OR|
|$03|* ||$0B|<   ||$12|AND|
|$04|/ ||$0C|\|\|||$13|XOR|
|$05|>=||$0D|&&  ||$14|MOD|
|$06|> ||$0E|\^\^||$15|DIV|
|$07|==||$0F|\^  ||$16|INSTR|
|$08|= | 


#### Monadic Operator Table
|Value|Operator| |Value|Operator|
| ---: | :---: |-| ---: | :---: |
|$01|-||$03|~~|
|$02|+||$04|NOT|


#### Separator Table
|Value|Separator| |Value|Separator|
| ---: | :---: |-| ---: | :---: |
|$01|,||$04|!|
|$02|;||$05|TO|
|$03|\|
