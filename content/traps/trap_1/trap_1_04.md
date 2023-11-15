---
title: "MT_RJOB/SMS.RMJB - Remove Existing Job"
date: 2013-06-03T14:50:00Z
lastmod: 2013-06-03T14:50:00Z
draft: false
keywords: ["Trap #1", "Trap", "Remove a job"]
weight: 1004
---

Remove an existing job from the system. The job should not be active, unless it is the current job -- ie, itself. To remove active jobs, use [MT_FRJOB/SMS.FRJB](/traps/trap_1/trap_1_05) instead.

|Call Parameters|Return Register|Return Parameter|
|---|---|---|---|
|D0.B|$04|D0.L|Error code.|
|D1.L|Id of job to be removed, or -1 for the current job.|D1.L|Corrupted.| 
|D2.L|Unused.|D2.L|Corrupted.|
|D3.L|Error code to return from killed job to any waiting jobs.|D3.L|Corrupted.| 
|A0.L|Unused.|A0.L|Corrupted.| 
|A1.L|Unused.|A1.L|Corrupted.|
|A2.L|Unused.|A2.L|Corrupted.| 
|A3.L|Unused.|A3.L|Corrupted.| 

### Errors

| Error Code| Description|
|---|---|
|ERR_NC|Not complete. The job being killed is still active.|
|ERR_NJ|Invalid job id.|  


### Notes
  * All registers not shown above are not used on entry and are preserved on exit.
  * Job 0 (SuperBasic) cannot be removed.
  * This trap is not atomic as it can exit via the scheduler - if the current job is being removed.
  * If the job being removed is active, then ERR_NC/ERR.NC (not complete) is returned. This tends to imply that passing -1 (current job) as the id of the job to be killed is a non-starter!
  * If the job has child jobs, they are removed whether or not they are active - (probably) using [MT_FRJOB/SMS.FRJB](/traps/trap_1/trap_1_05) below.
  * The error code in D3.L is passed to any job which is waiting for this job to complete.
  * Any resources - channels, memory etc - owned by the job being removed, or its children, are released.  

### Example 
The following shows the use of this call to remove the job with id $00010666.

```
start   moveq   #SMS.RMJB,d0      ; Or MT_RJOB.
        moveq   #$00010666,d1     ; I want to kill the job with this id.
        moveq   #0,d3             ; Don't pass any error codes to the job I'm killing.
        trap    #1                ; Kill this job. 
        tst.l   d0                ; Did it work?
        bne.s   <error handler>   ; It appears not.
        ...
```
