+++
title = "SuperBASIC Name Table"
author = "Norman Dunbar"
tags = ["Name table", "SuperBASIC name table"]
categories = ["Name table", "SuperBASIC name table"]
description = "SuperBASIC name table"
date = "2018-12-28T11:20:00Z"
lastmod = "2018-12-28T11:20:00Z"
weight = 40
+++

The Name Table consists of a number of eight byte rows, each in the following form:

|Type|Offset|Value pointer|
|-|-|-|

* The first word is a code giving the Type. 
* This is followed by another word which is the offset from the start of the [Name List](/superbasic_internals/name_list/) to the associated name, or -1 if there is no associated name, which is the case for an expression and a substring. 
* Finally there is a long word containing a value pointer which can be one of four things:

    * An offset into the variables area.
    * A pointer to the maths stack.
    * A SuperBASIC line number held in the high word.
    * An Absolute address.

### Type Codes

|Type |Description| Value pointer to ...|
| :---: | :--- | :--- |
|$0001|Unset string               |Variables area|
|$0002|Unset fp                   |Variables area|
|$0003|Unset integer              |Variables area|
|$0101|String expression          |Maths stack|
|$0102|Fp expression              |Maths stack|
|$0103|Integer expression         |Maths stack|
|$0201|String                     |Variables area|
|$0202|Fp number                  |Variables area|
|$0203|Integer                    |Variables area|
|$0300|Substring                  |Variables area|
|$0301|String array               |Variables area|
|$0302|Fp array                   |Variables area|
|$0303|Integer array              |Variables area|
|$0400|SuperBASIC procedure       |SuperBASIC line number|
|$0501|SuperBASIC string function |SuperBASIC line number|
|$0502|SuperBASIC fp function     |SuperBASIC line number|
|$0503|SuperBASIC integer function|SuperBASIC line number|
|$0602|REPeat loop index          |Variables area|
|$0702|FOR loop index             |Variables area| 
|$0800|Machine code procedure     |Procedure address in RAM|
|$0900|Machine code function      |Function address in RAM|

### Parameters 
When a machine code routine is called the parameters are put into the Name Table. In this case the Type code contains information about the parameters.

* Bit 7 is set if the parameter is preceded by '#' (hash).
* Bits 6 to 4 contain the Separator type.
* Bits 3 to 0 contain the Type of the parameter.

|Bit 7|Hash prefix?||Bits 6 to 4|Separator||Bits 3 to 0|Type|
| :---: | :--- |-| :---: | :---: |-| :---: | :---: |
|%0| No hash||%000|None||%0000|Null|
|%1| Hash   ||%001|,   ||%0001|String|
| |         ||%010|;   ||%0010|Floating Point|
| |         ||%011|\\  ||%0011|Integer|
| |         ||%100|!   ||     | |
| |         ||%101|TO  ||     | |
