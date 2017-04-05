## Eslint简介
[eslint](http://eslint.org/)是一个被广泛应用的javascript/jsx代码检查工具。

eslint配置文件可以使用js/yaml/json格式，或者在package.json中添加"eslintConfig"选项。.eslintrc已被废弃。js格式更为灵活，react项目用的是js后缀，即.eslintrc.js。

### extends

`extends`是指扩展eslint官方支持的lint规则

```
extends: 'fbjs',
```

`fbjs`是`eslint-config-fbjs`和简写，对应npm模块[eslint-config-fbjs](https://www.npmjs.com/package/eslint-config-fbjs)。从这个配置中，我们可以看到react没有使用eslint默认的解析器`Espree`，而是选择了`babel-eslint`。为什么呢？看`babel-eslint`官方介绍，当且仅当你使用了强类型如(Flow)或者一些eslint不支持的仍处于实验阶段的js特性时，你才需要使用`babel-eslint`。

> You only need to use babel-eslint if you are using types (Flow) or experimental features not supported in ESLint itself yet. Otherwise try the default parser (you don't have to use it just because you are using Babel)

### plugins

`plugins`是指通过自定义插件，扩展可用的lint规则。比如eslint-react插件，就自定义了跟jsx语法相关的lint规则。

```
plugins: [
	'react',
	'react-internal',
],
```

上面的`react`是`eslint-plugin-react`的简称。`react-internal`是`eslint-plugin-react-internal`的简称。




