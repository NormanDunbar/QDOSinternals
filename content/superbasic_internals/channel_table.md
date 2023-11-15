+++
title = "SuperBASIC Channel Table"
author = "Norman Dunbar"
tags = ["SuperBASIC channel table", "SuperBASIC Internals"]
categories = ["SuperBASIC channel table", "SuperBASIC Internals"]
description = "SuperBASIC channel table"
date = "2018-12-28T11:25:00Z"
lastmod = "2018-12-28T11:25:00Z"
weight = 50
+++

The SuperBASIC channel table has the following layout.

|Offset|Size|Mnemonic|Description|
| :---: | :---: | :---: | :--- |
|  $00   |Long |CH_ID |QDOS internal channel id|
|  $04   |Float |CH_CCPX |Graphics cursor X position|
|  $0A   |Float |CH_CCPY |Graphics cursor Y position|
|  $10   |Float |CH_ANGLE |Turtle angle|
|  $16   |Byte |CH_PEN |Pen status (0 = up or 1 = down)|
|  $20   |Word |CH_CHPOS |Character position on line for PRINT and INPUT etc|
|  $22   |Word |CH_WIDTH |Width of the channel. Set by WIDTH command in SuperBasic but defaults to 80 when OPEN is called.|
|  $24   |Long |CH_SPARE |Spare - currently unused|

The following code shows how to convert from a SuperBasic channel number, #3 for example, into the actual channel id that QDOS/SMS uses internally to access the channel to the device/file. This would be the code used in a machine code extension to SuperBasic which needs to deal with channels.

```
channel_id  mulu    #$28,d0             ; Offset into channel table
            add.l   $30(a6),d0          ; Add table start address
            cmp.l   $34(a6),d0          ; Valid ?
            bge.s   ch_bad              ; No, channel # off end of table
            move.l  0(a6,d0.l),d0       ; Channel id
            bmi.s   ch_bad              ; Channel closed
            move.l  d0,a0               ; We need id in A0
            moveq   #0,d0               ; No errors
            rts                         ; Finished

ch_bad      moveq   #-6,d0              ; Channel not open (ERR_NO/ERR_ICHN)
            rts                         ; Bale out
```

The code assumes that D0 holds the SuperBasic channel number and after the call to 'channel_id', if the Z flag is set, then A0 holds the actual QDOS/SMS channel id. If the Z flag is not set then the channel number passed in D0 was either invalid (off the end of the table) or the channel is actually closed.

```
get_chan3   moveq   #3,d0               ; Channel #3 is desired
            bsr     channel_id          ; Call the routine
            bne     no_chan3            ; It failed !

            ; Process channel 3 via its id in A0.L here


no_chan3    ; Process errors etc here. Channel #3 is invalid/closed.
```
