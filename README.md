##### 06 Feb '15 12h -  Logboek bachelorproef.
Hier vindt u het logboek voor mijn bachelorproef. Logboek wordt beheerd met jrnl. https://maebert.github.io/jrnl/

##### 09 Feb '15 10h -  VDE - TUN/TAP.
VDE en Virtualsquare nader onderzocht alsook TUN/TAP-interfaces.

##### 09 Feb '15 12h -  Working Ping Demo.
Got the quick start guide demo working. Had some problem to make the picoTCP stack. Turned out I forgot to define the TUN/TAP-flag.

##### 09 Feb '15 17h -  Picoapp, examples.
Tried the picoapp.elf program with some examples and figured out how it works.

##### 09 Feb '15 17h -  MDNS w/o picoapp.
Working on a MDNS-app without the picoapp.elf wrapper. Got some problems figuring out the VDE Networking system.

##### 10 Feb '15 12h -  MDNS-demo with VDE.
Finally got VDE correctly setup and figured out. Working demo of MDNS, including multiple clients claiming the same name.

##### 10 Feb '15 12h -  MDNS w/o picoapp - UPDATE.
Not going continue to write the MDNS-app w/o the picoapp-wrapper despite the fact that I said that earlier. I now understand the wrapper and the MDNS-demo app so it would be a waste of time to write it again. Now I have a decent test-environment with VDE.

##### 10 Feb '15 17h -  Wrote Documentation.
Wrote some documentation about running the picoTCP-demos, TUN/TAP-interfaces and VDE networking, etc. before I forget all these things I learned.

##### 11 Feb '15 18h -  Reading RFC6762 - MDNS.
Started reading RFC6762 and documenting it. I'm not really sure but, the first thing that caught my attention when I tried the same hostname for multiple hosts (Shared Records) with mDNS-demo app was that it wasn't really conflicting, which is allowed by the RFC: 'Enforcing uniqueness of host names is probably desirable in the common case, but this document does not mandate that. [...] It is important that both modes of operation be supported'.
![alt tag](https://raw.githubusercontent.com/jelledevleeschouwer/log/master/mdns_demo_1.png)
However, in the user_doc it says that '...the host will defend its name when another peer tries to claim it. ...'. And if this mode of operation (Shared Records) is the desired behaviour, Shouldn't the Unique Records mode of operation also be implemented like the RFC prescribes? Not completely sure if i'm correct, it's just an assumption. This requires further examination, still. I'm only on page 15, probably more information will be given further on.

##### 13 Feb '15 15h -  Hosts not defending their names.
I think I might have figured wout what the problem was with hosts not defending their name. I think they don't reply to *Probing Queries* at all. Which makes it impossible for the probing host to know that the probed hostname is already in use.

##### 13 Feb '15 17h -  Still reading.
Still reading and documenting RFC6762 (Almost finished though). Probably start diving deeper into the code and compare with the RFC on Monday. Don't have the time to start with it this weekend.

##### 16 Feb '15 17h -  Finished reading RFC6762.
Will start examining the code tommorrow.

##### 17 Feb '15 11h -  Examining code.
Started examining the code this morning. Writing comments in the code with RFC rules and such.

##### 17 Feb '15 11h -  Probing with QU-question.
Did a Wireshark-log of the mDNS-app again, seems the Unicast Response bit of the probing queries isn't being set. This results in the probing queries not being QU-questions which it SHOULD following the RFC. ![alt tag](https://raw.githubusercontent.com/jelledevleeschouwer/log/master/mdns_demo_2.png)

##### 17 Feb '15 15h -  QU-questions - UPDATE.
Made a first conceptual improvement to the mDNS modules, yay! :-) (*Just had a little urge to program while I was examining the code*) Probe queries are now being sent with the Unicast response bit set. Handling the probe queries correctly still needs to be implemented. But first, I am examining the code further. I am making a list of items that I think should be discussed. ![alt tag](https://raw.githubusercontent.com/jelledevleeschouwer/log/master/mdns_demo_3.png)

##### 17 Feb '15 16h -  Simultaneous Probe Tiebreaking.
Seems no proposed records are included in the Authority Section of a probing query. Hereby, Simultaneous Probe Tiebreaking is not possible.

##### 18 Feb '15 17h -  Not responding to Probe Queries.
Figured out why the hosts dont respond to probe queries. Seems while creating an answer, the code cant determine what the length should be of the rdata, according to qtype ANY. The problem lies within the mdns_get_len-function, it cant handle qtype ANY. If solved, the host still doesnt do anything with the defending reply though.

##### 18 Feb '15 18h -  Examining the code - Continued.
Continued examining the code today. Almost finished, still need to examing the cache functions, which won't take long I think.

##### 19 Feb '15 12h -  Name decompression.
Name compression function only works if pointers to other label is not first label in the name. If first label is a pointer, there is a chance that function will probably move out of the boundaries of buf.

##### 19 Feb '15 14h -  Finished examining the code.
Finished examining the code today. Since I have spare time until we'll be discussing my findings on RFC6762 and the code, I will conceptually start improving the functions that I think are very imortant. And then, when we are going to discuss my findings, we can already discuss what I changed and how I did it, and why I think it is important.

##### 20 Feb '15 12h -  Multiple Questions - Answers.
Currently working on a function to create a single question. I'm including the possibility to create a linked-list of multiple questions which you can pass to the 'create a query' function so multiple questions can be added in a single DNS packet. I am planning to this also for answer records, so it is possible to include multiple answers in either the Answer, Authority or Additional Section.

##### 20 Feb '15 14h -  Single Question function and query filling.
Function to create a single question 'standalone' finished. Function to fill the query with multiple questions in the Question Section, works too. Right now, only a question list is given to the fill-query function, but, when I have finished the function to create a single standalone resource record (answer), I will implement the functionallity to add muliple records in the Answer, Authority and/or Additional Sections.

##### 20 Feb '15 21h -  Single Resource Record function and Resource Record Sections filling.
Finished function to create single Resource Record. Also finished function to fill the Resource Record Sections of a DNS packet with multiple resource records, including in the Authority Section and the Additional Section, which wasn't implemented before. Will test on Monday, looks good though, a lot cleaner now. Small issue, I've written these functions in the mDNS-module, but it makes a lot more sense if they would be included in the DNS-common module. Probably will change that next week, but I'll guess that's something we can discuss on Tuesday when we meet up.
