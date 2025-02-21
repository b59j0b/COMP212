java c
Department   of   Computer   Science 
COMP212 -   2025 -   CA   Assignment   1 
Coordination   and   Leader   Election 
Simulating   and   Evaluating   Distributed   Protocols in   Java 
Assessment Information 
Assignment Number 
1 (of 2) 
Weighting 
15% 
Assignment Circulated 
7th February 2025 
Deadline 
6th March 2025, 17:00 UK Time 
Submission Mode 
Electronic via CANVAS 
Learning outcomes assessed (1) An appreciation of the main principles underlying distributed    systems:       processes,    communication,    nam- ing,    synchronisation,    consistency,    fault    tolerance,    and security.       (3)    Knowledge and understanding of the es- sential facts, concepts, principles and theories relating to Computer Science in general, and Distributed Com- puting in particular. (4) A sound knowledge of the crite- ria and mechanisms whereby traditional and distributed systems can be critically evaluated and analysed to de- termine the extent to which they meet the criteria de- fined for their current and future development. 
Purpose of assessment 



Marking criteria 
This assignment assesses the understanding of coordina- tion and leader election in distributed systems and im- plementing, simulating, and evaluating distributed pro- tocols by using the Java programming language. 
Marks for each question are indicated under the corre- sponding question. 
Submission necessary in order 
to satisfy Module requirements? 
No 
Late Submission Penalty 
Standard UoL Policy. 
1 Overall marking scheme 
The   coursework   for   COMP212   consists   of   two   assignments   contributing   altogether   30%   of   the   final   mark.   The   contribution   of the   individual   assignments   is   as   follows:Assignment   1            15%Assignment   2            15%TOTAL                                    30%
2 Objectives This   assignment   requires   you   to   implement   in   Java   two   distributed   algorithms   for   leader   election in a ring network   and then   to   experimentally   validate   their   correctness   and   evaluate   their   performance.
3 Description of coursework 
Throughout   this   coursework,   the   network   on   which   our   algorithms   are   to   be   executed   is   a   bidirectional   ring,   as   depicted   in   Figure 1. 

