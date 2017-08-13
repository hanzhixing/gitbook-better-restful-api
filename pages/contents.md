# 数据格式
强烈建议使用**application/json**作为默认的内容格式。

## 所谓的默认格式

很多人误认为网页表单提交时刻服务端得到的数据格式是HTTP的“默认”数据类型。

实际是浏览器在请求阶段把表单项键值对拼接成key1=value1&key2[]=value21&key2[]=value22这种字符串放在Body中发送。

这种格式为**application/x-www-form-urlencoded**。

浏览器的这个“默认行为”也要看具体是什么样的表单数据，如果是文件上传，会变成**multipart/form-data**。

因此，这只是浏览器考虑了网页表单的针对MIME type的实现，没有什么所谓“HTTP的默认格式”。

即使API对应的客户端确实是浏览器，随着前端技术的发展，同步表单提交也已经少的可怜。

同时对异步请求而言，最新的Javascript框架或库也已经不再把application/x-www-form-urlencoded作为自己默认的数据格式，而是转而使用application/json，或者让开发者自己定义。

application/x-www-form-urlencoded由于受限于它自身的定义，尤其描述复杂的数据关系的时候是无能为力。

目前，不管是服务端还是客户端，正确处理不同的MIME type，在技术上已经没有任何障碍。因此，我们更没有理由在RESTful API上使用表达能力非常有限的application/x-www-form-urlencode作为默认格式，是时候把这个类型丢在脑后了。


## Accept和Content-Type

HTTP协议中客户端和服务端使用下方2个头来协商数据格式(MIME type)。

| 类型 | HTTP头 | 说明 | 实例 |
| --- | --- | --- | --- |
| 请求 | Accept | 客户端告知服务端自己能处理的格式。 | Accept: application/json,application/xml; |
| 响应 | Content-Type | 服务端告知客户端自己返回的格式。需要定义字符集。 | Content-Type: application/json; charset=utf-8 |

常用类型见[MIME types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types)。

因此使用它们非常合乎逻辑，同时也能能够让你的URL更佳干净。

再则，现在对任何一个开发者来讲，使用Chrome或者Firefox等浏览器的插件，已经不是什么负担，而是常识。

下面是可以构造HTTP请求的浏览器插件。

- Chrome: [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?utm_source=chrome-ntp-icon), [Advanced REST client](https://chrome.google.com/webstore/detail/advanced-rest-client/bljmokabgbdkoefbmccaeficehkmlnao)
- Firefox: [RESTClient, a debugger for RESTful web services](https://addons.mozilla.org/en-US/firefox/addon/restclient/)

## Body内容中不使用修饰词

与在URL避免用废话修饰的理由相同

```javascript
// Evil!
{
    "info": { // 这里info是废话
        "id": 134,
        "title": "我是标题内容",
        ...
    },
}
// Evil!
{
    "data": { // 这里data是废话
        "id": 134,
        "title": "我是标题内容",
        ...
    },
}
// Good!
{
    "id": 134,
    "title": "我是标题内容",
    ...
}
```

有时，这种“信封信息”(Envelope)并不完全是“废话”。但即使是使用，也应非常谨慎，应作为别无他法时不得已的最后选择。

例如:
- 使用total来为翻页功能提供必要的信息。
```javascript
{
    "total": 123432, // 总帖子数 
    "entities": { // 这里entities的意义是避免实体对象的数据与total等meta信息散列在同一级
        "posts": [
            {
                "id": 134,
                "title": "我是标题内容",
                ...
            },
            ...
        ],
    }
    ...
}
```

使用自定义头，上面的total, entites和posts都可以消除掉。

从目前发展来看，客户端也好，服务端也好使用自定义HTTP头不存在技术屏障。

从职责来看，这是API服务的提供方有没有良好的服务意识的问题，因此应优先考虑在服务方得到解决。

关于自定义头的使用参考了不少资料，大部分资料或者讨论都围绕着下方链接里的信息。

- [Custom HTTP headers : naming conventions](https://stackoverflow.com/questions/3561381/custom-http-headers-naming-conventions)。
- [Deprecating the "X-" Prefix and Similar Constructs in Application Protocols](https://tools.ietf.org/html/rfc6648)
- [MDN:Setting HTTP request headers](https://developer.mozilla.org/en/docs/Setting_HTTP_request_headers)

现实中自定义头的命名问题上暂时还没有非常明朗的标准告诉你怎么做是正确的，但把问题描述清楚了，以便你知道怎么做的错误的。

总之，X-前缀方式的自定义头命名现在已经不再被推荐，尽量以更加具体的不大可能与他人的命名冲突的方式命名自定义头。

已经在很多实践中大量被使用，含义明确的自定义头是任何标准也没有明确指出“必须不得使用”。

因此，回到上方例子，你可以使用同样在业界大量被使用的自定义头: **X-Total-Count**。

## 命名风格

一般来讲，API是要充分尊重客户端语言的命名规范。

例如:
- Javascript中通常使用camelCase。
- Python中通常使用snake_case。
- PHP可能使用camelCase，也有可能使用snake_case。
- Java使用camelCase。

所以，选择一种风格，始终如一地使用同一种规范即可。

>据统计snake_case风格比camelCase要容易阅读20%左右!


<a name="performance"></a>
## 格式化和性能

> ?_pretty=true

这里的格式化是指使用空白符缩进进行格式化的对人友好的数据展现。因此同样考虑对人友好，HTTP头等其他手段都会增加成本，只有URL的GET参数是最小成本的，同时实现它的代价也是微乎其微。

这种格式化由于包含了很多空白符，因此一定会增加HTTP数据包的大小。

通常对中等规模的数据来讲，这种格式化带来的大小上的差异在10%以内，而使用gzip压缩的情况下，可能连3%都不到。同时目前绝大多数应用中，响应数据是默认使用gzip进行压缩，而且这种压缩往往都是对客户端透明，更值得一提的是gzip压缩情况下甚至会带来60%以上的大小上的优化，压缩效果是非常显著的，因此格式化带来的大小增长，大部分情况下可以忽略不计。

如果你的场景需要用非常极限的压缩，换来更佳极致的性能，那么application/json格式本身就不是你的最佳选择，因为json毕竟是使用字符串类型表示所有数据，同时它还包含了很多《"》。这种情况下，你可能更愿意让服务端和客户端使用二进制的自定义的MIME type来进行数据交互。

如果是为第三方提供开放API的服务，建议你最好增加这种机制，你的用户心里会默默感激你的良苦用心。

如果都是自己人使用，最好让使用者都使用浏览器插件查看格式化后的数据，因为服务端增加pretty=true的适配，或多或少都是成本。

查看格式化json的浏览器插件。

- Chrome: [JSON Editor](https://chrome.google.com/webstore/detail/json-editor/lhkmoheomjbkfloacpgllgjcamhihfaj)
- Chrome: [JSON Formatter](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa)
- Firefox: [JSONView](https://addons.mozilla.org/en-us/firefox/addon/jsonview/)

