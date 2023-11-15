---
title: "Trap #4 - SuperBasic Absolute Address Trap"
date: 2008-06-08T14:50:00Z
lastmod: 2008-06-08T14:50:00Z
draft: false
keywords: ["SuperBASIC", "SuperBASIC absolute address trap", "Trap #4", "Trap"]
weight: 50
---

This trap simply converts addresses in A0 or A1 which are relative to A6 into absolute addresses for the next TRAP #2 or TRAP #3 call. Calling TRAP #4 corrupts D0 - all other registers are preserved.

Basically, bit 7 of JB_RELA6 is set in the job's header, don't try this for some other job, only the current one. When the next TRAP #2 or TRAP #3 system call is made, the value in A6 is added to A0 or A1 to make the addresses absolute rather than relative. A6 is not affected by this action.

When the TRAP #2 or TRAP #3 routine has finished, bit 7 of JB_RELA6 in this job's header is reset automatically *unless* the error ERR_NO (Channel not open) occurs after a TRAP #3. In this case, you are responsible for clearing the bit.

How do you find and reset bit 7 of JB_RELA6? As follows :

* Get the base address for the system variables using the MT_INF trap
* Get the longword at offset $64. This points to the current executing job's entry in the QDOS job table.
* Fetch the long word entry in the job table. This is the address of JB_LEN for the current job's header.
* Clear bit 7 of the byte which is $16 bytes on from this address.

This translates to the following code :

```
clear   moveq   #0,d0           ; MT_INF
        trap    #1              ; Get system variables address into A0
        move.l  $64(a0),a0      ; Get current job entry in QDOS job table into A0
        move.l  (a0),a0         ; Get start of this job's header = JB_LEN
        bclr    #7,$16(a0)      ; Clear bit 7 of JB_RELA6
```

