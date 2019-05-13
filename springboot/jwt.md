Json Web Token
==============

> Json Web Token (JWT) 是一个开放标准，它定义了一种紧凑、独立的方式，从而在各方之间安全地传递信息。

**Json Web令牌使用场景**

* 授权：用户登录后，后续的每个请求都将带上token，从而访问到该token所允许访问的路由、资源及服务。
* 信息交换

**Json Web令牌结构**
Json Web令牌由`.`分隔的三个部分组成。
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhdXRoMCIsImV4cCI6MTU1Nzc3MjU4NiwidXNlciI6MX0.Vpcwvs13YaRJpXXT4lU1tFlSRsQ-YdCdmGaK6tMoYZM
```
* Header 头
* Payload 负载
* Signature 签名

**Header**
在Header中，包含经过Base64编码的两个部分，一个是签名所使用的算法（默认HS256），一个是token的类型（JWT）。
```javascript
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**Payload**
Payload中包含着实体和其它一些数据的声明，这些数据是不加密的，所以一般放置一些可见的数据。
```javascript
{
  "iss": "auth0",      // 发行人
  "iat": 1516239022,   // 签发时间
  "exp": 1557772586,   // 过期时间
  "sub": "subject",    // 使用Token的主体
  "aud": "audience"    // 观众
}
```

**pom.xml**
```xml
<!-- https://mvnrepository.com/artifact/com.auth0/java-jwt -->
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.8.0</version>
</dependency>
```

**JWT工具类**

```java
import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.exceptions.JWTVerificationException;
import com.notalent.bookstore.pojo.user.UserInfo;

import java.util.Date;

/**
 * Json Web Token Utils
 * JWT Utils
 * @author noTalent
 * @version 1.0
 * 2019.05.10
 */
public class JwtUtils {

    // token 过期时间
    private static final long EXPIRES = 60 * 60 * 1000;

    // token 发行人
    private static final String ISSUER = "auth0";

    // token 用户id
    private static final String USER_ID = "user";

    // 获取令牌
    public static String getToken(UserInfo ui) {
        return JWT.create()
                .withIssuer(ISSUER)
                .withClaim(USER_ID, ui.getUserInfoId())
                .withExpiresAt(new Date(System.currentTimeMillis() + EXPIRES))
                .sign(Algorithm.HMAC256(ui.getPassword()));
    }

    // 验证令牌
    public static void verifyToken(UserInfo ui, String token) throws Exception {
        JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256(ui.getPassword()))
                .withClaim(USER_ID, ui.getUserInfoId())
                .build();
        try {
            // 验证不通过则抛出异常
            jwtVerifier.verify(token);
        } catch (JWTVerificationException exception) {
            throw new Exception("token已过期");
        }
    }

}
```
