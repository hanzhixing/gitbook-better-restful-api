# 状态码

HTTP状态码很多，可以看[List of HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)了解一下。 

使用HTTP的状态码来描述状态，服务端和客户端都有需要分别处理它们的代价。

下方列表为几个候选状态码。

挑选个别几个在团队内部约定好即可，同时只靠HTTP的状态码是不足以描述清楚所有情况。

所有状态码都只有客户端针对不同的情况能够做出不同的反馈的时候才会有意义。

| Code | 默认返回文本 | 说明 |
| --- | --- | --- |
| 200 | OK | 一切正常。|
| 201 | Created | 与POST搭配使用。 |
| 204 | No Content | 与DELETE搭配使用。 |
| 304 | Not Modified | 对于调用频率非常高，应用了服务端缓存技术的API来讲使用要使用此状态码告知客户端使用的是缓存中的数据是必不可少的。 |
| 400 | Bad Request | 服务端要返回尽可能详细的错误反馈。见[下一节](#h4.2)。 |
| 401 | Unauthorized | 对需要身份验证的API必不可少。见[资源授权、身份验证、权限验证](#h9) |
| 403 | Forbidden | 服务端有能力处理请求，但是根据目前授权不允许响应操作。与401配套使用。 |
| 404 | Not found | 请求的资源不存在。非常实用。 |
| 405 | Method Not Allowed | 无法用请求的方法对目标资源进行操作。 |
| 410 | Gone | 废弃API可以用它告知客户端。 |
| 415 | Unsupported Media Type | API不支持处理该格式数据。例如向只支持json数据的API发送了xml格式的数据。 |
| 422 | Unprocessable Entity | 表单验证失败可以用这个状态码。例如客户端提交数据符合协议，但某些字段之间的逻辑关系可能有问题。  |
| 429 | Too Many Requests | 限制频率的API应该使用它。 |
| 500 | Internal Server Error | 任何一个API都应该避免让web服务返回5XX的状态码。不应让客户端能够感知到服务端配置错了或者写错了代码。 |

