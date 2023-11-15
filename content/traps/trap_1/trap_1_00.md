---
title: "MT_INF/SMS.INFO - System Information"
date: 2008-06-25T12:21:00Z
lastmod: 2008-06-25T12:21:00Z
draft: false
keywords: ["Trap #1", "Trap", "System Information"]
weight: 1000
---

This trap returns information about the Operating System.

|Call Register|Call Parameter|Return Register| Return Parameter|
|---|---|---|---|
|D0.B|$00|D0.L|Undefined. Ignore return value.|
|D1.L|Unused.|D1.L|Job id of current job.|
|D2.L|Unused.|D2.L|QDOS/SMSQ version as ASCII string - '1.03' for example.|
|A0.L|Unused.|A0.L|Address of system variables.|


### Errors

No errors are returned by this trap. Ignore any value in D0 on return.

### Notes

* All registers not shown above are not used on entry and are preserved on exit.
* The returned address of the system variables in A0 is a 'throwback' to the old days of QDOS whereby they could live at any address in RAM. Then, they became fixed for a while at address $28000 (163,840) and now, the pendulum has swung back again - with Minerva and so on, the system variables do indeed live in different places in RAM once more.

### Example

The example below shows the use of this trap call to obtain the address of the system variables prior to some (unknown?) usage of that returned address.

```
info    moveq   #SMS.INFO,d0    ; Or MT_INF.
        trap    #1              ; Get system variables address into A0
        ...                     ; Do something here with A0.
```

