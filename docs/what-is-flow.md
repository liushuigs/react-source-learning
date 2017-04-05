## Flow 简介
[flow](https://flow.org/en/docs/)是facebook推出的js类型检查工具。js是一门弱类型语言，没有从语言层面去保证变量类型不匹配的基本错误。flow使得咱们可以指定变量的类型，避免此类错误的发生。

```
// @flow
function square(n: number): number {
  return n * n;
}

square("2", "2"); // Error!
```
> 如图，使用flow之后，square函数的参数和返回值，都可以指定类型。当你在代码中写square('2', '2')的时候，用`flow`命令一检查，不需要看业务逻辑，就知道调用的参数有错误。

### flow入门

1. 新建项目，初始化flow配置

	```
	mkdir flow-demo && cd flow-demo && mkdir src && mkdir lib
	```

2. 设置编译器

	```
	yarn add --dev babel-cli babel-preset-flow
	```
	> 此时会生成`package.json`和`yarn.lock`文件。`yarn`是`npm`的替代品，可以加速npm模块的安装，而且能兼容大多数的npm命令。`yarn`的官网移步[这里](https://yarnpkg.com/en/docs/cli/)。
	
	然后在根目录新建一个`.babelrc`文件，并配置flow作为presets
	
	```
	{
	  "presets": ["flow"]
	}
	```
	> 其中`"flow"`就是指刚才安装的`babel-preset-flow`的简写，省略了`babel-preset-`。
	
	此时，你可以用以下命令来做代码编译：
	
	```
	yarn run babel src/ -D lib/
	```
	
	当然，也可以将这个命令配置到`package.json`文件中：
	
	```
	{
	  "name": "flow-demo",
	  "main": "lib/index.js",
	  "scripts": {
	    "build": "babel src/ -D lib/",
	    "prepublish": "yarn run build"
	  }
	}
	```

3. 设置flow
	
	推荐将flow安装到当前项目目录，而不是全局安装。
	
	```
	yarn add --dev flow-bin
	```
	> 此时
	
	运行`yarn run flow init`生成配置文件`.flowconfig`	
	运行`yarn run flow`进行代码检查
	
	> 运行上述代码时会生成一个可复用的后台进程，以加快后续代码检查的速度。
	
	> 注意上面两命令都带上`yarn run flow`，而不是直接运行`flow`。区别是`yarn run flow`调用了本项目中`flow-bin`的flow命令。
	
	> 另外，npm上有一个[flow](https://www.npmjs.com/package/flow)，和这里面的flow是完全不相关的，不能混淆。
	
	停用flow后台进程，使用`flow stop`
	
以上示例的[源码](https://github.com/liushuigs/react-source-learning/examples/flow-demo)。
	
### flow配置文件.flowconfig
这部分将结合react的[.flowconfig](https://github.com/facebook/react/blob/master/.flowconfig)和[flow官方文档](https://flow.org/en/docs/config/)进行解析。

flowconfig大概遵循INI文档格式。一个`.flowconfig`文件，可以包含下以五个部分：

	[include]
	[ignore] 用正则表达式匹配文件或目录，满足条件的将被flow忽略；<PROJECT_ROOT>表示项目根目录
	[libs]
	[options]
	[version]

#### [ignore]
```
[ignore]
# Ignore Docs
<PROJECT_ROOT>/docs/.*
<PROJECT_ROOT>/.*/docs/.*
```
> react的ignore部分，都使用了<PROJECT_ROOT>这种绝对路径的写法。在配置中使用注释，可以在行首加`#`号。

```
[ignore]
<PROJECT_ROOT>/.*/node_modules/y18n/.*
```

> 不清楚为什么react只将`node_modules`下的`y18n`忽略，而不是将整个`node_modules`目录忽略？（TODO）

#### [libs]

```
[libs]
./node_modules/fbjs/flow/lib/dev.js
./flow
```

> [dev.js](https://github.com/facebook/fbjs/blob/master/packages/fbjs/flow/lib/dev.js)只有一行代码：`declare var __DEV__: boolean;`， react跨项目引用一行代码可见flow项目简直就是应react而生。

说到[libs]，就必须要说一说flow的一个重要概念：`flow library definition`。一个flow library definition文件（简称libdef），就类似于C++中的头文件，是用来定义跨项目可用的全局变量。可以定义全局的Function/Class/Variables/Type/Module。详见flow[文档](https://flow.org/en/docs/libdefs/creation/)。

> TODO: flow为什么要支持libdef？又是如何使用libdef文件的？

### [options]

```
[options]
# 可选项node|haste，haste已不再被维护，可react还在用
module.system=haste

# 允许在class中使用static关键字
esproposal.class_static_fields=enable
# 允许在class中使用instance关键字
esproposal.class_instance_fields=enable

# 不允许在class中使用下划线作为私有函数
munge_underscores=false

# 约束的类型，可以用来代替TODO
suppress_type=$FlowFixMe
# 这个正则是什么含义？TODO
suppress_comment=\\(.\\|\n\\)*\\$FlowFixMe\\($\\|[^(]\\|(\\(>=0\\.\\(3[0-3]\\|[1-2][0-9]\\|[0-9]\\).[0-9]\\)? *\\(site=[a-z,_]*www[a-z,_]*\\)?)\\)

```

### [version]

```
[version]
^0.41.0
```

此version是指`flow-bin`的版本，可以用`yarn run flow version`查看。