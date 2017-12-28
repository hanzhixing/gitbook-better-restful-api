# EE RESTful API设计规范

## 适用范围

**内网 + UUAP + Web App + 结构化文本数据传输**

## 一. URL

### 1.1 [强制] 不使用动词 

> Why? 动作（操作）应使用GET、POST、PUT、DELETE、PATCH等HTTP方法进行抽象。 避免“到底在什么时候用get，什么时候用list，什么时候用fetch?”的困惑。

```javascript
// Bad
/getPosts
/getUsers
/listPosts
/fetchAllComments

// Good
/posts
/users
```

### 1.2 [强制] 使用名词
> Why? “统一资源标识符“从来都是名词的抽象。

### 1.3 [强制] 使用复数

> Why? 避免“什么时候该用单数，什么时候该用复述”的困惑。

> Notice! person, people这种单复数关系，应牺牲英文词法的精准，使用persons, peoples。

```javascript
// Bad
/blogs/123/post              // 部分使用单数，部分使用复数
/blog/123/post/123/comments

// Good
/blogs/123/posts/123/comments/456
/users/123/posts/123
```

### 1.4 [强制] 不使用修饰词

> Why? 避免“是不是也可以用Items, Objects, Entities, List等作前缀”的困惑。

```javascript
// Bad
/usersList // 使用List
/tagItems // 使用Items

// Good
/users
/tags
```

### 1.5 [强制] Query参数的值部分做标准的URL_ENCODE处理

### 1.6 [建议] URL中多个资源名拼接使用kebab-case
> Why? 见[1.8]

> Why? 不同浏览器的地址栏以及网页内容中分别双击snake_case和kebab-case的命名，观察选中效果。

## 二. 数据格式

### 2.1 [强制] 请求中指定application/json。
> Why? 现发展阶段已经没有理由使用表达能力有限的application/x-www-form-urlencoded。

```javascript
// Accept: application/json
```

### 2.2 [强制] 响应中指定application/json并使用utf-8字符集。

```javascript
// Content-Type: application/json; charset=utf-8
```

### 2.3 [强制] Body内容中不使用自定义“信封信息”(Envelope)

> Why? 避免“是不是也可以用Items, Objects, Entities, List等作前缀”的困惑。它们是毫无用途的信息。

```javascript
// Bad
{
    "info": { // 使用了info
        "id": 134,
        "title": "我是标题内容",
        ...
    },
}
// Bad
{
    "data": { // 使用了data
        "id": 134,
        "title": "我是标题内容",
        ...
    },
}
// Good
{
    "id": 134,
    "title": "我是标题内容",
    ...
}
```

### 2.4 [建议] Body内容中实体对象的属性不使用修饰词

> Why? 它们是毫无用途的信息。

```javascript
// Bad
{
    ...
    "userInfo": ..., // 使用了Info
    ...
}

// Bad
{
    ...
    "userList": [
        ...,         // 使用了List
    ],
    ...
}

```

### 2.5 [强制] 若使用meta性质的信封信息，使用统一的meta key（被白名单严格管控的信封信息），meta key一律英文“_”开头。

> Why? 可以延展出公共库，使用公共代码解决问题。

所有meta信息的key都应是精心挑选，合理并为兼容性、扩展性负责。

|key|含义|
|---|---|
|_total|实体总数|
|_entities|实体容器|


