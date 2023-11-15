---
title: "MT_ACTIV/SMS.ACJB - Activate a Newly Created Job"
date: 2008-06-25T12:21:00Z
lastmod: 2008-06-25T12:52:00Z
draft: false
keywords: ["Trap #1", "Trap", "Jobs", "Activating jobs"]
weight: 1010
---

Activate a newly created job.

|Call Parameters|Return Register|Return Parameter|
|---|---|---|---|
|D0.B|$0A|D0.L|Error code.|
|D1.L|Id of the job to be activated.|D1.L|Preserved.|
|D2.B|Job's priority. See notes below.|D2.L|Undefined. See notes below.|
|D3.W|Timeout.|D3.L|Undefined. See notes below.|
|A0.L|Unused. |A0.L|Base address of activated job.|
|A3.L|Unused. |A3.L|Preserved if D3.W is zero, corrupted otherwise.|

### Errors

| Error Code| Description|
|---|---|
|ERR_NJ|Invalid job id.|
|ERR_NC|Not complete (Job is already active).|

### Notes
 * All registers not shown above are not used on entry and are preserved on exit.
  * If the timeout in D3.W is zero, then this equates to EXEC or EX and the job doing the activation will continue to run. Otherwise, it will wait for the activated job to complete before continuing. Dickens indicates that D3.W should be 0 or -1. Pennell refers to the value as 0 or <>0. Best to assume 0 or -1. 
  * A3.L is corrupted if the timeout was not zero on entry.
  * The priority passed in D2.B is documented as being in the range 0 to 127, however, it appears that values from 0 to 255 can be used.
  * Dickens states that D2 and D3 are preserved and Pennell says corrupted. Best to assume the worst and consider them corrupted.
  * This trap always exits via the job scheduler, so it is not atomic.
  * When the job is created by [MT_CJOB/SMS.CRJB](/traps/trap_1/trap_1_01), all registers except A4 to A7 are zero. A6 points to the start of code, ie to just after the end of the $68 byte header block. (A6,A4) points to the start of the data area and (A6,A5) points to just after the end of the data area. A7 is set to A6 + A5 -4. Before calling MT_ACTIV/SMS.ACJB it is possible for a programmer to alter the contents of any of these registers. For example the SuperBASIC commands EX and EW can set channel IDs and a parameter string on the stack. Their code achieves this by altering the contents of the stack and also the value of A7 as stored in the job's header, before calling MT_ACTIV/SMS.ACJB.
  
### Example 
The following shows the use of this call.

```
        moveq   #SMS.ACJB,d0    ; Or MT_ACTIV.
        moveq   #job_id,d1      ; Job_id is a known job id
        moveq   #32,d2          ; Standard default priority is 32
        moveq   #0,d3           ; Basically, EXEC/EX the other job. Then
                                ; continue executing from here.
        trap    #1              ; Activate the job
```
