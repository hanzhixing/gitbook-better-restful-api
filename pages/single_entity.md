# <a name="h5">数据操作</a>

>数据操作可以抽象为最基本的5类：增、删、改、查、搜。

>你还需要了解《[异常详情](#h4.2)》两节。

### <a name="h5.1">单实体操作</a>

#### <a name="h5.1.1">增</a>

相当于SQL中的INSERT。

创建对象的时候由服务端产生的“创建时间、默认分类“等部分属性是客户端可能无法知道的。

因此，建议返回新创建的实体对象。

成功时状态码：**201**

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs
// Request Method: POST
// Accept: application/json
// Accept-Language: zh-CN
{
  "user_id": 12,
  "title": "合理的设计",
  "sub_title": "整理并且持续更新各种合理的设计约定",
}
```

```javascript
// 响应
// Status Code: 201 Created
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
{
  "id": 123
  "user_id": 12,
  "title": "合理的设计",
  "sub_title": "整理并且持续更新各种合理的设计约定",
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00" // 见页面底部Links中的ISO 8601
}
```

#### <a name="h5.1.2">删</a>

这个操作逻辑上也能知道只能成功一次。

对后续多次请求应返回“对象不存在”的状态。

成功时建议使用HTTP状态码：**204**

建议不要返回实体数据。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123
// Request Method: DELETE
// Accept: application/json
// Accept-Language: zh-CN
```

```javascript
// 响应
// Status Code: 204 No Content
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
//
// 想在删除后返回被删除对象，是需要至少执行两个SQL语句。
// 一般删除操作向最终用户反馈删除成功与否即可。
// 事实上用户确认自己要删除的目标对象那一步，往往是需要在提交删除操作之前要完成。
```

#### <a name="h5.1.3">改</a>

幂等方式修改已有的数据。

相当于SQL中的UPDATE，HTTP Body相当于SET的参数，而URL相当于WHERE。

成功时建议使用HTTP状态码：**200**

建议返回被更改的实体对象数据。


```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123
// Request Method: PUT
// Accept: application/json
// Accept-Language: zh-CN
{
  "user_id": 12,
  "title": "用心设计",
  "sub_title": "整理各种设计约定",
}

// 响应
// Status Code: 200 Ok
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
{
  "id": 123
  "user_id": 12,
  "title": "用心设计",
  "sub_title": "整理各种设计约定",
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```

#### <a name="h5.1.4">查</a>

相当于SQL中的SELECT。而URL则相当于WHERE。

```javascript
/posts/2341/comments/5543
// 1. 所有帖子中找到编号为2341的帖子。
// 2. 在这个帖子的所有评论中，找到编号为5543的评论。

/posts/2341/comments?user_id=3244&rate=5
// 1. 所有帖子中找到编号为2341的帖子。
// 2. 在这个帖子的所有评论中，找到用户编号为3244，并且被赞5次的所有评论。
```

成功时建议使用HTTP状态码：**200**

一定返回实体数据。


```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123/posts
// Request Method: GET
// Accept: application/json
// Accept-Language: zh-CN

// 响应
// Status Code: 200 Ok
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
{
  "id": 35135,
  "blog_id": 123,
  "tags": [12, 17, 34, 25],
  "title": "合理的设计RESTful API",
  "content": "..."
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```

##### <a name="h5.1.4.1">关系</a>
如果，你已经阅读到这里，那么心里应该已经有很大的困惑：
>我的API要表达多种实体数据，而且还要把它们的关系表达出来，这个怎么办？

所以，我把这种关系的描述，放在了“复杂操作”中的第一个。同时，其他操作中，多少都会涉及到关系的描述。

如[前面](#h2.4)提到，[URL中描述关系](#h2.4)的时候很难描述实体关系比较复杂的情况。

而Body内容中，描述这种关系，也不是那么简单。有几种选择。

- 所有API都只操作单个实体对象。
  这意味着由非常多的简单API构成系统，同时会导致只有依赖很多次API请求才能完成很多业务逻辑。
  而这对受限于有限的HTTP连接数的浏览器应用来讲，相应的异步操作的等待时间会增加。
  没有良好的交互设计，可能会给用户带来非常糟糕的体验。
- 所有API都返回固定的数据结构，实体之间的关系用嵌套对象来描述，这样实现起来比较简单。
  但是，实体之间的关系比较复杂的系统中，这种方式会带来很多交互数据上的冗余。
  而这种冗余意味着很多场景下，实际的HTTP数据包要比真正需要的数据包要大。
  同时，在某些实体对象到底通过A接口，还是B接口的选择上，非常考验设计者在很高层次上的抽象能力。
- 最后一个选项，从逻辑上，是最佳方案。但实现代价也最高。
  就是让客户端来决定是否获取关联的实体对象，如果获取，用什么方式组织响应体的结构。
  下面是这种方案的实现方法。在数据获取方式的描述上，要增加约定好的GET参数。
  个人更佳倾向外链方式。

>内嵌方式：/posts/123?_embed=user,tags

```javascript
// 内嵌方式
{
  "id": 123
  "user": {
    "id": 32423,
    "username": "hanzhixing",
    "email": "zhixing.han.0409@gmail.com",
  }
  "tags": [
    {
      "id": 23,
      "name": "restful",
    },
    {
      "id": 12,
      "name": "convention",
    }
  ],
  "title": "用心设计",
  "content": "...",
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```

>外链方式：/posts/123?_extend=user,tags

```javascript
// 外链方式
{
  "post": {
    "id": 123
    "user_id": 32423,
    "title": "用心设计",
    "tags": [12, 23],
    "content": "..."
    "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
    "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
  },
  "user": {
    "id": 32423,
    "username": "hanzhixing",
    "email": "zhixing.han.0409@gmail.com",
  },
  "tags": [
    {
      "id": 12,
      "name": "convention",
    },
    {
      "id": 23,
      "name": "restful",
    },
    // ...
  ],
}
```

##### <a name="h5.1.4.2">字段挑选</a>

>可以按需获取所需要的信息，减少通信数据的大小。需要约定。

```javascript
// 请求
// Request Method: GET
// Accept: application/json
// _field: 字段列表，英文“,”分割。
/posts?_field=id,title,tags.id,tags.name&_extend=tags
```

#### <a name="h5.1.5">异常</a>

API为客户端反馈异常情况的时候，往往状态码和简单的一句描述，是远远不够的。

例如客户端提交注册表单，希望能够友好地提示用户哪些表单项填错了，是怎么错的。

又例如API是接收多个实体对象，服务端操作的时候检测到部分实体对象的操作由于某种原因无法完成。
这种情况下也要告知客户端到底发生什么事情了，只有这样客户端才能够对用户反馈有价值的提示或警告信息。

经在不同场景试用，如下这种方案比较实用。

```javascript
// 单实体
{
  "error": {
    // code字段根据实际情况可以不使用。
    // 服务端想返回的消息内容和客户端希望使用的消息内容不一样的场景较多的时候，尽量使用。
    // 比如，如果msg内容足以帮助客户端为用户提供友好的反馈或者基于它直接进行国际化等处理，那么你可以不使用它。
    "code": 422, 
    // 消息内容在任何时候都不应该省略，一是服务端从记录日志角度可能需要它，客户端也有可能直接使用它的内容。
    "msg": "Invalid characters in username",
    // 以上是异常情况的总体描述，除此之外，你往往还需要资源每个属性项的异常信息。
    "detail": [ // 可选
      {
        "key" : "username",
        "code" : 422001,
        // 警告！如果考虑OAuth这种标准，msg里边应该只返回英文描述。
        "msg" : "The 'username' can only contains alphabets, numbers and '_'."
      }, // 同时对同一个字段，针对不同的异常情况，也会有不同的错误码和消息内容。
      ...
    ],
  }
}
```
