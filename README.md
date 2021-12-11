# JWT_CaDS_IO_JW

Research about JWT (JSON Web Token)

1. What is JWT?
2. How does JWT's authentication works?
3. Strong and weak parts (Pros and cons, from developer point of view - this is important since easy and performance optimal to use will lead to being more frequent used by developers, and as the popularity rises so does missconfiguration)
4. Known attacks
5. PoC usage
6. PoC crack
7. Summary
   Links/topics to go through:

- https://jwt.io/introduction
- https://research.securitum.com/jwt-json-web-token-security/
- https://blog.pentesteracademy.com/hacking-jwt-tokens-bruteforcing-weak-signing-key-jwt-cracker-5d49d34c44
- https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html
- https://www.softwaresecured.com/security-issues-jwt-authentication/
- https://www.softwaresecured.com/security-issues-jwt-authentication/
- symmetric vs assymetric

---

# 0. Preface

Since JWT is a very commonly used in web/application developement there is already a surplus of documentation in every format (youtube videos, online labs, books). Hence no need to create another wall of text with scrapes of information gathered from different sources in belief that this one will be better. Therefore aim of this is to gather key aspects, put them in one place and based on those, perform proof of concept in usage and cracking. The last part _cracking_ is the main input of this article, motivated by the fact that cracking aspect of JWT is very often badly showcased.

# 1. What is JSON Web Token?

JSON Web Token also knows as JWT has found its way into all major web frameworks. It has simple, compact and easy to use architecture. Token has three parts _MAKE THIS RED_-> HEADER, PAYLOAD SIGNATURE
`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.*MAKE THIS RED* eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IklyZWsiLCJpYXQiOjE1MTYyMzkwMjJ9. MjHSMG585yN1uz8uIYiZGOAV2uBMFnHnnJdFUgqDyvs`

```
Header
{
 "alg": "HS256",
 "typ": "JWT"
 }

Payload
{
  "sub": "1234567890",
  "name": "Irek",
  "iat": 1516239022
}
```

The last part `MjHSMG585yN1uz8uIYiZGOAV2uBMFnHnnJdFUgqDyvs` it is Payload Signature. Ensures that data has not been tempered with. In our case the algorithm for it is "Hash-based Message Authentication Code" as specified in Header `"alg" : "HS256"`. The Payload Signature is created in the following way:

```
HMACSHA256(
base64UrlEncode(header) + "." +
base64UrlEncode(payload),
secret)
```

# 2. How does JWT's authentication works?

The Payload Signature `MjHSMG585yN1uz8uIYiZGOAV2uBMFnHnnJdFUgqDyvs` is a hash (cannot be reverse engineered back to original plaintext), so if anyone would change any bit of data in Header or Payload the hash would be different and that would propmt error.

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
