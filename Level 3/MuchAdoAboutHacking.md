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
Open your mind signals an input from the user and remember yourself assigns it to the variable spoken to. So B0 = user_input
```
Benedick:
You are as red as the sum of yourself and a tree. --> B1 = 1
Am I as lovely as a cunning charming honest peaceful bold pony? --> check B0 == 32
```


