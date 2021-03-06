# talentui-dev-server

## 问题是什么

当进行前端开发的时候，前端在开发过程中的流程一般是这样的。

* 首先打开线上任何一个环境，（开发，测试，线上）
* 把前端资源服务器地址指向本地 （配host, 比如stnew03.beisen.com 127.0.01）
* 启动项目的dev server，监测项目文件变化 ，然后提供静态资源服务，这里会依赖一个类似webpack-dev-server这样的微型web server, 端口常用的是3000，8000，8080等。 （启动项目的开发环境）
* 安装IIS（windows）或者nginx(unix)的等web server，配置反向代理，这时启动的是80端口，对应线上环境的请求的端口。因为配置了host所有对应的请求会请求到web server上。(配置反向代理服务器)
* 在web server上对请求文件的路径进行解析，重写(rewrite) 比如，我请求一个stnew03.beisen.com/xxx/xxx/func.min.x39fd0fdfd.js 返回给我的是重新请求127.0.0.1:3000/dist/func.js文件 （url rewrite）
* 这时基本流程已经完成，打开线上环境，会发现请求真实返回结果是通过第3步服务提供的文件。此时本地开发环境配置基本完成。

这时有几个问题，

1. 首先这个流程很麻烦，
2. 需要的专业外的知识比较多，有的时候只能照着做不知道为啥
3. 对环境依赖比较严重，换台电脑就，或者重装系统就得再来一遍，还不一定能成功。
4. 如果项目换了，规则就需要调整，配置文件在哪，应该改哪里，这是个问题

## 反正就是不方便

那应该如何简化这个流程呢。

首先我们要看，整个流程中哪些是可以优化的，创建启动本地项目很简单，开发，测试环境的准备不需要前端怎么操作，**比较麻烦的是配置nginx 或者IIS代理的过程。**

* 第一个比较麻烦的，就是安装，安装IIS或者nginx首先是相当前端专业知识之外的东西，我记得我入职的时候就是照着文档一步一步搞的。有的时候照着文档做完了，中间步骤出了错，完全不知道怎么处理，还得去问其他同时，费时费力。
* 第二个比较麻烦的就是，一但开了代理，还得配置host，把比如xxx.com指向127.0.0.1, 这样一来所有的请求才能到达代理服务器，然后转发到本地项目上。当你不需要的时候还得记得关掉host，虽然有switchhost，还得请dns缓存
* 开启了代理和配置了host之后，来回切切也没啥，突然你发现一个问题，就是访问的页面是个iframe,一个产品的页面嵌入了另外一个产品的页面，这是两个前端的项目，而你本地只启了其中一个，另外一个因为没有资源服务文件全部404了，导致啥也看不着。不得己只能换域名了。
* 我可能只需要替换一个文件，nginx不合适，又得使用charles或者fiddler，但是mac下收费...，还得破解，其实我们的需求并不复杂，没那个必要啊。
* 哎呀，我要去另外一个项目去支援两天，上面的步骤还得再来一遍......

所以，我们需要一个专门满足我开发需求的东西，不需要复杂的配置，不需要太重，切换起来要方便，不需要来回切host，不需要匹配的文件可以直接走线上，调试起来方便。

这些问题已困绕很久了...直到有一天，我们决定改变他。

首先，我们要清楚流程是什么，请看图。
