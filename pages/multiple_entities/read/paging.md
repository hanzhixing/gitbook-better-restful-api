# 翻页

|参数|说明|
|---|---|
|_offset|数据偏移量|
|_limit|查询个数|

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/posts?_offset=2400&_limit=20
// Request Method: GET
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
