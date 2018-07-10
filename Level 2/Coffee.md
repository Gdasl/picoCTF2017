# Coffee
You found a suspicious USB drive in a jar of pickles. It contains this file.

Well the hints here are just overwhelming. "Jar" and "Coffee" aka. Beans. In fact we get a .class file. Opening it in any decomplier worth its salt, e.g. IntelliJ, reveals a simple function that converts a few strings to bytearrays, perform a few basic operations, convert them back into ascii to reveal a b64 string that can be simply decoded to reveal:

flag_{pretty_cool_huh}

I chose to rewrite the function in python because why not.

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

import java.util.Arrays;
import java.util.Base64;

public class problem {
    public problem() {
    }

    public static String get_flag() {
        String var0 = "Hint: Don't worry about the schematics";
        String var1 = "eux_Z]\\ayiqlog`s^hvnmwr[cpftbkjd";
        String var2 = "Zf91XhR7fa=ZVH2H=QlbvdHJx5omN2xc";
        byte[] var3 = var1.getBytes();
        byte[] var4 = var2.getBytes();
        byte[] var5 = new byte[var4.length];

        for(int var6 = 0; var6 < var4.length; ++var6) {
            var5[var6] = var4[var3[var6] - 90];
        }

        System.out.println(Arrays.toString(Base64.getDecoder().decode(var5)));
        return new String(Base64.getDecoder().decode(var5));
    }

    public static void main(String[] var0) {
        System.out.println("Nothing to see here");
    }
}

```

and the corresponding python code:

```python
import array
import base64

var0 = "Hint: Don't worry about the schematics"
var1 = "eux_Z]\\ayiqlog`s^hvnmwr[cpftbkjd"
var2 = "Zf91XhR7fa=ZVH2H=QlbvdHJx5omN2xc"

v0 = array.array('B',var0)
v1 = array.array('B',var1)
v2 = array.array('B',var2)
v3=[]
for i in range(32):
	v3.append(0)

v3 = array.array('B',v3)

for i in range(len(v1)):
	v3[i] = v2[v1[i]-90]

newstr = ""
for i in v3:
	newstr += chr(i)
	
print base64.b64decode(newstr)

