## react源码解析 - Intro

> A declarative, efficient, and flexible JavaScript library for building user interfaces.

declarative：声明式。React将此作为第一个词，可见其重要性。declarative对应imperative（命令式），移步[这里](http://www.redotheweb.com/2015/09/18/declarative-imperative-js.html)看看什么是 declarative Javascript.

对于react，declarative的含义是：declarative UI.
> Declarative views make your code more predictable, simpler to understand, and easier to debug.

这说的是JSX语法，能够在JS里面直接写html和css，是一次大胆的创新。JSX让人写前端代码写得直呼过瘾。

> React is flexible and can be used in a variety of projects. You can create new apps with it, but you can also gradually introduce it into an existing codebase without doing a rewrite.

你没有必要非在整个网页或者整个项目中使用react。你可以只在一个dom节点中使用，然后慢慢地去做重构。所以，使用react和使用jquery,angular并不冲突。