Figure   1:   A   bidirectional   ring   network   on   n   processors.In   our   setting,   all   processors   execute   the   same   algorithm,   do   not   know   the   number   n   of   processors   in   the   system   in   advance,   but   they   do   know   the   structure   of   the   network   and   are   equipped   with   unique    ids.      The   ids   are   not   necessarily   consecutive   and   for   simplicity   you   can   assume   that   they   are   chosen   from   {1,   2,...,αn},   where   α   ≥   1   is   a   small   constant   (e.g.,   for   α   = 3,   the   n   processors   will   be   every   time   assigned   unique   ids   from   {1, 2, . . . ,   3n −   1,   3n}).   Additionally, every processor can distinguish   its   clockwise   from   its   counterclockwise   neighbour,   so   that,   for   example,   it   can   choose   to   send   to   only   one   of   them   or   to   send a different   message   to   each   of   them.      Processors   execute   in   synchronous   rounds,   as   in   every   example   we   have   discussed   so   far   in   class.
3.1 Implementing the LCR Algorithm—30% of the assignment mark As   a   first   step,   you   are   required   to   implement   the   LCR   algorithm   for   leader   election   in   a   ring.      The   pseudocode   of   the   non-terminating   version   of   LCR   can   be   found   in   the   lecture   notes   and   is   also   given   here   for   convenience   (Algorithm 1).
Algorithm 1 LCR   (non-terminating version)
Code   for   processor   ui   ,   i   ∈   {1,   2,...,   n}:
Initially:
ui    knows   its   own   unique   id   stored   in   myIDi
sendIDi    := myIDi
statusi      := “ unknown ” 
1: if round   =   1 then 
2:                         send   ⟨sendIDi   ⟩   to   clockwise   neighbour
3: else//   round   >   1
4:                      upon   receiving   ⟨inID⟩    from   counterclockwise   neighbour
5: if inID   > myIDi then 
6:                                             sendIDi    := inID
7:                                             send   ⟨sendIDi   ⟩   to   clockwise   neighbour
8: else if inID   = myIDi then 
9:                                           statusi      :=   “   leader   ”
10: else if inID   < myIDi then 
11:                                              do   nothing
12: end if 
13: end if 
You are required to implement a terminating version of the LCR algorithm in which all processors eventually terminate and know the id of the elected leader. 
3.2 Implementing the HS Algorithm—30% of the assignment mark Next,   you   are   required   to   implement   another   algorithm   for   leader   election   on   a   ring,   known   as the HS algorithm.    As LCR, HS   also   elects the   processor   with   the   maximum   id.    The main   difference is that HS, instead of trying to   send   ids   all   the   way   around   in   one   direction   (which   is what LCR does), has every   processor   trying   to   send   its   id   in   both   directions   some   distance away   (e.g.,   k)   and then   has the   ids turn   around   and   come   back to   the   originating   processor.   As   long   as   a   processor   succeeds,   it   does   so   repeatedly   (in   “phases”)   to   successively   greater   distances   (doubling   the   distance   to   be   travelled   each   time,   e.g.,   2k).      See   Figure 2 for   an   illustration.
Figure   2:   Trajectories   of successive   “phases” originating   at processor   u4 (imagine the rest of   the processors doing something   similar in parallel, but not depicted here).   The id transmitted   byu4   aims to travel some distance out   in both directions and then return back.    If it succeeds,   then   u4    doubles   the   aimed   distance   and   repeats.Informally, each processor ui      “operates in phases” l   =   0, 1, . . .   (where each phase l consists   of   one   or   more   rounds).    In   each   phase   l,   processor   ui      sends   out   a    “token”   (i.e.,   a   message)   containing   its   id   idi      in   both   directions.      These   are   intended   to   travel   distance   2l       (that   is,   as   in   Figure 2,   distance   20       =    1   for   l   =   0,   distance   21       =   2   for   l   =    1,   distance   22       =   4   for   l   = 2,   and   so   on) and   then   return   to   their   origin.    If   both   tokens   manage   to   return   back   then ui      goes   to   the   next   phase,   otherwise   it   stops   to   produce   its   own   tokens   (and   only   performs   from   that   point   on   the   rest   of   the   algorithm’s   operations).    A   token   is   discarded   if   it   ever meets   a   processor   with   greater   id   while   travelling   outwards   (away   from   its   origin).    While   travelling   inwards   (back   to   its   origin),   a   token   is   forwarded   by   all   processors   without   any   check.   The   termination   criterion   is   as   follows:   If   a   token   travelling   outwards   meets   its   own   origin   ui      (meaning   that   this   token   managed   to   perform   a   complete   turn   of   the   whole   ring   while travelling outwards), thenui    elects itself as the leader.    Observe that in order for tokens   to   know   how   far   they   should   travel   each   time   and   in   which   direction,   this   information   has   to be   included   inside   the   transmitted   messages   (that   is,   apart   from   the   id   being   transmitted, the   messages   should   also   contain   this   auxiliary   information).The   pseudocode   of the   non-terminating   version   of   HS   is   given   in   Algorithm 2. As   with   LCR, you are required to implement a terminating version of the HS algorithm in which all processors eventually terminate and know the id of the elected leader.3.3 Experimental Evaluation, Comparison  Report—40% of the assignment mark 
After   implementing   the   terminating   LCR   and   HS   algorithms,   the   next   step   is   to   conduct an   experimental   evaluation   of their   correctness   and   performance.Correctness.    Execute   each   algorithm   in   rings   of   varying   size   (e.g.,   n   =   3,   4, . . . , 1000, . . .;   actually,   up   to   a   point   where   simulation   does   take too   much time to   complete)   and   starting   from   various    different    id    assignments    for    each    given    ring    size.          For    instance,    you    could   execute   them   on   both   specifically   constructed   id   assignments   (e.g.,   ids   ascending   clockwise   or    counterclockwise)    and      random      id      assignments.             In      each      execution,       your    simulator   should   check   that   eventually   precisely   one   leader   is   elected.      Of   course,   this   will   not   be   a   replacement   of   a   formal   proof   that   the   algorithms   are   correct   as   you   won’t   be   able   to   test   them   on   all   possible   combinations   of ring   sizes   and   id   assignments,   but   at   least   it   will   be   a   first   indication   that   they   may   do   as   intended.Performance.   Execute,   as   above,   each   algorithm   in   rings   of varying   size   and   starting   from   various   different   id   assignments   for   each   given   ring   size.   For   each   execution,   your   simulator   should   record   the number of rounds and   the total number of messages transmitted   until termination.
1.    Execute   both   algorithms   in   rings   of   varying   size   for   the   case   in   which   ids   are   always   clockwise   ordered.
2.    Execute   both   algorithms   in   rings   of   varying   size   for   the   case   in   which   ids   are   always   counterclockwise   ordered.
3.    Execute   both   algorithms   in   rings   of   varying   size   and   various   random   id   assignments   for   each   given   ring   size.      Note   here   that   both   algorithms   should   be   simulated   (e.g.,   one   after   the   other)   on   every   given   choice   of   ring   size   and   id   assignment,   so   that   a   comparison   of their   performance   makes   sense.In    Summary: For   both   correctness   validation   and   performance   evaluation   a   suggestion   is   to   simulate   both   algorithms   (for   all   types   of   id   assignments   mentioned   above)   in   rings   containing   up   to   at   least   1000   processors.   Specifically   in   the   case   of random   id   assignments,   for   each   ring   size   n   repeat   the   simulation   for   many   different   id   assignments   (e.g.,   at   least   100 distinct simulations) and record the   correctness   and   the   worst,   the   best,   and   the   average   performance   so   that   you   get   meaningful   results.
Algorithm 2 HS   (non-terminating version)
Messages are triples of the form   ⟨ID,direction,hopCount⟩, where   direction   ∈   {out,in}   and   hopCount   positive   integer.
Code   for   processor   ui   ,   i   ∈   {1,   2,...,   n}:
Initially:
ui    knows   its   own   unique   id   stored   in   myIDi
sendClocki             containing          a          message       to          be          forwarded          clockwise       or          null,          initially
sendClocki      :=   ⟨myIDi   ,   out,1⟩
sendCounterclocki    containing   a   message   to   be   forwarded   counterclockwise   or   null,   initially
sendCounterclocki    :=   ⟨myIDi   ,   out,1⟩
statusi      ∈   {   “   unknown   ”   ,   “   leader   ”},   initially   statusi      :=    “   unknown   ”
phasei      recording   the   current   phase   number,   nonnegative   integer,   initially   phasei      = 0
1:    upon   receiving   ⟨inID,   out,hopCount⟩    from   counterclockwise   neighbour
2: if inID   > myIDi    and   hopCount   >   1 then 
3:                      sendClocki      :=   ⟨inID,   out,hopCount   −   1⟩
4: else if inID   > myIDi    and   hopCount =   1 then  
5:                      sendCounterclocki      :=   ⟨inID,   in,1⟩
6: else if inID   = myIDi then  
7:                           statusi      :=   “   leader   ”
8: end if  
9:
10:    upon   receiving   ⟨inID,   out,hopCount⟩    from   clockwise   neighbour
11: if inID   > myIDi    and   hopCount   >   1 then 
12:                      sendCounterclocki      :=   ⟨inID,   out,hopCount   −   1⟩
13: else if inID   > myIDi    and   hopCount =   1 then 
14:                      sendClocki      :=   ⟨inID,   in,1⟩
15: else if inID = myIDi then  
16:                           statusi      :=   “   leader   ”
17: end if  
18:
19:    upon   receiving   ⟨inID,   in,1⟩   from   counterclockwise   neighbour,   in which   inID   ≠   myIDi
20:    sendClocki      :=   ⟨inID,   in,1⟩
21:
22:    upon   receiving   ⟨inID,   in,1⟩   from   clockwise   neighbour,   in which   inID   ≠   myIDi
23:    sendCounterclocki      :=   ⟨inID,   in,1⟩
24:
25:    upon   receiving      ⟨inID,   in,1⟩    from      both   clockwise   and   counterclockwise   neighbours,   in both of which   inID   = myIDi    holds
26:    phasei      := phasei      + 1
27:    sendClocki      :=   ⟨myIDi   ,   out,2phasei⟩
28:    sendCounterclocki      :=   ⟨myIDi   ,   out,2phasei⟩
29:
30:      //   The   following   to   be   always   executed   by   all   processors,   i.e.,
31:      //   also   in   round   1   in   which   no   message   has   been   received
32:    send    ⟨sendClocki   ⟩   to   clockwise   neighbour
33:    send   ⟨sendCounterclocki   ⟩   to   counterclockwise   neighbourAfter   gathering   the   simulation   data,   plot   them   as   follows.    In   each   plot,   the   x-axis   will represent   the   (increasing)   size   of the   ring   and   the   y-axis   will   represent   the   complexity   mea-   sure   (e.g.,   number   of   rounds   or   number   of   messages).      You   may   produce   individual   plots   depicting the performance of each   algorithm   (possibly comparing   against   standard   complex-   ity   functions,   like   n,   nlog   n,   or   n2   )   and   you   are   required   to   produce   plots   comparing   the   performance of both algorithms in identical settings.    For example, when measuring   the   total   number   of   messages   in   the   case   of   counterclockwise   increasing   ids,   a   plot   would   show   at   the   same   time   the   performance   of both   algorithms   for   increasing   ring   size   n,   using   curves   of   different colours and possibly also a   legend   with   explanations.    Then, for   each   given   ring   size,   the   corresponding   point   of each   curve will   represent the total   number   of messages   generated   by   the   algorithm    (indicated   on   the   y-axis).      You   can   use   gnuplot,   JavaPlot   or   any   other   plotting   software that you   are   familiar   with.The   final   crucial   step   is   to   prepare   a   concise   report   (at   most   5   pages   including   plots)   clearly   describing   your   design,   the   main   functionality   of   your   simulator,   the   set   of   experi-   ments   conducted,   and   the   findings   of your   experimental   evaluation   of the   above   algorithms.   In particular, in the latter part   you   should   try   to   draw   conclusions   about   (i)   the   algorithms’   correctness   and    (ii)   the   performance    (time    and    messages)   of   each   algorithm   individually   (e.g.,   what   was   the   worst/best/average   performance   of   each   algorithm   as   a   function   of   n?   For   example,   we   know   from   the   lectures   that   the   worst-case   communication   complexity   of   LCR   is   O(n2   ):   can   you   verify   this   experimentally?)   and   when   the   two   algorithms   are   being   compared   against   each   other   (e.g.,   which   one   performs   better   and   in   which   settings?).





         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
