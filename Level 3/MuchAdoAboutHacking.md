# Much Ado About Hacking
Right away it's clear that this here is code written in spl. A nice intro can be found on wikipedia [here](https://en.wikipedia.org/wiki/Shakespeare_Programming_Language). Let's analyze it iece by piece.

### Personas: Variables
In this language, stack variables are declared at the beginning in the form of character names. So we have 6 different variables. The description is irrelevant and is ignored.
Thus we have the following variables:
```
Benedick (B0)
Beatrice (B1)
Don Pedro (D0)
Don John (D1)
Achilles (A0)
Cleopatra (C0)
```

### Act and Scenes: the execution part
Unlike many of Shakespeare's plays this is code execution, not actual execution. Basically, Acts and Scenes act as gotos (like .L's in assembly). In this case we only have 1 Act but 3 Scenes.

Scene 1:
```
[Enter Beatrice and Don John]
Beatrice:
You are nothing!
[Exit Don John]
[Enter Don Pedro]
Beatrice:
You are nothing!
[Exit Don Pedro]
[Enter Achilles]
Beatrice: 
You are as proud as a bold brave gentle noble amazing hero. 
[Exit Achilles]
[Enter Cleopatra]
Beatrice:
You are as vile as the difference between a beautiful gentle bold fair peaceful sunny lovely flower and Achilles. 
[Exit Cleopatra]
[Enter Benedick]
Beatrice:
You are nothing!
```

Scene II: 
```
Beatrice: 
Open your mind! Remember yourself.
Benedick:
You are as red as the sum of yourself and a tree. //increment by 1
Am I as lovely as a cunning charming honest peaceful bold pony? //B0 == 32
Beatrice:
If not, let us return to scene II.
Benedick: 
You are as worried as the sum of yourself and a Microsoft. //decrement by 1
Beatrice:
Recall your father's disappointment!
```

Scene III:
```
Beatrice:
Recall your latest blunder! You are as smelly as the difference between yourself and Achilles.  //B0-A0
Benedick: 
You are as disgusting as the sum of yourself and a flirt-gill! // B1 = B1 -1
[Exit Beatrice]
[Enter Don John]
Benedick:
Thou art as damned as I. //D1 = B0
[Exit Don John]
[Enter Don Pedro]
Don Pedro:
You are as rotten as the sum of yourself and myself. //B0 = B0 + D1
Thou art as normal as the remainder of the quotient between thyself and Cleopatra. //B0 = B0 % C0
[Exit Benedick]
[Enter Don John]
Don John:
You are as good as I. //D0 = D1
[Exeunt]
[Enter Beatrice and Benedick]
Beatrice:
You are as foul as the sum of yourself and Achilles. Speak your mind! //B0 = B0 + A0 --> output as ascii
Benedick:
Are you better than nothing? //B1 > 0?
Beatrice:
If so, let us return to scene III.
```

and finally the return 0 call: ```[Exeunt]```

### Diving in
#### Scene 1
Let's start with Scene I. A basic rule of SPL is that only 2 characters (variables) may be on the stage at a given point. Invoking and 'devoking' occurs by using "Enter", "Exit" or "Exeunt". Moreover, characters interact by referring to eachother as 'you'.
```
[Enter Beatrice and Don John]
Beatrice:
You are nothing!
[Exit Don John]
```
B1 sets D1 = 0
```
[Enter Don Pedro]
Beatrice:
You are nothing!
[Exit Don Pedro]
```
B1 sets D0 = 0
```
[Enter Achilles]
Beatrice: 
You are as proud as a bold brave gentle noble amazing hero. 
[Exit Achilles]
```
This is a bit more tricky. In SPL, as per wikipedia: "Positive and neutral nouns have a value of 1 and negative nouns have a value of -1. Any adjective multiplies a noun by 2, and adjectives can be compounded. A sentence that assigns a value to a character starts with "You", may optionally continue with "are as [any adjective] as", and then gives the mathematical formula in nouns, adjectives, variables, and operations for the new value." In this case thus, A0 is set to 2**5.
```
[Enter Cleopatra]
Beatrice:
You are as vile as the difference between a beautiful gentle bold fair peaceful sunny lovely flower and Achilles. 
[Exit Cleopatra]
```
Applying the rules above, we get C0 = 2**7-2**5 = 128-32 = 96
The last line sets Benedick (B0) to 0.

