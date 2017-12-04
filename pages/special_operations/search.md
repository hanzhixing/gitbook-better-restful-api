# 跨资源搜索

关键字来进行搜索，但可能会匹配文章，可能会匹配评论，也有可能会匹配新闻公告等。

这种情况下是没有办法事先明确资源。因此系统提供/searches这种专用的URL是比较可取的。

## 第一步

创建一次搜索，搜索也是一种资源。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/searches
// Request Method: POST
{
  ...
  非常复杂的关系描述
  ...
}
```

## 第二步

服务端生成uuid，并把这uuid和第一步的Body中的条件描述对象进行关联。

### 客户端自主处理

```javascript
// 响应
// Status Code: 201 Created
{
  "uuid": "498b882a-25d1-4305-ace7-e76bfc0fa48c"
}
```

得到上面这种响应数据之后再发起新的请求，这时服务端根据uuid找到查询条件进行真正的查询。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/searches/498b882a-25d1-4305-ace7-e76bfc0fa48c
// Request Method: GET
{
  ...
  搜索到的对象数据
  ...
}
```

### 对客户端透明

如果客户端是浏览器环境，浏览器会自动处理Redirect，可以利用这一点。

第一步之后，可以直接得到最终的搜索结果。

```javascript
// 响应
// Status Code: 302 Found
// Location: https://api.domain.com/rest/v1/searches/498b882a-25d1-4305-ace7-e76bfc0fa48c
{
  ...
  搜索到的对象数据
  ...
}
```
