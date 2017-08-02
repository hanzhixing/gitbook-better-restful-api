## <a name="h12">HATEOAS</a>
>**H**ypermedia **a**s **t**he **E**ngine **o**f **A**pplication **S**tate

为客户端提供利用API进一步查找数据的附加信息。
在应用这项原则之前，需要评估一下API的客户端是否是在运行时刻根据服务端响应的数据动态地决定进一步的动作。
绝大多数网页都不是运行时刻决定，而是开发时刻由程序员写响应的代码来决定的。
因此我们的世界可能还没有准备好大面积使用HATEOAS原则。

```javascript
{
    ...
    "links": [
    {
        "rel": "self",
        "href": "/api/v1/posts/23",
        ...
    },
    ...
}
```
