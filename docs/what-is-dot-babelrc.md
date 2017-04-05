# What is .babelrc

## babel简介

`.babelrc`是[babel的配置文件](https://babeljs.io/docs/usage/babelrc/)，使用[json5](https://github.com/json5/json5)的语法。babel是用来将es6的js转成es5的工具。而json5是json格式的一种扩展，支持在json文件中写注释，使用尾部逗号，不需在key上加双引号等。例如：

```
{
    foo: 'bar',
    while: true,

    this: 'is a \
multi-line string',

    // this is an inline comment
    here: 'is another', // inline comment

    /* this is a block comment
       that continues on another line */

    hex: 0xDEADbeef,
    half: .5,
    delta: +10,
    to: Infinity,   // and beyond!

    finally: 'a trailing comma',
    oh: [
        "we shouldn't forget",
        'arrays can have',
        'trailing commas too',
    ],
}
```

`.babelrc`文件的查询顺序

1. 查询当前目录下是否有`.babelrc`文件
2. 查询`package.json`文件是否有`"babel": {}`这样的选项

`.babelrc`的详细参数，可以查看[这里](https://babeljs.io/docs/usage/api/#options)。咱只讨论一下react用的几个参数。

## presets
presets是指一组babel插件。目前官方提供这些presets，

```
env
es2015
es2016
es2017
latest (deprecated in favor of env)
react
flow
```
可以看到,[react preset](http://babeljs.io/docs/plugins/preset-react/)是被官方支持的。当然，你也可以创建自己的presets。

## ignore
igore中的`third-party`是指这个文件:`src/__mocks__/vendor/third_party/WebComponents.js`。但是react为什么会将WebComponents.js文件放到项目中呢？（TODO）

## plugins
`presets`没有覆盖到的plugin，就需要写入`plugins`配置。react用到了很多plugins，我们大概看看都是用来干什么的？

1. [transform-class-properties](https://babeljs.io/docs/plugins/transform-class-properties/) 将es6的class属性转成es5属性
2. [syntax-trailing-function-commas](http://babeljs.io/docs/plugins/syntax-trailing-function-commas/) 移除函数尾部的逗号
3. [transform-object-rest-spread](http://babeljs.io/docs/plugins/transform-object-rest-spread/) 

	```
	let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
	console.log(x); // 1
	console.log(y); // 2
	console.log(z); // { a: 3, b: 4 }
	```
	```
	let n = { x, y, ...z };
	console.log(n); // { x: 1, y: 2, a: 3, b: 4 }
	```
4. [transform-es2015-template-literals](http://babeljs.io/docs/plugins/transform-es2015-template-literals/) 编译es6模板
	```
	`foo${bar}`; => 	"foo" + bar;
	```
	
5. [transform-es2015-literals](http://babeljs.io/docs/plugins/transform-es2015-literals/)

	```
	var b = 0b11; // binary integer literal
	var o = 0o7; // octal integer literal
	const u = 'Hello\u{000A}\u{0009}!'; // unicode string literals, newline and tab
	```
	```
	var b = 3; // binary integer literal
	var o = 7; // octal integer literal
	const u = 'Hello\n\t!'; // unicode string literals, newline and tab
	```

6. [transform-es2015-arrow-functions](http://babeljs.io/docs/plugins/transform-es2015-arrow-functions/) 编译箭头函数
7. [transform-es2015-block-scoped-functions](http://babeljs.io/docs/plugins/transform-es2015-block-scoped-functions/)

	```
	if (x) {
	    function fn() {
	        // Do stuff
	    }
	    someObj.method = fn;
	}
	console.log(fn); // ReferenceError: fn is not defined
	```
8. [transform-es2015-classes](https://babeljs.io/docs/plugins/transform-es2015-classes/)
9. [transform-es2015-object-super](http://babeljs.io/docs/plugins/transform-es2015-object-super/)
10. [transform-es2015-shorthand-properties](http://babeljs.io/docs/plugins/transform-es2015-shorthand-properties/)

	```
	var o = { a, b, c };
	```
	```
	var o = { a: a, b: b, c: c };
	```
	
11. [transform-es2015-computed-properties](http://babeljs.io/docs/plugins/transform-es2015-computed-properties/)
	
	```
	var obj = {
	  ["x" + foo]: "heh",
	  ["y" + bar]: "noo",
	  foo: "foo",
	  bar: "bar"
	};
	```
12. [transform-es2015-for-of](http://babeljs.io/docs/plugins/transform-es2015-for-of/) 关于for-of，可以查看[更多](http://www.benmvp.com/learning-es6-for-of-loop/)。
13.  [check-es2015-constants](http://babeljs.io/docs/plugins/check-es2015-constants/) 此plugin只是对const进行检查，如果要将const转成var，还需要和上面的`transform-es2015-block-scoped-functions` plugin配合使用。
14.  [transform-es2015-spread](http://babeljs.io/docs/plugins/transform-es2015-spread/) In loose mode, all iterables are assumed to be arrays.(TODO know more about es6 iterables)

	```
	var a = ['a', 'b', 'c'];
	var b = [...a, 'foo'];
	
	var c = { foo: 'bar', baz: 42 };
	var d = {...c, a: 2};
	```
	
15. [transform-es2015-parameters](http://babeljs.io/docs/plugins/transform-es2015-parameters/)
16. [transform-es2015-destructuring](http://babeljs.io/docs/plugins/transform-es2015-destructuring/)
17. [transform-es2015-block-scoping](http://babeljs.io/docs/plugins/transform-es2015-block-scoping/) 将`const`和`let`转成es5
18. [transform-es2015-modules-commonjs](http://babeljs.io/docs/plugins/transform-es2015-modules-commonjs/)
	
	```
	export default 42;
	```
	```
	Object.defineProperty(exports, "__esModule", {
	  value: true
	});
	
	exports.default = 42;
	```
	
19. [transform-es3-member-expression-literals](http://babeljs.io/docs/plugins/transform-es3-member-expression-literals/) 在使用保留关键字作为属性名的时候，此plugin会给它加上双引号。
	```
	foo.catch;
	```
	```
	foo["catch"];
	```

20. [transform-es3-property-literals](http://babeljs.io/docs/plugins/transform-es3-property-literals/)
21. `./scripts/babel/transform-object-assign-require` 使用`Object.assign`时自动require('object-assign')
22. [transform-react-jsx-source](http://babeljs.io/docs/plugins/transform-react-jsx-source/) Adds source file and line number to JSX elements.