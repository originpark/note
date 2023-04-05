## 浏览器本地存储

```javascript
localStorage.setItem('key', 'value')// 设置
localStorage.getItem('key')// 读取
localStorage.removeItem('key')// 移除
localStorage.clear()// 清空
```



## JWT

jwt的组成

- `Header` 
  - `typ`(type): token类型,即JWT
  - `alg`(Algorithm): 生成token所使用的的算法,例如HS256 
- `Payload`
  - `Registered Claims`: 注册声明
    - `iss`(issuer): JWT的签发方
    - `iat`(issuerd at time): 签发时间
    - `sub`（subject）：JWT 主题
    - `aud`（audience）：JWT 接收方
    - `exp`（expiration time）：JWT 的过期时间
    - `jti`（JWT ID）：JWT 唯一标识
    - `nbf`（not before time）：JWT 生效时间，早于该定义的时间的 JWT 不能被接受处理
  - `Public Claims`: 公有声明
  - `Private Claims`: 私有声明
- `Signature` 签名,防止JWT被篡改
  - 计算依赖: Header(算法类型) + payload + 存放在服务器的秘钥



jwt验证流程

1. 用户初次登录，服务端生成token，返回给前端，该token中包含用于标识用户的字段
2. 前端将获取的token保存在localStorage中
3. 前端发送需要登录权限的请求，将token设置在请求头中
4. 服务端获取请求头中的token，解析该token，获取标识用户的字段,执行操作