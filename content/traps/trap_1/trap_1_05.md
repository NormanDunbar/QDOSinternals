---
title: "MT_FRJOB/SMS.FRJB - Force Remove a Job"
date: 2008-06-25T12:21:00Z
lastmod: 2008-06-25T12:52:00Z
draft: false
keywords: ["Trap #1", "Trap", "Jobs", "Killing Jobs", "Job Removal"]
weight: 1005
---

Force remove an existing job. The job may be active, if so it will be rendered inactive and then removed.

|Call Parameters|Return Register|Return Parameter|
|---|---|---|---|
|D0.B|$05|D0.L|Error code.|
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
|ERR_NJ|Invalid job id.|  

### Notes
  * All registers not shown above are not used on entry and are preserved on exit.
  * Job 0 (SuperBasic) cannot be removed.
  * This trap is not atomic as it can exit via the scheduler - if the current job is being removed.
  * If the job being removed is active, it is inactivated and removed.
  * If the job has child jobs, they are removed whether or not they are active.
  * The error code in D3.L is passed to any job which is waiting for this job to complete.
  * Any resources - channels, memory etc - owned by the job being removed, or its children, are released.  

### Example 
The following shows the use of this call to remove the current job.

```
        ...
kill_me moveq   #SMS.FRJB,d0      ; Or MT_FRJOB.
        moveq   #-1,d1            ; Indicate the current job.
        moveq   #0,d3             ; Don't pass any error codes.
        trap    #1                ; Kill this job. 
        bra.s   kill_me           ; Shouldn't get here, but just in case!
```
