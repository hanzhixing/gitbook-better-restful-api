# 删

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
