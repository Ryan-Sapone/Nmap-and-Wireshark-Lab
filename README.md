<h1>Nmap and Wireshark Lab</h1>

I took some time to utilize a few different Nmap scans and see how exactly the data is being sent across a network and what it looks like in Wireshark. In this README file you'll find screenshots of what my syntax looked like for the Nmap scan, pictures of the results of the scan, and also screenshots of the Wireshark pcaps. You can download the pcap files yourself if you want to do a deeper analysis of the traffic. 

Note: I used the -F flag in all of my scans to only scan for 100 ports maximum. I also had TCP ports 80, 143, and 995 opened via a Netcat listener. I also opened UDP ports 593 and 1645 via a Netcat listener. You can see in the first picture that my attacking host is 192.168.12.235 and the target machine was 192.168.12.148. 

Each of these scans will have the Nmap documentation (available from https://nmap.org/book/man-port-scanning-techniques.html) accompanied by my limited Wireshark analysis. Find me on LinkedIn and feel free to read my full article.

<h2>Nmap ping scan</h2>
Here is just the ping sweep of my network to identify my attacking machine (.235) and my target (.148)
<img src="https://user-images.githubusercontent.com/107446796/180568373-77133887-27fb-4822-b2dc-b87ba1f64e48.png">

<h2>-sS</h2>
SYN scan is the default and most popular scan option for good reasons. It can be performed quickly, scanning thousands of ports per second on a fast network not hampered by restrictive firewalls. It is also relatively unobtrusive and stealthy since it never completes TCP connections. SYN scan works against any compliant TCP stack rather than depending on idiosyncrasies of specific platforms as Nmap's FIN/NULL/Xmas, Maimon and idle scans do. It also allows clear, reliable differentiation between the open, closed, and filtered states.

This technique is often referred to as half-open scanning, because you don't open a full TCP connection. You send a SYN packet, as if you are going to open a real connection and then wait for a response. A SYN/ACK indicates the port is listening (open), while a RST (reset) is indicative of a non-listener. If no response is received after several retransmissions, the port is marked as filtered. The port is also marked filtered if an ICMP unreachable error (type 3, code 0, 1, 2, 3, 9, 10, or 13) is received. The port is also considered open if a SYN packet (without the ACK flag) is received in response. This can be due to an extremely rare TCP feature known as a simultaneous open or split handshake connection (see https://nmap.org/misc/split-handshake.pdf).

<img src="https://user-images.githubusercontent.com/107446796/180570057-8903c43e-1995-4fbd-a3a5-843216bb97b8.png">
<img src="https://user-images.githubusercontent.com/107446796/180570496-86ba9360-ac44-47df-9e22-7d38854d93b3.png">
You can see that Nmap picked up on my three open TCP ports, and in Wireshark, you can see the SYN and SYN/ACK for port 80, and the SYN and RST for closed port 113.

<h2>-sT</h2>
TCP connect scan is the default TCP scan type when SYN scan is not an option. This is the case when a user does not have raw packet privileges. Instead of writing raw packets as most other scan types do, Nmap asks the underlying operating system to establish a connection with the target machine and port by issuing the connect system call. This is the same high-level system call that web browsers, P2P clients, and most other network-enabled applications use to establish a connection. It is part of a programming interface known as the Berkeley Sockets API. Rather than read raw packet responses off the wire, Nmap uses this API to obtain status information on each connection attempt.

When SYN scan is available, it is usually a better choice. Nmap has less control over the high level connect call than with raw packets, making it less efficient. The system call completes connections to open target ports rather than performing the half-open reset that SYN scan does. Not only does this take longer and require more packets to obtain the same information, but target machines are more likely to log the connection. A decent IDS will catch either, but most machines have no such alarm system. Many services on your average Unix system will add a note to syslog, and sometimes a cryptic error message, when Nmap connects and then closes the connection without sending data. Truly pathetic services crash when this happens, though that is uncommon. An administrator who sees a bunch of connection attempts in her logs from a single system should know that she has been connect scanned.

<img src="https://user-images.githubusercontent.com/107446796/180570524-560e81f4-15c5-4e48-8b58-a9db7d8d527e.png">
<img src="https://user-images.githubusercontent.com/107446796/180570533-f4464d30-28a5-4556-ae56-a2d182b1d718.png">
Same information, however you can see the ACK message being sent for the open port 80, completing the connection.

<h2>-sA</h2>
This scan is different than the others discussed so far in that it never determines open (or even open|filtered) ports. It is used to map out firewall rulesets, determining whether they are stateful or not and which ports are filtered.

The ACK scan probe packet has only the ACK flag set (unless you use --scanflags). When scanning unfiltered systems, open and closed ports will both return a RST packet. Nmap then labels them as unfiltered, meaning that they are reachable by the ACK packet, but whether they are open or closed is undetermined. Ports that don't respond, or send certain ICMP error messages back (type 3, code 0, 1, 2, 3, 9, 10, or 13), are labeled filtered.

<img src="https://user-images.githubusercontent.com/107446796/180570692-b9db068d-f131-4d38-9da7-eaa2f05ec9b7.png">
<img src="https://user-images.githubusercontent.com/107446796/180570700-c50d5e09-e6ef-4c8b-8ed4-325b69d90715.png">
You can see that even though port 143 is open from earlier scans, the ACK scan doesn't show up as opened for us. There's also no difference in Wireshark that I found.

<h2>-sW</h2>
Window scan is exactly the same as ACK scan except that it exploits an implementation detail of certain systems to differentiate open ports from closed ones, rather than always printing unfiltered when a RST is returned. It does this by examining the TCP Window field of the RST packets returned. On some systems, open ports use a positive window size (even for RST packets) while closed ones have a zero window. So instead of always listing a port as unfiltered when it receives a RST back, Window scan lists the port as open or closed if the TCP Window value in that reset is positive or zero, respectively.

This scan relies on an implementation detail of a minority of systems out on the Internet, so you can't always trust it. Systems that don't support it will usually return all ports closed. Of course, it is possible that the machine really has no open ports. If most scanned ports are closed but a few common port numbers (such as 22, 25, 53) are filtered, the system is most likely susceptible. Occasionally, systems will even show the exact opposite behavior. If your scan shows 1,000 open ports and three closed or filtered ports, then those three may very well be the truly open ones.

<img src="https://user-images.githubusercontent.com/107446796/180570779-91208809-0042-46b0-828b-0cda672679d6.png">
<img src="https://user-images.githubusercontent.com/107446796/180570787-2ff595c1-67ed-4085-a74c-a5babd3340f2.png">

<h2>-sM</h2>
The Maimon scan is named after its discoverer, Uriel Maimon. He described the technique in Phrack Magazine issue #49 (November 1996). Nmap, which included this technique, was released two issues later. This technique is exactly the same as NULL, FIN, and Xmas scans, except that the probe is FIN/ACK. According to RFC 793 (TCP), a RST packet should be generated in response to such a probe whether the port is open or closed. However, Uriel noticed that many BSD-derived systems simply drop the packet if the port is open.

<img src="https://user-images.githubusercontent.com/107446796/180570863-7db60e7b-eca2-455c-88fe-5e47f0999bd9.png">
<img src="https://user-images.githubusercontent.com/107446796/180570869-894dd00c-fe53-4ff5-b74b-dd6a337a030b.png">

<h2>-sU</h2>
While most popular services on the Internet run over the TCP protocol, UDP services are widely deployed. DNS, SNMP, and DHCP (registered ports 53, 161/162, and 67/68) are three of the most common. Because UDP scanning is generally slower and more difficult than TCP, some security auditors ignore these ports. This is a mistake, as exploitable UDP services are quite common and attackers certainly don't ignore the whole protocol. Fortunately, Nmap can help inventory UDP ports.

UDP scan is activated with the -sU option. It can be combined with a TCP scan type such as SYN scan (-sS) to check both protocols during the same run.

UDP scan works by sending a UDP packet to every targeted port. For some common ports such as 53 and 161, a protocol-specific payload is sent to increase response rate, but for most ports the packet is empty unless the --data, --data-string, or --data-length options are specified. If an ICMP port unreachable error (type 3, code 3) is returned, the port is closed. Other ICMP unreachable errors (type 3, codes 0, 1, 2, 9, 10, or 13) mark the port as filtered. Occasionally, a service will respond with a UDP packet, proving that it is open. If no response is received after retransmissions, the port is classified as open|filtered. This means that the port could be open, or perhaps packet filters are blocking the communication. Version detection (-sV) can be used to help differentiate the truly open ports from the filtered ones.

A big challenge with UDP scanning is doing it quickly. Open and filtered ports rarely send any response, leaving Nmap to time out and then conduct retransmissions just in case the probe or response were lost. Closed ports are often an even bigger problem. They usually send back an ICMP port unreachable error. But unlike the RST packets sent by closed TCP ports in response to a SYN or connect scan, many hosts rate limit ICMP port unreachable messages by default. Linux and Solaris are particularly strict about this. For example, the Linux 2.4.20 kernel limits destination unreachable messages to one per second (in net/ipv4/icmp.c).

Nmap detects rate limiting and slows down accordingly to avoid flooding the network with useless packets that the target machine will drop. Unfortunately, a Linux-style limit of one packet per second makes a 65,536-port scan take more than 18 hours. Ideas for speeding your UDP scans up include scanning more hosts in parallel, doing a quick scan of just the popular ports first, scanning from behind the firewall, and using --host-timeout to skip slow hosts.

<img src="https://user-images.githubusercontent.com/107446796/180570916-fe82072b-123d-4ee2-949c-d902f21baead.png">
<img src="https://user-images.githubusercontent.com/107446796/180570921-395a4c36-ab58-4bbd-9d2f-0b22df8b7534.png">
Even though I had UDP ports 1645 and 593 open, the destinations were still unreachable.

<h2>-sN</h2>
These three scan types (even more are possible with the --scanflags option described in the next section) exploit a subtle loophole in the TCP RFC to differentiate between open and closed ports. Page 65 of RFC 793 says that “if the [destination] port state is CLOSED .... an incoming segment not containing a RST causes a RST to be sent in response.” Then the next page discusses packets sent to open ports without the SYN, RST, or ACK bits set, stating that: “you are unlikely to get here, but if you do, drop the segment, and return.”

When scanning systems compliant with this RFC text, any packet not containing SYN, RST, or ACK bits will result in a returned RST if the port is closed and no response at all if the port is open. As long as none of those three bits are included, any combination of the other three (FIN, PSH, and URG) are OK. Nmap exploits this with three scan types:
Null scan (-sN)
Does not set any bits (TCP flag header is 0)

<img src="https://user-images.githubusercontent.com/107446796/180570980-38b39589-0673-4043-9c3a-bb5c59c2bb2a.png">
<img src="https://user-images.githubusercontent.com/107446796/180570993-b2cd3941-92e0-40f7-859f-ba481050d488.png">

<h2>-sF</h2>
FIN scan (-sF)
Sets just the TCP FIN bit.

<img src="https://user-images.githubusercontent.com/107446796/180571108-d83528a0-9b5d-4702-aa34-8991f75d98d9.png">
<img src="https://user-images.githubusercontent.com/107446796/180571116-30086a87-88e7-4d1e-b738-51bed552022e.png">

<h2>-sX</h2>
Xmas scan (-sX)
Sets the FIN, PSH, and URG flags, lighting the packet up like a Christmas tree.

<img src="https://user-images.githubusercontent.com/107446796/180571189-a5d5a266-5c11-4b53-ac98-4698bb2c8f17.png">
<img src="https://user-images.githubusercontent.com/107446796/180571196-1c1e24e5-7c4c-4329-a51c-a7d7a3b842e7.png">

<h2>-sY</h2>
SCTP is a relatively new alternative to the TCP and UDP protocols, combining most characteristics of TCP and UDP, and also adding new features like multi-homing and multi-streaming. It is mostly being used for SS7/SIGTRAN related services but has the potential to be used for other applications as well. SCTP INIT scan is the SCTP equivalent of a TCP SYN scan. It can be performed quickly, scanning thousands of ports per second on a fast network not hampered by restrictive firewalls. Like SYN scan, INIT scan is relatively unobtrusive and stealthy, since it never completes SCTP associations. It also allows clear, reliable differentiation between the open, closed, and filtered states.

This technique is often referred to as half-open scanning, because you don't open a full SCTP association. You send an INIT chunk, as if you are going to open a real association and then wait for a response. An INIT-ACK chunk indicates the port is listening (open), while an ABORT chunk is indicative of a non-listener. If no response is received after several retransmissions, the port is marked as filtered. The port is also marked filtered if an ICMP unreachable error (type 3, code 0, 1, 2, 3, 9, 10, or 13) is received.

<img src="https://user-images.githubusercontent.com/107446796/180571517-1abb92ec-e121-4d7a-9535-7f1788da23c1.png">
<img src="https://user-images.githubusercontent.com/107446796/180572229-56f3634d-3427-44be-8519-5d5346a8320c.png">

<h2>-sZ</h2>
SCTP COOKIE ECHO scan is a more advanced SCTP scan. It takes advantage of the fact that SCTP implementations should silently drop packets containing COOKIE ECHO chunks on open ports, but send an ABORT if the port is closed. The advantage of this scan type is that it is not as obvious a port scan than an INIT scan. Also, there may be non-stateful firewall rulesets blocking INIT chunks, but not COOKIE ECHO chunks. Don't be fooled into thinking that this will make a port scan invisible; a good IDS will be able to detect SCTP COOKIE ECHO scans too. The downside is that SCTP COOKIE ECHO scans cannot differentiate between open and filtered ports, leaving you with the state open|filtered in both cases.

<img src="https://user-images.githubusercontent.com/107446796/180571748-b4358b6b-94a1-4ef7-901b-7858c97e2b64.png">
<img src="https://user-images.githubusercontent.com/107446796/180572236-e58ff400-e35d-4770-a7d2-9a9a2aa989b2.png">

<h2>-sV</h2>
(see https://nmap.org/book/vscan.html)

<img src="https://user-images.githubusercontent.com/107446796/180571842-135a9f06-bf60-4f92-8d02-7dca35076054.png">
<img src="https://user-images.githubusercontent.com/107446796/180571937-2690ca67-0893-44eb-bddd-344bf72706e4.png">
Due to only having the port open because of a Netcat listener, Nmap was not able to list any version or script information.

<h2>-sC</h2>
(see https://nmap.org/book/nse.html)

<img src="https://user-images.githubusercontent.com/107446796/180571872-4af824df-1181-4bb7-a6f6-ebff9bea47d9.png">
<img src="https://user-images.githubusercontent.com/107446796/180571928-a1d07beb-ec48-4201-8fd1-be9b44958d7f.png">
Due to only having the port open because of a Netcat listener, Nmap was not able to list any version or script information.