So let's recapitalute. At the end of the first act we have the following:
```
Benedick (B0) = 0
Beatrice (B1) = 0
Don Pedro (D0) = 0
Don John (D1) = 0
Achilles (A0)  = 32
Cleopatra (C0) = 96
```

#### Scene 2
Remember at this point, Beatrice and Benedick are still on stage.
```
Beatrice: 
Open your mind! Remember yourself.
```
Open your mind signals an input from the user and remember yourself appends it to the stack. So ```B0 = [B0] + user_input```.
```
Benedick:
You are as red as the sum of yourself and a tree. --> B1 = 1
Am I as lovely as a cunning charming honest peaceful bold pony? --> check B0 == 32
Beatrice:
If not, let us return to scene II.
```
We see that Beatrice is basically a rudimentary length operator. It increments itself by 1 for each new char. The last line basically is a "jne" instruction i.e. if the input char is not 32 (a space), continue reading. Finally we have this:
```
Benedick: 
You are as worried as the sum of yourself and a Microsoft. //decrement by 1
Beatrice:
Recall your father's disappointment!
```
Because the char is tested _after_ having been read, it's still sitting on the stack. As such, we pop it and decrement B0 (Beatrice)  in order to reflect the new length of the string in memory.

#### Scene 3
Oh boy, that's a long one. Here's a line by line translation. It's nothing new and builds upon what we've seen up to now.
```
Beatrice:
Recall your latest blunder! You are as smelly as the difference between yourself and Achilles.  //B0-A0
```
Pop value on Benedick, assign it to temp variable and substract 32.
```
Benedick: 
You are as disgusting as the sum of yourself and a flirt-gill! // B1 = B1 -1
[Exit Beatrice]
```
Decrement Beatrice by 1
```
[Enter Don John]
Benedick:
Thou art as damned as I. //D1 = B0
[Exit Don John]
```
Set Don John to current temp value (current char - 32)
```
[Enter Don Pedro]
Don Pedro:
You are as rotten as the sum of yourself and myself. //B0 = B0 + D1
Thou art as normal as the remainder of the quotient between thyself and Cleopatra. //B0 = B0 % C0
[Exit Benedick]
```
Sum the temp variable and don_pedro (0 during the first run), then return that mod 96
```
[Enter Don John]
Don John:
You are as good as I. //D0 = D1
[Exeunt]
```
Assign temp variable (current char-32) to don_pedro
```
[Enter Beatrice and Benedick]
Beatrice:
You are as foul as the sum of yourself and Achilles. Speak your mind! //B0 = B0 + A0 --> output as ascii
```
First add 32 to the current variable. Then, "Speak your mind" is equivalent of print char.
```
Benedick:
Are you better than nothing? //B1 > 0?
Beatrice:
If so, let us return to scene III.
```
Again a check. Basically do this whole loop (Scene 3), while there are still chars to be read.


### Pseudo and python code

Basically the program can be summed up with the following code:
```python
benedick = []
beatrice = 0
don_john = 0
don_pedro = 0
achilles = 32
cleopatra = 96
for i in inp:
        if ord(i) != 32:
            benedick.append(ord(i))
            beatrice += 1
        else:
          break
     
while beatrice > 0:
        a = benedick.pop()
        a = a - achilles
        beatrice -= 1

        don_john = a
        a += don_pedro
        a %= 96

        don_pedro = don_john
        a += 32
        print chr(a)
```
So all we need is to reverse that function:
```python
ending = open('ending.txt').read().strip('\n')
def solve(inpt):
    bene2 = []
    for i in inpt:
        bene2.append(ord(i))

    bea2 = len(bene2)
    temp = 0
    flag = ""
    for i in bene2:
    ##    a = bene2[bea2-1]
        a=i
        bea2 -= 1
        a-=32
        a-=temp
        if a >0:
            pass
        else:
            a += 96
        temp = a
        print a+32
        flag += (chr(a+32))
    return flag[::-1]
```

The flag is then returned!!

*Interesting note*: because of the modulo, this reverse function won't work to encode bytes that are higher than 128. E.g. if the value to encode is 192, mod 96 wil yield 0 and the decrypt function will actually return 96 because it can't predict wrap arounds. Still, a fun challenge overall!!
