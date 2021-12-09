# JWT_CaDS_IO_JW
Research about JWT (JSON Web Token)
1. What is JWT?
2. Where it is used? How to find it in browser console, and where it is stored on PC/client endpoint?
3. Why is it used?
4. Strong and weak parts (Pros and coins, from developer point of view - this is important since easy and performance optimal to use will lead to being more frequent used by developers, and as the popularity rises so does missconfiguration)
5. PoC usage
6. PoC crack
7. Summary
Links/topics to go through:
* https://jwt.io/introduction
* https://research.securitum.com/jwt-json-web-token-security/
* https://blog.pentesteracademy.com/hacking-jwt-tokens-bruteforcing-weak-signing-key-jwt-cracker-5d49d34c44
* https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html
* https://www.softwaresecured.com/security-issues-jwt-authentication/
* https://www.softwaresecured.com/security-issues-jwt-authentication/
* symmetric vs assymetric

---


# 0. Preface

Since JWT is a very commonly used in web/application developement there is already a surplus of documentation in every format (youtube videos, online labs, books). Hence no need to create another wall of text with scrapes of information gathered from different sources in belief that this one will be better. Therefore aim of this is to gather key aspects, put them in one place and based on those, perform proof of concept in usage and cracking. The last part _cracking_ is the main input of this article, motivated by the fact that cracking aspect of JWT is very often badly showcased.

# 1. What is JSON Web Token?
  JSON Web Token also knows as JWT has found its way into all major web frameworks. It has simple, compact and easy to use architecture. Token has three parts *MAKE THIS RED*-> HEADER, PAYLOAD SIGNATURE
`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.*MAKE THIS RED*
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IklyZWsiLCJpYXQiOjE1MTYyMzkwMjJ9.
MjHSMG585yN1uz8uIYiZGOAV2uBMFnHnnJdFUgqDyvs`
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

# 2. Where is JSON Web Token used?


















