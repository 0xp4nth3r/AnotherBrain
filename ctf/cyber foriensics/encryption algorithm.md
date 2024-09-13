TLS_DHE_RSA_WITH_AES_128_CBC_SHA
  - Transport layer security
  - Rivest,Shamir,Adleman
  - advanced encryption standard
  - chiper bloack chain
  - secure hash algorithm



### Diffie-Hellman Algorithm

~~~
Step 1: Alice and Bob get public numbers P = 23, G = 9  
  
Step 2: Alice selected a private key a = 4 and  
        Bob selected a private key b = 3  
  
Step 3: Alice and Bob compute public values  
Alice:    x =(9^4 mod 23) = (6561 mod 23) = 6  
Bob:    y = (9^3 mod 23) = (729 mod 23)  = 16  
  
Step 4: Alice and Bob exchange public numbers  
  
Step 5: Alice receives public key y =16 and  
        Bob receives public key x = 6  
  
Step 6: Alice and Bob compute symmetric keys  
        Alice:  ka = y^a mod p = 65536 mod 23 = 9  
        Bob:    kb = x^b mod p = 216 mod 23 = 9  
  
Step 7: 9 is the shared secret.
~~~







