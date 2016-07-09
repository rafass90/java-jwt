# Java JWT

An implementation of [JSON Web Tokens](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) developed against `draft-ietf-oauth-json-web-token-08`.

### Usage

Note for Auth0 users: By default, Auth0's CLIENT_SECRET is base64-encoded.

To work with JWTSigner and JWTVerifier, special care should be taken to decode the Auth0 Client Secret first.

Working example of bothing signing and verifying (here using HS256):

```java
import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.security.SignatureException;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

import org.apache.commons.codec.binary.Base64;

public class Application {

    public static void main(String[] args) throws Exception {

        final String issuer = "https://arcseldon.auth0.com/";
        final String clientId = "{{your auth0 client id}}"; // this is the audience
        final String clientSecret = "{{your auth0 client secret}}";
        final String name = "arcseldon";
        final String email = "arcseldon+test@gmail.com";
        final String subject = "auth0|576be978a93121cc48c7487d";

         final long iat = System.currentTimeMillis() / 1000l;
         final long exp = iat + 60L;

        // Base64 Decode Auth0 Client Secret before signing !!!
        final JWTSigner signer = new JWTSigner(new Base64(true).decodeBase64(clientSecret));
        final HashMap<String, Object> claims = new HashMap<String, Object>();
        claims.put("name", name);
        claims.put("email", email);
        claims.put("email_verified", "true");
        claims.put("iss", issuer);
        claims.put("roles", new String[] {"ROLE_ADMIN"});
        claims.put("sub", subject);
        claims.put("aud", clientId);
        claims.put("exp", exp);
        claims.put("iat", iat);

        final String token = signer.sign(claims);
        System.out.println("token = " + token);

        // Base64 Decode Auth0 Client Secret before verifying !!!
        final JWTVerifier jwtVerifier = new JWTVerifier(new Base64(true).decodeBase64(clientSecret), clientId, issuer);
        final Map<String,Object> decodedPayload = jwtVerifier.verify(token);
        System.out.println(decodedPayload.get("name"));

    }

}
```

#### Maven coordinates?

Yes, here you are:

```xml
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>2.2.1</version>
</dependency>
```


### Why another JSON Web Token implementation for Java?

We believe existing JWT implementations in Java are either too complex or not tested enough.
This library aims to be simple and achieve the right level of abstraction.

## Issue Reporting

If you have found a bug or if you have a feature request, please report them at this repository issues section. Please do not report security vulnerabilities on the public GitHub issue tracker. The [Responsible Disclosure Program](https://auth0.com/whitehat) details the procedure for disclosing security issues.

## Author

[Auth0](auth0.com)

## License

This project is licensed under the MIT license. See the [LICENSE](LICENSE.txt) file for more info.
