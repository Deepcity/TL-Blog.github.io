---
title: 为什么我用Typora——Typora与其他markdown的比较
date: 2023-08-13 15:20:34
tags: [Typora, Markdown, 随笔]
categories:
   - 软件
   - 编辑器
   - Markdown
---
## Typora

​	Typora是我一直在使用的markdown编辑器，对比起其他的markdown编辑器来说，==Obsidian==虽然有更加具有逻辑性的文档之间的双向链接和git式的设定，==Vscode==，==Sublime==则是Markdown插件构成的markdown编辑器，Typora更加纯粹和统一。具体表现在，它的使用逻辑非常简洁，仅仅只是文档编辑器，除了markdown的即时渲染之外，并没有过多独特的特性，在编辑上的极致优化使得它受众广泛。

<!--more-->

## Typora的Markdown语法

​	Typora中的Markdown语法分为严格和非严格两种语法格式，这里建议使用严格语法格式，提高文档的可移植性并且养成良好的习惯。

​	但由于各个平台之间的的Markdown语法标准并不统一，比较常见的有ZhihuMarkdown，CSDNMarkdown和GithubMarkdown，在不同的Markdown语法中，对文字的渲染有所不同，主要的差异集中在**Latex语法格式**。

​	因此，如果你有一份md文本，在release时tag自己的环境是一个不错的选择。

> ps: 根据互联网信息，Markdown语法截至24/7/21仍然未完成统一，这是一片未被规范化的蓝海，希望尽快规范化

<!--more-->

### 代码块语法

​	代码块几乎支持所有语言的高亮，并且支持对解释性语言的即时运行

### Html、js、css

​	Typora实际上是一个浏览器控制样式的文本编辑工具，因此在typora的配置文件中，可以通过修改css达到统一样式控制的效果，甚至可以全部用html编写实现一个web页面。

​	通过html也可以实现索引官方的方式是 `[这样]()`

## Typora中的Mermaid，Latex

​	Markdown语法格式中的两个接近标准化的工具Latex，Mermaid，前者侧重于学术理论，后者侧重于图像绘制、文档整理。Latex的格式渲染以及宏包功能更强大，Mermaid更利于编写。二者都具有强大的功能和实用性。个人认为MD文档的特色主要就在于即时渲染，索引结构、对图表绘制的支持。

​	但值得注意的是Mermaid中的Fontawesome 为v5版本，而latest version为v6，因此**Typora并未bundle Fontawesome**。因此无法使用fa: fa-xxx表示图标。

## Typora中的媒体

​	Typora支持插入图片，视频，音频。但总的来讲，对一个md文档，图片占用的频率是最大的。基于此，Typora对上传图片做了优化，基于Picgo和图床对图片进行了上传功能的实现，具体配置方法为：

1. 以ssms图床为例[Image Upload - SM.MS - Simple Free Image Hosting](https://sm.ms/)
2. 到ssms官网注册张航并拿到apikey
3. 填入picgo插件的配置文件
4. ![配置图](https://s2.loli.net/2024/07/21/JY243QnhxmFZLtR.png)

> ```
> {
>   "picBed": {
>     "current": "smms",
>     "smms": {
>       "token": "xxx"
>     }
>   },
>   "picgoPlugins": {}
> }
> ```

5. 测试并运行

## Typora的文档保存机制

​	当Typora保存一个文档时通常会在本地保存多个文档不同时间节点的副本，并建立可恢复的索引，这样的措施虽然占用了一些空间，但是从我自己体验的角度来说，他帮助我恢复了多个因各种原因丢失的文档，搭配everything使用，几乎可以避免掉盘之外的数据丢失

## Typora的个性化主题

​	Typora在Github上有许多用户自定义的主题，我个人简单使用的是Github的官方主题。

## 总结

​	对不需要太多功能的md文档编写具有很强的优势，如果不喜欢Obsidian的逻辑，不适应复杂的功能设计，可以使用Typora，它的程序设计逻辑与Word，Notebility很相似，最初拿来只做非常简单的工作，然后因为各种需求逐渐加深使用的功能。即使在程序设计的思想上没有使用如git，文件索引这种现代化的思想，但在编写上的巅峰造极也成就了Typora。

## 一些比较实用的不在Typora文档中的使用技巧

1. 通过html标签扩展typora的显示格式

   如`<details></details>`标签可以显示一个展开栏，做错题本时很好用

2. 一个很常见的Typora插件，更新了typora的显示格式，内置思维导图与类似python的格式提醒typora_plugin

   [obgnail/typora_plugin: Typora plugin. Feature enhancement tool | Typora 插件，功能增强工具 (github.com)](https://github.com/obgnail/typora_plugin)

---

待更新