---
title: "Vectors"
date: 2018-12-28T11:02:05+06:00
lastmod: 2018-12-28T11:02:05+06:00
icon: "ti-vector"
description: "Information regarding the numerous available utility vectors on QDOS and SMSQ/E."
type : "docs"
weight: 50
---

There are numerous vectors on QDOS and SMSQ/E, which are shoe-horned into the first half of the QL ROM. They can all be accessed by code, similar to the following :

```
start   move.w  UT_CON,a2       ; Fetch the routine address from the vector table
        ;                       ; Set parameter registers etc here
        jsr     (a2)            ; Call the routine
        tst.l   d0              ; Set the Z flag if all ok
        beq.s   ok              ; All worked ok

oops    ;                       ; Process errors here
        ;                       ; Exit or carry on appropriately

ok      ;                       ; Carry on processing here
```

|Vector|QDOS Mnemonic|SMSQ/E Mnemonic|Description|
|---|--|---|---|
| $C0  |MM_ALCHP |MEM_ACHP  |Allocate space in the common heap|
| $C2  |MM_RECHP |MEM_RCHP  |Release space in the common heap|
| $C4  |UT_WINDW |OPW_WIND  |Create a window channel|
| $C6  |UT_CON |OPW_CON     |Create a console channel|
| $C8  |UT_SCR |OPW_SCR     |Create a screen channel|
| $CA  |UT_ERR0 |UT_WERSY   |rint error message on #0|
| $CC  |UT_ERR |UT_WERMS    |Print error message to any channel|
| $CE  |UT_MINT |UT_WINT    |Print a decimal number to any channel|
| $D0  |UT_MTEXT |UT_WTEXT  |Send a message to a channel|
| $D2  |UT_LINK |MEM_LLST   |Link an item into a list|
| $D4  |UT_UNLNK |MEM_RLST  |Unlink an item from a list|
| $D6  |UT_CRASH |CV_DATIL  |QDOS - Crashes the QL. SMS - Convert date & time (6 words) into a long integer|
| $D8  |MM_ALCHP |MEM_ALHP  |Allocate space from a user heap|
| $DA  |MM_LNKFR |MEM_REHP  |Link free space (back) into a user heap|
| $DC  |IO_QSET |IOQ_SETQ   |Set up a queue header|
| $DE  |IO_QTEST |IOQ_TEST  |Test a queue's status|
| $E0  |IO_QIN |IOQ_PBYT    |Put a byte into a queue|
| $E2  |IO_QOUT |IOQ_GBYT   |Get a byte from a queue|
| $E4  |IO_QEOF |IOQ_SEOF   |Put EOF marker into a queue|
| $E6  |UT_CSTR |UT_CSTR    |Compare two strings|
| $E8  |IO_SERQ |IOU_SSQ    |Direct queue handling|
| $EA  |IO_SERIO |IOU_SSIO  |General I/O handling|
| $EC  |CN_DATE |CV_ILDAT   |Get date as a string|
| $EE  |CN_DAY |CV_ILDAY    |Get day as a string|
| $F0  |CN_FTOD |CV_FPDEC   |Convert a floating point number to ASCII|
| $F2  |CN_ITOD |CV_IWDEC   |Convert an integer to ASCII|
| $F4  |CN_ITOBB |CV_IBBIN  |Convert binary byte (8 bits) to ASCII|
| $F6  |CN_ITOBW |CV_IWBIN  |Convert binary word (16 bits) to ASCII|
| $F8  |CN_ITOBL |CV_ILBIN  |Convert binary long word (32 bits) to ASCII|
| $FA  |CN_ITOHB |CV_IBHEX  |Convert hex byte (8 bits) to ASCII|
| $FC  |CN_ITOHW |CV_IWHEX  |Convert hex word (16 bits) to ASCII|
| $FE  |CN_ITOHL |CV_ILHEX  |Convert hex long word (32 bits) to ASCII|
| $100 |CN_DTOF |CV_DECFP   |Convert ASCII to floating point|
| $102 |CN_DTOI |CV_DECIW   |Convert ASCII to word integer|
| $104 |CN_BTOIB |CV_BINIB  |Convert ASCII to binary byte (8 bits)|
| $106 |CN_BTOIW |CV_BINIW  |Convert ASCII to binary word (16 bits)|
| $108 |CN_BTOIL |CV_BINIL  |Convert ASCII to binary long word (32 bits)|
| $10A |CN_HTOIB |CV_HEXIB  |Convert ASCII to hex byte (8 bits)|
| $10C |CN_HTOIW |CV_HEXIW  |Convert ASCII to hex word (16 bits)|
| $10E |CN_HTOIL |CV_HEXIL  |Convert ASCII to hex long word (32 bits)|
| $110 |BP_INIT |SB_INIPR   |Add new SuperBasic procedures & functions|
| $112 |CA_GTINT |SB_GTINT  |Fetch word integer parameters|
| $114 |CA_GTFP |SB_GTFP    |Fetch floating point parameters|
| $116 |CA_GTSTR |SB_GTSTR  |Fetch string parameters|
| $118 |CA_GTLIN |SB_GTLIN  |Fetch long integer parameters|
| $11A |BV_CHRIX |QA_RESRI  |Reserve space on the maths stack|
| $11C |RI_EXEC |QA_OP      |Perform a single maths operation|
| $11E |RI_EXECB |QA_MOP    |Perform a list of maths operations|
| $120 |BP_LET |SB_PUTP     |Return a result into a parameter|
| $122 |IO_NAME |IOU_DNAM   |Decode a device name|

