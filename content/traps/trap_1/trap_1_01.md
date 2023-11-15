---
title: "MT_CJOB/SMS.CRJB - Create a New Job"
date: 2013-06-03T14:50:00Z
lastmod: 2013-06-03T14:50:00Z
draft: false
keywords: ["Trap #1", "Trap", "Creating jobs"]
weight: 1001
---


Create but *do not* activate a new job. The trap can be called to create a blank area into which some job code can be loaded, or, to create a new job that re-uses some other code already in memory. A good example is found in [Dickens, Advanced QL User Guide](https://qlwiki.qlforum.co.uk/doku.php?id=qlwiki:ql_advanced_user_guide) - his clone job is quite amusing and shows how multiple copies of the same job can exist in memory and all share the same code.

|Call Register|Call Parameter|Return Register| Return Parameter|
|---|---|---|---|
|D0.B|$01|D0.L|Error code.|
|D1.L|ID of the job which will own the job being created. |D1.L|Job id of created job.|
|D2.L|Size, in bytes, of the size of the new job's code space. |D2.L|Preserved.|
|D3.L|Size, in bytes, of the size of the new job's data space. |D3.L|Preserved.|
|A0.L|Unused.|A0.L|Base address of the new job. Points to JB_END within the job header.|
|A1.L|Start address of the job's code, or zero.|A1.L|Preserved.|

### Errors 

| Error Code| Description|
|---|---|
|ERR_NJ|No room in the QDOS job table, or D1 was not a valid job id on entry.|
|ERR_OM|Not enough memory to create the job.|

### Notes 

* All registers not shown above are not used on entry and are preserved on exit.
* If A1 is zero on entry, the job's code should be loaded to the address returned in A0.
* If A1 is not zero, the job's code will already be elsewhere, and will be re-entrant and so will not need to be loaded. However, to give this job a name the following bytes should be loaded as code. Six zero bytes, then $4AFB then the length of the name (word) then the name.
* The job's $68 byte header space will be zeroed then registers A4 to A7 as well as JB_OWNER, JB_TAG, JB_PC and JB_START are set. A6 points to just after the end of the header, (A6,A4.L) points to the start of the data area and (A6,A5.L) points to just after the data end, which is the bottom of the stack. A7 points to 4 bytes before the bottom of the stack, because two zero words have been placed there. The contents of any or all of the registers can now be altered by the programmer before the job is activated by MT_ACTIV. 
    

### Example 
```
start   moveq   #SMS.CRJB,d0      ; Or MT_CJOB.
        moveq   #-1,d1            ; Current job will be the owner.
        move.l  #2048,d2          ; Code size is 2048 bytes.
        move.l  #1024,d3          ; Data space and stack takes 1024 bytes.
        suba.l  a1,a1             ; Indicate I will load code later, so ...
                                  ; the trap creates a blank area ready for me.
        trap    #1                ; Do it.
        tst.l   d0                ; Check for errors
        bne.s   <Error handler>   ; Oops, an error occurred
        ...
        <Load job's code from a file etc to the address in A0.L>
        ...
```
