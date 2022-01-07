# JWT_CaDS_IO_JW

Research about JWT (JSON Web Token)

0. Preface
1. What is JWT?
2. How does JWT's authentication works?
3. Strong and weak parts
4. PoC crack
5. Further reading

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
 - **registered claims** -  set of preconfigured claims that are not required but are recommended in order to give a set of relevant, interoperable claims. Some examples include: iss (issuer), exp (expiration date), sub (topic), aud (audience) 
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
### The whole token

The final token consists of header JSON and payload JSON, both encoded with Base64Url, and signature which is created from encoding header and payload with secret in choosen signing algorithm.

Example JWT created in [jwt.io Debugger](https://jwt.io/#debugger-io):

![image](https://user-images.githubusercontent.com/32808627/148589206-8d66fc87-70c9-4a27-9ba0-832c68b105e2.png)


# 2. How does JWT's authentication works?

A common way to use JWTs is as OAuth bearer tokens. In this example, an authorization server creates a JWT at the request of a client and signs it so that it cannot be altered by any other party. The client will then send this JWT with its request to a REST API. The REST API will verify that the JWT’s signature matches its payload and header to determine that the JWT is valid. When the REST API has verified the JWT, it can use the claims to either grant or deny the client’s request.

In simpler terms, you can think of a JWT bearer token as an identity badge to get into a secured building. The badge comes with special permissions; that is, it may grant access to only select areas of the building. The authorization server in this analogy is the reception desk — or the issuer of the badge. And to verify that the badge is valid, the company logo is printed on it, similar to the signature of the JWT. If the badge holder attempts to access a restricted area, the permissions on the badge determine whether or not they can access the area, similar to the claims in a JWT.

![image](https://user-images.githubusercontent.com/32808627/148589420-776e7b63-4ea2-4db2-a225-7ed86cf64ccf.png)

# 3. Strong and weak parts

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

## 4. Proof of concept - craking

### Lab machine
Machine used in cracking has Kali as operating system and following parameters:
![obraz](https://user-images.githubusercontent.com/82705344/148592865-07467b65-dc55-4590-ad8e-0971d0067533.png)

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

### C-jwt-crakcer
https://github.com/brendan-rius/c-jwt-cracker
![obraz](https://user-images.githubusercontent.com/82705344/148592355-86372201-4447-4d16-ba8a-70ac0a3f2d39.png)
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
Secret having 4 digit can be broken in several secounds. Incresing lenght to 8 makes it difficult to crack by brute force for the machine presented in the lab. However, most of the attacks will be performed on much stronger machines.
### Security Concerns and Recommendation
A key of the same size as the hash output (for instance, 256 bits for "HS256") or larger MUST be used with this algorithm. NIST SP 800-117 states that the effective security strength is the minimum of the security strength of the key and two times the size of the internal hash value. As a rule of thumb, make sure to pick a shared-key as long as the length of the hash. For HS256 that would be a 256-bit key (or 32 bytes) minimum.

It is imporntant to notice that brute force attacks happen in the wild all the time, so having bad password policy will eventually lead to exploitation. That includes both low entropy passwords and leaked password vulnerable to dictionary atttacks.

## 5. Further reading
- https://jwt.io/introduction
- https://www.akana.com/blog/what-is-jwt
- https://research.securitum.com/jwt-json-web-token-security/
- https://blog.pentesteracademy.com/hacking-jwt-tokens-bruteforcing-weak-signing-key-jwt-cracker-5d49d34c44
- https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html
- https://www.softwaresecured.com/security-issues-jwt-authentication/
- https://github.com/ticarpi/jwt_tool
- https://github.com/ticarpi/jwt_tool/wiki
