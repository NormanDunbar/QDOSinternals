---
title: "MT_RELJB | SMS.USJB - Release a Suspended Job"
date: 2008-06-25T12:21:00Z
lastmod: 2008-06-25T12:52:00Z
draft: false
keywords: ["Trap #1", "Trap", "Jobs", "Release suspended jobs"]
weight: 1009
---

Release an existing job from suspension. (ok, how do I release a non-existing job then? LOL)

|Call Parameters|Return Register|Return Parameter|
|---|---|---|---|
|D0.B|$09|D0.L|Error code.|
|D1.L|Id of the job to be Released.|D1.L|Preserved.|
|A0.L|Unused. |A0.L|Base address of released job.|

### Errors

| Error Code| Description|
|---|---|
|ERR_NJ|Invalid job id.|

### Notes
  * All registers not shown above are not used on entry and are preserved on exit.
  * The flag byte (at the address held in JB_HOLD in the job header) will be cleared when the job is released.
  * This trap always exits via the job scheduler, so it is not atomic.
  
### Example 
The following shows the use of this call.

```
        moveq   #mt_reljb,d0    ; Or MT_RELJB.
        moveq   #job_id,d1      ; Job_id is a known job id.
        trap    #1              ; Release the suspended job.
```
