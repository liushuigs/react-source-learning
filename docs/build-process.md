## 构建过程
先从`package.json`中找到`build`命令为`npm run version-check && node scripts/rollup/build.js`，可见`react`使用`rollup`来打包。

`scripts/rollup/bundles.js`文件中

```
Isomorphic的入口文件为: src/umd/ReactUMDEntry.js，将打包成react.js
React DOM的入口文件为：src/umd/ReactDOMUMDEntry.js，将打包成reac-dom.js
React DOM Server的入口文件为：src/umd/ReactDOMServerUMDEntry.js
```

`npm run build`在`build/dist`目录中生成以文件

```
react-art.development.js
react-art.production.min.js
react-dom-server.development.js
react-dom-server.production.min.js
react-dom.development.js
react-dom.production.min.js
react.development.js
react.production.min.js
```
> 使用 `ll -1 -h build/dist`拿到上面的列表


