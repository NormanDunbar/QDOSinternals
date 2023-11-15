---
title: "MT_JINF/SMS.INJB - Get Job Information"
date: 2013-06-03T14:50:00Z
lastmod: 2013-06-03T14:50:00Z
draft: false
keywords: ["Trap #1", "Trap", "Job Information"]
weight: 1002
---

Return information about a specific job. 

|Call Parameters|Return Register|Return Parameter|
|---|---|---|---|
|D0.B|$02|D0.L|Error code.|
|D1.L|Id of job to get information for.|D1.L|Id of next job in tree.|
|D2.L|Id of job at top of the tree.|D2.L|Parent job's id for the job we enquired upon.|
|D3.L|Unused.|D3.L|$000000pp = Active job's priority. $FFFFFFpp = Suspended job's priority.|
|A0.L|Unused.|A0.L|Base address, JB_END, of the job.| 
|A1.L|Unused.|A1.L|Corrupted.|

### Errors

| Error Code| Description|
|---|---|
|ERR_NJ|Invalid job id, in D1, on entry.|

### Notes
  * All registers not shown above are not used on entry and are preserved on exit.
  * This call can be used to scan the entire job tree, by initially calling it in a loop with D1.L = D2.L = zero, then on each subsequent call, leaving D1.L with the value returned, in D1.L, by the previous call. Eventually, D1.L will be returned as zero which indicates end of the tree. 
  * If, on entry, D1.L = D2.L <> zero, then only that sub-tree will be scanned, not the entire tree.  

### Example 
The following shows the use of this call to scan the entire job tree :

```
start   moveq   #0,d1             ; Zero = SuperBasic.
        move.l  d1,d2             ; Job at top of tree also = SuperBasic.
loop    moveq   #SMS.INJB,d0      ; Or MT_JINF
        trap    #1                ; Fetch job information, sets D1.L = next job in tree.
        tst.l   d0                ; Check for errors.
        bne.s   <Error handler>   ; Oops, an error occurred.
        tsl.l   d1                ; Are we done yet?
        beq.s   done              ; Yes, no more jobs.
        ...
        <Do stuff here with info returned, but preserve D1.L and D2.L>
        ...
        bra.s   loop              ; Using D1 and D2's values from the trap, go and do next job.
done    ...
```
