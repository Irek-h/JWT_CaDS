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
# 1. What is JSON Web Token?
  JSON Web Token also knows as JWT has found its way into all major web frameworks. It has simple, compact and easy to use architecture. Token has three parts *MAKE THIS RED*-> HEADER, PAYLOAD SIGNATURE
`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.*MAKE THIS RED*
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IklyZWsiLCJpYXQiOjE1MTYyMzkwMjJ9.
MjHSMG585yN1uz8uIYiZGOAV2uBMFnHnnJdFUgqDyvs`

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

The last part `MjHSMG585yN1uz8uIYiZGOAV2uBMFnHnnJdFUgqDyvs` it is Payload Signature. Ensures that data has not been tempered with.
















