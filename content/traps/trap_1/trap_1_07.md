---
title: "MT_TRAPV/SMS.EXV - Set the TRAP Vectors in RAM"
date: 2008-06-25T12:21:00Z
lastmod: 2008-06-25T12:52:00Z
draft: false
keywords: ["Trap #1", "Trap", "Jobs", "TRAP vectors"]
weight: 1007
---

Set the TRAP Vector table in RAM for the specified job.

|Call Parameters|Return Register|Return Parameter|
|---|---|---|---|
|D0.B|$07|D0.L|Error code.|
|D1.L|Id of the job for which the TRAP table is to be redirected. |D1.L|Preserved.|
|A0.L|Unused |A0.L|Base of job whose id was passed in D1.L.|
|A1.L|Ponter to new TRAP table. |A1.L|Corrupted.|

### Errors

| Error Code| Description|
|---|---|
|ERR_NJ|Invalid job id.|

### Notes
  * All registers not shown above are not used on entry and are preserved on exit.
  * This trap allows a job to redirect the TRAP #5 to TRAP #15 calls and exception handlers, //but not all of them//, which are normally unused by QDOS/SMS. 
  * Different jobs can have different vector tables, but a job is given the same table as its parent when first created. See the example code for full details of the format of the table etc.

### Example 
This example code, shows the use of this trap to create an exception handler for an **original QL** only. Later processors have different stack frames for the Address exception.

```
*==============================================================================
* This code adds a 'protective barrier' to the QL so that silly programming
* errors can be intercepted and hopefully handled before the QL crashes out.
*
* This code should only be run on a 68000 or 68008 as the exception stack
* frame is (probably) different on 68010 and above.
*==============================================================================

start       lea exceptions,a1       ; Table of exceptions (empty !)
            lea x_address,a2        ; Routine for address exceptions
            move.l a2,(a1)+         ; Save in table

            lea x_illegal,a2        ; Routine for illegal exceptions
            move.l a2,(a1)+         ; Save in table

            lea x_divide,a2         ; Divide by zero
            move.l a2,(a1)+         ; Save in table

            lea x_check,a2          ; CHK instruction
            move.l a2,(a1)+         ; Save in table

            lea x_trapv,a2          ; TRAPV instruction
            move.l a2,(a1)+         ; Save in table

            lea x_priv,a2           ; Privilege violation
            move.l a2,(a1)+         ; Save in table

            lea x_trace,a2          ; Trace exception
            move.l a2,(a1)+         ; Save in table

            lea x_int_7,a2          ; Interrupt level 7
            move.l a2,(a1)+         ; Save in table

            lea x_trap,a2           ; TRAP #5 to TRAP #15
            moveq #10,d0            ; There are 11 entries to fill

trap_loop   move.l a2,(a1)+         ; Save one in table
            dbra d0,trap_loop       ; Then do the rest

            lea exceptions,a1       ; Exceptions table again - full
            moveq #-1,d1            ; Job id = 'this job'
            moveq #mt_trapv,d0      ; Routine to set exception table
            trap #1                 ; And do it
            rts                     ; Return to SuperBasic with error code in D0

*==============================================================================
* Now the actual exception handlers themselves. Apart from the ADDRESS 
* exception, all have 3 words on the stack when called. ADDRESS has more.
*==============================================================================

x_address   lea t_address,a1    ; Message to print
            bsr message_0       ; Print the message
            addq.l #8,A7        ; Tidy extra data off the stack
            rte                 ; Attempt to continue

t_address   dc.w 15
            dc.b 'ADDRESS error.'
            dc.b 10

x_illegal   lea t_illegal,a1    ; Message to print
            bsr message_0       ; Print the message
            addq.l #2,2(a7)     ; Don't execute this instruction again !
            rte                 ; Attempt the next instruction

t_illegal   dc.w 21
            dc.b 'ILLEGAL instruction.'
            dc.b 10


x_divide    lea t_divide,a1     ; Message to print
            bsr message_0       ; Print the message
            rte                 ; Attempt to carry on

t_divide    dc.w 16
            dc.b 'DIVIDE BY ZERO.'
            dc.b 10


x_check     lea t_check,a1      ; Message to print
            bsr message_0       ; Print the message
            rte                 ; Attempt to carry on

t_check     dc.w 17
            dc.b 'CHK instruction.'
            dc.b 10


x_trapv     lea t_trapv,a1      ; Message to print
            bsr message_0       ; Print the message
            rte                 ; Attempt to carry on

t_trapv     dc.w 19
            dc.b 'TRAPV instruction.'
            dc.b 10


x_priv      lea t_priv,a1       ; Message to print
            bsr message_0       ; Print the message
            rte                 ; Attempt to carry on

t_priv      dc.w 21
            dc.b 'PRIVILEGE VIOLATION.'
            dc.b 10


x_trace lea t_trace,a1          ; Message to print
            bsr message_0       ; Print the message
            rte                 ; Attempt to carry on

t_trace     dc.w 25
            dc.b 'TRACE - not implemented.'
            dc.b 10


x_int_7 lea t_int_7,a1          ; Message to print
            bsr message_0       ; Print the message
            rte                 ; Attempt to carry on

t_int_7     dc.w 26
            dc.b 'DO NOT PRESS CTRL ALT 7 !'
            dc.b 10


x_trap      lea t_trap,a1       ; Message to print
            bsr message_0       ; Print the message
            rte                 ; Attempt to carry on

t_trap      dc.w 39
            dc.b 'TRAP #5 to TRAP #15 - not implemented.'
            dc.b 10


*==============================================================================
* This routine prints a message to channel 0. The message is at 0(A1) in the 
* usual QDOS format of a size word followed by the text. The UT_ERR0 routine
* expects an error code in D0 -or- the address of a user defined error message
* in D0 with bit 31 set to show that it is user defined.
*==============================================================================
message_0   move.l a1,d0        ; Address of user defined error message
            bset #31,d0         ; Mark as user defined
            move.w ut_err0,a2   ; The required routine is vectored
            jsr (a2)            ; Do the routine & print the message
            moveq #0,d0         ; Clear error flag
            rts

exceptions  ds.l    19          ; There are 19 exceptions to define
```
