# 替

相当于SQL中的UPDATE，HTTP Body相当于SET的参数，而URL相当于WHERE。

需要返回被更改的实体对象数据。

提交包含完整的实体属性列表。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123
// Request Method: PUT
{
  "user_id": 12,
  "title": "用心设计",
  "sub_title": "整理各种设计约定",
}
```

```javascript
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
