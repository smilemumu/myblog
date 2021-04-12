---
title: "Oauth2.0"
date: 2020-12-25
tags: [安全]
categories: [Web]
draft: true
---

# Oauth2.0
![OAuth2授权码方式流程图](img/Oauth2.0.Banner.png)
## 什么是Oauth2.0?
OAuth 2.0 是目前最流行的授权机制，用来授权第三方应用，获取用户数据。
简单说：OAuth 就是一种授权机制。数据的所有者告诉系统，同意授权第三方应用进入系统，获取这些数据。系统从而产生一个短期的进入令牌（token），用来代替密码，供第三方应用使用。

## 令牌与密码
令牌（token）与密码（password）的作用是一样的，都可以进入系统，但是有三点差异。
- （1）令牌是短期的，到期会自动失效，用户自己无法修改。密码一般长期有效，用户不修改，就不会发生变化。

- （2）令牌可以被数据所有者撤销，会立即失效。以上例而言，屋主可以随时取消快递员的令牌。密码一般不允许被他人撤销。

- （3）令牌有权限范围（scope），比如只能进小区的二号门。对于网络服务来说，只读令牌就比读写令牌更安全。密码一般是完整权限。

上面这些设计，保证了令牌既可以让第三方应用获得权限，同时又随时可控，不会危及系统安全。这就是 OAuth 2.0 的优点。

## OAuth 2.0 对于如何颁发令牌？四种授权类型（authorization grant）

### 授权码（authorization code）方式

授权码方式指的是：第三方应用先申请一个授权码，然后再用该码获取令牌。
这种方式是最常用的流程，安全性也最高，它适用于那些有后端的 Web 应用。授权码通过前端传送，令牌则是储存在后端，而且所有与资源服务器的通信都在后端完成。这样的前后端分离，可以避免令牌泄漏。
![OAuth2授权码方式流程图](img/Oauth2.0.png)
- 第一步:A网站生成授权链接，用户点击后跳转到B网站，授权用户数据给A网站使用。
```
https://b.com/oauth/authorize?
  response_type=code&
  client_id=CLIENT_ID&
  redirect_uri=CALLBACK_URL&
  scope=read
```
- 主要参数：
	- response_type参数表示要求返回授权码（code）
	- client_id参数让 B 知道是谁在请求
	- redirect_uri参数是 B 接受或拒绝请求后的跳转网址
	- scope参数表示要求的授权范围（这里是只读）

- 第二步：跳转到B网站后，需要用户确认授权，用户确认后跳转到第一步的redirect_url，并返回一个授权码。
```
https://a.com/callback?code=AUTHORIZATION_CODE
```

- 第三步：A网站拿到授权码后，后台请求B网站，获取令牌。
```
https://b.com/oauth/token?
client_id=CLIENT_ID&
client_secret=CLIENT_SECRET&
grant_type=authorization_code&
code=AUTHORIZATION_CODE&
redirect_uri=CALLBACK_URL
```
- 主要参数
	- client_id参数和client_secret参数用来让 B 确认 A 的身份（client_secret参数是保密的，因此只能在后端发请求）。
	- grant_type参数的值是AUTHORIZATION_CODE，表示采用的授权方式是授权码。
	- code参数是上一步拿到的授权码。
	- redirect_uri参数是令牌颁发后的回调网址。

- 第四步：B 网站收到请求以后，就会颁发令牌。具体为向第三 步的redirect_url发送一段令牌json。
```
{    
  "access_token":"ACCESS_TOKEN",
  "token_type":"bearer",
  "expires_in":2592000,
  "refresh_token":"REFRESH_TOKEN",
  "scope":"read",
  "uid":100101,
  "info":{...}
}
```
access_token就是令牌，refresh_token为刷新令牌。

### 隐藏式（implicit）
有些 Web 应用是纯前端应用，没有后端。这种方式没有授权码这个中间步骤，所以称为（授权码）"隐藏式"（implicit）。
- 第一步:A 网站提供一个链接，要求用户跳转到 B 网站，授权用户数据给 A 网站使用。
```
https://b.com/oauth/authorize?
  response_type=token&
  client_id=CLIENT_ID&
  redirect_uri=CALLBACK_URL&
  scope=read
```
- 重要参数：
	- response_type参数为token，表示要求直接返回令牌。
	
- 第二步:用户跳转到 B 网站确认授权。这时，B 网站就会回调redirect_uri，并且把令牌作为 URL 参数，传给 A 网站。
```
https://a.com/callback#token=ACCESS_TOKEN
```
- 这种方式把令牌直接传给前端，是很不安全的。因此，只能用于一些安全要求不高的场景，并且令牌的有效期必须非常短，通常就是会话期间（session）有效，浏览器关掉，令牌就失效了。

### 密码式（password）
- 第一步:A 网站要求用户提供 B 网站的用户名和密码。拿到以后，A 就直接向 B 请求令牌。
```
https://oauth.b.com/token?
  grant_type=password&
  username=USERNAME&
  password=PASSWORD&
  client_id=CLIENT_ID
```

### 凭证式（client credentials）
- 第一步:A 应用在命令行向 B 发出请求。
```
https://oauth.b.com/token?
  grant_type=client_credentials&
  client_id=CLIENT_ID&
  client_secret=CLIENT_SECRET
```
- 主要参数：
	- grant_type参数等于client_credentials表示采用凭证式
	- client_id和client_secret用来让 B 确认 A 的身份。
- 第二步，B 网站验证通过以后，直接返回令牌。
这种方式给出的令牌，是针对第三方应用的，而不是针对用户的，即有可能多个用户共享同一个令牌。

## 令牌使用
每个发到 API 的请求，都必须带有令牌。
具体做法是在请求的头信息，加上一个Authorization字段

## 令牌更新
Oauth2.0允许用户自动更新令牌。
- B 网站颁发令牌的时候，一次性颁发两个令牌，一个用于获取数据，另一个用于获取新的令牌（refresh token 字段）。令牌到期前，用户使用 refresh token 发一个请求，去更新令牌。
```
https://b.com/oauth/token?
  grant_type=refresh_token&
  client_id=CLIENT_ID&
  client_secret=CLIENT_SECRET&
  refresh_token=REFRESH_TOKEN
```
- 主要参数：
	- rant_type参数为refresh_token表示要求更新令牌。
	- client_id参数和client_secret参数用于确认身份。
	- refresh_token参数就是用于更新令牌的令牌。

参考：<http://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html>