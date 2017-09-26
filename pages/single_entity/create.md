# 增

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
