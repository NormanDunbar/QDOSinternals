---
title: "Trap #0 - Supervisor Mode"
date: 2008-06-08T14:46:00Z
lastmod: 2008-06-08T14:46:00Z
draft: false
keywords: ["Supervisor", "Supervisor trap", "Trap #0", "Trap"]
weight: 10
---

This trap simply switches the QL into Supervisor mode. This means that from this point onwards, nothing will multi-task and you have total control of the machine. It also means that nothing will get shifted around in memory.

When in Supervisor mode, it is advised to keep routines small and not use more than about 64 bytes of stack. Remember that you are using a different stack now and the value in A7 before the trap is not what it will be after the trap.

Getting out of supervisor mode requires that you turn off the **S** bit in the status register as follows :

```
trap #0             ; Switch into supervisor mode
;                   ; Do supervisor stuff here
andi #$07ff,sr      ; Return to user mode
```

The state of the machine, whether in user or supervisor mode is controlled by the Status Register, which looks like this :


Bit 15 | 14 | 13 | 12 | 11 | 10 9 8 | 7 6 5 | 4 | 3 | 2 | 1 | 0 
-------|----|----|----|----|--------|-------|---|---|---|---|---
   T   | -  |  S | -  | -  |   III  |  ---  | X | N | Z | V | C


Where the bits are used as follows:

* T is the trace bit. When this bit is set, the CPU will halt between each instruction.
* S is the Supervisor bit. When this bit is set, the QL is running in Supervisor mode.
* III are the interrupt level bits. These can take levels 0 through 7.

The remaining bits are used to flag up various conditions, or state, after executing Assembly Langiuage instructions.

* X is the extend flag.
* N is the negative flag.
* Z is the zero flag.
* V is the overflow flag.
* C is the carry flag.



Bit 13 is the supervisor state bit, and when set to 1 indicates that the system is running in Supervisor mode and will be using the Supervisor Stack Pointer version of A7 as opposed to the User Stack Pointer version. When set to 0, the system is running in user mode. 

In the code example above, to return to user mode after the supervisor code has been executed, the instruction:

```
andi #$07ff,sr
```

is used. This preserves all bits of the SR, except for the **T**race & **S**upervisor bits which are forced back to zero, thus turning these options off. 

**Note** : I have seen code which uses $0dff to switch back into user mode. This has the effect of setting the interrupt level in bits 10 to 8 incorrectly as bit 9 in the register will be ANDed with a 0 bit, resulting in a zero bit. We want a 1 bit.

I would actually state nowadays that we should probably be using:

```
andi #$5fff,sr
```

to return to user mode. This preserves the MC68008 unused bits in the status register - bits 14, 12 and 11 - as it is entirely possible that these are actually used in newer processor models. Just a thought!

