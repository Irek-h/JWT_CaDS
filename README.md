# JWT_CaDS_IO_JW

Research about JWT (JSON Web Token)

0. Preface
1. What is JWT?
2. How does JWT's authentication works?
3. Strong and weak parts
4. PoC crack

# 0. Preface

Since JWT is a very commonly used in web/application developement there is already a surplus of documentation in every format (youtube videos, online labs, books). Hence no need to create another wall of text with scrapes of information gathered from different sources in belief that this one will be better. Therefore aim of this is to gather key aspects, put them in one place and based on those, perform proof of concept in usage and cracking. The last part _cracking_ is the main input of this article, motivated by the fact that cracking aspect of JWT is very often badly showcased.

# 1. What is JSON Web Token?

JSON Web Token also knows as JWT has found its way into all major web frameworks. It has simple, compact and easy to use architecture. Token has three parts Header, Payload & Signature - diveded by '.'

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6InVzZWxvbmdhbmRjb21wbGljYXRlZHBhc3N3b3JkIiwiaWF0IjoxNTE2MjM5MDIyfQ.
pBq_Z_QwOhKJJuctpuCwMs4GbE3sBDsG4D4MQRutTuY`

```
Header
{
 "alg": "HS256",
 "typ": "JWT"
 }

Payload
{
  "sub": "1234567890",
  "name": "uselongandcomplicatedpassword",
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

A common way to use JWTs is as OAuth bearer tokens. In this example, an authorization server creates a JWT at the request of a client and signs it so that it cannot be altered by any other party. The client will then send this JWT with its request to a REST API. The REST API will verify that the JWT’s signature matches its payload and header to determine that the JWT is valid. When the REST API has verified the JWT, it can use the claims to either grant or deny the client’s request.

In simpler terms, you can think of a JWT bearer token as an identity badge to get into a secured building. The badge comes with special permissions; that is, it may grant access to only select areas of the building. The authorization server in this analogy is the reception desk — or the issuer of the badge. And to verify that the badge is valid, the company logo is printed on it, similar to the signature of the JWT. If the badge holder attempts to access a restricted area, the permissions on the badge determine whether or not they can access the area, similar to the claims in a JWT.

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

### Brute Force Secret
Brute forcing the most commonly used symmetric algorithm HMACSHA256 is fairly easy, as long as password is not long and complicated it will be broken.
![obraz](https://user-images.githubusercontent.com/82705344/147357754-2e22cabc-cc5d-412e-8c71-63a1abd3e898.png)
![obraz](https://user-images.githubusercontent.com/82705344/147357878-cc80b8aa-e6de-4123-ba48-4aba74d4db1f.png)
![obraz](https://user-images.githubusercontent.com/82705344/147357895-191df1eb-74e4-4b04-a0c9-465c1f4ce2cd.png)
![obraz](https://user-images.githubusercontent.com/82705344/147358225-08a3c9d8-9bf5-4612-a5d4-bad54e1e1700.png)


## 5. Further reading
- https://jwt.io/introduction
- https://www.akana.com/blog/what-is-jwt
- https://research.securitum.com/jwt-json-web-token-security/
- https://blog.pentesteracademy.com/hacking-jwt-tokens-bruteforcing-weak-signing-key-jwt-cracker-5d49d34c44
- https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html
- https://www.softwaresecured.com/security-issues-jwt-authentication/
- https://github.com/ticarpi/jwt_tool
- https://github.com/ticarpi/jwt_tool/wiki
