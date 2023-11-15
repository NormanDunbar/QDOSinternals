+++
title = "SuperBASIC Name List"
author = "Norman Dunbar"
tags = ["Name list", "SuperBASIC name list"]
categories = ["Name list", "SuperBASIC name list"]
description = "SuperBASIC name list"
date = "2018-12-28T11:15:00Z"
lastmod = "2018-12-28T11:15:00Z"
weight = 30
+++

The Name List holds the names of all SuperBASIC variables. Each name is preceded by a single byte giving the length of the name and there is no padding to align the next name's entry onto an even address. The entries in that list are indexed from the [Name Table](/superbasic_internals/name_table/). The layout of the name list is:

|Size byte|Name 1 bytes|Size byte|Name 2 bytes|...|
|-|-|-|-|-|

For example:

|4|Temp|7|thisRow|2|x%|13|ram1_test_txt|5|flp1_|...|
|-|-|-|-|-|-|-|-|-|-|-|
