---
title: "Trap #1 - Manager Traps"
date: 2008-06-08T14:46:00Z
lastmod: 2017-09-09T23:28:00Z
draft: false
keywords: ["Manager trap", "Trap 1", "Trap"]
weight: 20
---

|D0 Key|QDOS/SMSQ Mnemonic|Description|
|:---:|:---|:---|
|  $00  |MT_INF/SMS.INFO |[Get system information](/traps/trap_1/trap_1_00)|
|  $01  |MT_CJOB/SMS.CRJB |[Create but do not ativate a new job](/traps/trap_1/trap_1_01)|
|  $02  |MT_JINF/SMS.INJB |[Get information for an existing job](/traps/trap_1/trap_1_02)|
|  $03  |||Not implemented|
|  $04  |MT_RJOB/SMS.RMJB |[Remove an existing job](/traps/trap_1/trap_1_04)|
|  $05  |MT_FRJOB/SMS.FRJB |[Force remove an existing job](/traps/trap_1/trap_1_05)|
|  $06  |MT_FREE/SMS.FRTP |[Find the maximum amount of free memory](/traps/trap_1/trap_1_06)|
|  $07  |MT_TRAPV/SMS.EXV |[Set the TRAP Vector table in RAM](/traps/trap_1/trap_1_07)|
|  $08  |MT_SUSJB/SMS.SSJB |[Suspend an existing job](/traps/trap_1/trap_1_08)|
|  $09  |MT_RELJB/SMS.USJB |[Release a suspended job](/traps/trap_1/trap_1_09)|
|  $0A  |MT_ACTIV/SMS.ACJB |[Activate a newly created job](/traps/trap_1/trap_1_0a)|
|  $0B  |MT_PRIOR/SMS.SPJB |[Set a job's priority](/traps/trap_1/trap_1_0b)|
|  $0C  |MT_ALLOC/SMS.ALHP |Allocate user heap space|
|  $0D  |MT_LNKFR/SMS.REHP |Free allocated user heap space|
|  $0E  |MT_ALRES/SMS.ARPA |Allocate resident procedure space|
|  $0F  |MT_RERES |Not Implemented on SMSQ/E|Release allocated resident procedure space|
|  $10  |MT_DMODE/SMS.DMOD |Read or set the display mode|
|  $11  |MT_IPCOM/SMS.HDOP |Use the IPC second processor commands|
|  $12  |MT_BAUD/SMS.COMM |Set the baud rate for the serial ports|
|  $13  |MT_RCLCK/SMS.RRTC |Read the system clock|
|  $14  |MT_SCLCK/SMS.SRTC |Set the system clock|
|  $15  |MT_ACLCK/SMS.ARTC |Adjust the system clock|
|  $16  |MT_ALBAS/SMS.AMPA |Allocate SuperBasic space|
|  $17  |MT_REBAS/SMS.RMPA |Free allocated SuperBasic space|
|  $18  |MT_ALCHP/SMS.ACHP|Allocate space in the common heap|
|  $19  |MT_RECHP/SMS.RCHP |Release allocated space in the common heap|
|  $1A  |MT_LXINT/SMS.LEXI |Link an external interrupt handler routine|
|  $1B  |MT_RXINT/SMS.REXI |Unlink an external interrupt handler routine|
|  $1C  |MT_LPOLL/SMS.LPOL |Link a polled task|
|  $1D  |MT_RPOLL/SMS.RPOL |Unlink a polled task|
|  $1E  |MT_LSCHD/SMS.LSHD |Link a scheduler task|
|  $1F  |MT_RSCHD/SMS.RSHD |Unlink a scheduler task|
|  $20  |MT_LIOD/SMS.LIOD |Link a new I/O driver|
|  $21  |MT_RIOD/SMS.RIOD |Unlink an I/O driver|
|  $22  |MT_LDD/SMS.LFSD |Link a new directory driver|
|  $23  |MT_RDD/SMS.RFSD |Unlink a directory driver|
|  $24  |MT_TRA/SMS.TRNS |Set TRA & message tables |

The following are not found in the original QDOS and are SMS only.

|D0 Key|SMSQ/E Mnemonic|Description|
|------|---------------|-----------|
|  $26  | SMS.LTHG |Link a Thing into the Thing list|
|  $27  | SMS.RTHG |Unlink a Thing from the Thing list|
|  $28  | SMS.UTHG |Use a Thing|
|  $29  | SMS.FTHG |Free a Thing|
|  $2A  | SMS.ZTHG |Zap a Thing|
|  $2B  | SMS.NTHG |Get next Thing|
|  $2C  | SMS.NTHU |Get next Thing user|


The following are now in SMSQ.

|D0 Key|SMSQ/E Mnemonic|Description|
|------|---------------|-----------|
|  $25  | SMS.XTOP |Do external operation|
|  $2E  | SMS.IOPR |IO priority|
|  $2F  | SMS.CACH |Cache handling|
|  $30  | SMS.LIDM |Link in language dependent module|
|  $31  | SMS.LENQ |Language enquiry|
|  $32  | SMS.LSET |Language set|
|  $33  | SMS.PSET |Printer translate set|
|  £34  | SMS.MPTR |Find a message pointer|
|  $35  | SMS.FPRM |Find preferred module|
|  $36  ||Not implemented|
|  $37  ||Not implemented|
|  $38  | SMS.SCHP |Shrink allocation in common heap|
|  $39  ||Not implemented|
|  $3A  | SMS.SEVT |Send event to job|
|  $3B  | SMS.WEVT |Wait for event|

