---
title: "Error Codes"
date: 2018-12-28T11:02:05+06:00
lastmod: 2018-12-28T11:02:05+06:00
icon: "ti-control-pause"
description: "Information regarding the error codes on QDOS and SMSQ/E."
type: "docs"
weight: 20
---


These are the error codes that QDOSMSQ can return from a vectored utility, a Trap or from SuperBasic extensions.

### Always check for errors 

When writing assembler routines, it is always advised to explicitly check the value in D0.L for an error code. This is done quickest using :

```
        :
        trap    #n                ; Do the trap.
        tst.l   d0                ; Did it work?
        beq.s   all_ok            ; Yes, all is well

errors  :                         ; Handle errors here
        bra     kill_job          ; And exit the job, or code somehow.
        
all_ok  :                         ; Carry on from here if all worked.
```

Some traps and vectored utilities do not set the Z flag before returning which is why you have to do it. Well, actually, it's not that they don't set it but when you call a trap, the status register is stacked for the duration and restored at the end, so while D0 may have an appropriate error code in it, the Z flag's state is unknown. A vector should always have correctly set the Z flag, so save some coding and execution time by simply checking the Z flag, as follows:

```
        move.w  <whatever>,a2     ; Get the vectored routine.
        jsr     (a2)              ; Do it.
        beq.s   vec_ok            ; All is well, skip error handler.

v_error :                         ; Handle errors here
        bra     kill_job          ; And exit the job, or code somehow.
        
vec_ok  :                         ; Carry on from here if all worked.
```

### Error codes 

|Value|QDOS Mnemonic|SMS Mnemonic|Description |
|---|---|---|---|
|-1|ERR_NC|ERR.NC|Operation not complete .|
|-2|ERR_NJ|ERR.IJOB|Not a (valid) job.| 
|-3|ERR_OM|ERR.IMEM|Out of memory.| 
|-4|ERR_OR|ERR.ORNG|Out of range.| 
|-5|ERR_BO|ERR.BFFL|Buffer overflow.| 
|-6|ERR_NO|ERR.ICHN|Channel not open.| 
|-7|ERR_NF|ERR.FDNF|File or device not found.| 
|-8|ERR_EX|ERR.FEX|File already exists.| 
|-9|ERR_IU|ERR.FDIU|File or device already in use.| 
|-10|ERR_EF|ERR.EOF|End of file.| 
|-11|ERR_DF|ERR.DRFL|Drive full.| 
|-12|ERR_BN|ERR.INAM|Bad device.| 
|-13|ERR_TE|ERR.TRNS<html><br></html>ERR_PRTY|Transmission error<html><br></html>Parity error (SMS only).| 
|-14|ERR_FF|ERR.FMTF|Format failed.| 
|-15|ERR_BP|ERR.IPAR|Bad parameter.| 
|-16|ERR_FE|ERR.MCHK|File error.| 
|-17|ERR_EX|ERR.IEXP|Expression error.| 
|-18|ERR_OV|ERR.OVFL|Arithmetic overflow.| 
|-19|ERR_NI|ERR.NIMP|Not implemented.| 
|-20|ERR_RO|ERR.RDO|Read only.| 
|-21|ERR_BL|ERR.ISYN|Bad line of Basic.| 
|-22| |ERR_RWF|Read/write failed (SMS only).| 
