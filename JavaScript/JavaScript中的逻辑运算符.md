> && 和 || 运算符的返回值并不一定是布尔类型，而是两个操作数其中的一个的值。 --ES5规范11.11节

在 JavaScript 中，`||` 和 `&&` 的返回值并不是布尔值。

它们的返回值是两个操作数中的一个（仅且一个），即选择两个操作数中的一个，然后返回他的值。

例如：

```javascript
var a = 42;
var b = 'abc';
var c = null;

a || b; // 42
a && b; // 'abc'

c || b; // 'abc'
c && b; // null
```

在C和PHP中，上例的结果是 `true` 或 `false`。在 JavaScript（以及 Python 和 Ruby） 中却是某个操作数的值。

|| 和 && 首先会对第一个操作数执行条件判断，如果其不是布尔值，先进行 ToBoolean 强制类型转换，然后再执行条件判断。

对于 || 来说，如果条件判断结果为 true，就返回第一个操作数的值，如果为 false 就返回第二个操作数的值。

&& 则相反，如果条件判断结果为 false，就返回第二个操作数的值，如果为 true 就返回第一个操作数的值。

换一个角度来理解：

```javascript
a || b; 
// 大致相当于
a ? a : b;

a && b;
// 大致相当于
b ? a : b;
```

之所以说 `a || b` 与 `a ? a : b` 大致相当，是因为他们返回结果虽然相同但是却有一个细微的差别。在 `a ? a : b` 中，如果a是一个复杂一些的表达式（比如有副作用的函数调用等），他有可能被执行两次（如果第一次结果为真）。而在 `a || b` 中a只执行一次，其结果用于条件判断和返回结果（如果适用的话）。

```javascript
function foo(a,b) {
    a = a || 'Hello'; // 又被称为 C# 的控制合并运算符的 JavaScript 版本
    b = b || 'World';
    console.log(a + ' ' + b);
}

foo(); // 'Hello World'
foo('yeah', 'yeah'); // 'yeah yeah'

foo('hi'); // 'hi World'
```



