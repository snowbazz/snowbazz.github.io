
<!--more-->



{{% admonition tip %}}
学习JWT时的一篇笔记，内容没整理，比较杂乱。
{{% /admonition %}}

<br>

## 基于Session的服务认证

基于Session的服务认证的流程

​	1、用户向服务器发送用户名和密码。

​	2、服务器验证通过后，在当前对话（session）里面保存相关数据，比如用户角色、登录时间等等。

​	3、服务器向用户返回一个 session_id，写入用户的 Cookie。

​	4、用户随后的每一次请求，都会通过 Cookie，将 session_id 传回服务器。

​	5、服务器收到 session_id，找到前期保存的数据，由此得知用户的身份。

基于Session的服务认证的弊端

​	拓展性（scaling）差，如果服务器集群，或者跨域的服务导向架构，要求session数据共享，每台服务器都能够读取session

解决方法：

​	session数据持久化，各种服务收到请求后，都向持久层请求数据，工程量大，如果持久层挂了，单点失败

​	CAS实现SSO

​	

<br>

## JWT's Data Structure

In its compact form, JSON Web Tokens consist of three parts separated by dots (`.`), 

which are:

- Header

  > The header *typically* consists of two parts: the type of the token, which is JWT, and the signing algorithm being used, such as HMAC SHA256 or RSA.

  - ```java
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```

- Payload

  - ```java
    {
    "sub": "1234567890",
    "name": "John Doe",
    "admin": true
    }
    ```

> The second part of the token is the payload, which contains the claims. Claims are statements about an entity (typically, the user) and additional data. There are three types of claims: *registered*, *public*, and *private* claims.

> Do note that for signed tokens this information, though protected against tampering, is readable by anyone. Do not put secret information in the payload or header elements of a JWT unless it is encrypted.
>
> 注意：尽管具备Header和Payload元素的signed tokens 能够防止篡改，但任何人都可以读取。除非加密，否则不要把信息放在patload和header元素中

  

- Signature

  > To create the signature part you have to take the encoded header, the encoded payload, a secret, the algorithm specified in the header, and sign that.
  >
  > 要创建Signature，必须采用上述已经编码的header，payload，一个secret( 秘钥 )和header中指定的加密算法，并对上述进行签名

  - ```java
    HMACSHA256(
    base64UrlEncode(header) + "." +
    base64UrlEncode(payload),
    secret)
    ```

一个完整的JWT结构如下：

![Encoded JWT](https://cdn.auth0.com/content/jwt/encoded-jwt3.png)



<br>

## How do JSON Web Tokens work?

In authentication, when the user successfully logs in using their credentials, a JSON Web Token will be returned. Since tokens are credentials, great care must be taken to prevent security issues. In general, you should not keep tokens longer than required.

Whenever the user wants to access a protected route or resource, the user agent should send the JWT, typically in the **Authorization** header using the **Bearer** schema. The content of the header should look like the following:

```
Authorization: Bearer <token>
```

This can be, in certain cases, a <font color='red'>stateless authorization mechanism</font>. The server's protected routes will check for a valid JWT in the `Authorization` header, and if it's present, the user will be allowed to access protected resources. If the JWT contains the necessary data, the need to query the database for certain operations may be reduced, though this may not always be the case.(服务器就不保存任何 session 数据，服务器变成无状态了，从而比较容易实现扩展。)

<font color='red'>If the token is sent in the `Authorization` header, Cross-Origin Resource Sharing (CORS) won't be an issue as it doesn't use cookies.</font>(as 翻译成由于)

The following diagram shows how a JWT is obtained and used to access APIs or resources:

![How does a JSON Web Token work](https://cdn2.auth0.com/docs/media/articles/api-auth/client-credentials-grant.png)

1. The application or client requests authorization to the authorization server. This is performed through one of the different authorization flows. For example, a typical [OpenID Connect](http://openid.net/connect/) compliant web application will go through the `/oauth/authorize` endpoint using the [authorization code flow](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth).
2. When the authorization is granted, the authorization server returns an access token to the application.
3. The application uses the access token to access a protected resource (like an API).

Do note that with signed tokens, all the information contained within the token is exposed to users or other parties, even though they are unable to change it. This means you should not put secret information within the token.

<br>

## JWT 的使用方式

客户端收到服务器返回的 JWT，可以储存在 Cookie 里面，也可以储存在 localStorage。

此后，客户端每次与服务器通信，都要带上这个 JWT。你可以把它放在 Cookie 里面自动发送，但是这样不能跨域，所以更好的做法是放在 HTTP 请求的头信息`Authorization`字段里面。

> ```javascript
> Authorization: Bearer <token>
> ```

另一种做法是，跨域的时候，JWT 就放在 POST 请求的数据体里面。总结为以下两点：

（1）放在HTTP请求头信息Authorization字段里

（2）放在POST请求的数据体里

<br>

## JWT 的几个特点

（1）JWT 默认是不加密，但也是可以加密的。生成原始 Token 以后，可以用密钥再加密一次。

（2）JWT 不加密的情况下，不能将秘密数据写入 JWT。

（3）JWT 不仅可以用于认证，也可以用于交换信息。有效使用 JWT，可以降低服务器查询数据库的次数。

> ## When should you use JSON Web Tokens?
>
> Here are some scenarios where JSON Web Tokens are useful:
>
> - **Authorization**: This is the most common scenario for using JWT. Once the user is logged in, each subsequent request will include the JWT, allowing the user to access routes, services, and resources that are permitted with that token. Single Sign On is a feature that widely uses JWT nowadays, because of its small overhead and its ability to be easily used across different domains.
> - **Information Exchange**: JSON Web Tokens are a good way of securely transmitting information between parties. Because JWTs can be signed—for example, using public/private key pairs—you can be sure the senders are who they say they are. Additionally, as the signature is calculated using the header and the payload, you can also verify that the content hasn't been tampered with.

（4）JWT 的最大缺点是，由于服务器不保存 session 状态，因此无法在使用过程中废止某个 token，或者更改 token 的权限。也就是说，一旦 JWT 签发了，在到期之前就会始终有效，除非服务器部署额外的逻辑。

（5）JWT 本身包含了认证信息，一旦泄露，任何人都可以获得该令牌的所有权限。为了减少盗用，JWT 的有效期应该设置得比较短。对于一些比较重要的权限，使用时应该再次对用户进行认证。

（6）为了减少盗用，JWT 不应该使用 HTTP 协议明码传输，要使用 HTTPS 协议传输。



<br>

## Demo

一个简单的生成和解析JWT Demo: <https://github.com/snowbazz/blog-code/tree/master/Jwt>