```javascript
{
    "_total": 123432, // 不是资源属性的一部分 
    "_entities": { // 这里_entities的意义是避免实体对象的数据与total等meta信息散列在同一级
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

### 2.6 [强制] 若使用HTTP Header表示meta信息，使用统一的header名。

> Why? 可以延展出公共库，使用公共代码解决问题。

> X-Total-Count可以避免像[1.5]中那样不得不使用meta性质信封信息，并以在业界广泛被使用。

所有Header都应是精心挑选，合理并为兼容性、扩展性负责。

|key|含义|
|---|---|
|X-Total-Count|实体总数|

### 2.7 [强制] 不随意使用X-开头的HTTP Header

> Why? RFC6648中已明确废弃X-。

### 2.8 [建议] Body中Schema属性的命名风格尊重客户端语言。

> Tip: 据统计snake_case风格比camelCase要容易阅读20%左右!

> Tip: snake_case在目前绝大多数语言中可以作为安全的标识符!（有的语言不允许在标识符中出现《-》)

> Tip: snake_case在使用现有工具自动转换命名格式的时候最为安全!

> Tip: snake_case更适用于不区分大小写的上下文环境!


例如:
- Javascript中通常使用camelCase。
- Python中通常使用snake_case。
- PHP可能使用camelCase，也有可能使用snake_case。
- Java使用camelCase。

选择一种风格，始终如一地使用同一种命名规范。

## 三. 异常

### 3.0 [强制] 异常使用**4XX**状态码，禁止使用超出http1.1协议范围的状态码。

### 3.1 [强制] 身份验证未通过使用**401**
> Why? 浏览器端异步请求如果返回3XX的状态码，会自动被浏览器拦截并重定向，Javascript现阶段API是无法获得重定向报文中的信息。

```javascript
// 响应
// Status Code: 401 Unauthorized
// Location: http://auth.domain.com/the-base-path-for-login-page?redirectUrl= // 可选
```

### 3.2 [建议] 权限验证未通过使用**403**
如果不使用它，权限验证未通过可以像IIS的实现中那样，可以复用401，这个时候你需要[3.4]中的error.code区分不同场景。

```javascript
// 响应
// Status Code: 403 Forbidden
// Location: http://help.domain.com/the-help-page-about-permission // 可选
```

### 3.3 [强制] 资源不存在使用**404**
```javascript
// 响应
// Status Code: 404 Not Found
```

### 3.4 [强制] 其他异常一律使用**400**，使用统一Schema的错误描述对象。

**警告**
错误描述对象只允许出现在4XX和5XX的响应Body中出现。

```javascript
// 响应
// Status Code: 400 Bad Request
{
  // 你可以和团队协商自定义code, 例如使用http1.1中没有定义的6xx-9xx, 或者干脆使用位数更多的数字.
  "code": 422, // 必选，422状态码并未在http1.1的RFC中定义，是WebDAV的RFC4198中定义。
  // 消息内容在任何时候都不应该省略, 一是服务端从记录日志角度可能需要它，客户端也有可能直接使用它的内容.
  // 警告！如果考虑OAuth这种标准，msg里边应该只返回英文描述。
  "msg": "Invalid characters in username", // 必选
  // 以上是异常情况的总体描述，除此之外，你往往还需要资源每个属性项的异常信息.
  "detail": [ // 可选，
    // 同时对同一个字段, 针对不同的异常情况, 也会有不同的错误码和消息内容.
    {
      "key" : "username",
      "code" : 422001,
      "msg" : "The 'username' is too short."
    }, 
    {
      "key" : "username",
      "code" : 422001,
      "msg" : "The 'username' can only contains alphabets, numbers and '_'."
    }, 
    {
      "key" : "mobile",
      "code" : 422001,
      "msg" : "The 'mobile' can only be 11 digital numbers."
    }, 
    ...
  ],
}
```

## 四. 单实体操作

### 4.1 增
#### 4.1.1 [强制] 使用POST方法请求
#### 4.1.2 [强制] 成功时刻状态码使用201
#### 4.1.3 [强制] 响应Body中返回完整的实体对象
> Why? 自增主键，实体创建时间，通常在服务端生成，客户端无法获得这些信息。
```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs
// Request Method: POST
{
  ...
}
```

```javascript
// 响应
// Status Code: 201 Created
{
  "id": 123
  ...
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00" // 建议ISO8601时间格式
}
```

### 4.2 删
#### 4.2.1 [强制] 使用DELETE方法请求
#### 4.2.2 [强制] 请求Body为空
#### 4.2.3 [强制] 成功时刻状态码使用204
#### 4.2.4 [强制] 响应Body为空
> Why? 想在删除后返回被删除对象，是需要至少执行两个SQL语句。

> Why? 用户确认自己要删除什么，往往是需要在提交删除操作之前完成。

> Why? 一般删除操作向最终用户反馈删除成功与否即可。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123
// Request Method: DELETE
```

```javascript
// 响应
// Status Code: 204 No Content
```

