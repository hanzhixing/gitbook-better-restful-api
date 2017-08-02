
## <a name="h13">穿过特殊的网络环境</a>

>网络环境中有的代理服务可能只支持GET和POST方法

服务端最好利用自定义的HTTP Header来识别PUT、DELETE等请求。

```javascript
// 请求
// Request Method: GET
// X-HTTP-Method-Override: PUT
```
