# 特殊的网络环境

网络环境中有的代理服务可能只支持GET和POST方法

最好协商自定义的HTTP Header来标识PUT、DELETE等请求。

```javascript
// 请求
// Request Method: GET
// X-HTTP-Method-Override: PUT
```
