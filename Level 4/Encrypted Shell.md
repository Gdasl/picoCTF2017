# Encrypted shell

This service gives a shell, but it's password protected! We were able intercept this encrypted traffic which may contain a successful password authentication. Can you get shell access and read the contents of flag.txt?

We get 2 files, traffic.pcap and dhshell.py. We start with the pcap and see if there's aynthing interesting. We quickly see in info that there are only 2 distinct parties communicating with eachother, we assume one is the user the other the service (sorry if this seems obvious).

Parsing through what the service send us, we see that there is some nice output which gives us a variable p, g and A. Now looking at the service script, this is indeed the case as it cleary prints first a welcome message, then p and g and then A. It then asks for an input by the user (B) and uses that to compute K =  B^a mod p where a is a randint between 2 and 2^48. The key is encrypted using sha256 and used to encrypt ehe password that the user is prompted to enter.

If that equals to the password stored on the server, you gets a shell. So wat do?

First instinct is to go through the pcap dump and see what the user (37210) send to the service (22071). There are 21 packets o.w. 9 are sent by the service and 12 by the user.

The first interesting packet is n°4, where the service displays the welcome message as well as p and g to the user. In packet 6 it sends A. Now we should have B somewhere near, coming from the user and lo and behold there it is in packet 8.

The next interaction we expect is when the user provides the input that will encrypt to the password and in return an answer from the service (either a shell or an "invalid message" message). Packet 10 looks like a good candidate for the former: a 128 long hex string. Immediately after comes a second 96 long hex string, also from the user. This second one is followed by a response from the service, also 
 96 long. 
 
 A quick test reveals that encrypting "Invalid password!\n" with the method outlined in the shell file yields a hex string 96 long. SO that must be it.
