# JWT_CaDS_IO_JW

Research about JWT (JSON Web Token)

0. Preface
1. What is JWT?
2. How does JWT's authentication works?
3. Involved encryption mechanisms
4. Strong and weak parts
5. PoC crack
6. Further reading

# 0. Preface

Since JWT is a very commonly used in web/application developement there is already a surplus of documentation in every format (youtube videos, online labs, books). Hence no need to create another wall of text with scrapes of information gathered from different sources in belief that this one will be better. Therefore aim of this is to gather key aspects, put them in one place and based on those, perform proof of concept in usage and cracking. The last part _cracking_ is the main input of this article, motivated by the fact that cracking aspect of JWT is very often badly showcased.

# 1. What is JSON Web Token?

JSON Web Token also knows as JWT is an open standard (RFC 7519) that specifies how data can be securely transmitted between pages using a JSON object. The digital signature included with the token allows the information delivered to be verified.
The JWT is signed with a signature, either using the HMAC algorithm or an RSA or ECDSA public/private key. JWT has found its way into all major web frameworks. It has simple, compact and easy to use architecture.

## When to use?

There are two most common scenarios when JSON Web Tokens are useful:

- **Authorization** - after successful user login, each request sent to API contains the JWT which is associated witch specific resources access permissions. JWT is often used in Single Sign-on features that allow to gain access to several independent, yet related, software systems after single log in
- **Data transmission** - when we want to send information between parties and we need to be confident that the sender is who they claims they are and that the data they deliver has not been altered. We can verify this since the JWT includes a digital signature.

## Architecture

Token has three parts **Header**, **Payload** & **Signature** - diveded by '.'

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6InVzZWxvbmdhbmRjb21wbGljYXRlZHBhc3N3b3JkIiwiaWF0IjoxNTE2MjM5MDIyfQ
.
pBq_Z_QwOhKJJuctpuCwMs4GbE3sBDsG4D4MQRutTuY
```

### Header

The header contains information about the type of the token - JWT and what algorithm we use - HMAC SHA256 or RSA.

Example:

```
{
	"alg": "HS256",
	"typ": "JWT"
}
```

### Payload

This part is responsible for storing the data that we want to send in the token. Usually the data is about the user. JWT distinguishes three types of information included in payload:

- **registered claims** - set of preconfigured claims that are not required but are recommended in order to give a set of relevant, interoperable claims. Some examples include: iss (issuer), exp (expiration date), sub (topic), aud (audience)
- **public claims** - they can be defined at will by those using JWTs, but in order to avoid collisions they should be defined in the IANA JSON Web Token Registry or be defined as a URI that includes a collision resistant namespace.
- **private claims** - custom claims created to share information between parties that agree on using them and are neither registered or public claims.

Example:

```
{
	"sub": "1234567890",
	"name": "uselongandcomplicatedpassword",
	"iat": 1516239022
}
```

### Signature

The signature ensures that data has not been tempered with. In the case of tokens signed with a private key, it can also verify that the sender of the JWT is who it says it is. In our case the algorithm for it is "Hash-based Message Authentication Code" as specified in Header `"alg" : "HS256"`. The Signature is created in the following way:

```
HMACSHA256(
	base64UrlEncode(header) + "." +
	base64UrlEncode(payload),
	secret)
