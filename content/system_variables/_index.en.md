---
title: "System Variables"
date: 2018-12-28T11:01:00+06:00
lastmd: 2018-12-28T11:01:00+06:00
icon: "ti-settings"
description: "Information about the QDOS and SMSQ/E System Variables."
type : "docs"
weight: 60
---


The system variables are used by QDOSMSQ to hold details of how the system looks. Some of these are helpful to user written programs, others are not. 

In the old days, QDOS was written on the assumption that the system variables would move around in memory. Then this was changed, and they became resident at address $28000 or 163840.

Recent changes have put things back where they were again, so *never* assume that the variables are fixed in memory.

For this reason, the table below gives *offsets* from the start of the system variables and not absolute addresses.

|Offset|Size|QDOS Mnemonic|SMSQ Mnemonic|Description|
| ----: | :----: | :----: | :----: | :---- |
|$00 |Long |SV_IDENT |SYS_IDNT |Identification word.<html><br></html>QDOS =  $D2540000<html><br></html>SMS = 'S2AT'<html><br></html>SMSQ = 'SMSQ'<html><br></html>Thor (ARGOS) = $DC010000 |
|$04 |Long |SV_CHEAP |SYS_CHPB |Pointer to start of common heap  |
|$08 |Long |SV_CHPFR |SYS_CHPF |Pointer to first free space in common heap  |
|$0C |Long |SV_FREE |SYS_FSBB |Pointer to start of free memory  |
|$10 |Long |SV_BASIC |SYS_SBAB |Pointer to SuperBasic area  |
|$14 |Long |SV_TRNSP |SYS_TPAB |Pointer to start of Transient Program area  |
|$18 |Long |SV_TRNFR |SYS_TPAF |Pointer to first free space in Transient Program area  |
|$1C |Long |SV_RESPR |SYS_RPAB |Pointer to start of Resident Procedure area  |
|$20 |Long |SV_RAMT |SYS_RAMT |Pointer to the end of RAM + 1  |
|$24 |Long |Unused |SYS_MXFR |Maximum return from free memory call |
|$28 |Long |Unused |SYS_RTC |Real time in seconds |
|$2C |Word |Unused |SYS_RTCF |Real time fractional countdown |
|$2E |Word |SV_RAND |SYS_RAND |Pseudo random number | 
|$30 |Word |SV_POLLM |SYS_PICT |Count of poll interrupts that have been missed  |
|$32 |Byte |SV_TVMOD |SYS_DTYP |TV/Monitor mode.<html><br></html>0 = Monitor.<html><br></html>1 = PAL TV (SMSQ only).<html><br></html>2 = NTSC TV ((525 Lines.)).<html><br></html><>2 = PAL TV (QDOS Only) ((625 lines.)) |
|$33 |Byte |SV_SCRST |SYS_DFRZ |Screen status (MODE corrupts this variable).<html><br></html>0 = Screen is active.<html><br></html><>0 = Screen is 'frozen'  |
|$34 |Byte |SV_MCSTA |SYS_QLMR |Copy of TV hardware register  |
|$35 |Byte |SV_PCINT |SYS_QLIR |Copy of Interrupt hardware register  |
|$36 |Byte |Unused |SYS_RSHD |OK to reschedule |
|$37 |Byte |SV_NETNR |SYS_NNNR |Network station number (1 to 64)  |
|$38 |Long |SV_I2LST |SYS_EXIL |Pointer to start of external interrup list  |
|$3C |Long |SV_PLIST |SYS_POLL |Pointer to start of polled tasks list  |
|$40 |Long |SV_SHLST |SYS_SHDL |Pointer to start of scheduler task list | 
|$44 |Long |SV_DRLST |SYS_IODL |Pointer to start of simple device driver list  |
|$48 |Long |SV_DDLST |SYS_FSDL |Pointer to start of directory device list  |
|$4C |Long |SV_KEYQ |SYS_CKYQ |Pointer to current keyboard queue (zero if no queue)  |
|$50 |Long |SV_TRAPV |SYS_ERTB |Pointer to current RAM vector table (zero if none)  |
|$54 |Long |SV_BTPNT |SYS_SBRP |Pointer to most recent slave block entry  |
|$58 |Long |SV_BTBAS |SYS_SBTB |Pointer to base of slave block table  |
|$5C |Long |SV_BTTOP |SYS_SBTT |Pointer to end of slave block table +1 | 
|$60 |Word |SV_JBTAG |SYS_JBTG |Current value of job tag  |
|$62 |Word |SV_JBMAX |SYS_JBTP |Highest job number so far  | 
|$64 |Long |SV_JBPNT |SYS_JBPT |Pointer to current job's entry in job table  |
|$68 |Long |SV_JBBAS |SYS_JBTB |Pointer to start of job table  |
|$6C |Long |SV_JBTOP |SYS_JBTT |Pointer to top of job table +1  |
|$70 |Word |SV_CHTAG |SYS_CHTG |Current value of channel tag  |
|$72 |Word |SV_CHMAX |SYS_CHTP |Highest channel number so far  |
|$74 |Long |SV_CHPNT |SYS_CHPT |Last channel checked by the waiting for I/O scheduler routine  |
|$78 |Long |SV_CHBAS |SYS_CHTB |Pointer to start of channel table  |
|$7C |Long |SV_CHTOP |SYS_CHTT |Pointer to end of channel table +1  |
|$80 |Long |Unused |SYS_FRBL |Free block list. (List of blocks to be returned to the common heap)| 
|$84 |Long |Unused|Unused|Unused|
|$88 |Word |SV_CAPS |SYS_CAPS |Caps lock indicator.<html><br></html>0 = off.<html><br></html>$FF00 = on  |
|$8A |Word |SV_ARBUF |SYS_LCHR |Last key pressed  |
|$8C |Word |SV_ARDEL |SYS_RDEL |Key repeat delay (default = 30 (QDOS), 25 (SMSQ))  |
|$8E |Word |SV_ARFRQ |SYS_RTIM |Key repeat frequency (default = 4 (QDOS), 2 (SMSQ)) | 
|$90 |Word |SV_ARCNT |SYS_RCNT |Key repeat counter  |
|$92 |Word |SV_CQCH |SYS_SWTC |Change keyboard queue code (3 = CTRL C)  |
|$94 |Word |SV_WP | |Write protect status of microdrives (Not implemented)  |
|$96 |Word |SV_SOUND |SYS_QLBP |Sound status.<html><br></html>0 = off.<html><br></html>$FF00 = on  |
|$98 |Long |SV_SER1C |SYS_SER1 |Pointer to serial port 1's input queue  |
|$9C |Long |SV_SER2C |SYS_SER2 |Pointer to serial port 2's input queue  |
|$A0 |Byte |SV_TMODE |SYS_TMOD |ULA transmit mode. <html><br></html>Bits 0 - 2 = baud rate number.<html><br></html>Bit 3 = 0 = ser1.<html><br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</html>= 1 = ser2.<html><br></html>Bit 4 = A microdrive is running?|
|$A1 |Byte |SV_PTYP |SYS_PTYP |Processor type.<html><br></html>$00 = 68000 or 68008.<html><br></html>$10 = 68010.<html><br></html>$20 = 68020 | 
|$A2 |Long |SV_CSUB |SYS_CSUB |Pointer to a routine to call when CAPS held down (or is it?)  |
|$A6 |Word |SV_TIMO |SYS_TMOT |Counter for timing serial output. ((On QDOS, the //word// at offset $A6 has the use described here. It appears, however, that in SMS[Q] on an Atari ST, the //byte// at $A6 is used to define if DMA (Direct Memory Access) is on or off.))|
|$A8 |Word |SV_TIMOV |SYS_TMOV |Value of timeout for the above. (= 1200/baud +1) ????  |
|$AA |Word |SV_FSTAT |SYS_FSTT |Cursor flash counter  |
|$AC |Long |SV_PROGD |SYS_PRGD |Pointer to current TK2 PROGD$, or zero  |
|$B0 |Long |SV_DATAD |SYS_DATD |Pointer to current TK2 DATAD$, or zero  |
|$B4 |Long |SV_SPLD |SYS_DSTD |Pointer to current TK2 spool device name  |
|$B8 |Long |SV_THGLST |SYS_THGL |Pointer to thing list (PE) |
|$BC |Long| |SYS_PSF|Primary stack frame pointer (SMSQ)|
|$C0 |Byte| |SYS_200I|200 Hz in service (SMSQ)|
|$C1 |Byte| |SYS_50I|50 Hz in service (SMSQ)|
|$C2 |Byte| |SYS_10I|10 Hz in service (SMSQ)|
|$C3 |Byte| |SYS_PLRQ|Poll requested (-ve for request) (SMSQ)|
|$C4 |Long| |SYS_CLNK|Pointer to console linkage (SMSQ)|
|$C8 |Byte| |SYS_CASTAT|-1 cache on, +1 instruction cache temp off (SMSQ)|
|$C9 |Byte| |SYS_CASUP|Cache suppressed timer (SMSQ)|
|$CA |Word| |SYS_IOPR|IO priority (SMSQ)|
|$CC |Long| |SYS_CBAS|Current basic (copy of SYS_JBPT) (SMSQ)|
|$D0 |Byte| |SYS_FPU|Type of FPU (FPSAVE)<html><br></html>-1 = no FPU<html><br></html>1  = 68881 and no FPSP<html><br></html>2  = 68882 and no FPSP<html><br></html>4  = 68040 and no FPSP<html><br></html>6  = 68060 and no FPSP<html><br></html>9  = 68881 and FPSP (library version)<html><br></html>10 = 68882 and FPSP (library version)<html><br></html>12 = 68040 and FPSP (library version)<html><br></html>14 = 68060 and FPSP (library version)|
|$D1 |Byte| |SYS_MMU|Type of MMU (FPSAVE)<html><br></html>-1 = no MMU<html><br></html>1 = 68851<html><br></html>3 = 68030<html><br></html>4 = 68040 or 68LC040<html><br></html>7 = 68060 or 68LC060|
|$D2 |Word| |SYS_FPZS|Maximum length of FPSAVE area (FPSAVE)|
|$D4 |Long| |SYS_FPSL|Address of save area list (FPSAVE)|
|$D8 |Long| |SYS_CLFP|Address to access the FPSP (FPSAVE)|
|$DC |Long| | |Unused|
|$E0 |Byte| |SYS_PRTC|Set if real time clock protected (SMSQ)|
|$E1 |Byte| |SYS_PMEM|Memory protection level (SMSQ ST)|
|$E2 |Word| |SYS_SLUG|Slug level (SMSQ)|
|$E4 |Long| | |Unused|
|$E8 |Long| |SYS_KLNK|Pointer to keyboard linkage|
|$EC |Word| | |Unused|
|$EE |Byte |SV_MDRUN |SYS_MDRN |Which microdrive is running (1 - 8)  |
|$EF |Byte |SV_MDCNT |SYS_MDCT |Microdrive run up/down counter  |
|$F0 |8 Bytes |SV_MDDID |SYS_MDID |Drive id * 4 for 8 microdrives|  
|$F8 |8 Bytes |SV_MDSTA |SYS_MDST |Status of each microdrive. 0 = no pending operations  |
|$100 |16 Longs |SV_FSDEF |SYS_FSDD |16 Pointers to file system definitions |
|$140 |Long |SV_FSLST |SYS_FSDT |Pointer to list of file channel definitions |
|$144 |Byte |SV_TRAA |SYS_XACT |TRAnslate is active flag. 0 = inactive, <>0 = active  |
|$145 |Byte | | |Unused |
|$146 |Long |SV_TRATAB |SYS_XTAB |Pointer to TRAnslate table |
|$14A |Long |SV_MSGTAB |SYS_ERMS |Pointer to (TRA) message table |
|$14E |Long| |SYS_MSTAB|Pointer to SMSQ message table|
|$154 |4 Longs| |SYS_TASKM|used by Taskmaster - conflicts with . .|
|$160 |Long| |SYS_TURBO|used by Turbo|
|$164 |Long| |SYS_QSOUND|used by QSound - conflicts with . .|
|$166 |Long| |SYS_LANG|Current language code (SMSQ)|
|$168 |Long| |SYS_LDMLST|Language dependent module list (SMSQ)|
|$16C |Word| |SYS_LANG|Current language (SMSQ)|
|$16E |Word| | |Unused|
|$170 |Long| |SYS_VERS|Operating system version (SMSQ)|

