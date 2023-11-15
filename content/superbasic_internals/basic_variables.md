+++
title = "SuperBASIC Variables"
author = "Norman Dunbar"
tags = ["Basic variables"]
categories = ["SuperBASIC Variables"]
description = "SuperBASIC Variables"
date = "2018-12-28T11:05:00Z"
lastmod = "2018-12-28T11:05:00Z"
weight = 10
+++

The basic variables are used by SuperBASIC to hold details of how the Basic system looks. Some of these are helpful to user written programs, others are not. 

### SuperBasic Variables Area 

|Offset|Size|Mnemonic|Description|
| ----: | :----: | :----: | :---- |
|$00 |Long |BV_BFBAS |Buffer Base |
|$04 |Long |BV_BFP |Buffer running pointer - aka Top |
|$08 |Long |BV_TKBAS |Token List |
|$0C |Long |BV_TKP |Token List Top |
|$10 |Long |BV_PFBAS |Program File Base |
|$14 |Long |BV_PFP |Program File Top |
|$18 |Long |BV_NTBAS |Name Table Base |
|$1C |Long |BV_NTP |Name Table Top |
|$20 |Long |BV_NLBAS |Name List Base |
|$24 |Long |BV_NLP |Name List Top |
|$28 |Long |BV_VVBAS |Variables Values Base |
|$2C |Long |BV_VVP |Variables Values Top |
|$30 |Long |BV_CHBAS |Channel Table Base |
|$34 |Long |BV_CHP |Channel Table Top |
|$38 |Long |BV_RTBAS |Return Table Base |
|$3C |Long |BV_RTP |Return Table Top |
|$40 |Long |BV_LNBAS |Line Number Table Base |
|$44 |Long |BV_LNP |Line Number Table Top |
|$48 |Long |BV_BTP |Backtrack Stack during parsing |
|$4C |Long |BV_BTBAS |Backtrack Stack Base |
|$50 |Long |BV_TGP |Temporary Graph Stack during Parsing |
|$54 |Long |BV_TGBAS |Temporary Graph Stack Base |
|$58 |Long |BV_RIP |Maths Stack Top |
|$5C |Long |BV_RIBAS |Maths Stack Base |
|$60 |Long |BV_SSP |System Stack Top |
|$64 |Long |BV_SSBAS |System Stack Base |
|$68 |Word |BV_LINUM |Current Line Number |
|$6A |Word |BV_LENGTH |Current Line Length |
|$6C |Byte |BV_STMNT |Current Statement on Line |
|$6D |Byte |BV_CONT |Continue ($80) or stop ($00) Processing |
|$6E |Byte |BV_INLIN |Processing in-line clause or not. Loop ($01), other ($FF) or off ($00) |
|$6F |Byte |BV_SING |Single line execution on ($ff) or off ($00) |
|$70 |Word |BV_INDEX |Name table row of last in-line loop index used |
|$72 |Long |BV_VVFREE |First free space in variables area |
|$76 |Long |BV_SSSAV |Value of SP to go to when Out of Memory occurs |
|$7A |6 Bytes | |Currently unused |
|$80 |Long |BV_RAND |Randon number |
|$84 |Long |BV_COMCH |Command Channel ID |
|$88 |Word |BV_NXLIN |Line number to start after |
|$8A |Byte |BV_NXSTM |Statement to start after |
|$8B |Byte |BV_COMLN |Command line saved ($FF) or not ($00) |
|$8C |Word |BV_STOPN |Set stop number |
|$8E |Byte |BV_EDIT |Program has been edited ($FF) or not ($00) |
|$8F |Byte |BV_BRK |Break during I/O ($00) or not ($80) |
|$90 |Byte |BV_UNRVL |Need to unravel ($FF) or not ($00) |
|$91 |Byte |BV_CNSTM |Statement to CONTinue after |
|$92 |Word |BV_CNLNO |Line number to CONTinue after |
|$94 |Word |BV_DALNO |Current DATA line number |
|$96 |Byte |BV_DASTM |Current DATA stamement number |
|$97 |Byte |BV_DAITM |DATA item counter |
|$98 |Word |BV_CNIND |In-line loop index to CONTinue with |
|$9A |Byte |BV_CNINL |In-line loop flag for CONTinue |
|$9B |Byte |BV_LSANY |Whether checking LIST ($FF) or not ($00) |
|$9C |Word |BV_LSBEF |Invisible top line number |
|$9E |Word |BV_LSBAS |Bottom line number in window |
|$A0 |Word |BV_LSAFT |Invisible bottom line number |
|$A2 |Word |BV_LENLN |Length of window line |
|$A4 |Word |BV_MAXLN |Number of lines in window |
|$A6 |Word |BV_TOTLN |Number of window lines so far |
|$A8 |Word | |Currently unused |
|$AA |Byte |BV_AUTO |AUTO or EDIT is on ($FF) or off ($00) |
|$AB |Byte |BV_PRINT |Print from PRTOK ($FF) or not ($00) |
|$AC |Word |BV_EDLIN |Next editable line number |
|$AE |Word |BV_EDINC |AUTO incerment number |
|$B0 |Long |BV_TKPOS |Location of A4 in Token List when starting a PROCedure |
|$B4 |Long |BV_PTEMP |Temporary pointer for GO_PROC |
|$B8 |Byte |BV_UNDO |Undo return stack ($FF) or not ($00). Results in PROC/FN Cleared. |
|$B9 |Byte |BV_ARROW |Arrow down ($FF) or up ($01) or neither ($00) |
|$BA |Word |BV_LSFIL |Where to fill window to, when relisting |
|$BC |Word |BV_WRLNO |WHEN ERRor line number. QDOS 1.10 onwards |
|$BE |Byte |BV_WRSTM |WHEN ERRor statement number. QDOS 1.10 onwards |
|$BF |Byte |BV_WRINL |WHEN ERRor in-line ($FF) or not ($000). QDOS 1.10 onwards |
|$C0 |Word |BV_WHERR |Processing WHEN ERRor ($80) or not ($00). QDOS 1.10 onwards |
|$C2 |Long |BV_ERROR |Last error code. QDOS 1.10 onwards |
|$C6 |Word |BV_ERLIN |Last error line number. QDOS 1.10 onwards |
|$C8 |Word |BV_WVNUM |Number of watched (WHEN) variables. QDOS 1.10 onwards |
|$CA |Long |BV_WVBAS |Base of WHEN variable table. QDOS 1.10 onwards  |
|$CF-$9F|49 Bytes | |Currently unused |
|$100 | |BV_END |End of Basic Variables |
