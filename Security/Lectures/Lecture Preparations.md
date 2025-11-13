The documents for this course was
[[Chapter 4.3.1.pdf]] Information Security 2/E Mark Stamp
[[Chapter 4.4.pdf]] Information Security 2/E Mark Stamp
[[Chapter 4.8.pdf]] Information Security 2/E Mark Stamp
[[Chapter 9.3.pdf]] Information Security 2/E Mark Stamp
[[Chapter 10.3.pdf]] Information Security 2/E Mark Stamp
[[SQL-injection and defenses.pdf]]
[[Password-based user authentication.pdf]]


### Chapter 4.3.1 
The Mechanics of RSA Decryption 
The RSA cryptosystem relies on a clever mathematical relationship to ensure that a message encrypted with a public key can only be decrypted by the corresponding private key. This process validated through application of Euler's Tehorem. 

A simplified RSA example
1. Key Generation:
	1. Select two small primes $p = 11$ and $q = 3$ 
	2. Calculate the modulus $N = pq = 11 · 3 = 33$ 
	3. Calucate Euler's totient $ϕ(N)=(11−1)(3−1)=10⋅2=20ϕ(N)=(11−1)(3−1)=10⋅2=20$
	4. Choose a public exponent $e$ that is relatively prime to $ϕ(N)=20$ let's choose $e=3$
	5. Compute the private exponent $d$ such that $ed≡1(mod20)$. In this case, $d=7$ because $3 · 7=21$ and $21≡1(mod20)$
	6. The resulting public key is $N,e)=(33,3)$ and private key is $d=7$
2. Encryption
	1. Supposed the message is sent is the number $M=15$ 
	2. The sender uses the public key to compute the ciphertext $C$ $$C=Me(modN)=153(mod33)=3375(mod33)$$
	3. Since $3375=102⋅33+9$ the ciphertext is $C=9$ 
3. Decryption
	1. The recipient uses their private key $d=7$ to decrypt $C=9$: $$M=Cd(modN)=97(mod33)=4,782,969(mod33)$$
	2. Since $4,782,969=144,938·33+15$, the decryption message is $M=15$, which matches the original plaintext. 

#### Concepts
##### Ciphertext 
An encrypted message is the result of the converting readable content into an unreadable form using an algorithm, often determined by the formula $C=MemodN$. Anyone intercepting this encoded data cannot discern its meaning without the proper decryption key.

##### Private key 
The confidential decryption exponent $d$, computed so that $ed=1modϕ(N)$ reverses the encryption process by transforming ciphertext back into the original message. Only trusted users possess this secret parameter, ensuring that messages remain secure despite public access to the pair.

##### 


