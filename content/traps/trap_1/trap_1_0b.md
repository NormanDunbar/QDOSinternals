---
title: "MMT_PRIOR/SMS.SPJB - Set a Job's Priority"
date: 2008-06-25T12:21:00Z
lastmod: 2008-06-25T12:52:00Z
draft: false
keywords: ["Trap #1", "Trap", "Jobs", "Job priority"]
weight: 1011
---

Set a job's priority.

|Call Parameters|Return Register|Return Parameter|
|---|---|---|---|
|D0.B|$0B|D0.L|Error code.|
|D1.L|Id of the job to be prioritised.|D1.L|Preserved unless on entry it was -1 in which case it will become the Id of the current job.|
|D2.B|Job's new priority. See notes below.|D2.L|Preserved.|
|D3|Unused.|D3|Preserved.|
|A0.L|Unused. |A0.L|Base address of prioritised job.|

### Errors

| Error Code| Description|
|---|---|
|ERR_NJ|Invalid job id.|

### Notes
  * All registers not shown above are not used on entry and are preserved on exit.
  * The priority passed in D2.B is documented as being in the range 0 to 127, however, it appears that values from 0 to 255 can be used.
  * This trap always exits via the job scheduler, so it is not atomic.
  
### Example 
The following shows the use of this call.

```
        moveq   #mt_prior,d0    ; Or MT+PRIOR.
        moveq   #-1,d1          ; Change my own priority.
        moveq   #1,d2           ; Set priority very low.
        trap    #1              ; Change the priority.
```
