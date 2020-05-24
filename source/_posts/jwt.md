---
title: 前后端分离中的身份验证解决方案——jwt(JSON Web Token)
date: 2020-05-21 13:00:12
categories:
- [前端]
- [后端]
tags:
- 前端
- 后端
- jwt
- 身份验证
- 前后端分离
---

# 前后端分离中的身份验证

在过去的或较为简单的web项目中，通常不采用前后端分离的架构，这样的编写简单，利于seo，但是不利于维护，也不适用于团队的开发模式，由此越来越多的团队开始采用前后端分离的模式进行开发。

在前后端不分离时，关于身份验证或数据缓存大多使用session进行。

# Session和Cookie

cookie是一段不超过4KB的小型文本数据，由一个名称（Name）、一个值（Value）和其它几个用于控制Cookie有效期、安全性、使用范围的可选属性组成。存储在客户端（浏览器），如果设置了过期时间，则过期后会销毁，如果没设置过期时间，则关闭浏览器后会销毁。

session和cookie的最大区别就是，session是存储在服务端的数据，同时，会向客户端下发一份sessionid，用于辨识用户身份

![sessionid](http://imagezxr.test.upcdn.net/blogimg/2020-05/jwt1.png)

通过sessionid，获取存储在session中的用户信息，进行业务逻辑。

这是在前后端不分离的情况下的一种身份验证解决方案。

# token——前后端分离中的身份信息传递

在前后端分离的项目中，通常使用基于token的令牌方式进行身份验证。

使用token有以下好处：

1. 跨域 / CORS: cookies + CORS 并不能跨不同的域名.而基于令牌能够使用 AJAX 调用服务器，在任何域名下你都可以使用HTTP header头部来传输用户信息。
2.  无态(代表[服务器端](https://www.baidu.com/s?wd=服务器端&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)可伸缩): 没有必要将会话保存，令牌 token 自己是一个自我包容的实体，包含用户各种信息，其他状态信息可以保存在cookie或客户端本地存储器中。可以有效的降低服务器的压力。
3. token不只能应用于web，也可以应用于不同的其他平台（包括但不限于windows，ios，Android），在这些平台中不包含cookie或cookie不是一个优秀的解决方案。



# JWT——Json Web Token

jwt是一款基于json的token类型，一个基本的jwt应该是以下格式：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

这段jwt是由以下这段json加密：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```

jwt按 . 进行分割，可以看出分为三部分。

#### header部分

第一部分是jwt的头部（HEADER），用于描述jwt的加密方式和token类型。

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

将以上json转化为字符串，在进行base64Url加密。

#### payload部分

第二部分是jwt的内容（有效载荷），用于存放jwt的内容。

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```

同样，将以上json转化为字符串，在进行base64Url加密。JWT指定七个默认字段供选择。

iss：发行人

exp：到期时间

sub：主题

aud：用户

nbf：在此之前不可用

iat：发布时间

jti：JWT ID用于标识该JWT

除此之外，jwt同样允许自定义字段。

#### 签名部分

第三部分是jwt的签名部分。在jwt的前两部分，信息都是可以被解密的（base64并不是一个不可逆加密），所以要求我们在第二部分存储个人信息时，不要存储过于私密的信息（例如密码）。

这一部分将前两部分的加密内容拼接起来，并进行hs256加密（在header部分定义的加密方式）。但是，要保证我们的jwt不会被别有用心的人任意修改，我们必须要在jwt中加盐。截止现在，我们能做的，客户端都可以解析并修改。加盐，就是在秘文中混入一段随机字符（储存在服务端，并且不能被人知道）。之后，第三步，对hs256加密后的密文进行base64url加密。

此时，用户即使修改了密文，在服务端校验时，将获取的token的一二部分结合服务端存储的“盐”进行加密，与第三部分进行对比，如果对比结果不一致，说明用户非法修改过token的值，如果校验通过，则依照业务逻辑继续操作。即使用户知道我们使用了jwt并了解其加密方式，但缺少储存在服务端的secret字符串，就无法通过服务端的验证。

通常情况下，token存在于cookie，header或携带的token参数中。

通过jwt的[官网](https://jwt.io/)，我们可以快速解析一个jwt字符串，或者获取基于各种语言的jwt插件。



