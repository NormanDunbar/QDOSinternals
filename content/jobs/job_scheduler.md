+++
title = "The Scheduler - by Dave Westbury"
date = 2008-03-13T09:07:00Z
lastmod = 2008-03-13T09:07:00Z
keywords = ["Jobs", "Job details", "Tasks"]
draft = false
weight = 20
+++

Having [disassembled](#scheduler_disassembly) part of JS QDOS many years ago to get an idea of exactly how it worked, I jotted down an algorithm of how the scheduler performed its task. Thought some readers might find the following interesting.

### The Theory 

The scheduler is the part of the operating system which controls the executing and scheduling of all the jobs in the machine. It is normally called every time a frame interrupt occurs (approx every 1/50th second), or indirectly after other certain operating system routines have been used (Traps). Apart from switching the processor from the current job to the next deserving processor time (based on priority) it also deals with waiting input/output. Since this function makes the system more efficient I'll explain it in more detail.

To save a job wasting time (ie, using a whole processor time slice of 20mS) for things like; waiting for the user to press a key (input), or to send a byte to SER (eg, output to printer) QDOS allows a job to be suspended and its waiting input/output attempted by the scheduler. To do this, every time a job requests an input/output operation to be done (through a previously opened channel), the job is required to specify a 'timeout' for the operation. Each time the scheduler attempts the jobs input/output it decrements this timeout according to the number of frame interrupts that (may) have occurred since the last attempt. If it never gets done in the time allocated it returns its request as incomplete, otherwise the operation is done and (in both cases) the job is released from suspension so that it can continue. As a frame interrupt normally occurs every 20mS this timeout figure thus specifies how long in 1/50ths of a second it has to do the operation. Instead of giving a finite timeout most jobs usually give -1, which means wait until the operation is done else wait forever.

For example the SuperBasic function INKEY$ requires a timeout to be specified, this is used directly in the call to get a key from a channel opened to the device 'con'. Eg, a$=INKEY$(50) means wait for up to 1 second for a key to be pressed else return, (incomplete in this case returns an empty string). Thus using -1 means wait forever if no key pressed. Since infinite timeouts in jobs can cause a job to be suspended forever if they never can do the input or output operation, SuperBasic has a special key to force an abort for any of its input/output operations ie, the 'break' key CTRL SPACE.

If we could likewise specify timeouts on PRINTing in SuperBasic (like other jobs can) we could write programs (or compile them) such that it would be possible to detect when we have been unable to print to our buried job windows. eg, "here's the answer you wanted, but if I find I'm buried (ie, times out incomplete) I'll tell you later when I'm not buried, but in the meantime I'll get on with something else rather than wait idly by in suspension".

The scheduler also performs some other housekeeping tasks on the computer ie, flash the cursor, service any SER transmit, and any other tasks that may be linked in to be done when the scheduler is run. For example, a task could be set up to release the buffer memory used after a previously closed output channel's data has all been sent to the device, or any other (User mode) task a job might like to link in to be done periodically rather than depend on the job being in a position to do it.

The scheduler decides which job is next in line for the processor by looking at information stored in the header added to each job currently in the machine (including SuperBASIC). The jobs registers (ie, the CPU state for that job) are stored in this header whilst the job is waiting for its slice of processor time.

Along with other parameters in the job header, which are used by the job control routines of QDOS, are the four which the scheduler inspects and updates in turn for each job to find out which job merits control of the processor next :

|Name|Description|
|---|---|
|JB.PRIOR|This holds the jobs current running total of 'priority' points. Whosoever is the highest after the job table has been scanned has the processor next. (The job who had the processor last automatically has this reset to 1 beforehand).|
|JB.PRINC|This is the actual job priority. This gets set as required either when a job is started (defaults to 32 if started by SuperBASIC) or it can be changed whilst the job is running (ie, MT.PRIOR or Toolkit 2 command SPJOB). A priority of 0 means the job is inactive (not the same as suspended, whereby the priority setting is retained). It is used as the priority increment; each time the scheduler scans for the next job it adds this figure to JB.PRIOR (JB.PRIOR never exceeds 255).|
|JB.STAT|The status of this particular job. It can have the values in the table below. A job can be suspended for a finite time if required (ie, made idle). The timeout until reactivation, which is stored here (as does the timeout for waiting input/output), gets decremented for each frame interrupt until it reaches 0. Only jobs with a JB.STAT of 0 are considered eligible for 'next' job. Jobs at -1 are either waiting forever for their input/output or have been suspended indefinitely (ie, MT.SUSJB), -2 means this job has been suspended until another has finished (ie, like EXEC_W from SuperBASIC).|
|JB.HOLD|Location to be cleared when this job is released from suspension (equals 0 if not required). This can be used, if required, to let another job know when this job has timed out of suspension.|

|JB.STAT|Description|
|:---:|---|
|  0|Not suspended|
|  >0|Delay time to reactivation|
|  -1|Suspended|
|  -2|Waiting for another job to complete|

Another parameter taken into account when the scheduler is run is the system variable SV.POLLM, this figure is incremented every time a frame interrupt is serviced. The scheduler uses the value of this variable to decrement any positive value found in a job's JB.STAT (SV.POLLM is reset to zero once used). Because the scheduler is normally run every time a frame interrupt occurs SV.POLLM usually has the value 1, but occasions may arise when a frame interrupt doesn't automatically call the scheduler, this is if the frame interrupt occured whilst the processor was in Supervisor mode (ie, an atomic action). Thus when the scheduler may finally get called SV.POLLM ensures that any job in (finite) suspension gets timed out correctly, the 'missed' frame interrupts having been stored in SV.POLLM. Of course it doesn't stop frame interrupts getting missed when interrupts are turned off ie, for FLP access, network driver etc!

The process the scheduler goes through whenever it is called can now be illustrated by the following algorithm;

```
Entry; Frame interrupt in User mode, or
       After certain non-atomic TRAP calls

If current job's JB.PRIOR <> 0 THEN 
   JB.PRIOR = 1
   Save current jobs registers in its header

D3 = SV.POLLM      (* re-entry point, see below)
SV.POLLM = 0
Increment SV.RAND  (psuedo random number)

Do scheduler linked list;

    * Flash cursor
    * Do any SER transmit
    * Do any waiting input/output (ie, scan the channel table)
    * Do any other externally linked in tasks

Set D1 = 0   (highest JB.PRIOR found so far)
Set D0 = -2  (to indicate if no 'next' job found)
```

Now a search for the next possible job is done by interrogating each job's header. The job table is searched from the entry of the (last) current job +1 to the end of the table, and then from the start of the table back upto the entry for the last job.

```
REPeat for each valid job in table
  IF JB.PRINC = 0 (inactive) THEN ignore, look at next job.
  IF JB.STAT < 0 (suspended) THEN ignore, look at next job.
  IF JB.STAT > 0 (waiting I/O or idle)
    JB.STAT = JB.STAT - D3                  (ie, old SV.POLLM)
    IF JB.STAT > 0 THEN ignore, look at next job
    JB.STAT = 0 (in case overshot out-of-time)
    IF JB.HOLD <> 0 THEN clear location pointed to by JB.HOLD
  END IF

  IF JB.PRIOR = 0                     (ie, when MT.PRIOR done)
    JB.PRIOR = 1
  ELSE
    JB.PRIOR = JB.PRIOR + JB.PRINC
    IF JB.PRIOR > 255 THEN JB.PRIOR = 255
    IF JB.PRIOR > D1
      D1 = JB.PRIOR
      D0 = this job header location
    END IF
  END IF
END REPeat for each valid job in table
```

After the job table has been scanned the next job is deemed to be the one pointed to by D0 which has the (first found) highest accumulated priority (D1). The jobs registers are then unloaded from job header and put back into the processor and control handed to the job until the next frame interrupt.

If no suitable job is found (D0=-2) then the scheduler is re entered (at *) until one is found. Although this might appear unlikely, ie, no job wants a slice of processor time, in fact it happens quite often. Most jobs spend their time waiting for the user! (ie, the scheduler is doing their pending input/output attempts whilst JB.STAT >0 and the job has a channel open with pending action). It reminds of the time when logging off a computer terminal used to generate the report; "You have been logged on 2 hours 47 minutes and you have used 11.31 seconds of processor time"!

### In Practice 

The following simple SuperBASIC program illustrates how assigning different priorities to jobs affects how much processor time is apportioned to each, you may be surprised at the results. For example, it is better to assign two competing jobs priorities of 2 and 64, rather than 8 and 127 respectively, if you wish give one job more precedence over the other.

At the prompt for each of the five example jobs input a priority of between 0 and 127 (ENTER alone =0, ie, to represent an inactive or suspended job). Change the value used in the INKEY$ function (line 230) to speed up/slow down the update rate (0-25). Notice some low priority jobs hardly get a look-in on the processor until quite a time has elapsed, their low priority increment takes quite a time to build up their job merit. After an erratic start the percentage figures should settle down to reflect the long term proportion of processor time assigned to the job (use CTRL F5, as usual, to momentarily freeze the display if necessary).

The SuperBASIC program mimics quite closely what actually happens on a JS QDOS machine (standard QL or with SGC/GC). Some of the behaviour was so unexpected at first I didn't believe it until I ran some machine code tasks specially written to confirm it. For example if you only have two active jobs running with priorities a factor of two apart (eg, 64/32, 16/8 etc) then they end up both getting half the total processor time slices each. If one priority is changed by just 1 (ie, priorities 65 and 32) then the ratio becomes the expected 2:1 (approx). Once other jobs become active (at the same time), this behaviour (of 64/32 etc) changes to reflect the true priority spread.

The Minerva scheduler appears to have a slightly different action. Although it still exhibits the same effect as mentioned above it doesn't drop off when you have, say, five jobs with priorities; 1,2,4,8 and 16 (or similar). The SuperBASIC program shows that in this case (as with a real JS with SGC/GC), the lowest two jobs get the same amount of processor time (ie, approx 6%,6%,13%,25% and 50% respectively). With Minerva the time is apportioned as you would expect; 3%,6%,13%,26% and 52%.

If there is any interest I can detail the simple machine code jobs I used (tasks with definable priorities) in another article. I have yet to get around to delving into Minerva's background priorities and the SMSQ's scheduler with its definable IO_PRIORITY (retry loading for waiting input/output).

Note that any jobs waiting for input/ouput on a real machine are effectively suspended (ie, SuperBASIC waiting at cursor).

```
100 ch%=FOPEN('con') : PAPER#ch%;4 : INK#ch%;0 : BORDER#ch%;8,4
110 DIM jp%(4),ap%(4),jx%(4)
120 REPeat main
130   CLS#ch% : t%=0 : c%=-1
140   FOR n=0 TO 4
150     INPUT#ch%;'Job '&n&' priority :'!a$
160     IF a$='' THEN jp%(n)=0 : ELSE jp%(n)=a$
170     ap%(n)=1 : jx%(n)=0
180   END FOR n
190   CLS#ch% : PRINT#ch%;'Job  Priority','Processor time'\\
200   FOR n=0 TO 4 : PRINT#ch%;' ';n,jp%(n)\\
210   PRINT#ch%\\'Press ESCape to STOP, any other key to re run'
220   REPeat show
230     a$=INKEY$(10) : IF CODE(a$)=27 THEN CLOSE#ch% : STOP
240     IF CODE(a$) THEN EXIT show : ELSE t%=t%+1
250     AT#ch%;0,34 : PRINT#ch%;t% : h%=0 : j%=-1 : p=1
260     FOR n=c%+1 TO 4, 0 TO c%
270       IF jp%(n)
280         ap%(n)=ap%(n)+jp%(n) : IF ap%(n)>255 THEN ap%(n)=255
290         IF ap%(n)>h% THEN h%=ap%(n) : j%=n
300       END IF
310     END FOR n
320     IF j%>-1
330       jx%(j%)=jx%(j%)+1 : c%=j% : ap%(c%)=1
340       p=0 : FOR n=0 TO 4 : p=p+jx%(n)
350     END IF
360     FOR n=0 TO 4
370       z=100*jx%(n)/p : z%=INT(z*2) : y%=20+n*20
380       AT#ch%;n*2+2,15 : PRINT#ch%;FDEC$(z,6,2);'%'
390       BLOCK#ch%;z%,10,140,y%,7
400       BLOCK#ch%;200-z%,10,140+z%,y%,0
410     END FOR n
420   END REPeat show
430 END REPeat main
```

### Scheduler disassembly {#scheduler_disassembly}

```
00936  jsr       $000009D4(PC)          save current job/check JB.PRIOR
0093A  move.w    $0030(a6),d3           get lost polls, SV.POLLM
0093E  clr.w     $0030(a6)              zero SV.POLLM
00942  addq.w    #$01,$002E(a6)         increment SV.RAND
00946  moveq     #-$10,d0               link offset
00948  movea.l   $0040(a6),a0           SV.SHLST
0094C  jsr       $00000A9E(PC)          do scheduler task list
                                        ie; $120A flash cursor
                                            $2D14 as transmit interrupt
                                            $3488 do waiting I/O
00950  jsr       $00000A0C(PC)          do job table
00954  tst.l     d0                     d0=-2 if no active job!
00956  blt.s     $0000093A              try again...
00958  move.l    d0,$0064(a6)           update SV.JBPNT to new job
0095C  jsr       $00000A78(PC)          rte by restore to next active job
00960  jsr       $000003BC(PC)
00964  move.w    d3,$0014(a0)
00968  move.l    a1,$000C(a0)
0096C  moveq     #$00,d0
0096E  bra       $00000936

* Save current job details
009D4  move.l    a6,-(a7)
009D6  movea.l   $0064(a6),a6           SV.JBPNT current job table entry
009DA  movea.l   (a6),a6                current job header
009DC  tst.b     $0012(a6)              JB.PRIOR current job
009E0  beq.s     $000009E8              branch if 0
009E2  move.b    #$01,$0012(a6)         else reduce to 1
009E8  movem.l   d0-d7/a0-a4,$0020(a6)  save jobs registers
009EE  move.l    (a5)+,$003C(a6)        d7
009F2  move.l    (a5)+,$0054(a6)        a5
009F6  move.l    (a5)+,$0058(a6)        a6
009FA  move      USP,a0
009FC  move.l    a0,$005C(a6)           a7=USP
00A00  move.w    (a5)+,$0060(a6)        SR
00A04  move.l    (a5)+,$0062(a6)        PC
00A08  movea.l   (a7)+,a6               Restore a6 with $28000
00A0A  rts

* Go through job table
00A0C  moveq     #-$02,d0               nul job table entry return code
00A0E  moveq     #$00,d1                highest JB.PRIOR found
00A10  movea.l   $0064(a6),a2           SV.JBPNT current job entry
00A14  movea.l   a2,a4
00A16  move.w    $0062(a6),d2           SV.JBMAX highest ever job no.
00A1A  lsl.w     #$02,d2
00A1C  movea.l   $0068(a6),a3           SV.JBBAS base of job table
00A20  adda.w    d2,a3                  a3 = table entry SV.JBMAX
00A22  addq.w    #$04,a2                next table entry
00A24  cmpa.l    a3,a2
00A26  ble.s     $00000A2C              do each entry till top
00A28  movea.l   $0068(a6),a2           then wrap round to base
00A2C  tst.b     (a2)
00A2E  blt.s     $00000A72              branch if job invalid, 'FF000000'
00A30  movea.l   (a2),a0                get job header
00A32  tst.b     $0013(a0)              JB.PRINC
00A36  beq.s     $00000A72              if inactive (=0) ignore, get next
00A38  tst.w     $0014(a0)              JB.STAT
00A3C  beq.s     $00000A54              branch if active (=0)
00A3E  blt.s     $00000A72              if suspended or waiting, get next
00A40  sub.w     d3,$0014(a0)           else deduct lost polls SV.POLLM
00A44  bgt.s     $00000A72              if frames left, do next job
00A46  clr.w     $0014(a0)              else timeout expired
00A4A  move.l    $000C(a0),d2           JB.HOLD
00A4E  beq.s     $00000A54              branch if no location to clear
00A50  movea.l   d2,a1
00A52  sf        (a1)                   clear job waiting flag location

00A54  move.b    $0012(a0),d2           ; if JB.PRIOR = 0, set to 1
00A58  beq.s     $00000A64              ; else add JB.PRINC 
00A5A  add.b     $0013(a0),d2           ;    if >255 set to 255
00A5E  bcc.s     $00000A66              ;    else set to result
00A60  st        d2                     ;
00A62  bra.s     $00000A66              ;
00A64  moveq     #$01,d2                ;
00A66  move.b    d2,$0012(a0)           Update JB.PRIOR
00A6A  cmp.b     d1,d2                  highest JB.PRIOR to this JB.PRIOR
00A6C  bls.s     $00000A72              if not higher branch
00A6E  move.l    a2,d0                  highest job table entry to d0
00A70  move.b    d2,d1                  this now highest JB.PRIOR
00A72  cmpa.l    a4,a2                  done all entries in job table ?
00A74  bne.s     $00000A22
00A76  rts

* Restore to next job
00A78  movea.l   $0064(a6),a0           SV.JBPNT
00A7C  movea.l   (a0),a0
00A7E  adda.w    #$0016,a7              reset SSP stack
00A82  move.l    $0062(a0),-(a7)        JB.PC to SSP
00A86  move.w    $0060(a0),-(a7)        JB.SR to SSP
00A8A  move.l    $001C(a0),$0050(a6)    JB.TRAPV to SV.TRAPV
00A90  movea.l   $005C(a0),a1           JB.A7 
00A94  move      a1,USP
00A96  movem.l   $0020(a0),d0-d7/a0-a6  get jobs previous regs back
00A9C  rte

* Do linkage block list
00A9E  move.w    d0,-(a7)               stack offset
00AA0  movea.l   a0,a3                  link block base address
00AA2  adda.w    (a7),a3                set up a3 to link with offset
00AA4  move.l    a0,-(a7)               stack current link address
00AA6  beq.s     $00000ABC              empty or finished list ?
00AA8  move.w    d3,-(a7)               stack current flag (?)
00AAA  andi.w    #$007F,d3              mask flag (?)
00AAE  movea.l   $0004(a0),a0           get link start address
00AB2  jsr       (a0)                   go do link routine
00AB4  move.w    (a7)+,d3               restore flag (?)
00AB6  movea.l   (a7)+,a0               restore last link done
00AB8  movea.l   (a0),a0                address of next link
00ABA  bra.s     $00000AA0              do next link
00ABC  addq.l    #$06,a7                clear stack
00ABE  rts
```
