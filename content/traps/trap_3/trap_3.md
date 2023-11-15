---
title: "Trap #3 - Input/Output & Pointer Environment Traps"
date: 2008-06-08T14:46:00Z
lastmod: 2017-09-09T23:28:00Z
draft: false
keywords: ["I/O traps", "Trap #3", "Trap", "PE traps"]
weight: 40
---

Trap #3 covers an awful lot of utilities. Almsot everything to do wiuth the screen, file output and input -- as opposed to opening and closing files that is -- and there has also been the addition of the PE traps to the mix.

You can [skip to the PE traps](#pe_traps) if you wish.

|D0 Key|QDOS Mnemonic|SMSQ/E Mnemonic|Description|
|------|-------------|---------------|-----------|
|  $00  |IO_PEND |IOB.TEST  |Test for pending input|
|  $01  |IO_FBYTE |IOB.FBYT |Fetch a byte|
|  $02  |IO_FLINE |IOB.FLIN |Fetch a line of bytes|
|  $03  |IO_FSTRG |IOB.FMUL |Fetch a string|
|  $04  |IO_EDLIN |IOB.ELIN |Edit a line|
|  $05  |IO_SBYTE |IOB.SBYT |Send one byte|
|  $06  | - |IOB.SUML|[[SUML|Send a string of untranslated bytes|
|  $07  |IO_SSTRG |IOB.SMUL |Send a string of bytes|
|  $08  |||Not implemented|
|  $09  |SD_EXTOP |IOW.XTOP |Calls an external screen operation|
|  $0A  |SD_PXENQ |IOW.PIXQ |Get window size/cursor position (pixels)|
|  $0B  |SD_CHENQ |IOW.CHRQ |Get window size/cursor position (Characters)|
|  $0C  |SD_BORDR |IOW.DEFB |Sets the border on a window|
|  $0D  |SD_WDEF |IOW.DEFW |Re-defines a window|
|  $0E  |SD_CURE |IOW.ECUR |Enable a cursor|
|  $0F  |SD_CURS |IOW.DCUR |Disable a cursor|
|  $10  |SD_POS |IOW.SCUR |Set cursor position|
|  $11  |SD_TAB |IOW.SCOL |Set horizontal cursor position|
|  $12  |SD_NL |IOW.NEWL    |Do a newline|
|  $13  |SD_PCOL |IOW.PCOL  |Cursor to previous column|
|  $14  |SD_NCOL |IOW.NCOL  |Cursor to next column|
|  $15  |SD_PROW |IOW.PROW  |Cursor to previous row|
|  $16  |SD_NROW |IOW.NROW  |Cursor to next row|
|  $17  |SD_PIXP |IOW.SPIX  |Set cursor position by pixel|
|  $18  |SD_SCROL |IOW.SCRA |Scroll window|
|  $19  |SD_SCRTP |IOW.SCRT |Scroll top of window|
|  $1A  |SD_SCRBT |IOW.SCRB |Scroll bottom of window|
|  $1B  |SD_PAN |IOW.PANA   |Pan window|
|  $1C  |||Not implemented|
|  $1D  |||Not implemented|
|  $1E  |SD_PANLN |IOW.PANL |Pan cursor line|
|  $1F  |SD_PANRT |IOW.PANR |Pan right side of cursor line|
|  $20  |SD_CLEAR |IOW.CLRA |Clears a window|
|  $21  |SD_CLRTP |IOW.CLRT |Clear the top of a window|
|  $22  |SD_CLRBT |IOW.CLRB |Clear the bottom of a window|
|  $23  |SD_CLRLN |IOW.CLRL |Clears the whole of the cursor line|
|  $24  |SD_CLRRT |IOW.CLRR |Clears the right side of the cursor line|
|  $25  |SD_FOUNT |IOW.FONT |Set the fonts for a window|
|  $26  |SD_RECOL |IOW.RCLR |Recolour a window|
|  $27  |SD_SETPA |IOW.SPAP |Set paper colour|
|  $28  |SD_SETST |IOW.SSTR |Set strip colour|
|  $29  |SD_SETIN |IOW.SINK |Set ink colour|
|  $2A  |SD_SETFL |IOW.SFLA |Set flash mode|
|  $2B  |SD_SETUL |IOW.SULA |Set underline mode|
|  $2C  |SD_SETMD |IOW.SOVA |Set PRINT mode|
|  $2D  |SD_SETSZ |IOW.SSIZ |Set character size|
|  $2E  |SD_FILL |IOW.BLOK |Fill a block|
|  $2F  |SD_DONL |IOW.DONL |Do a pending new line|
|  $30  |SD_POINT |IOG.DOT |Plot a point|
|  $31  |SD_LINE |IOG.LINE |Draw a line|
|  $32  |SD_ARC |IOG.ARC |Draw an arc of a circle|
|  $33  |SD_ELIPS |IOG.ELIP |Draw an ellipse (or circle)|
|  $34  |SD_SCALE |IOG.SCAL |Set the scale in a window|
|  $35  |SD_FLOOD |IOG.FILL |Set fill mode|
|  $36  |SD_GCUR |IOG.SGCR |Set cursor position using graphics co-ordinates|
| $37-$3F |||Not implemented|
|  $40  |FS_CHECK |IOF.CHEK |Check pending operations on a file|
|  $41  |FS_FLUSH |IOF.FLSH |Flush a file's buffers to the device|
|  $42  |FS_POSAB |IOF.POSA |Set file position by absolute address|
|  $43  |FS_POSRE |IOF.POSR |Set file position by relative address|
|  $44  ||| Not implemented. Originally reserved for rename|
|  $45  |FS_MDINF |IOF.MINF |Get medium information|
|  $46  |FS_HEADS |IOF.SHDR |Set a file header|
|  $47  |FS_HEADR |IOF.RHDR |Read a file header|
|  $48  |FS_LOAD |IOF.LOAD  |Load a file|
|  $49  |FS_SAVE |IOF.SAVE  |Save a file|
|  $4A  |FS_RENAME |IOF.RNAM |Rename a file|
|  $4B  |FS_TRUNCATE |IOF.TRNC |Truncate a file at its current position|
|  $4C  ||IOF.DATE |Set or get a file's date(s)|
|  $4D  ||IOF.MKDIR |Create a new directory|
|  $4E  ||IOF.VERS |Set or get a file's version|
|  $4F  ||IOF.XINF |Get extended information|
|  $50  ||IOW.PAPP |Palette colour paper |
|  $51  ||IOW.STRP |Palette colour strip|
|  $52  ||IOW.INKP |Palette colour ink|
|  $53  ||IOW.BORP |Palette colour border|
|  $54  ||IOW.PAPT |True colour paper|
|  $55  ||IOW.STRT |True colour strip|
|  $56  ||IOW.INKT |True colour ink|
|  $57  ||IOW.BORT |True colour border|
|  $58  ||IOW.PAPN |Native colour paper|
|  $59  ||IOW.STRN |Native colour strip|
|  $5A  ||IOW.INKN |Native colour ink|
|  $5B  ||IOW.BORN |Native colour border|
|  $5C  ||IOW.BLKP |Palette colour block|
|  $5D  ||IOW.BLKT |True colour block|
|  $5E  ||IOW.BLKN |Native colour block|
|  $5F  ||| Not implemented|
|  $60  ||IOW.PALQ |Define QL palette|
|  $61  ||IOW.PAL8 |Define 8 bit palette|
|$62-$6A|||Not implemented|
|  $6B  ||IOW.WPAP |Define screen wallpaper| 


## Trap #3 - Pointer Environment {#pe_traps}

|D0 Key|Mnemonic|Description|
|---|---|---|
| $6C | IOP.FLIM | Find window limits|
| $6D | IOP.SVPW | Partial window save|
| $6E | IOP.RSPW | Partial window restore|
| $6F | IOP.SLNK | Set linkage block|
| $70 | IOP.PINF | Information enquiry|
| $71 | IOP.RPTR | Read pointer|
| $72 | IOP.RPXL | Read pixel at x,y|
| $73 | IOP.WBLB | Write blob at x,y|
| $74 | IOP.LBLB | Write line of blobs|
| $75 || Not implemented |
| $76 | IOP.WSPT | Write sprite at x,y|
| $77 | IOP.SPRY | Spray pixels in blob|
| $78 || Not implemented |
| $79 || Not implemented |
| $7A | IOP.OUTL | Set window outline|
| $7B | IOP.SPTR | Set pointer position|
| $7C | IOP.PICK | Pick window|
| $7D | IOP.SWDF | Set sub-window definition pointer|
| $7E | IOP.WSAV | Save window area|
| $7F | IOP.WRST | Restore window area|

