# 异常

任何API请求，都有可能发生异常。

API为客户端反馈异常情况的时候，往往状态码和简单的一句描述，是远远不够的。

例如客户端提交注册表单，希望能够友好地提示用户哪些表单项填错了，是怎么错的。

又例如API是接收多个实体对象，服务端操作的时候检测到部分实体对象的操作由于某种原因无法完成。
这种情况下也要告知客户端到底发生什么事情了，只有这样客户端才能够对用户反馈有价值的提示或警告信息。

经在不同场景试用，如下这种方案比较实用。

## 身份验证没通过
```javascript
// 响应
// Status Code: 401 Unauthorized
```
Authorize一般指对特定的资源授予权限的行为，而Authenticate一般指授予特定身份的行为，拥有一种身份，不代表对任何资源都有访问权限。
http1.1协议中401的描述文本使用了Unauthorized，但是，如果去看IIS的401的实现，就可以发现401是既包含Authentication，也包含Authorization。

## 权限验证没通过 (资源访问权限)
```javascript
// 响应
// Status Code: 403 Forbidden
```
服务端能够识别请求，但拒绝获取资源。

## 资源不存在
```javascript
// 响应
// Status Code: 404 Not Found
```

## 资源冲突
一般在使用PUT或PATCH更新资源的时候遇到。

也可以用下方《其他》中的方法实现这种场景。

由于RESTful架构方式，本身会涉及到较复杂的关系，大多数情况是很难用一个状态码描述到底是哪个资源冲突。
因此，推荐使用400来约定这种场景。

```javascript
// 响应
// Status Code: 409 Conflict
```

## 其他 (统统都是400 Bad Request)

```javascript
// 响应
// Status Code: 400 Bad Request
{
  // 你想完全符合http1.1协议, 但需要422这种其他标准中定义，但很有意义的状态码, 你可以利用这个code.
  // 你可以和团队协商自定义code, 例如使用http1.1中没有定义的6xx-9xx, 或者干脆使用位数更多的数字.
  "code": 422, 
  // 消息内容在任何时候都不应该省略, 一是服务端从记录日志角度可能需要它，客户端也有可能直接使用它的内容.
  // 警告！如果考虑OAuth这种标准，msg里边应该只返回英文描述.
  "msg": "Invalid characters in username",
  // 以上是异常情况的总体描述，除此之外，你往往还需要资源每个属性项的异常信息.
  "detail": [ // 可选
    // 同时对同一个字段, 针对不同的异常情况, 也会有不同的错误码和消息内容.
    {
      "key" : "username",
      "code" : 422001,
      "msg" : "The 'username' is too short."
    }, 
    {
      "key" : "username",
      "code" : 422001,
      "msg" : "The 'username' can only contains alphabets, numbers and '_'."
    }, 
    ...
  ],
}
```

