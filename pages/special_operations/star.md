# 收藏、取消收藏

## 收藏

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/users/123/favorites
// Request Method: PUT
{
  "entity": "post",
  "post_id": 456,
}
```

```javascript
// 响应
// Status Code: 201 Created
{
  "id": 789,
  "entity": "post",
  "post_id": 456,
  "user_id": 123,
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```

## 取消收藏

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/users/123/favorites
// Request Method: DELETE
```

```javascript
// 响应
// Status Code: 204 No Content
```
