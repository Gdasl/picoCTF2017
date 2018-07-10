This one is a doozie. Shouldn't be since it's only 90 points so it should be easy.
We have c, p, q, dp and dq. A quick verification tells us that p and q are both primes. We don't have e. Assuming that e is 65537 get us nowhere.

SO in the end a bit of bruteforcing never hurts. We know that:

d = (dp+dq - (e*dp*dq)) mod ((p-1)*(q-1)))

we also know that d mod (p-1) = dp

So basically bruteforce the exponent until we find a value that satsifies the equation system above which gives us:
e = 353535

From there we calculate the inverse modulo of e and phi = (p-1)*(p-1) which gives us d and calulate c^d mod n, encode as hex, decode as ascii and get the flag:

Theres_more_than_one_way_to_RSA
