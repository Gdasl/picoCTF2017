# Encrypted shell
## Tough Beginnings
This service gives a shell, but it's password protected! We were able intercept this encrypted traffic which may contain a successful password authentication. Can you get shell access and read the contents of flag.txt?

We get 2 files, traffic.pcap and dhshell.py. We start with the pcap and see if there's aynthing interesting. We quickly see in info that there are only 2 distinct parties communicating with eachother, we assume one is the user the other the service (sorry if this seems obvious).

Parsing through what the service send us, we see that there is some nice output which gives us a variable p, g and A. Now looking at the service script, this is indeed the case as it cleary prints first a welcome message, then p and g and then A. It then asks for an input by the user (B) and uses that to compute K =  B^a mod p where a is a randint between 2 and 2^48. The key is encrypted using sha256 and used to encrypt ehe password that the user is prompted to enter.

If that equals to the password stored on the server, you gets a shell. So wat do?

First instinct is to go through the pcap dump and see what the user (37210) send to the service (22071). There are 21 packets o.w. 9 are sent by the service and 12 by the user.

The first interesting packet is n°4, where the service displays the welcome message as well as p and g to the user. In packet 6 it sends A. Now we should have B somewhere near, coming from the user and lo and behold there it is in packet 8.

The next interaction we expect is when the user provides the input that will encrypt to the password and in return an answer from the service (either a shell or an "invalid message" message). Packet 10 looks like a good candidate for the former: a 128 long hex string. Immediately after comes a second 96 long hex string, also from the user. This second one is followed by a response from the service, also 
 96 long. 
 
 A quick test reveals that encrypting "Invalid password!\n" with the method outlined in the shell file yields a hex string 96 long. SO that must be it.
 
 Also, A is printed by the service but serves no real other purpose. That leads me to think that it is the key to this whole thing. We need the small exponent a, the correct one, in order to be able to decrypt whatever was sent.

## The Reckoning
As it turns out, that was right. What I was missing was the correct PC and algorithm. I had tried over several hours to use Baby Steps Giant Steps to no avail. Turns out the hardware was at fault. Using my 16gb i7 8th gen it took only about a minute using ~4.5gb of ram. The idea is to create a hastable with all possible values of $g^a\mod\p$ and then apply BSGS as a meet in the middle algo. I used the following code:

```python
def baby_steps_giant_steps(a,b,p,N = None):
    if not N: N = p
    N = 1 + int(math.sqrt(N))
 
    #initialize baby_steps table
    baby_steps = {}
    baby_step = 1
    for r in range(N+1):
        baby_steps[baby_step] = r
        baby_step = baby_step * a % p
       
       
    print 'done hashing'
    #now take the giant steps
    giant_stride = pow(a,(p-2)*N,p)
    giant_step = b
    for q in range(N+1):
        if giant_step in baby_steps:
            return q*N + baby_steps[giant_step]
        else:
            giant_step = giant_step * giant_stride % p
    return "No Match"
```

## The Solving
a turns out to be 8568666222532 (for me, it stands to reason that it would be different for everyone). We thus simply take the original script and hardcode p, g, A, a and B. 
There are 5 hex in- and outputs. 
```
input1:  'ThisIsMySecurePasswordPleaseGiveMeAShell\n'
input2:  'echo "Does this shell work?"'
output1: 'Does this shell work?\n'
input3:  'exit'
output2:  ''
```
So all we need is to send the encrypted passcode (input1) to the service.

## The setback
Until now, I solved all the challenges apart from the soRandom one "offline" as in I would just input the solution in the end. I realized too late that a has to basically be bruteforced in real time in order to correctly encrypt the password and send it to the service (since a changes everytime you call it....). That took me a little while especially since I botched the keyz challenge and was never able to put my private key and thus access the shell from outside the challenge grounds. I ended up expanding my script to encrypt and decrypt the input in real time and copied pasted back and forth... Not glorious but in the end, after 6 hours, o.w. 5 trying to run an algo on the wrong machine, I finally got the flag.
