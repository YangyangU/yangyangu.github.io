---
title: 一文搞懂 HTTP 缓存
date: 2024-04-22 16:00:00
tags: [HTTP]
categories: HTTP
index_img: /img/http.png
banner_img: /img/default.png
---

> HTTP 缓存可谓前端工程师必备技能之一，在最近的面试中，也经常被问到，那么为什么要有 HTTP 缓存，以及它是如何工作的，下面咱们展开来好好聊聊。

还不知道什么是 HTTP？可以跳转到我的前一篇文章—— [捋一下 HTTP 发展史 - 掘金 (juejin.cn)](https://juejin.cn/post/7351700046486863881)

## 为什么要有 HTTP 缓存

缓存缓存，顾名思义就是将一些资源保存在本地，后续请求时就可以直接在本地读取，而不用请求服务器。使用 HTTP 缓存是提高性能优化和提升用户体验的关键技术，它主要体现在几个方面：

> 1.  **减少延迟**：当资源被缓存后，用户可以直接从浏览器缓存或更近的网络节点（如 CDN）获取数据，而不必每次都请求原始服务器。网络延迟和数据传输时间都大大缩短了。
> 1.  **减轻服务器负担**：通过缓存常用资源（如 Logo），服务器不必每次都处理相同的请求。
> 1.  **节约带宽**：缓存可以显著减少需要通过网络传输的数据量，降低用户和供应商的流量成本。
> 1.  **改善用户体验**：缓存使得网页、图片等内容的加载更快，从而直接提高了用户的浏览体验。
> 1.  **离线浏览**：HTTP 缓存在一些场景可以让用户在没有网络连接的情况下，仍然能够访问先前加载过的内容。（如网易云音乐可以离线听之前缓存过的歌）
> 1.  **负载均衡**：通过在多个地理位置缓存内容，避免所有请求都集中在一个服务器上。可以有效处理高流量和大规模分布的用户。

总之，HTTP 缓存是前端开发者必备一项至关重要的技能，话不多说，开始介绍！

## HTTP 缓存

HTTP 缓存是一种客户端缓存，当浏览器向服务器发送资源请求时，服务器可以在响应头中查看是否包含缓存相关的信息。浏览器依据这些信息缓存响应资源，再次请求时如果命中缓存则直接读取本地缓存资源，而不再发请求。HTTP 缓存分为**强缓存**和**协商缓存**。

### 强缓存

强缓存直接决定了浏览器是否需要向服务器发起请求，只需要设置缓存的过期时间。如果强缓存有效，浏览器将直接从本地缓存中读取资源，不会与服务器进行任何交互。

#### Cache-Control

`Cache-Control`是通用消息头字段，被用于在 HTTP 请求和响应中，通过指定指令来实现缓存机制，通过设置`max-age=xxx`来指定资源在缓存中可以存活的最大时间（单位是秒）。

举个栗子 🌰：

这里我们缓存一个图片资源，过期时间为 86400 秒即一天

```js
res.writeHead(200, {
  "Content-Type": mime.lookup(ext),
  "cache-control": "max-age=86400", //缓存一天
});
```

当我们第一次请求时，会正常发请求拿到资源

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ba6bdbf76f7f4142adf814f086a56034~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=998&h=281&s=22453&e=png&b=fdfdfd)

但当我们刷新页面再次发请求时发现，此时的履行者变为`memory cache`，大小和时间都变为 0，说明这次的资源并不是从服务器请求回来的，而是来自本地内存缓存。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/760581758f064a0ca785bd599eb88c2b~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=981&h=291&s=34416&e=png&b=fdfdfd)

并且我们可以在响应头中查看`Cache-Control`字段和我们设置的过期时间。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3ec48160b7fc4e3fb31dee812ba8e9d3~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=569&h=401&s=28906&e=png&b=fefdfd)

