# 数据格式
>强烈建议统一使用application/json，而且它应该成为默认的交互协议。

<a name="application-json"></a>
## application/json

很多人误认为这是HTTP的“默认”数据类型。

实际上在请求阶段会把表单项键值对拼接成key1=value1&key2[]=value21&key2[]=value22这种字符串放在Body中发送。

它并不是什么HTTP的“默认”数据格式，而是早些年实现的浏览器的默认行为实现。这个默认行为也要看具体是什么样的表单项，如果是大文件上传，这个格式可能会变成multipart/form-data，并不是application/x-www-form-urlencoded。因此，事实上没有什么所谓HTTP的默认格式。

最新的Javascript框架或库，以angular为例，也已经不再把老式浏览器的默认MIME type作为自己默认的数据格式，而是转而使用application/json，或者让开发者自己定义。

application/x-www-form-urlencoded由于受限于它自身的定义，尤其描述复杂的数据关系的时候是无能为力。

目前，不管是服务端还是客户端，正确处理不同的MIME type，在技术上已经没有任何障碍。因此，我们更没有理由在RESTful API上使用表达能力非常有限的application/x-www-form-urlencode作为默认格式，是时候把这个类型丢在脑后了。


<a name="headers-for-content-type"></a>
## Accept和Content-Type

| 类型 | HTTP头 | 说明 | 实例 |
| --- | --- | --- | --- |
| 请求 | Accept | 客户端能接收处理的格式。 | Accept: application/json,application/xml; |
| 响应 | Content-Type | 服务端响应的格式，同时需要定义字符集。 | Content-Type: application/json; charset=utf-8 |

常用类型见[MIME types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types)。

HTTP协议中定义Accept和Content-Type头，正是为了达到客户端和服务端能够协商MIME type的目的，因此使用它们非常合乎逻辑，同时也能能够让你的URL更佳干净。

再则，现在对任何一个开发者来讲，使用Chrome或者Firefox等浏览器的插件，已经不是什么负担，而是常识。

下面是可以构造HTTP请求的浏览器插件。

- Chrome: [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?utm_source=chrome-ntp-icon), [Advanced REST client](https://chrome.google.com/webstore/detail/advanced-rest-client/bljmokabgbdkoefbmccaeficehkmlnao)
- Firefox: [RESTClient, a debugger for RESTful web services](https://addons.mozilla.org/en-US/firefox/addon/restclient/)

<a name="file-ext-in-url"></a>
## .json/.xml/.html

```javascript
/posts/2341/comments/5543.json
/posts/2341/comments.xml?user_id=3244&rate=5
```

通过这种方式，可以在不依赖第三方工具的前提下，直接在浏览器中查看返回数据的不同格式。

HTTP头这样的信息是，要么通过浏览器插件，要么通过程序中才有办法定义，用户是没有其他途径。

缺点是服务端需要解析URL中的后缀信息，但是如果你的程序分层和抽象做的好，那么你可以用AOP的方式为你的API增加这种能力。

以增量的方式对已有的API进行适配升级，升级的时候把原来使用的格式作为默认格式，其实代价不会大到无法接受。


<a name="no-noun-modifiers"></a>
## 不使用修饰词

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

有时，这种“信封信息”(Envelope)并不完全是“废话”。

例如:
- 使用total来为翻页功能提供必要的信息。
- 为jsonp形式的请求返回不同的callback表达式。
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
    "callback": "renderPostListPage(posts, total_count, 344)",
    ...
}
```

尽管如此，从目前发展来看，[CORS](http://www.w3.org/TR/cors/)或者[The Link Header Field(RFC5988)](http://tools.ietf.org/html/rfc5988#page-6)这种新标准的应用在绝大多数场景下都可以满足。因此，这种修饰方式封装实体数据的做法，应尽可能避免。

对服务没有配置权限的，很难处理跨域请求，只好使用jsonp的方式。

但从职责来看，这是API服务的提供方有没有良好的服务意识的问题，因此应优先考虑在服务方得到解决。


<a name="naming"></a>
## 命名风格

一般来讲，API是要遵守客户端语言的命名规范。

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

