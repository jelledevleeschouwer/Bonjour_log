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

##### 06 Mar '15 19h -  Upated mDNS cookie structures.
Today I worked on the structures of the mDNS cookies. These cookies now support probe queries, plain answers and plain queries. Shared records are probed immediatelly, while unique records need to pass the probing step first. It is some pretty complex code now, because all the records which are claimed at the same time with the function 'pico_mdns_claim()' aren't announced at the same time, so to call the claimed-callback function, some mechanism is required to only call the callback when all the records passed in the claim-function passed the probing & announcing steps. This mechanism is provided by the claim ID, which is set when the records are added to 'my records'. So when a record passed the probing & announcement step, it checks if all 'my records' with the same claim ID passed the initialisation and when this is the case, the callback-function gets called. Didn't finished as much functionalities as I was planning to, because of my change in logics. Next week, I will start with finishing the probing cookies and the conflict resolution mechanism.

##### 11 Mar '15 12h -  API-functions - Cookies finished.
Finally finished the Cookie structures and API-functions this morning. Multiple records with different flags can be claimed at once. That means; unique records together with shared records and multiple records with different types but with the same name. What I still need to do, is check if a record is requested to be claimed, but with the same rname/rtype/rclass than a record that is already in 'my records' and then don't add it too. Claimed Callback only gets called when all the to claim records passed the probe and/or announcement steps. Today I think I am going to finish the conflict resolution part and responding the queries-part.

##### 18 Mar '15 10h -  Presentation Day.

