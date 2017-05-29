# 干净URL的服务器支持

在前面的课程中, 我们学习了如何为我们的应用程序创建干净的URL. 基本上, 我们让URL像这样:

[http://localhost:3000/p/my-blog-post](http://localhost:3000/p/my-blog-post)

但是, 它现在只能用于客户端导航. 当我们重新加载页面的时候, 它给了我们一个404错误页面.

这是因为, 在`pages`目录中, 并不存在一个实际的`p/my-blog-post`模块文件.

![404 Page](https://cloud.githubusercontent.com/assets/50838/24919433/475417bc-1f01-11e7-9fae-a17030d3d051.png)

我们使用Next.js的[自定义服务器API](https://github.com/zeit/next.js#custom-server-and-routing)来解决这个问题, 让我们看看如何使用它?

## 设置

首先, 我们需下载示例代码:

```shell
git clone https://github.com/arunoda/learnnextjs-demo.git
cd learnnextjs-demo
git checkout clean-urls
```

然后通过下面的命令运行我们的示例程序:

```shell
npm install
npm run dev
```

最后通过 [http://localhost:3000](http://localhost:3000) 访问Web应用程序.

## 创建自定义服务器

我们使用 [Express](https://expressjs.com/) 来创建一个自定义服务器. 它非常简单:

首先, 添加 Express 依赖到应用程序:

```shell
npm install --save express
```

然后创建一个 `server.js` 文件, 并添加下面的内容:

```js
const express = require('express')
const next = require('next')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare()
.then(() => {
  const server = express()

  server.get('*', (req, res) => {
    return handle(req, res)
  })

  server.listen(3000, (err) => {
    if (err) throw err
    console.log('> Ready on http://localhost:3000')
  })
})
.catch((ex) => {
  console.error(ex.stack)
  process.exit(1)
})
```

现在, 更新NPM的开发脚本为:

```json
{
  "scripts": {
    "dev": "node server.js"
  }
}
```

然后, 运行`npm run dev`, 你会看到什么?

## 创建自定义路由

现在你所看到的, Next.js 可以和 Express 一起协同工作. 现在我们要添加一个自定义的路由来匹配博客的URL.

用新的路由, `server.js` 看起来就是这个样子:

```js
const express = require('express')
const next = require('next')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare()
.then(() => {
  const server = express()

  server.get('/p/:id', (req, res) => {
    const actualPage = '/post'
    const queryParams = { title: req.params.id }
    app.render(req, res, actualPage, queryParams)
  })

  server.get('*', (req, res) => {
    return handle(req, res)
  })

  server.listen(3000, (err) => {
    if (err) throw err
    console.log('> Ready on http://localhost:3000')
  })
})
.catch((ex) => {
  console.error(ex.stack)
  process.exit(1)
})
```

注意一下代码块:

```js
server.get('/p/:id', (req, res) => {
    const actualPage = '/post'
    const queryParams = { title: req.params.id }
    app.render(req, res, actualPage, queryParams)
})
```

我们简单的映射了一个自定义路由到我们现有的博客页面 `post`. 同时我们还映射了查询串.

现在重启应用程序, 访问下面的地址:

[http://localhost:3000/p/hello-nextjs](http://localhost:3000/p/hello-nextjs)

现在, 不会再显示 404 页面了, 因为, 通过自定义路由, 我们把通过浏览器访问的地址映射到了实际的页面, 但是现在还有一个小问题, 你能看出来么?

## URL信息

我们的 `/post` 页面通过查询串参数 `title` 来接收标题. 在客户端路由中, 我们可以通过 Link 的 `as` 属性设置正确的值.

```jsx
<Link as={`/p/${props.id}`} href={`/post?title=${props.title}`}>
  <a>{props.title}</a>
</Link>
```

但是在服务器路由中, 我们得不到这个title, 因为我们只有一个URL中博客的ID, 因此我们使用这个ID作为服务器端查询串参数.

你可以通过如下的路由定义看到:

```js
server.get('/p/:id', (req, res) => {
  const actualPage = '/post'
  const queryParams = { title: req.params.id }
  app.render(req, res, actualPage, queryParams)
})
```

这就是问题. 但是在实际应用中, 这实际上不是一个问题, 因为, 通常我们在服务器端和客户端都通过ID从数据服务器获取数据. 因此实际上, 我们只需要一个ID.

## 最后

现在我们通过 Next.js 自定义服务器API 实现了一个简单的应用程序. 以此为基础, 我们添加了干净URL的服务器端支持. 就像这样, 你可以创建更多你想要的路由.

对于Web服务器, 不限于使用 [Express](https://expressjs.com/) 作为服务器, 你可以使用任何 Node.js Web框架. 对于 自定义服务器API的想信息文档, 参考 [定义服务器API文档](https://github.com/zeit/next.js#custom-server-and-routing)
