# 激活、反激活

可以通过把activated属性抽象成目标资源的属性的方式来解决。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/category/123
// Request Method: PUT
{
  "activated": true,
}
```

```javascript
// 响应
// Status Code: 200 Ok
{
  "id": 123
  "title": "随笔",
  "activated": true,
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```
