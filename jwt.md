# JWT

An open standard that defines compact self-contained way for securely transmitting data between parties as a JSON.

Signed tokens can verify the integrity of the claims contained within it, while encrypted tokens hide those claims from other parties

Authorization: most common scenario for JWT. Once user logged in, each subsequent request will include the JWT, allowing the user to access routes, services, and resources that are permitted with that token.

JWT consist of three parts separated by dots (.)

- Header
- Payload
- Signature

JWT typically looks like the following.

`xxxxx.yyyyy.zzzzz`

## Header

typically consists of two parts: the type of the token, which is JWT, and the signing algorithm being used, such as HMAC SHA256 or RSA.

`{ "alg": "HS256", "typ": "JWT" }`

Then, this JSON is Base64Url encoded to form the first part of the JWT

## Payload

The second part of the token is the payload, which contains the claims. Claims are statements about an entity (typically, the user) and additional data. There are three types of claims: registered, public, and private claims.

### Registered claims

Not mandatory but recommended predefined claims, to provide a set of useful, interoperable claims. Some of them are: iss (issuer), exp (expiration time), sub (subject), aud (audience), and others.

**Notice that the claim names are only three characters long as JWT is meant to be compact.**

### Public claims

These can be defined at will by those using JWTs. But to avoid collisions they should be defined in the IANA JSON Web Token Registry or be defined as a URI that contains a collision resistant namespace.

### Private claims

These are the custom claims created to share information between parties that agree on using them and are neither registered or public claims.

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

The payload is then Base64Url encoded to form the second part of the JSON Web Token.

## Signature

To create the signature part you have to take the encoded header, the encoded payload, a secret, the algorithm specified in the header, and sign that.

For example if you want to use the HMAC SHA256 algorithm, the signature will be created in the following way:

```js
HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret);
```

**The signature is used to verify the message wasn't changed along the way**

## Putting all together

The output is three Base64-URL strings separated by dots that can be easily passed in HTML and HTTP environments

# How does JSON Web Tokens work?

1. user successfully logs in using their credentials, JWT will be returned
2. Whenever the user wants to access a protected resource, the user agent should send the JWT, typically in the `Authorization` header using the _Bearer_ schema: `Authorization: Bearer <token>`
3. Server will check for a valid JWT in the `Authorization` header, and if it's present, user will be allowed to access protected resources (If the JWT contains the necessary data, the need to query the database for certain operations may be reduced)

- you should not keep tokens longer than required
- You also should not store sensitive session data in browser storage due to lack of security.
- If the token is sent in the Authorization header, Cross-Origin Resource Sharing (CORS) won't be an issue as it doesn't use cookies.

https://www.sohamkamani.com/1c2963a562418d9fccfa9c6667da826c/jwt-algo.svg

The details of how the algorithm is implemented is out of scope for this post, but the important thing to note is that it is one way, which means that we cannot reverse the algorithm and obtain the components that went into making the signature… so our secret key remains secret.

In order to verify an incoming JWT, a signature is once again generated using the header and payload from the incoming JWT, and the secret key. If the signature matches the one on the JWT, then the JWT is considered valid.

You can easily generate the header and payload, but without knowing the key, there is no way to generate a valid signature.

- https://www.sohamkamani.com/golang/2019-01-01-jwt-authentication/#implementation-in-go
