+++
title = "QDOS and SMSQ Channel Definition Block"
author = "Norman Dunbar"
tags = ["QDOSMSQ channel definition block", "QDOSMSQ Internals"]
categories = ["QDOSMSQ channel definition block", "QDOSMSQ Internals"]
description = "QDOS and SMSQ channel table"
date = "2018-12-28T11:30:00Z"
lastmod = "2018-12-28T11:30:00Z"
weight = 60
+++

Whenever a channel is opened, a *channel definition block* is allocated in the common heap. This block contains a certain amount of information regarding said channel. Different channel types have different channel definition blocks, but all channels have the first $18 bytes defined as the block header.

### The Block Header

The SuperBASIC channel table has the following layout.

|Offset|Size|QDOS Mnemonic|SMS Mnemonic|Description|
| :---: | :---: | :---: | :---: | :---
|  $00   |Long |CH_LEN |CHN_LEN |Size of this channel definition block|
|  $04   |Long |CH_DRIVR |CHN_DRVR |Address of the driver linkage block|
|  $08   |Long |CH_OWNER |CHN_OWNR |Job id of the owner of this channel|
|  $0C   |Long |CH_RFLAG |CHN_RFLG |*QDOS Companion* says that  this is the location to be *set* when space is released. The SMSQ docs state that it is the location to be *cleared* when channel is closed.|
|  $10   |Word |CH_TAG |CHN_TAG |Channel tag|
|  $12   |Byte |CH_STAT |CHN_STAT |See note below|
|  $13   |Byte |CH_ACTN |CHN_ACTN |Action code. (Value in D0 for last TRAP on this channel)|
|  $14   |Long |CH_JOBWT |CHN_JBWT |Job id of the job waiting for I/O on this channel|


**Note**
There is a difference of opinion between QDOS Companion and the SMSQ documentation about CH_STAT, as follows:

|Value|QDOS Companion|SMSQ Documentation|
| :---: | :---- | :--- |
|  1  | | Waiting |
|  0  | Ok | OK |
|  -1  | A1 has been absolute | waiting AND A1 is relative A6 |
|  -128  | A1 passed relative to A6 | |
| other negatives | Waiting | |

### TODO: Other channel definition blocks!
