+++
title = "Job Details"
date = 2020-11-16T19:44:00Z
lastmod = 2020-11-16T19:44:00Z
draft = false
keywords = ["Jobs", "Job details", "Tasks"]
weight = 10
+++

### What is a job ? 

A job is a piece of code (or even just data) created in the Transient Program Area of the QDOS memory map. It exists and runs (if it is executable) as a separate task and is under the control of the job scheduler. It will have a job Id and an entry in the Jobs table.

Jobs can be independent or owned by other jobs, and may have child jobs of their own. Independent jobs are actually owned by SuperBasic which is the 'Mother of all jobs' and has an id of zero.

Jobs are what allows multi-tasking in QDOS, well, the scheduler is really, but each individual job gives the impression that multi-tasking is taking place.


### The Job Table. 

In QDOS, there is a table which lives in memory and holds the absolute address in memory of all current jobs. This is not a pointer to the start address of the job's code, but to the start address of the job's header (see below) which is situated $68 bytes below the start of the job's code.


### Job Ids. 

Each job in the system has a unique identifier. SuperBasic is the first job created after bootup and has the unique id of zero. This job cannot be removed from the system.

A job id is a long word (4 bytes) in which the high word is a unique tag word and the low word is an index that holds the position of the job's location in the job table.

As spaces in the job table are reused, the tag is used to make sure that the job id is always unique. This avoids the (rare) possibility that a job id may be stored by a parent job for some communication purposes. The child job dies or is removed accidentally. In the meantime, another job uses the child's slot in the job table. Now the parent would be talking to the wrong job !

The tag word simply increments each time a new job is created in the system.

So, if all you have is a job's id, finding where its code lives in memory is 'simple':

* Find the base of the QDOS job table. This lives at the address SV_JBBAS(A6) and continues up to, but not including SV_JBTOP(A6) in memory.
* Take the low word of the job id and multiply it by 4 to get the offset from SV_JBBAS(A6) to the entry for this job.
* Read the long word from the table at the address given by SV_JBBAS(A6) plus your calculated offset. This is the address of JB_LEN in the jobs header.
* Add $68 to this address to get the first byte of code in the actual job.
* In the above, A6 is assumed to hold the base address of the system variables. This used to be always $28000 but can and probably will move around.
* Easy stuff this ;-) 


### Job Headers 

In memory, a job looks like this :

|Job header|Job code|Job data & stack space|
|---|---|---|

The job code and data sizes are specified when the job is created and cannot be changed. The header is always $68 bytes and looks like this :

