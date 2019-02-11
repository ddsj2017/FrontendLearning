# 详解Object.create(null)
## 问题
* 在Vue和Vuex的源码中，初始化一个新对象为什么使用`Object.create(null)`而不是`{}`？
## `Object.create()`的定义
* `Object.create(proto, [propertiesObject])`
    * `proto`：新创建对象的原型对象
    * `propertiesObject`：可选。要添加到新对象的**可枚举**（新添加的属性是其自身的属性，而不是其原型链上的属性）的属性。
	> [示例1](./1.html)
## `Object.create(null)`和`{…}`的区别
* `{}`新创建的对象继承了`Object`自身的方法，如`hasOwnProperty`、`toString`等，在新对象上可以直接使用。
* `Object.create(null)`新创建的对象除自身属性外，原型链上没有任何属性，，也就是没有继承Object的任何东西，此时如果我们调用`toString()`会报`Uncaught TypeError`的错误。
  * 第一个参数设为`null` → `null`设置成了新创建对象的原型，自然就不会有原型链上的属性。
	> [示例2](./2.html)
## `Object.create(null)`的使用场景
* 使用`create`创建的对象，没有任何属性，显示`No properties`，我们可以把它当作一个非常纯净的`map`来使用，我们可以自己定义`hasOwnProperty`、`toString`方法，不管是有意还是不小心，我们完全不必担心会将原型链上的同名方法覆盖掉。
	```javascript
	// Demo1:
	var a = {...省略很多属性和方法...};
	// 如果想要检查a是否存在一个名为toString的属性，你必须像下面这样进行检查：
	if (Object.prototype.hasOwnProperty.call(a, 'toString')) {
		...
	}
	// 为什么不能直接用a.hasOwnProperty('toString')？因为你可能给a添加了一个自定义的hasOwnProperty
	// 你无法使用下面这种方式来进行判断，因为原型上的toString方法是存在的：
	if (a.toString) {}

	// Demo2:
	var a = Object.create(null)
	// 你可以直接使用下面这种方式判断，因为存在的属性，都将定义在a上面，除非手动指定原型：
	if (a.toString) {}
	```
* 在我们使用`for..in`循环的时候会遍历对象原型链上的属性，使用`create(null)`就不必再对属性进行检查了，当然，我们也可以直接使用`Object.keys()`。
## 总结
* 需要一个非常干净且高度可定制的对象当作数据字典的时候；
* 想节省hasOwnProperty带来的一丢丢性能损失并且可以偷懒少些一点代码的时候
* 用`Object.create(null)`。
## 参考文章
* [详解Object.create(null) - 掘金 作者：司想君](https://juejin.im/post/5acd8ced6fb9a028d444ee4e)