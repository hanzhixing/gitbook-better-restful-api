# 过滤

|符号(参数=后需要URL_ENCODE)|含义|
|---|---|
|无|等于|
|!|不等于|
|>|大于|
|<|小于|
|%|like|

大于等于和小于一般都可以用大于和小于代替。

需要定制的URL参数构造器、解析器.

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/posts?id=123,456&user_id>=16&tag_id!=12,22&title%=哈哈
// Request Method: GET
```
