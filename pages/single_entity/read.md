# 查

相当于SQL中的SELECT。而URL则相当于WHERE。

一定返回实体数据。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123/posts/456
// Request Method: GET
```

```javascript
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