### 4.3 替
#### 4.3.1 [强制] 使用PUT请求
#### 4.3.2 [强制] 请求Body中提交资源的所有属性
#### 4.3.3 [强制] 成功时刻状态码使用200
#### 4.3.4 [强制] 响应Body中返回完整的实体对象（除二进制文件）
> Why? 更新时间，通常在服务端生成，客户端无法获得这些信息。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123
// Request Method: PUT
{
  ...
}
```

```javascript
// 响应
// Status Code: 200 Ok
{
  "id": 123
  ...
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```

### 4.4 改
#### 4.4.1 [强制] 使用PATCH请求
#### 4.4.2 [强制] 请求Body中只提交资源被修改的属性
#### 4.4.3 [强制] 成功时刻状态码使用200
#### 4.4.4 [强制] Body中返回完整的实体对象（除二进制文件）
> Why? 更新时间，通常在服务端生成，客户端无法获得这些信息。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123
// Request Method: PUT
{
  ...
}
```

```javascript
// 响应
// Status Code: 200 Ok
{
  "id": 123
  ...
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```

### 4.5 查
#### 4.5.1 [强制] 使用GET请求
#### 4.5.2 [强制] 请求Body为空
#### 4.5.3 [强制] 成功时刻状态码使用200

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123/posts/456
// Request Method: GET
```

```javascript
// 响应
// Status Code: 200 Ok
{
  ...
}
```

## 五. 多实体操作
### 5.1 [强制] 多实体的《增》《删》《替》《改》遵守单实体的对等操作
不同是Body中的实体数据由对象变为数组。

### 5.2 查
#### 5.2.1 （简单）过滤
##### 5.2.1.1 [强制] 使用逻辑运算符
> Why? 比_gt, _lt, _like等具有更好的可读性

> Why? 通用构造器和解析器

##### 5.2.1.2 [强制] 一旦使用，逻辑运算符置与《=》前

> Why? 若置于《=》后，处理值的内容中出现这些逻辑运算符的情况比较困难。

|符号|含义|
|---|---|
|无|等于|
|!|不等于|
|>|大于|
|<|小于|
|%|like|

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/posts?id=123,456&user_id>=16&tag_id!=12,22&title%=哈哈
// Request Method: GET
```

#### 5.2.2 [强制] 排序
> Why? 通用构造器和解析器

##### 5.2.2.1 [强制] 排序字段严格约定
> Why? 服务端需要考虑索引


|参数|说明|
|---|---|
|_sort|英文“,”分割，默认正序，英文“-”倒序。顺序影响排序结果。|

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/posts?_sort=user_id,-update_datetime,tag_id
// Request Method: GET
```

#### 5.2.3 [强制] 分页
> Why? 通用构造器和解析器

> Why? _page,_size隐含页码的显式实现, 只不过offset的值计算放在了服务端。

> Why? 显式实现页码的场景，客户端可以运用_offset和_limit计算得来_page和_size。

> Why? 越来越多的Web App不再使用显式页码。_offset和_limit更具通用性。

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
// X-Total-Count: 3243 // 如果不使用Header, 不得不在Body中使用信封信息。
[
  {
    ...
  },
  ...
]
```

#### 5.2.4 [提案] 关联
> Why? 通用构造器和解析器

- 所有API都只操作单个实体对象。
  这意味着由非常多的简单API构成系统，同时会导致只有依赖很多次API请求才能完成很多业务逻辑。
  而这对受限于同域HTTP连接数的浏览器应用来讲，相应的异步操作的等待时间会增加。
  没有良好的交互设计，可能会给用户带来非常糟糕的体验。
- 所有API都返回固定的数据结构，实体之间的关系用嵌套对象来描述，这样实现起来比较简单。
  但是，实体之间的关系比较复杂的系统中，这种方式会带来很多交互数据上的冗余。
  而这种冗余意味着很多场景下，实际的HTTP数据包要比真正需要的数据包要大很多。
  同时，在某些实体对象到底通过A接口，还是B接口的选择上，非常考验设计者在很高层次上的抽象能力。
- 最后一个选项，从逻辑上，是最佳方案。但实现代价也最高。
  就是让客户端来决定是否获取关联的实体对象，如果获取，用什么方式组织响应体的结构。
  下面是这种方案的实现方法。在数据获取方式的描述上，要增加约定好的GET参数。
  
##### 5.2.4.1 内嵌方式
```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/posts/123?_embed=user,tags
// Request Method: GET
```

```javascript
// 响应
// Status Code: 200 Ok
// 缺点: 冗余严重
{
  "id": 123
  "user": {
    "id": 32423,
    "username": "hanzhixing",
    ...
  }
  "tags": [
    {
      "id": 23,
      "name": "restful",
    },
    {
      "id": 12,
      "name": "convention",
    }
    ...
  ],
  ...
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```
##### 5.2.4.1 外链方式
```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/posts/123?_extend=user,tags
// Request Method: GET
```

```javascript
// 响应
// Status Code: 200 Ok
// 缺点: 需要使用者根据URL明确知道哪个资源是主资源，否则需要引入更多的信封信息。
{
  "post": {
    "id": 123
    "user_id": 32423,
    ...
    "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
  },
  "user": {
    "id": 32423,
    "username": "hanzhixing",
    ...
  },
  "tags": [
    {
      "id": 12,
      "name": "convention",
    },
    {
      "id": 23,
      "name": "restful",
    },
    ...
  ],
}
```

#### 5.2.5 [建议] 挑选
> Why? 减少数据传输量

> Why? 通用构造器和解析器

|参数|说明|
|---|---|
|_field|字段列表，英文“,”分割。|

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/posts?_field=id,title,tags.id,tags.name
// Request Method: GET
```

## 六. 版本化
> Why? 版本化会为你将来的重构、升级、并存等创造条件。

### 6.1 [强制] 版本标识使用与[Semantic Versioning 2.0.0](http://semver.org/)同含义的抽象概念和标记法
> Why? 此规范已经在多数主流语言中都支持，可以按标准化的方式修改、比较版本信息。

### 6.1.1 [强制] 版本的Major位并仅Major位前置，并以小写字母《v》作前缀。

> 注: 代表架构层面的版本

```javascript
// MAJOR位前置
/rest/v1/...
/api/v3/...
```

### 6.1.2 [建议] 以API的粒度进行升级，通过_ver参数指定以小写字母《v》作前缀的完整版本。
> Why? API粒度的升级经常发生，优雅升级，并存都需要区分版本。

```javascript
// 除了MAJOR位，还想要MINOR和PATCH位，最好使用query参数.
/rest/v1/posts/123?_ver=v1.1.0
/rest/v1/posts/123?_ver=v1.1.5
```
