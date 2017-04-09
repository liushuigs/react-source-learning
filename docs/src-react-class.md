## ReactClass.js
源文件: [https://github.com/facebook/react/blob/master/src/isomorphic/classic/class/ReactClass.js](https://github.com/facebook/react/blob/master/src/isomorphic/classic/class/ReactClass.js)

**2017.4.9** 更新 自`15.5`版本开始，`createClass`将不再包含到react核心库，而需要从一个独立的npm package - [create-react-class](https://www.npmjs.com/package/create-react-class)获取。[更新说明](https://facebook.github.io/react/blog/2017/04/07/react-v15.5.0.html#migrating-from-react.createclass)

我们最常用的React.createClass方法就是在这个文件中定义的（JSX代码最终都要转换成createClass这种形式的）。这也是我阅读react源码过程中，打算详细解读的第一个文件。下面，我将从上到下挑选出能给自己一些启发的源码做注解。有些代码，已有注释，就不在这里粘贴了。包含`__DEV__`的代码段，也暂且忽略。

```
'use strict';
```

`'use strict';`是[es5](http://www.ecma-international.org/publications/standards/Ecma-262.htm)语法中用来定义[严格模式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)的。顾名思议，严格模式使得js引擎校验js语法的时候，标准更加严格。一些在非严格模式下被忽略的非友好的语法，在严格模式下会直接报错。所以，总是使用严格模式，使得js代码更加安全。这是一个好习惯。

---

```
var ReactBaseClasses = require('ReactBaseClasses');
var ReactElement = require('ReactElement');
var ReactNoopUpdateQueue = require('ReactNoopUpdateQueue');
```

`ReactBaseClasses`定义了`React.Component`和`React.PureComponent`两个类，此处不展开。

`ReactElement`定义了一些操作React Element的方法，比如`createEelment`,`cloneElement`,`createFactory`等

`ReactNoopUpdateQueue`是默认的updater对象。当我们`setState`的时候，就会马上调用updater.enqueueReplaceState方法，将新的state放入一个队列中，异步处理。

---

```
var emptyObject = require('fbjs/lib/emptyObject');
var invariant = require('fbjs/lib/invariant');
var warning = require('fbjs/lib/warning');
```

[fbjs](https://github.com/facebook/fbjs/)是一个工具集，是独立于react的另一个项目，被facebook不同项目所共享，如[relay](https://github.com/facebook/relay/blob/master/package.json)。

`invariant`用在一些强制条件不被满足的地方，比如变量类型用错。`invariant`在react项目中大量地使用，必须要细看一下。找到`invariant`文件的[位置](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/__forks__/invariant.js)。继续只关注值得学习的地方。

```
var args = [a, b, c, d, e, f];
var argIndex = 0;
error = new Error(
	format.replace(/%s/g, function() { return args[argIndex++]; })
);
```
get新技能，原来string的replace方法，是可以递归调用的。

`warning`和`invariant`类似，也会在浏览器中输出红色的提示，只是`warning`输出的是警告信息，消息体中带有warning字样。`warning`所在的文件[位置](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/__forks__/warning.js)。

---

```
type SpecPolicy =
| 'DEFINE_ONCE'
| 'DEFINE_MANY'
| 'OVERRIDE_BASE'
| 'DEFINE_MANY_MERGED';

// ...
/**
 * @interface ReactClassInterface
 * @internal
 */
var ReactClassInterface: {[key: string]: SpecPolicy} = {
	mixins: 'DEFINE_MANY',
	// ...
}

```

要理解上面一段代码，就必须要了解react所用的类型系统[Flow](docs/what-is-flow.md)了。

`type SpecPolicy`定义了一个枚举类型。

注释中的`@interface`让我误以为`ReactClassInterface`是一个接口类型，**其实不然**，查flow[文档](https://flow.org/en/docs/types/objects/)，才确定其为Object Type。它规定这个object中的每个key必须是一个string，值必须是`SpecPolicy`类型。`[key: string]`中的`key`本身只是种注解而已，为了方便代码的阅读，没有实际的意义，你可以把它改为`name`等任何字符串。

---

```
// ... 省略了很多代码
var ReactClassComponent = function() {};
Object.assign(
  ReactClassComponent.prototype,
  ReactComponent.prototype,
  ReactClassMixin,
);

var ReactClass = {
	createClass: function(spec) {
		var Constructor = identity(function(props, context, updater) {
			this.props = props;
			this.context = context;
			this.refs = emptyObject;
			// ...
		};
		Constructor.prototype = new ReactClassComponent();
		Constructor.prototype.constructor = Constructor;
		
		// ...
		
		mixSpecIntoComponent(Constructor, spec);
		
		// ...
		return Constructor;
	}
}
```

通过这段精简的`createClass`，我们看到，`createClass`，返回的是一个构造函数，该函数原型继承于`ReactClassComponent`，并将参数`spec`中定义的属性合并到其prototype中。如果结合`createClass`的一个实例来看个文件，就更容易理解了。比如：

```
const Contacts = React.createClass({
	propTypes: {
	
	},
	getDefaultProps() {
		return {
		  
		};
	},
	render() {
		return (
		  <div></div>
		);
	}
});
```
基中，`Contacts`就是一个函数，一个构造函数。可以用来生成`React.Component`。

至此，这个800多行的`React.createClass`核心代码，就理顺了。