当然`Cache-Control`不止只允许我们设置`max-age`过期时间，还有其他字段，详情可参考 [Cache-Control - HTTP | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control#%E7%BC%93%E5%AD%98%E8%AF%B7%E6%B1%82%E6%8C%87%E4%BB%A4)

#### Expires

`Expires`响应头是一个具体的日期/时间，告诉浏览器在这个时间点之前不需要重新请求资源。

> ⚠️ 注意：如果响应中有指令为  `max-age`  或  `s-maxage`  的`Cache-Control`标头，则  `Expires`  标头会被忽略。

举个栗子 🌰：

```js
res.writeHead(200, {
  "Content-Type": mime.lookup(ext),
  Expires: "Mon Apr 22 2024 21:03:31 GMT+0800", //过期时间戳自己选择
});
```

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/38882c45d14349289b70dfb5937a5fc1~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=707&h=405&s=30659&e=png&b=fefefe)

与`Cache-Control`用法相似，就不过多赘述。

### 协商缓存

当强缓存过期或无效时，浏览器就会与服务器进行交互，通过发送带有特定验证头的请求来检查资源是否被修改。如果服务器确认缓存资源仍然是最新的，则返回 304 状态码，浏览器会从本地缓存加载资源。如果资源不是最新的，则返回 200 状态码和新的资源数据。

#### Last-Modified 和 If-Modified-Since

服务器在响应中提供`Last-Modified`日期，表明资源最后修改时间。浏览器在后续请求中使用`If-Modified-Since`头包含这个日期，如果服务器上的资源自那以后未修改，就会返回 304 状态码。

举个栗子 🌰：

```js
const stats = fs.statSync(filePath); //获取文件文件修改时的时间戳
const timeStamp = req.headers["if-modified-since"]; //获取请求头字段
let status = 200; //资源修改
if (timeStamp && Number(timeStamp) === stats.mtimeMs) {
  //时间戳不变
  status = 304; //资源未修改
}

res.writeHead(status, {
  "Content-Type": mime.lookup(ext),
  "last-modified": stats.mtimeMs, //时间戳
});
```

通过对比这次请求头的`if-modified-since`和响应头的`last-modified`判断资源是否修改

<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/494eff6380b14035b2c2b10872038d88~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=720&h=664&s=50192&e=png&b=fefefe" alt="image.png" width="70%" />

当资源未修改，返回 304 状态码，从缓存中拿资源。

现在我们试试手动修改资源，再次刷新页面：

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c995a290776440df8a579378f5f45268~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=712&h=667&s=50667&e=png&b=fefefe" alt="image.png" width="70%" />

资源修改，两次时间戳不同，返回 200 状态码，和新的资源。

#### ETag 和 If-None-Match

`ETag`是资源的唯一标识符，反映资源的内容状态。浏览器存储这个标识并在后续请求中通过`If-None-Match`头发送它。如果`ETag`未改变，服务器返回 304 状态码。

```js
if (req.headers["if-none-match"] === etag) {
  res.writeHead(304, {
    // 未修改，返回304
    "Content-Type": mime.lookup(ext),
    ETag: etag,
  });
  return res.end();
}

res.writeHead(200, {
  //修改，返回200
  "Content-Type": mime.lookup(ext),
  ETag: etag,
});
return res.end(content);
```

<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/76f551ed1d2c4a0b8e8bec1f166665ff~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=714&h=644&s=49796&e=png&b=fefefe" alt="image.png" width="70%" />

两种协商缓存的区别是一个比较时间戳一个比较哈希值。

## 总结

强缓存通过设置过期时间来控制资源的缓存；而协商缓存则是通过与服务器验证资源的状态来决定是否使用缓存。协商缓存可以避免强缓存的不足之处，比如当资源在有效期内但发生了变化时，协商缓存可以保证客户端获取到最新的资源。

最后配一张个人觉得总结十分到位的图（图片地址在文末）

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2c58c2a8b007459ebeecb8fd3950a09f~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=841&h=534&s=201743&e=png&b=eeeeee)

## 参考

- [HTTP 缓存 - HTTP | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching)
- [图解 HTTP 缓存 - 掘金 (juejin.cn)](https://juejin.cn/post/6844904153043435533?searchId=20240422101749A6A836A76242BD0217C2)
- [图片地址](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/10/171fea0fec0b4668~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.awebp)

## 最后

已将学习代码上传至 [github](https://github.com/YangyangU/CodeSpace)，欢迎大家学习指正！
