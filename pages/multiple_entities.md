
### <a name="h5.2">多实体操作(批量操作)</a>

#### <a name="h5.2.1">增</a>

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs
// Request Method: POST
// Accept: application/json
// Accept-Language: zh-CN
[
  {
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
  },
  {
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
  }
]
```

```javascript
// 响应
// Status Code: 201 Created
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
[
  {
    "id": 123
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
    "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00" // 见页面底部Links中的ISO 8601
  },
  {
    "id": 123
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
    "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00" // 见页面底部Links中的ISO 8601
  }
]
```

#### <a name="h5.2.2">删</a>

隐含“多实体”的“查”操作。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs?id=123,456,789
// Request Method: DELETE
// Accept: application/json
// Accept-Language: zh-CN
```

```javascript
// 响应
// Status Code: 204 No Content
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
```

#### <a name="h5.2.3">改</a>

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs
// Request Method: PUT
// Accept: application/json
// Accept-Language: zh-CN
[
  {
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
  },
  {
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
  }
]
```

```javascript
// 响应
// Status Code: 200 Ok
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
[
  {
    "id": 123
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
    "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
    "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
  },
  {
    "id": 123
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
    "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
    "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
  }
]
```

#### <a name="h5.2.4">查</a>

>它们是RESTful API的设计难点

##### <a name="h5.2.4.1">过滤</a>

```javascript
// 请求
// Request Method: GET
// Accept: application/json
// =, !=, >=, <=
/posts?id=123,456&user_id>=16&tag_id!=12,22
```

##### <a name="h5.2.4.2">排序</a>
```javascript
// 请求
// Request Method: GET
// Accept: application/json
// 无符号表示正排，英文“-”表示倒排，英文“,”分割字段，顺序影响排序结果。
/posts?_sort=user_id,-update_datetime,tag_id
```

##### <a name="h5.2.4.3">翻页</a>

```javascript
// 请求
// Request Method: GET
// Accept: application/json
// _offset表示数据便宜量，_limit表示查询个数
/posts?_offset=2400&_limit=20
```

```javascript
// 响应（推荐）
// 需要告知客户端总共还有多少数据，要不然页面上无法显示总页数。Link客户端往往可以自行拼接。
// X-Total-Count: 3243
// Link: </posts?_offset=15&_limit=5>; rel="next",</posts?_offset=50&_limit=3>; rel="last",</posts?_offset=0&_limit=5>; rel="first",</posts?_offset=5&_limit=5>; rel="prev"
```

```javascript
// 响应（需要信封）
// 优点是不需要自行处理Header，缺点是可能的命名冲突，结构也会多一层。
{
  total: 1343,
  entities: [
    {
        ...
    },
    ...
  ],
}
```

#### <a name="h5.2.5">异常</a>
```javascript
{
  // 实体key。
  // 仅返回操作成功的实体对象。如果所有实体对象都没有被成功操作，则不返回这个对象。
  "entities": [
    {
      "id": 3432,
      "title": "Any useful text",
    },
    ...
  ],
  // 仅在发生异常时返回errors，如果没有发生异常，则不返回这个对象。
  "errors": [
    {
      "index": 1, // 提交时的数组下标
      "code": 422, 
      "msg": "Invalid characters in username",
      // 以上是异常情况的总体描述，除此之外，你往往还需要每个表单项的异常信息。
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

### <a name="h5.3">其他</a>

API设计过程当中，也会碰到一些无法用以上的操作无法描述的操作。

这类API通常都需要专门的使用说明。

例如：
- 激活操作。可以用PATCH提交{activated: false}。
  但往往可以通过把activated属性抽象成目标资源的属性的方式来解决。
- 收藏、取消收藏。
  收藏对象也可以是一种资源。
- 跨对象的搜索。
  关键字来进行搜索，但可能会匹配文章，可能会匹配评论，也有可能会匹配新闻公告等。
  这种情况下是没有办法事先明确资源。因此系统提供/search这种专用的URL是比较可取的。
