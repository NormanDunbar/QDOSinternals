---
title: "Trap #2 - Input Output Management Traps"
date: 2021/12/17 11:09:00Z
lastmod: 2021/12/17 11:09:00Z
draft: false
keywords: ["Input Output Management trap", "Trap #2", "Trap", "I/O Traps"]
weight: 30
---

|D0 Key|QDOS Mnemonic|SMSQ/E Mnemonic|Description|
|------|-------------|---------------|-----------|
|  $01  |IO_OPEN |IOA.OPEN |[Open a channel](/traps/trap_2/trap_1_01)|
|  $02  |IO_CLOSE |IOA.CLOS |[Close a channel](/traps/trap_2/trap_1_02)|
|  $03  |IO_FORMT |IOA.FRMT |[Format a medium](/traps/trap_2/trap_1_03)|
|  $04  |IO_DELET |IOA.DELF |[Delete a file](/traps/trap_2/trap_1_04)|
|  $05  | -  |IOA.SOWN |[Set channel's owner](/traps/trap_2/trap_1_05)|
|  $06  | -  |IOA.CNAM |[Fetch channel's name](/traps/trap_2/trap_1_06)|