|Offset|Name|Size|Description|
|---|---|---|---|
|$00|JB_LEN|Long|Job area size. Code + data + header.|
|$04|JB_START|Long|Absolute address of the start of the code. (JB_END)|
|$08|JB_OWNER|Long|Job id of this job's parent job.|
|$0C|JB_HOLD|Long|Address which is to be cleared when this job is removed from the system. Zero if not required.|
|$10|JB_TAB|Word|Job tag = Location in QDOS job table for this job.|
|$12|JB_PRIOR|Byte|This job's priority.|
|$13|JB_PRINC|Byte|Priority increment. Zero if job is inactive.|
|$14|JB_STAT|Word|Job status :|
||||0 =Possibly active.|
||||Positive =Delay until job's reactivation.|
||||-1 =Job is suspended.|
||||-2 =Job is waiting for another to finish.|
|$16|JB_RELA6|Byte|Bit 7 is set after a trap #4. To signal that some parameters are relative to A6 and not absolute.|
|$17|JB_WFLAG|Byte|Bit 7 set if another job is waiting for this one to finish.|
|$18|JB_TRAPV|Long|Pointer to this jobs RAM exception table or zero if not used. See MT_TRAPV (Trap #1).|
|$20|JB_D0|Long|Storage for D0.|
|$24|JB_D1|Long|Storage for D1.|
|$28|JB_D2|Long|Storage for D2.|
|$2C|JB_D3|Long|Storage for D3.|
|$30|JB_D4|Long|Storage for D4.|
|$34|JB_D5|Long|Storage for D5.|
|$38|JB_D6|Long|Storage for D6.|
|$3C|JB_D7|Long|Storage for D7.|
|$40|JB_A0|Long|Storage for A0.|
|$44|JB_A1|Long|Storage for A1.|
|$48|JB_A2|Long|Storage for A2.|
|$4C|JB_A3|Long|Storage for A3.|
|$50|JB_A4|Long|Storage for A4.|
|$54|JB_A5|Long|Storage for A5.|
|$58|JB_A6|Long|Storage for A6.|
|$5C|JB_A7|Long|Storage for A7.|
|$60|JB_SR|Word|Storage for job's status register.|
|$62|JB_PC|Long|Storage for the job's program counter.|
|$66|JB_NU|Word|Not used.|
|$68|JB_END| |Op-code	This is where your job's code starts.|



### Job Stack 


Originally, the QDOS documentation specified the format of any extra data that appears on the stack when a job is first created. At that time, there was no way to actually get the extra data onto the job's stack (from SuperBasic at least). Since then, Toolkit 2, Turbo Toolkit and all the rest, have managed to make it easy to pass command strings and opened channels to the new job. These are passed on the new job's stack as follows :

The stack grows downwards into the data area. A7 holds the address of the top of the stack. If the long word at (A7) is zero, then no information is on the stack.

If the word at (A7) is not zero then this is a counter of the number of opened channels that have been passed to this job. This word is followed by a long word for each channel - the channel id - for the input channel, the output channel and the 'reporting' channel (in that order). If only one channel has been opened, this is the input channel and if only two channels have been opened, these are the input & output.

Above this list of channel ids, we have a word holding the length of a command string placed on the stack. And above this word, there are the bytes of the command string.

|||
|---|---|---|
|| Bytes of command string| Padded to an even length.|
|| Length of command string (WORD) or Zero||
|| Channel ID - report channel (LONG)|May not be present.|
|| Channel ID - output channel (LONG)|May not be present.|
|| Channel ID - input channel (LONG)|May not be present.|
| A7-> | Number of channel ids on stack (WORD) or zero <-(A7)||

The stack pointer for the job, A7, points at the word holding the number of opened channels.

So if your job discovers that there are open channels for it to use on the stack, it should use them !

### Priorities 

All jobs that can be activated are given a priority when they are activated. The priority for jobs that are EXEC/EXEC_W'd is 32 which is the same as the priority of SuperBasic.

In the original QDOS, priorities ranged from 0 to 127 and had to be positive. Then Minerva came along and allowed negative priorities as well, so now priorities range from -128 to +127.

If a job is given, or has, a priority of zero, then that job is deemed to be inactive and will never be executed (again). Priorities are relative, the bigger the priority, the bigger the slice of time it has to execute, but it is not proportional - a job with a priority of 10 doesn't get 10 times less time than a job with a priority of 100, for example.

### Inactive Jobs 

An inactive job is simply a job whose priority is zet to zero. JB_PRINC is zero in the job's header. As long as this is the case, then the job will not be given a time slice by the scheduler.

Inactive jobs can be used to hold data for other jobs, but if so, must never be activated.

### Suspended Jobs 

A suspended job is active, its header field, JB_PRINC, is not zero, but JB_STAT is a positive number. This is the amount of time (in fiftieths of a second, sixtieths if you live in the USA) until the job will be activated again.

Suspending a job is a good way of being 'friendly' to the rest of the machine as it allows the time slices that would have been allocated to that job to be utilised in other jobs - hopefully helping the response times of the system as a whole.

For example, a job that watches the keyboard for a specific key combination, say for a screen grabber program, need not be run at all times. Once per half second or so should be enough. This sort of job will suspend itself for 25 cycles (30 in the States) or half a second each time through its 'scanning' loop.


### Killing Jobs {#killing_jobs}

When a job has finished, it must call the operating system to kill itself. Killing a job releases all memory areas, channels, open files and any other resources it obtained while running. While there is no real need for the developer to manually reclaim these it is tidy to do so. In the event of a job error, for example, letting the system clean up is probably the best idea.

The code for a job to kill itself is:

```
job_end
    move.l  d0,d3               Copy error code from D0.L to D3.L for exit to SuperBasic
    moveq   #mt_frjob,d0        Job is about to die
    moveq   #-1,d1              And it is this job
    trap    #1                  RIP (there is not return)
```

The above assumes that on entry to job_end, D0.L holds either zero (no error) or an error code. I have also seen programs where the developer doesn't quite trust [mt_frjob](/traps/trap_1/trap_1_05) to work correctly, so they (and I) write code like this instead:

```
job_end
    move.l  d0,d3               Copy error code from D0.L to D3.L for exit to SuperBasic
    moveq   #mt_frjob,d0        Job is about to die
    moveq   #-1,d1              And it is this job
    trap    #1                  RIP (there is not return)
    bra.s   job_end             Should never get here, but ...
```


### Writing Jobs 

QDOS expects that a job's code be in a specific format. When writing a job, you must adhere to this format, which is :

```
start
    bra.s JobCode
    dc.l  0
    dc.w  $4AFB
    dc.w  size of job name
    dc.b  Bytes of job name
JobCode
    Do stuff here !
```

The address 'start' is actually JB_END. The job flag is the word $4AFB and this must be located at JB_END + 6, the word defining the size of the job name is at JB_END + 8 and the job's name starts at JB_END + $0A.

At the end of a job, you don't just RTS back to SuperBasic, you have to remove the job, see [Killing Jobs](#killing_jobs) above for details.
