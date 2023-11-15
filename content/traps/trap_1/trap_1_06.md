---
title: "MT_FREE/SMS.FRTP - Find Maximum Free Memory"
date: 2008-06-25T12:21:00Z
lastmod: 2008-06-25T12:52:00Z
draft: false
keywords: ["Trap #1", "Trap", "Jobs", "Free memeory size"]
weight: 1006
---

Force remove an existing job. The job may be active, if so it will be rendered inactive and then removed.

|Call Parameters|Return Register|Return Parameter|
|---|---|---|---|
|D0.B|$06|D0.L|Corrupted?|
|D1.L|Unused |D1.L|Maximum free space in bytes.|
|D2.L|Unused |D2.L|Corrupted.|
|D3.L|Unused |D3.L|Corrupted.|
|A0.L|Unused |A0.L|Corrupted.|
|A1.L|Unused |A1.L|Corrupted.|
|A2.L|Unused |A2.L|Corrupted.|
|A3.L|Unused |A3.L|Corrupted.| 

### Errors

No errors are returned by this trap.

### Notes
  * All registers not shown above are not used on entry and are preserved on exit.
  * This trap returns the number of bytes in the biggest free area of Transient Program space or the number of bytes between SV_BASIC and SV_FREE minus 512 - whichever is the biggest.
  * Beware of the fact that by the time you have obtained the figure, you might still fail to allocate it all if another job has got there first and allocated some space for itself. LOL

### Example 
The following shows the use of this call :

```
        ...
mfree   moveq   #SMS.FRTP,d0    ; Or MT_FREE.
        trap    #1              ; Find amount of free space 
        ...
        <do stuff here with the number of bytes in D1.L>
        ...
```
