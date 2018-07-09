# Weirder RSA (Master challenge 4)

Another message encrypted with RSA. It looks like some parameters are missing. Can you still decrypt it? 

```
e = 65537
n = 322814151822712090417072986222040863976116810564688225857612669613873525544411916192190873170659640245132624332148528862557298858047718123680584189321603561572531085350943820096602427469072548352484298259311246970844280748826511224806763370175398713221869278200049987373235557037109327912470754581096543208171
dp = 1645290358212409422232746895795831441626283889531554181610891065406049074561186629194625112109008921901603982854724720356054695566958493400403652147655273
c = 221694945369260878729367790446190608376816159787337255497612821696086933773832626353950534688450477800836115181814125410615514257361695600423563942990338966224347660337810820828785086890647125610160687197625953592532396882816465380392068955862495831688432918749571416093244018492296109515310016706336588414261

```

The hint mentions Fermat's little theorem which basically says that:

If p is a prime number, then for any integer a, the number ap − a is an integer multiple of p (Wikipedia)

re⋅dP=rmodp

So if we take an arbitrary number r and we compute r^e*dp - r mod n, this number should be a multiple of p and as such have p as a common factor with n (since n=p*q).

Now from a helpful KhanAcademy Site [link], we know that:
(A + B) mod C = (A mod C + B mod C) mod C

so we have ((redp mod n) + (r mod n)) mod n

From here it's really simple since we have n,p,q,e, we can decrypt the message in several ways ultimately yielding the flag.

**flag{wow_leaking_dp_breaks_rsa?_59151007912}**