##### 23 Mar '15 23h -  Conflict Resolution.
Today I worked on finishing the conflict resolution possibility. When a conflict occurs for a probing query, the cookie with the probing query is found in the global cookie-list and conflict resolution is applied to that cookie. The 'ANY'-question for the conflicting record is removed, a new name is generated from the conflicting one and new records are created with that name. Conflicting records are removed from the cookie and 'my records' and the newly created records are claimed. There is still something wrong with that the module keeps resolving conflicts even if the suffix ' (n)' is appended, but I think that has something to do with the cookie that still exists and the timer events that still occur. Will debug that tommorrow but don't think it's that much of a problem. ![alt tag](https://raw.githubusercontent.com/jelledevleeschouwer/log/master/mdns_demo_4.png)

##### 24 Mar '15 21h -  Finished defending names.
Finished the defending names functionality completely now. Except for a problem that occurs when a situation with 3 hosts is tested but I think it has nothing to do with the conflict resolving itself. The second hosts sends it's defences correctly via unicast, but the third host doesn't seem to receive them.. Don't know yet what's wrong here, something for tommorrow.
![alt tag](https://raw.githubusercontent.com/jelledevleeschouwer/log/master/mdns_demo_5.png)
In the image above, you see a host which is initialised completely. It has claimed the hostname 'vlees.local' and succeeded in it (1). Then, a second host tries to claim the same name as the first host (2). The second host begins its initialisation-steps and starts probing. Following directly on the first probe the first host defends its name by sending a response on the probe query. A conflict occured. The second host then runs through a conflict resolution mechanism and starts it probing step again with a new name. At last, the second host claimed the hostname 'vlees (2).local' successfully as you can see in the image above.
In the image below you can see the multicast packets send over the wire in wireshark. The pink packets are the ones from the first host and the green, the ones from the second host. You can clearly see the second host probing another name after it's first attempt of probing. Why you don't see the defense from the first host is because the probe queries request an answer sent via unicast, which the second host correctly fulfills.
![alt tag](https://raw.githubusercontent.com/jelledevleeschouwer/log/master/mdns_demo_6.png)

##### 25 Mar '15 12h -  Third host doesn't seem to receive defenses - Debugging.
When probe questions request for unicast responses and from the time that more than 2 hosts participate in the network, host 'n > 2' doesn't seem to receive unicast defenses anymore after they started the probing step the second time. Nevertheless, the hosts who are defending, receive the third hosts' probe queries and respond to them via unicast. But, the third host doesn't seem to receive them anymore. Really don't know what the problem is here.
It has nothing to do with the conflict resolving because, if I let the hosts send probe queries that request for multicast responses, the defending names mechanism works correctly, as you can see in the image below.
![alt tag](https://raw.githubusercontent.com/jelledevleeschouwer/log/master/mdns_demo_7.png)

##### 26 Mar '15 15h -  Found the problem 'n > 2'.
Found the problem today behind hosts 'n > 2' not receiving defenses other than the first. The problem was localized in the ARP-module. Because more than two 2 hosts sended ARP-requests quickly after each other, the third host thought somebody was ARP flooding the network. Assuming this, the third host discarted the ARP requests and didn't reply to them. So the hosts that wanted to defend their names couldn't send their defense via unicast, so that was where the problem was. If I increased PICO_ARP_MAX_RATE, the conflict resolution worked perfectly with unicast probing.

##### 26 Mar '15 15h -  Conflict resolution - Robustness.
Tested the conflict resolution robustness with over 10 different hosts. All did they detect and resolved conflicts perfectly. Even when suffixes changed at numbers to dozens (so from ' (9)' to ' (10)') and from dozens to hundreds (so from ' (99)' to ' (100)') and so on. Only when the suffix is ' (65535)', it is resolves to ' (0)'. But I think that is actually pretty robust, since you will never have more than 65535 devices trying to claim the same name. If you, however, do have that many devices on you local network, you might as well upgrade to some more advanced infrastructure ;-).

##### 27 Mar '15 11h -  Questions for special record types.
Hosts crash when query is received for record with rrtype other than ANY, A, AAAA or PTR. Debugging.

##### 27 Mar '15 12h -  Known-Answer Suppression.
Implemented Known-Answer Suppression (KAS). Records in the Answer section of queries are now removed from the list of answers the host is planning to send to the other host in response to the query.

##### 27 Mar '15 15h -  Implemented Simultaneous Probe Tiebreaking.
When 2 hosts happen to probe for records with the same name in the exactly the same period as another host, a race condition will occur. Neither of the hosts have claimed their records, so neither of them will defend their records. So both the hosts don't receive any defenses what results in both hosts claiming the same name. This is the reason why their proposed records need to be included in the authority section of their probe queries. When simultaneous probing occurs, the host with the lexicographically later record will win an the host with the lexicographically earlier data needs to postpone it's probing with 1 second. This is what I implemented today. When Simultaneous probing occurs, this will be detected and Simultaneous Probe Tiebreaking will be applied. This results in 1 host postponing his probing with 1 second, letting the other host finish it's probing step.

##### 30 Mar '15 15h -  implemented DNS name compression and decompression.
Hosts now fully compress and decompress DNS packets following DNS Name Compression mechanism. DNS name compression was already implemented, but now, host do the name compression on their side as well when packets are sent on the wire.

##### 30 Mar '15 15h -  Starting with caching of records.

##### 01 Apr '15 21h -  Implemented Caching.
Today, finished caching of records and continuous updating of records. Answers received are now cached and reconfirmed at 80% of their TTL. If no response is received on the query the host is going to try to reconfirm again at 85%, 90% and finally 95%. If the record reaches 100% TTL, the record is flushed from the cache.

##### 01 Apr '15 21h -  API-function getrecord.
Now, there is only one API function to get records. If the record (or records) is (or are) found in the cache the callback is immediatelly called with the found record(s). If the record(s) is not found, the host is going to send a query on the wire.

##### 01 Apr '15 21h -  Duplicate Question-Answer Suppression.
I noticed that if a situation is simulated with multiple hosts, Duplicate Question & Answer Suppression is really recommended. Since those multiple hosts receive records of other hosts at mostly the same time, they are also going to try to reconfirm those records around the same time. If there is no Duplicate Questions & Answer Suppression, unnessecary questions and answers are sent right after each other, which really increases the unnessecary burden on the network. I am going to implement this functionality tommorrow.

##### 01 Apr '15 22h -  Fixed Crashing hosts.
Fixed the problem with hosts crashing when they received queries for which they don't have the authority to answer. When no records were found, the host dereferenced a NULL-pointer which is clearly not allowed. Fixed it.

##### 02 Apr '15 09h -  Continuous Querying - Active interests.
Maybe in the future the continuous querying possibility should only be applied to records for which the application sets an active interest on. The application could perhaps set an active interest on a certain type of records and the mDNS-module then applies Continuous updating only on those type of records. This reduces the amount of resources needed by the hosts.

##### 02 Apr '15 10h -  Duplicate Query Suppression.
Implemented duplicate query suppression, now. When a query is received, a corresponding query cookie is looked for. If there is one found, the status of the cookie is set to 'PICO_MDNS_COOKIE_CANCELLED' which doesn't let the host send the query again.

##### 02 Apr '15 17h -  Discovered gdb!
Really could use some backtrace information, so looked up a little information about command-line debugging. Well, this is a relief..

##### 03 Apr '15 12h -  Refactoring mDNS-module.
Currently working on refactoring the mDNS-module. I'm updating all the storage facilities like the cookies, cache and my records to work with PICO_TREE instead of linked-lists. This for memory safety and performance reasons. Linked-lists are still used inside the cookies themselves, because they need to have the ability to store dynamic amounts of questions and records. I preferred linked-lists above the PICO_TREE previously because that allowed me to search the lists in different manners. Especially for the cookies, for which I couldn't really figure out a way to compare them, since they contain multiple questions and records. For example, if the list of questions and records are the same, but ordered differently, a simple comparing implementation would not work. But now, I figured out that the cookies don't really need to be compared in that manner (comparing all the questions and records sequentially), only with inserting them into the tree, where that simple comparing-implementation would work, since there is no reason to insert a cookie with questions that are already in the tree. I can implement a search-algorithm myself for external inputs, for example searching a query cookie for a received answer.

##### 03 Apr '15 18h -  efactoring - finished.
Refactoring of code is done now. All storage facilities are now build on PICO_TREE instead of linked lists. Following image shows what it's look like when 11 hosts are up and running and are maintaining the mDNS-system.
![alt tag](https://raw.githubusercontent.com/jelledevleeschouwer/log/master/mdns_demo_8.png)

##### 03 Apr '15 19h -  A lot of timers.
Apparently the hosts are using a lot of timers at the moment. Don't know yet if this is a problem. In any case, when the hosts are connected to a real life network and DNS-SD is implemented, there will be a whole lot more records than 11, like in the situation shown above. So in case this is a problem, another solution should be found to time events. Nonetheless, the hosts seem to manage it for now.

##### 03 Apr '15 19h -  Duplicate Query Suppression with short TTL.
When the TTL of records are pretty short, in the order of TTL < 60, Duplicate Query Suppression cannot be guaranteed. The chance of two hosts scheduling the sending of records on exactly the same moment gets a lot bigger, since the random variation gets a lot smaller. This isn't really a big of a deal, since, first of all, the TTL of records isn't most likely going to be in the order of TTL < 60. Secondly, Duplicate Query Suppression isn't more than a optimalisation to reduce the burden on the network.

##### 04 Apr '15 11h -  Updating hostname after conflict.
Now the global hostname is updated after a conflict occured while trying to claim the hostname.

##### 09 Apr '15 22h -  Vector-like data structures.
Updated the entire DNS common code and mDNS module last week to support vector-like data structures instead of linked lists, was a bit of work. This again for memory safety reasons. Should maybe run my code in a profiler like valgrind to check for memory leaks and the like. Tommorrow I'll be writing Unit tests.

##### 10 Apr '15 16h -  Fixed wrong cookie-compare function.
Comparing cookies went wrong because cookies don't work with linked-lists anymore. Fixed this.

##### 19 Apr '15 12h -  Finished Unit tests for DNS common code.
Written 46 Unit tests for the DNS common code. Will be starting tonight with the mDNS unit tests.

##### 20 Apr '15 11h -  Updated Timer system of cache records.
As a result of some feedback by Daniele Lacamare on my code I've updated the TTL timing of cache records to use a single function where all the records in the cache are updated and checked simultaneously. This results in a bit of error, but it isn't that critical, so it doesn't really mind.

##### 20 Apr '15 11h -  Updated Timer system of cookie timeout.
Like with the TTL of the cache records I've updated the timeout of query-cookies to use the same function as the cache records to check if they are expired or not.

##### 20 Apr '15 15h -  Fixed some errors with deleting records from Cache tree.
There where some errors when deleting some expired records from the cache-tree, resulting in an endless loop in a pico_tree_foreach. This probably because the when the record is deleted, the pico_tree_next couldn't find the next node. Fixed this by moving to the next node before deleting the current node.

##### 20 Apr '15 15h -  Added Shared over Unique priority.
Implemented the conflict resolution mechanism to resolve conflicts when somebody else announces a shared record with the same name and type as a record which you previously claimed as unique. The RFC prescribes a method for which Shared records have priority over Unique records, so you (because you claimed the unique record first) should apply the conflict resolution mechanism.

##### 20 Apr '15 15h -  Finished mDNS module.
Today, I finished the mDNS module entirely. Entirely is of course a big word, since there is always room for improvement. But, I finished all functionalities I wanted to implement and the code seems robust enough to move on to DNS-SD.

Functionalities I implemented:

* Claiming of multiple unique records
* Claiming of multiple shared records
* Claiming of the 2 above in a single API-function call
* Defending of unique records
* Conflict resolution mechanism
* Simultaneous Probe Tiebreaking
* Known Answer Suppression
* Caching of records
* Continuous querying
* DNS name compression and decompression
* Duplicate query suppression
* Priority of shared records over unique records

##### 21 Apr '15 12h -  Fixed bug with 2 claims directly after each other.
When 'pico_mdns_claim' is called 2 times right after each other with 2 different records, there was a bug that the record from the first claim was probed again the second time, which we don't want. Fixed.

##### 21 Apr '15 22h -  Written 35 of 72 Unit tests for the mDNS module

##### 22 Apr '15 11h -  Deleting multiple tree nodes at once - PICO_TREE.
When you want to delete multiple records at once in a pico_tree memory structure, you immediatelly think of iterating over the tree with a foreach and deleting the nodes you want to be deleted. However, this doesn't work like that, since when you call 'pico_tree_delete(tree, key);', the node-pointer you provided in the foreach will be pointing to a location in memory which is freed. When you then move to the next iteration the pico_tree_next will be dereferencing this pointer, which gives you a segmentation fault.

I originally solved this by doing:
```
pico_tree_foreach(node, tree) {
	node_record = node->keyValue;
	pico_tree_next(node);
	pico_tree_delete(tree, node_record);
	if (pico_mdns_record_delete(&node_record) < 0)
		return -1;
}
```
But if you do this, a node will be skipped in the next iteration because the foreach call pico_tree_next() again. So you don't want this to happen twice so you'll have move back again first before you move to the next iteration:
```
pico_tree_foreach(node, tree) {
	node_record = node->keyValue;
	pico_tree_next(node);
	pico_tree_delete(tree, node_record);
	if (pico_mdns_record_delete(&node_record) < 0)
		return -1;
	pico_tree_prev(node); // Moving back one node
}
```

What you also can do is finding all the records you want to delete first, then iterating over them and deleting them in the tree like that, but I think previous method is more efficient.

##### 22 Apr '15 16h -  Finished all the unit test for the mDNS module.
Finished 58 unit tests for the mDNS module, now cleaning them up.

##### 22 Apr '15 17h -  [MILESTONE] - Finished mDNS and Unit tests.
Reached a milestone today. Finished the mDNS module, 46 unit test for the DNS common code and 58 unit tests for the mDNS module. Tommorrow will discuss with Thomas how the DNS-SD module will look like, and then I can start right away! Yay!

##### 24 Apr '15 13h -  SLAACv4 after DHCP failure.
Tried to update a the DHCPv4 demo app to use SLAACv4 when the DHCPv4 negotiation fails. But, I noticed the DHCP implementation keeps sending DHCPDISCOVER messages without returning. Wouldn't it be nice if the DHCP client 'fails' when 3 DHCPDISCOVER messages are sent without any response? In this way, the application who uses DHCP can use SLAACv4 to assign a Link-Local address to itself. Now, this isn't a possibility.

##### 24 Apr '15 14h -  Added required rdata length to create DNS record.
Changed record create function so that the length of rdata needs to be given when creating a record. Previously the length of rdata was determined based on the DNS type but that only worked for A, AAAA and PTR records. Now that I need to support TXT and SRV records, that works no longer.

##### 27 Apr '15 16h -  Created skeleton for the DNS-SD module.
Today, I started working on the DNS-SD module. Created a skeleton in the header file, added some makefile rules, created a DNS SD demo app for testing purposes.

##### 27 Apr '15 16h -  Added support for SRV records.
Now SRV records can be created easily in the DNS-SD module. 'Shouldn't you move that to the DNS common code?' Well, I think not. Because, the purpose of the DNS common code is to provide the bare functionalities to create DNS resource records and DNS questions. It should be up to the module or application using the DNS common code to provide right formatted rdata when creating those records as is the case with SRV records. In my opinion, the DNS common code already provides even too much functionalities. Like converting the URL given in rdata to DNS name format when PTR records are created. In my opinion this should be up to the application or other module as well. But for now, it doesn't do any harm.

##### 28 Apr '15 00h -  Whoops, already have a working DNS-SD module.
Haha, wow, think I overestimated DNS-SD a bit. I already finished a working DNS-SD demo in bit over 4 hours. It can register a service with SRV record and a TXT record but no browsing for now.

##### 28 Apr '15 00h -  Added TXT records.
Now, TXT records can be created in the DNS-SD module with a key-value pair vector. Seperate key-value pairs can be added to the vector. When the value is a NULL-ptr the '='-symbol will not be added in the txt and the key will be interpreted as a boolean. Otherwise the value can be empty or with a value.

##### 28 Apr '15 00h -  Had a bug with compression of multiple records.
Still had a bug in the DNS common code. Which means my unit tests for that code, didn't eliminate the bug, what means I was sloppy.. Debugged a long time on this one. There was someting wrong with memcpy(), which was really really weird. If i gave a number of bytes to copy, like: ```memcpy(dest, src, 40);``` ìt didn't copy all the number of bytes given, but if I did: ```memcpy(dest, src, 40 + 1);``` It copied 1 more (!) than originally but still not all of them. I thought this was really weird, and I have no idea what the problem is here. So I implemented my own way of moving data...

##### 28 Apr '15 15h -  Poked around with OpenOCD and Eclipse.
Since I already finished registering services almost entirely (except for a callback and probe questions removal), I wanted to test this on the Hitex Evaluation Board. So spend some time with the connected-device repository, OpenOCD and eclipse to build projects for the platform. Couldn't program the flash yet since I didn't have an adapter to connect the j-link dongle I got from school to the board. Tommorrow will have an adapter so I can play around tommorrow. Will write a bit on my thesis in the meanwhile.

##### 05 May '15 11h -  Finally a working development environment.
Got an solid working environment with eclipse CDT and SEGGER J-Link debuggin plugin. Took a while.

##### 06 May '15 14h -  demo-webserver with Zeroconf.
Updated the demo-webserver application to make use of zeroconf technology. The service of the HTTP service is correctly registered on the network as you can see in the image below. ![alt tag](https://raw.githubusercontent.com/jelledevleeschouwer/log/master/dns_sd_demo_1.png)

##### 06 May '15 15h -  HardFault on PICO_FREE.
Think I've got a memory issue with the demo application. Everytime I debug the application, on a sudden moment a HardFault will be triggered. This mostly occurs in the pico_dns_qname_to_url function. The first thing that I notice is that the string passed in the function is wrong, this in the trend:

```
"\x9_kerberos\xBKingRichard\x5local!"
				    ^ /* This exclamation mark isn't supposed to be here */
```

So I debugged this, and checked the string that is being passed into this function. What is remarkable is that the string being passed in the function _is_ correctly formatted. So:

```
"\x9_kerberos\xBKingRichard\x5local"
```

So I don't think anything is wrong here. But still, the string happens to be changed somewhere. So I updated the function pico_dns_qname_to_url to make use of a predefined string of 256 bytes. I don't really like this method but it works and it happens to be good for memory safety.

You'd think the issue would be solved here. But nothing could be more further from the truth. If I rerun the program several times. The HardFault just happens to be triggered somewhere else. If it's not in the pico_frame_discard()-function then it is in some other function.

I can't really figure out where the problem lies. It's really difficult to debug since it always happens to occur somewhere else. The only cause I can vaguely think of is that the program write somewhere where I'm not supposed to write to, that memory is being corrupted somewhere but figuring this out seems a bit out of my capacity.

##### 06 May '15 15h -  HardFault on STD-functions.
It appears that the HardFault isn't only triggered at the free()-function but also at other STD-functions, like strlen(), zalloc(), etc.But if there is one thing the internet and C programming learned me, is that the STD-functions most likely are not wrong. Bummer.

##### 08 May '15 11h -  Back to DNS-SD.
Okay, I'm gonna let the demo be what it is for now. I'll fix it later. Now I should focus on DNS SD again and writing unit tests. First did a fix of generating probe questions. The names where converted to lowercase which caused incorrect conflict detection.

##### 08 May '15 13h -  Fixed updating of shared PTR record after conflict.
Fixed some bugs in the code which resulted in the shared PTR record not being updated after a conflict occurs. Now the DNS SD module succesfully resolves conflicts.

##### 08 May '15 14h -  Made continuous refreshing configurable.
Made the possibility in mDNS to continuously update the cache entries configurable since this asks some unnessecary performance. It's actually only needed on the browsing-side.

##### 09 May '15 20h -  Written Unit tests for DNS-SD.
Finished writing 17 unit tests for DNS-SD, refactored the code a bit.

##### 14 May '15 17h -  Pull request.
After fixing some reasonable TICS violations, I did a pull request with the development branch. I'm really curious. Now I can focus on my thesis the entire weekend.
