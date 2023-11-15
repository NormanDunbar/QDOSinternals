---
title: "MT_SUSJB/SMS.SSJB - Suspend an Existing Job"
date: 2008-06-25T12:21:00Z
lastmod: 2008-06-25T12:52:00Z
draft: false
keywords: ["Trap #1", "Trap", "Jobs", "Suspending Jobs"]
weight: 1008
---

Suspend an existing job. (ok, how do I suspend a non-existing job then? LOL)

|Call Parameters|Return Register|Return Parameter|
|---|---|---|---|
|D0.B|$08|D0.L|Error code.|
|D1.L|Id of the job to be suspended. -1 indicates the current job. |D1.L|Preserved. Unless the job id of -1 was used on entry, in which case the return value is the job id of the current job.|
|D2.L|Unused.|D2.L|Preserved.|
|D3.W|Timeout period in frames. -1 indicates indefinite suspension.|D3.L|Preserved.|
|A0.L|Unused. |A0.L|Base address of suspended job.|
|A1.L|Address of the flag byte (see below) or zero. |A1.L|Preserved. (or is it? See notes below.).|
### Errors

| Error Code| Description|
|---|---|
|ERR_NJ|Invalid job id.|

### Notes
  * All registers not shown above are not used on entry and are preserved on exit.
  * A1.L is documented as being preserved, but personal experience has shown that it is //not always// preserved.
  * The value in D3.W is stored in the job's header at JB_STAT.
  * The value in A1.L is stored in the header at JB_HOLD. The flag byte at (A1) will be cleared when the job is released (unsupended) again.
  * A frame period is 1/50th of a second in UK/Europe but 1/60th in the USA.
  * If the job being suspended is already suspended, then the current suspend period is changed to the new one, and the job is re-suspended for the new period of time in full.
  * This trap always exits via the job scheduler, so it is not atomic.
### Example 
The following shows the use of this call.

```
        moveq   #SMS.SSJB,d0    ; Or MT_SUSJB
        moveq   #-1,d1          ; Suspend myself
        moveq   #50,d3          ; Suspend for 50 frames
        suba.l  a1,a1           ; No flag byte required
        trap    #1              ; Suspend the job
        ...
        <Carry on as normal when the suspension is over.>
        ...
```