```

JSON Web Token can be signed using a key known to both parties (secret) using the HMAC algorithm or with the use of a private / public key pair (RSA or ECDSA algorithms).

### The whole token

The final token consists of header JSON and payload JSON, both encoded with Base64Url, and signature which is created from encoding header and payload in choosen signing algorithm.

Example JWT created in [jwt.io Debugger](https://jwt.io/#debugger-io):

![image](https://user-images.githubusercontent.com/32808627/148589206-8d66fc87-70c9-4a27-9ba0-832c68b105e2.png)

# 2. How does JWT's authentication works?

A common way to use JWTs is as OAuth bearer tokens. An authorization server creates a JWT after successful user login and signs it so that it cannot be altered by any other party. The client will then send this JWT with its request to a REST API when they will want to accessed protected resources or routes. The REST API will verify that the JWT’s signature matches its payload and header to determine that the JWT is valid. When the REST API has verified the JWT, it can use the claims to either grant or deny the client’s request.

In simpler terms, you can think of a JWT bearer token as an identity badge to get into a secured building. The badge comes with special permissions; that is, it may grant access to only select areas of the building. The authorization server in this analogy is the reception desk — or the issuer of the badge. And to verify that the badge is valid, the company logo is printed on it, similar to the signature of the JWT. If the badge holder attempts to access a restricted area, the permissions on the badge determine whether or not they can access the area, similar to the claims in a JWT.

OBRAZEK
![image](https://user-images.githubusercontent.com/32808627/148589420-776e7b63-4ea2-4db2-a225-7ed86cf64ccf.png)

# 3. Involved encryption mechanisms

Encryption in JWT is used to sign the token, i.e. create the signature. It is possible to use several different encryption algorithms. Below is a description of the algorithms that can be used to sign the token.

## Symmetric encryption

Symmetric encryption algorithms rely on a single key that is shared by two or more users. The same key is used to encrypt and decrypt a so-called plaintext that represents the message or data that is being encoded. The encryption process involves literally passing plain text (input) through an encryption algorithm called a cipher, which in turn generates ciphertext (output).

If the encryption algorithm is strong enough, the only way to read the message or access the ciphertext information is to use the appropriate decryption key. The decryption process essentially converts the ciphertext back to plaintext.

The security of symmetric encryption algorithms is directly related to how difficult it is to randomly guess the corresponding key. For example, in the case of a 128-bit key, it takes billions of years to be able to guess it correctly (using modern computer hardware). Generally speaking, the longer the key is, the harder it is to break it. Keys as long as 256 bits are generally considered to be highly secure.

OBRAZEK

### HMAC

The HMAC algorithm stands for Hash-based Message Authentication Code. It is a MAC algorithm derived from cryptographic hash functions. HMAC shows high resistance to cryptanalysis attacks because it uses the concept of hashing twice.
As a hash function, HMAC is intended to be one-way, i.e. easy to generate output from the input, but complex the other way around. Its goal is to be less influenced by collisions than hash functions.
HMAC reuses algorithms such as MD5 and SHA-1 and examines to see if the embedded hash functions can be replaced with more secure hash functions. HMAC attempts to manage Keys in a more simple manner.
The algorithm definition is as follows:

OBRAZEK

where:

- H - cryptographic hash function
- K - the secret key
- K' - is a block-sized key derived from the secret key, K; either by padding to the right with 0s up to the block size, or by hashing down to less than or equal to the block size first and then padding to the right with zeros
- opad - the block-sized outer padding, consisting of repeated bytes valued 0x5c
- ipad - the block-sized inner padding, consisting of repeated bytes valued 0x36

## Asymmetric encryption

In asynchronous encryption algorithms, the public key is used by the sender to encrypt the information, while the private key is used by the receiver to decrypt that information. Since the two keys are different, the public key can be securely shared without compromising the security of accessing the private key.

Each asymmetric pair of keys is unique, so that a message encrypted with a public key can only be read by a person who has the corresponding private key.

Since the algorithms for asymmetric encryption generate pairs of keys that are mathematically related to each other, the keys generated in this way are much longer than the keys generated using symmetric cryptography. Their length - typically between 1024 and 2048 bits - makes it extremely difficult to compute a private key from its public counterpart.

OBRAZEK

### RSA

RSA (Rivest-Shamir-Adleman) is one of the most popular public key encryption algorithms today. It can be used both for message encryption and for digital signatures. The security of the algorithm relies on the practical difficulty of factoring the product of two large prime numbers.
The keys for the RSA algorithm are generated in the following way:

1.  Choose two distinct prime numbers _p_ and _q_.
2.  Compute _n_ = _pq_. The length n is the length of the RSA key.
3.  Compute _λ_(_n_), where _λ_ is Carmichael's totient function. Since _n_ = _pq_, _λ_(_n_) = lcm(_λ_(_p_), _λ_(_q_)), and since _p_ and _q_ are prime, _λ_(_p_) = _φ_(_p_) = _p_ − 1, and likewise _λ_(_q_) = _q_ − 1. Hence _λ_(_n_) = lcm(_p_ − 1, _q_ − 1).
4.  Choose an integer _e_ such that 1 < _e_ < _λ_(_n_) and gcd(_e_, _λ_(_n_)) = 1; that is, _e_ and _λ_(_n_) are coprime
5.  Determine _d_ as _d_ ≡ *e*−1 (mod _λ_(_n_)); that is, _d_ is the modular of _e_ modulo _λ_(_n_).

The public key consists of the modulus n and of the public exponent _e_, while the private key consists of the same modulus _n_ of the private exponent _d_. All numbers associated with the private key should be kept secret: both _n_ and _d_ and the following three: _p, qi φ (n)_ that can be used to compute _d_.  
_e_ having a short bit-length and small Hamming weight results in more efficient encryption – the most commonly chosen value for _e_ is 216 + 1 = 65537. _e_ is released as part of the public key.

Encryption is done with the public key _(n, e)_. The message has to be divided into parts and then each part should be changed to a number (greater than 0 and less than _n_). In practice, the message is divided into fragments, each of which consists of a certain number of bits.
Then each number in the message is made _modulo n_ to the power of _e_.
Encryption can also be performed using a private key. The entire procedure is identical to that described above, except that you will need to use the private key _(n, d)_ for encryption. However, the recipient of the message will have to use the corresponding public key in order to decrypt the message.

Decryption is done with the private key _(n, d)_.
The ciphertext consists of consecutive numbers less than _n_. Each number in the ciphertext is raised _modulo n_ to the power of _d_.
Received plaintext numbers should be combined in the correct order to create the original message.
If the message has been encrypted with a private key, the corresponding public key will need to be used to decrypt the message. The decryption procedure is identical to that described above, except that the public key _(n, e)_ is used.

### ECDSA

ECDSA, or Elliptic Curve Digital Signature Algorithm, is one of the most complicated public key cryptography encryption algorithms. Elliptic curve cryptography generates keys that are smaller than the typical keys generated by digital signature algorithms. Elliptic curve cryptography is a form of public key cryptography that uses the algebraic structure of elliptic curves over finite fields. Elliptic curve cryptography is mostly used to generate pseudo-random numbers, digital signatures, and other data. A digital signature is a type of authentication that uses a public key pair and a digital certificate as a signature to validate the identity of a receiver or sender of information.
A main feature of ECDSA versus another popular algorithm, RSA, is that ECDSA provides a higher degree of security with shorter key lengths.
A drawback of ECDSA is that it is complex to implement, whereas RSA is more easily set-up in comparison.

# 4. Strong and weak parts

JWT is a good choice in case of authorization or information exchange and API authentication. Although it cannot replace a server-side session, it is a clever and secure (when used properly) way to acquire/confirm a client's identity.

## Pros

- Token-based authorization (which includes JWT) is relatively scalable and effective. Tokens are stored on the client’s side, so the server is only responsible for creating and verifying tokens, so there are no problems with maintaining more users using the application at the same time.
- Thanks to the use of JWT, the number of database queries can be reduced, which translates into the response speed. When using paid services, this may mean slightly lower costs.
- JWT enables usage across services. There may be just one authorization server that deals with authentication (login/registration) and generates the token, all the subsequent requests don’t have to go to the authorization server as only the Auth-server have the private key, and the rest of the servers have the public-key to verify the signature.
- JWT is relatively easy to use from a developer point of view as thanks to its popularity there are many libraries and packages that speed up software development. The popularity also implies that there is a large community familiar with the mechanisms of JWT, which allows faster resolution of potential problems encountered during the development.

## Cons

- The biggest disadvantage of JWT is the fact that it relies only on one key. If this secret key is badly handled by the developer or administrator, it will probably lead to a breach of the system security and leakage of the users' data.
- JWT data encryption relies on a signing algorithm that can be exploited or become deprecated.
- There is no possibility of managing clients from the server. For example, there is no easy way to log the user out of all sessions, because they would have to delete the cookies, and removing existing user id from the database causes dangling pointers. And due to the lack of records of the logged users on the database end, there is no possibility to push messages to all clients.
- Storing a lot of data in JWT makes it linearly longer, which may cause data overhead because the token has to be sent in with each request. In the case of low-speed internet connection, it means longer loading time and bad user experience.
- Securely using JWT requires basic knowledge of cryptography. A developer that doesn’t understand how the tokens work may introduce security loopholes in the system and compromise the data security.

## Conclusion

JWT, when used properly, is a quite secure and comfortable (for a developer) way to exchange authentication information, but proper usage is the key.
The most secure way to use JWT:

- used for authorization, not for session management
- expected to be used once (authenticate and get the session ID)
- short-lived (minutes)

This way is often not the most practical, so the misusage may lead to compromising the system's security.

## 5. Proof of concept - craking

### Brute Force Secret

Brute forcing the most commonly used symmetric algorithm HMACSHA256 is fairly easy, as long as password is not long and complicated it will be broken.
HS256
![obraz](https://user-images.githubusercontent.com/82705344/147357754-2e22cabc-cc5d-412e-8c71-63a1abd3e898.png)
![obraz](https://user-images.githubusercontent.com/82705344/147357878-cc80b8aa-e6de-4123-ba48-4aba74d4db1f.png)
![obraz](https://user-images.githubusercontent.com/82705344/147357895-191df1eb-74e4-4b04-a0c9-465c1f4ce2cd.png)
![obraz](https://user-images.githubusercontent.com/82705344/147358225-08a3c9d8-9bf5-4612-a5d4-bad54e1e1700.png)
HS384
![obraz](https://user-images.githubusercontent.com/82705344/148546166-0f5bcdbe-8fdd-473b-86f4-eca232f1d286.png)
![obraz](https://user-images.githubusercontent.com/82705344/148546445-08fba006-61d2-4367-a504-30971149a19a.png)
HS512
![obraz](https://user-images.githubusercontent.com/82705344/148546528-7087fc71-9109-48ec-a305-e1d3d2f157df.png)
![obraz](https://user-images.githubusercontent.com/82705344/148546708-02be599e-fc65-4876-bf91-acc36e7f700f.png)
HS256 - 8 digit secret
![obraz](https://user-images.githubusercontent.com/82705344/148547462-4fa5331b-4199-42a3-b1d7-4a45c0b53a15.png)
![obraz](https://user-images.githubusercontent.com/82705344/148554105-d579471b-f07a-4adf-870a-8bd9675fbab6.png)

### Conclusion

Secret having 4 digit can be broken in several secounds. Incresing lenght to 8 makes it difficult to crack by brute force for the typical user. However, most of the attacks will be performed on stronger machines.
###Security Concerns and Recommendation
A key of the same size as the hash output (for instance, 256 bits for "HS256") or larger MUST be used with this algorithm. NIST SP 800-117 states that the effective security strength is the minimum of the security strength of the key and two times the size of the internal hash value. As a rule of thumb, make sure to pick a shared-key as long as the length of the hash. For HS256 that would be a 256-bit key (or 32 bytes) minimum.

### C-jwt-crakcer

https://github.com/brendan-rius/c-jwt-cracker
![obraz](https://user-images.githubusercontent.com/82705344/148577361-63be1d26-84bf-48f5-9516-3e6f88e45bdc.png)

HS384
![obraz](https://user-images.githubusercontent.com/82705344/148579403-eb597b16-080d-4a6e-9ce2-994dc37e5c0a.png)
![obraz](https://user-images.githubusercontent.com/82705344/148579439-90e50505-7ea1-445f-a894-fabf13996be8.png)

HS512
![obraz](https://user-images.githubusercontent.com/82705344/148579526-f76550bf-1aaa-43e1-9155-c12d8b11d057.png)
![obraz](https://user-images.githubusercontent.com/82705344/148580260-e367d439-5d5e-4f3e-a80a-afc8687ca3d7.png)

longer HS256
![obraz](https://user-images.githubusercontent.com/82705344/148580375-49a782e7-c546-4663-8024-d984235f5d9d.png)
![obraz](https://user-images.githubusercontent.com/82705344/148588679-5ef941d6-99ff-48ea-a8a2-455fc62897df.png)

### Conclusion

## 6. Further reading

- https://jwt.io/introduction
- https://www.akana.com/blog/what-is-jwt
- https://research.securitum.com/jwt-json-web-token-security/
- https://blog.pentesteracademy.com/hacking-jwt-tokens-bruteforcing-weak-signing-key-jwt-cracker-5d49d34c44
- https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html
- https://www.softwaresecured.com/security-issues-jwt-authentication/
- https://github.com/ticarpi/jwt_tool
- https://github.com/ticarpi/jwt_tool/wiki
