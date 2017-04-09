## ReactPropTypes.js

```
var productionTypeChecker = function() {
  // ...
};
productionTypeChecker.isRequired = productionTypeChecker;
var getProductionTypeChecker = () => productionTypeChecker;
ReactPropTypes = {
  array: productionTypeChecker,
  // ...
  arrayOf: getProductionTypeChecker,
}
```

以上源码，我们看到`productionTypeChecker`和`productionTypeChecker.isRequired`复用了同一个函数体。

所以，当我们在写`const propTypes = {data: PropTypes.array}`或者`const propTypes = {data: PropTypes.array.isRequired}`的时候，`propTypes.data`是一个typeChecker函数。这样，循环一遍组件的props，就可以根据每一个prop的名子，找到对应的typeChecker，以value作参调用typeChecker，就可以达到检验的目的。

---

```
if (Array.isArray(propValue)) {
  return propValue.every(isNode);
}
```
