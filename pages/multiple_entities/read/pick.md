# 挑选

|参数(URL_ENCODE)|说明|
|---|---|
|_field|字段列表，英文“,”分割。|

可以按需获取所需要的信息，减少通信数据的大小。需要约定。

```javascript
// 请求
// Request Method: GET
/posts?_field=id,title,tags.id,tags.name
```
