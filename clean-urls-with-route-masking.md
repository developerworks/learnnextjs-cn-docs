# 使用路由掩码创建干净的URL

在前面的课程中, 我们学到了如何使用查询串创建动态页面. 一次为基础, 我们一篇博客的链接会想这样: [http://localhost:3000/post?title=Hello%20Next.js](http://localhost:3000/post?title=Hello%20Next.js)

但是这个URL看起来不怎么好看, 如果我们想要下面这样的链接, 我们应该怎么办呢?

[http://localhost:3000/p/hello-nextjs](http://localhost:3000/p/hello-nextjs)

![Clean url](https://cloud.githubusercontent.com/assets/50838/24586820/b65be244-17c6-11e7-87fd-d6880152261e.png)

看起来是不是好很多了, 是吧?

## 设置

为了按照本课程学习, 需要有一个示例Next.js应用程序, 为此, 你可以下载下面的这个应用程序作为学习案例:

```shell
git clone https://github.com/arunoda/learnnextjs-demo.git
cd learnnextjs-demo
git checkout create-dynamic-pages
```

可以用下面的命令来运行:

```shell
npm install
npm run dev
```

现在, 访问 [http://localhost:3000/](http://localhost:3000/).

## 路由掩码

现在,我们将使用Next.js特有的叫做路由掩码的功能. 基本上, 它在浏览器地址栏上显示一个不同于实际URL的地址.

现在, 我们来给我们的博客地址添加一个路由掩码.

`pages/index.js` 的内容修改为如下:

```jsx
import Layout from '../components/MyLayout.js'
import Link from 'next/link'

const PostLink = (props) => (
  <li>
    <Link as={`/p/${props.id}`} href={`/post?title=${props.title}`}>
      <a>{props.title}</a>
    </Link>
  </li>
)

export default () => (
  <Layout>
    <h1>My Blog</h1>
    <ul>
      <PostLink id="hello-nextjs" title="Hello Next.js"/>
      <PostLink id="learn-nextjs" title="Learn Next.js is awesome"/>
      <PostLink id="deploy-nextjs" title="Deploy apps with Zeit"/>
    </ul>
  </Layout>
)
```

看一下下面的这个代码块:

```jsx
const PostLink = (props) => (
  <li>
    <Link as={`/p/${props.id}`} href={`/post?title=${props.title}`}>
      <a>{props.title}</a>
    </Link>
  </li>
)
```

在 `<Link>` 元素中, 我们使用另外一个叫做 `as` 的熟悉. 这是我们需要显示在浏览器地址栏中的URL. 应用程序看到的实际URL是实际上是在 `href` 熟悉中.

现在点击博客标题链接, 进入博客内容页.

## 历史感知

正如你所看到的, 路由掩码, 也能够很好的和浏览器历史一起工作. 所有需要做的事情只是给链接添加了一个`as`属性.

## 重加载

现在, 转到主页: [http://localhost:3000/](http://localhost:3000/), 然后点击第一个博客标题, 你会被导航到博客内容页面.

![Navigate to blog content page](https://cloud.githubusercontent.com/assets/50838/24586820/b65be244-17c6-11e7-87fd-d6880152261e.png)

点击刷新按钮重新加载页面, 会发生什么?

## 404

页面刷新操作放回了一个 404 错误页面. 这是因为, 我们再服务器上没有一个这样的可加载页面. 服务器会试图去加载 `p/hello-next.js` 文件, 但是实际上这个文件是不存在的, 我们现在只有两个文件 `index.js` 和 `post.js`.

因此, 我们需要解决这个问题.

在下一节课中, 我们使用Next.js的[自定义服务器API](https://github.com/zeit/next.js#custom-server-and-routing)来解决这个问题.
