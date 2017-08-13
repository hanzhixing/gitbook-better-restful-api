# 单实体操作

仅成功的场景。

## 增

相当于SQL中的INSERT。

创建对象的时候由服务端产生的“创建时间、默认分类“等部分属性是客户端无法知道。

因此，需要返回新创建的实体对象。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs
// Request Method: POST
{
  "user_id": 12,
  "title": "合理的设计",
  "sub_title": "整理并且持续更新各种合理的设计约定",
}
```

```javascript
// 响应
// Status Code: 201 Created
{
  "id": 123
  "user_id": 12,
  "title": "合理的设计",
  "sub_title": "整理并且持续更新各种合理的设计约定",
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00" // 见页面底部Links中的ISO 8601
}
```

## 删

逻辑上只能成功一次。

对后续多次请求应返回404。

不需要返回实体数据。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123
// Request Method: DELETE
```

```javascript
// 响应
// Status Code: 204 No Content
// 想在删除后返回被删除对象，是需要至少执行两个SQL语句。
// 一般删除操作向最终用户反馈删除成功与否即可。
// 用户确认自己要删除什么，往往是需要在提交删除操作之前完成。
```

## 改

幂等方式修改已有的数据。

相当于SQL中的UPDATE，HTTP Body相当于SET的参数，而URL相当于WHERE。

需要返回被更改的实体对象数据。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123
// Request Method: PUT
{
  "user_id": 12,
  "title": "用心设计",
  "sub_title": "整理各种设计约定",
}

// 响应
// Status Code: 200 Ok
{
  "id": 123
  "user_id": 12,
  "title": "用心设计",
  "sub_title": "整理各种设计约定",
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```

## 查

相当于SQL中的SELECT。而URL则相当于WHERE。

一定返回实体数据。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123/posts/456
// Request Method: GET

// 响应
// Status Code: 200 Ok
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
