# 过滤、排序、分页、关联、挑选

## 过滤

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/posts?id=123,456&user_id=>16&tag_id=!12,22&title=%哈哈
// Request Method: GET
// =, =!, =>, =<, =%
// 服务端需要定制的URL参数解析器.
// =后内容需要URL_ENCODE.
```

## 排序
```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/posts?_sort=user_id,-update_datetime,tag_id
// Request Method: GET
// 无符号表示正排，英文“-”表示倒排，英文“,”分割字段，顺序影响排序结果。
// 服务端需要定制的URL参数解析器.
// =后内容需要URL_ENCODE.
```

## 翻页

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/posts?_offset=2400&_limit=20
// Request Method: GET
// _offset表示数据便宜量，_limit表示查询个数
```

```javascript
// 响应
// Status Code: 200 Ok
// X-Total-Count: 3243
[
  {
    ...
  },
  ...
]
```

## 关联

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

### 挑选

可以按需获取所需要的信息，减少通信数据的大小。需要约定。

```javascript
// 请求
// Request Method: GET
// _field: 字段列表，英文“,”分割。
/posts?_field=id,title,tags.id,tags.name
```