The following table gives details of the microdrive vectors, which didn't fit into the first half of the QL ROM. They can all be accessed by code, similar to the following :

```
start   move.w  MD_READ,a2      ; Fetch the routine address from the vector table
        ;                       ; Set parameter registers etc here
        jsr     $4000(a2)       ; Call the routine in the second half of the ROM
        bra.s   oops            ; Routine returns here on error. Ignore D0 on return
        bra.s   ok              ; Routine returns here on ok.
oops    ;                       ; Process errors here
        ;                       ; Exit or carry on appropriately

ok      ;                       ; Carry on processing here
```

|Vector|QDOS Mnemonic|SMSQ/E Mnemonic|Description|
|---|---|---|---|
| $124 |MD_READ  |MD.READ   |Read a microdrive sector|
| $126 |MD_WRITE |MD.WRITE  |Write a microdrive sector|
| $128 |MD_VERIN |MD.VERIF  |Verify a microdrive sector|
| $12A |MD_SECTR |MD.RDHDR  |Read a microdrive sector header|

The following are not, to my knowledge, documented anywhere else. I have found these undocumented vectors in various places, not least being a DEA dissassembly of the Superbasic ED command (pre SBasic as ED is different in SBasic). The mnemonics are my own and unless someone complains, I shall not change them.

They can all be accessed by some code, similar to the following :

```
start   move.w  SB_SNTX,a2      ; Fetch the routine address from the vector table
        ;                       ; Set parameter registers etc here
        jsr     $4000(a2)       ; Call the routine in the second half of the ROM
```

**Beware** : I am not sure about the return mechanism. It could be an error code in D0.L or it could be a bra.s pair of instructions similar to the MD_XXXX routines. More investigation is required.

|Vector|My Mnemonic|Description|
|---|---|---|
| $12C |SB_SNTX  |SuperBasic syntax analyser|
| $12E |SB_FSTFC |First syntax table for SuperBasic commands|
| $130 |SB_SSTFC |Second syntax table for SuperBasic commands|
| $132 |SB_FPCBL |Format a pre-compiled SuperBasic line|
| $134 |SB_CMPER |Error when pre-compiling a SuperBasic line|
| $136 |SB_SPFBL |Store a pre-compiled SuperBasic line|
| $138 |SB_CCBTA |Convert pre-compiled SuperBasic line to ASCII|

There is always one awkward one isn't there ? The following vectored routine is difficult. If the QDOS version is 1.03 or less then call it like this :

```
start   move.w  SB_INSTK,a2     ; Fetch the routine address from the vector table
        ;                       ; Set parameter registers etc here
        jsr     $4318(a2)       ; Call the routine in the second half of the ROM
```
and if it is greater than 1.03, call it like this :
```
start   move.w  SB_INSTK,a2     ; Fetch the routine address from the vector table
        ;                       ; Set parameter registers etc here
        jsr     $4000(a2)       ; Call the routine in the second 16K of ROM
```

**Beware** : I am not sure about the return mechanism. It could be an error code in D0.L or it could be a bra.s pair of instructions similar to the MD_XXXX routines. More investigation is required.

|Vector|My Mnemonic|Description|
|---|---|---
| $13A |SB_INSTK|Initialise SuperBasic internal stacks|

