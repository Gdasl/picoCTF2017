# SoRandom

We get a nice source file:

```python
#!/usr/bin/python -u
import random,string

flag = "FLAG:"+open("flag.txt", "r").read()[:-1]
print flag
encflag = ""
random.seed("random")
for c in flag:
  if c.islower():
    #rotate number around alphabet a random amount
    encflag += chr((ord(c)-ord('a')+random.randrange(0,26))%26 + ord('a'))
  elif c.isupper():
    encflag += chr((ord(c)-ord('A')+random.randrange(0,26))%26 + ord('A'))
  elif c.isdigit():
    encflag += chr((ord(c)-ord('0')+random.randrange(0,10))%10 + ord('0'))
  else:
    encflag += c
print "Unguessably Randomized Flag: "+encflag
```

This was one of the few challenges that weren't solvable locally because the random.seed function somehow differs on my machines. Basically, the idea is that by choosing a fixed seed everytime, the random is not random at all but becomes deterministic, i.e. for the same order, the numbers obtained are the same. Easiest here is just to reverse which is what I did. Code below was basically just pasted in the challenge console directly and run.

```python
flaggy =""
random.seed("random")
for c in encflag:
  if c.islower():
    #rotate number around alphabet a random amount
    flaggy += chr((ord(c)-ord('a')-random.randrange(0,26))%26 + ord('a'))
  elif c.isupper():
    flaggy += chr((ord(c)-ord('A')-random.randrange(0,26))%26 + ord('A'))
  elif c.isdigit():
    flaggy += chr((ord(c)-ord('0')-random.randrange(0,10))%10 + ord('0'))
  else:
    flaggy += c
    
```

Boom done.
