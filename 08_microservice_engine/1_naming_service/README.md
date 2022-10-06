# 服务注册和发现

在我们的qtclass-admin的`POST /deploy` API中，我们需要调用来自qtclass的`POST /admin/courses/courseverison` API上传数据；考虑到实际上还需要调用qtuser的鉴权API验证鉴权Token，实际上一个请求需要调用三个微服务。

考虑到我们在不同的环境需要使用不同的主机，可以使用的几种写法包括：

1. 判断环境标识并给出对应的主机名，也就是把链接写在setting配置文件或者代码里。最简单也最不容易更变的方法。
2. 通过环境变量传入。这样增加很多环境变量的配置负担。
3. 通过网格的服务发现API获取。这样我们只需要合理标记服务名称及其API的名称即可。

Django提供了Site框架（https://docs.djangoproject.com/zh-hans/4.0/ref/contrib/sites/），提供了两个类Site和RequestSite，前者把网站存在数据库，后者不存数据库。因此，我们可以实现一个RequestSite的子类，比如PolarisRequestSite。

如果使用服务发现，则首先需要使用服务注册。目前我暂时采取了手动策略，后续需要把注册功能增加到Django框架，我暂时还没有想好怎么实现，可能的方案是实现一个Django CLI。
