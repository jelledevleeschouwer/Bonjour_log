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

##### 26 Feb '15 14h -  pico_dns_common.
Seeing which functionalities can be implemented in de DNS-common code instead of in the MDNS module itself.

##### 27 Feb '15 11h -  Debugging.
Debugged the code written last week, and pushed the changes. Going to implement the dns functions in the DNS module today.

##### 27 Feb '15 12h -  Devon Branch.
Did some examining in the Devon-branch. Seems this branch already includes a some of the generic functionalities I was planning on implementing myself in the DNS common code. Still no possibility of adding Authority or Additional records and inserting multiple resource records though.

##### 27 Feb '15 18h -  Moving to DNS_common.
Working on moving the generic DNS-functionalities in the mDNS module to the pico_dns_common module. Still working on it, need to still implement the answer and authority authority section filling in function pico_dns_query_create.

##### 02 Mar '15 11h -  Moved all DNS functionalities to pico_dns_common.
Today I'm going to begin writing the defending names part & maybe the Simultaneous Probe Tiebreaking part.

##### 03 Mar '15 10h -  Moved DNS name compression.
Moved DNS name compression functionalities to de DNS common module. Now updating the Unit test for the mDNS module to work again and creating a Unit test for the common DNS module.

##### 03 Mar '15 13h -  Written unit tests.
Written several unit tests for the common DNS module and made the test for the mDNS module work again. Removed a couple of test conditions or commented them out because I still to need revise these functions in the code itself, so no point in testing them already.

##### 03 Mar '15 14h -  Resource Record Sections in DNS Queries.
Now Multiple resource records can be inserted in the Resource Record Sections of a DNS query. This allows Simultaneous Probe Tiebreaking and the ability to distinguish Probe Queries from normal mDNS queries.

##### 03 Mar '15 17h -  IPv6 support and API update.
Worked on IPv6 support of the mDNS module and changing the behaviour of mDNS initialise-function so it initialises both an IPv4 and an IPv6 socket to communicate with. Had a little trouble with understanding how the picoTCP stack works with Dual Stack sockets, (IPv4 and IPv6) if it needed 2 separate sockets to bind to or 1 single socket that supports both modes. I've also added a pico_mdns_claim()-function which makes it possible to claim multiple any resource record on the network, not only A, AAAA and PTR records, still need to implement it with the new initialise-function.

##### 04 Mar '15 14h -  Worked on IPv6 support - Stuck.
Was working on IPv6 support for the mDNS module, got stuck on adding this multicast group of interest to the IPv6 socket. Debugged for a long time, figured out IPv6 multicast sockets aren't supported yet.. Saw in the 'issues' section, and it's noted there already. Going to see if it is possible to adding this support myself or not otherwise it's maybe possible to just only support IPv4

##### 05 Mar '15 13h -  Different Logics.
Until now, the mDNS module only claimed a single A, AAAA record for it's own local network address. I tought about it and I think this logic is wrong. The mDNS module should be mere to resolve DNS names to IP-addresses. But not only for it's own adresses, also the addresses of other peers it wants to be able to resolve. Why? Imagine you request a DNS server to resolve a specific domain name, f.e. 'www.google.com'. The DNS server isn't going to reply with it's own address just because that name is registered on that specific server, no, he is going to reply with the address of the HTTP server which is registered on the DNS server. The DNS server has only the task of resolving the name, what doesn't mean that the address in a record registered on the DNS server can't be of another host in the network. So in summary, it should be up to the application using the mDNS module to define wich records it should register and not up to the mDNS module itself, like it does now.

##### 05 Mar '15 22h -  Worked on API functions.
Today, I worked on the API functions to allow the application level (or another module, like 'dns-sd' in the future) to create resource records which it wants to be registered in the mDNS module. Added support for shared records as well. Multiple records can be claimed at the same time, and the proposed records are added in the Authority Sections of a Probe Query to allow Simultaneous Probe Tiebreaking. Tommorrow I will be working on  the cookie-structure and conflict resolution because it needs to be different now, as a result of multiquestion queries. A single cookie should contain more than one resource record and when a conflict occurs for a specific resource record in a cookie, the resource record should be removed from the cookie and conflict resolution should be applied to that conflicting record. That is, removing the resource record from 'my records', appending a suffix, adding the new record to 'my records' and repeating the probing and announcing step again with a new cookie. Shared records should be announced right away, without probing.
