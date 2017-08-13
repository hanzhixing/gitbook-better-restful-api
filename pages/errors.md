# 异常

API为客户端反馈异常情况的时候，往往状态码和简单的一句描述，是远远不够的。

例如客户端提交注册表单，希望能够友好地提示用户哪些表单项填错了，是怎么错的。

又例如API是接收多个实体对象，服务端操作的时候检测到部分实体对象的操作由于某种原因无法完成。
这种情况下也要告知客户端到底发生什么事情了，只有这样客户端才能够对用户反馈有价值的提示或警告信息。

经在不同场景试用，如下这种方案比较实用。

## 单实体
```javascript
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

## 多实体
```javascript
{
  // 实体key.
  // 仅返回操作成功的实体对象. 如果所有实体对象都没有被成功操作, 则不返回这个对象.
  "entities": [
    null, // null表示未被成功处理, 在errors中一定有对应的数组下标.
    {
      "id": 3432,
      "title": "Any useful text",
    },
    ...
  ],
  // 仅在发生异常时返回errors, 如果没有发生异常, 则不返回这个对象.
  "errors": [
    {
      "index": 1, // 提交时的数组下标
      "code": 422, 
      "msg": "Invalid characters in username",
      // 以上是异常情况的总体描述, 除此之外, 你往往还需要每个表单项的异常信息.
      "detail": [
        {
          "key" : "username",
          "code" : 422001,
          "msg" : "The 'username' can only contains alphabets, numbers and '_'."
        },
        ...
      ],
    },
    ...
  ]
}
```
