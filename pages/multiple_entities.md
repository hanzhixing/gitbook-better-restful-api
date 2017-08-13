# 多实体操作

俗称批量操作。

## 增

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs
// Request Method: POST
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

## 删

隐含“多实体”的“查”操作。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs?id=123,456,789
// Request Method: DELETE
```

```javascript
// 响应
// Status Code: 204 No Content
```

## 改

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs
// Request Method: PUT
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

## 查

```javascript
// 响应
// Status Code: 200 Ok
// 参考《增》的body
```

请求是RESTful API的设计难点

