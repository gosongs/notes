工具函数 `JSON.stringify()` 用于将 JavaScript 值转换为 JSON 字符串。

## 使用方法
语法：

```
JSON.stringify(value[, replacer[, space]])
```

参数说明：

+ value: 必需， 一个有效的 JSON 字符串。
+ replacer: 可选。用于转换结果的函数或数组。如果 replacer 为函数，则 JSON.stringify 将调用该函数，并传入每个成员的键和值。使用返回值而不是原始值。如果此函数返回 undefined，则排除成员。根对象的键是一个空字符串：""。如果 replacer 是一个数组，则仅转换该数组中具有键值的成员。成员的转换顺序与键在数组中的顺序一样。当 value 参数也为数组时，将忽略 replacer 数组。
+ space: 可选，文本添加缩进、空格和换行符，如果 space 是一个数字，则返回值文本在每个级别缩进指定数目的空格，如果 space 大于 10，则文本缩进 10 个空格。space 有可以使用非数字，如：\t。

返回值：返回包含 JSON 文本的字符串。

例子：

```javascript
JSON.stringify(42); // "42"
JSON.stringify("42"); // "42"
JSON.stringify(null); // "null"
JSON.stringify(true); // "true"
```

## 关于安全的JSON值
所有安全的 JSON 值（JSON-safe）都可以使用 `JSON.stringify()` 字符串化。安全的 JSON 值是值能够呈现为有效 JSON 格式的值。

为了简单起见，我们来看看什么是不安全的 JSON 值。`undefined`、`function`、`symbol(ES6+)` 和包含循环引用（对象之间相互引用，形成一个无限循环）的对象都不符合 JSON 结构标准，其他支持 JSON 的语言无法处理它们。

`JSON.stringify()` 在对象中遇到 `undefined`、`function` 和 `symbol` 时会自动将其忽略，在数组中则会返回 `null`。

```javascript
var foo = {
  a: undefined,
  b: function(){
    console.log('Hello World');
  },
  c: Symbol()
};
JSON.stringify(foo); // "{}"

var arr = [
  undefined,
  function(){
    console.log('Hello World');
  },
  Symbol,
  1
];
JSON.stringify(arr); // "[null,null,null,1]"
```

## 含有 toJSON 方法的对象
如果对象中定义了 `toJSON()` 方法，JSON 字符串化时会首先调用该方法，然后用它的返回值来进行序列化。

```javascript
var foo = {
  name: 'tony',
  age: 14,
  toJSON: function(){
    return 'Hello World';
  }
};
JSON.stringify(foo); // "Hello World" 而不是 ’{"name":"tony","age":14}‘
```

## replacer 参数
如果 `replacer` 是一个数组，那么它必须是一个字符串数组，其中包含序列化要处理的对象的属性名称，除此之外其他的属性则被忽略。

```javascript
var foo = {
  name: 'tony',
  age: 14,
  money: '$1000'
};
JSON.stringify(foo, ['name', 'age']); // '{"name":"tony","age":14}'
```

如果 `replacer` 是一个函数，它会对对象本身调用一次，然后对对象中的每隔属性各调用一次，每次传递两个参数：键和值。如果要忽略某个键就返回 `undefined`，否则返回指定的值。

```javascript
var foo = {
  name: 'tony',
  age: 14,
  likes: ['music', 'read', 'coding']
};
JSON.stringify(foo, function(k,v){
  if(k !== 'likes') return v;
}); // '{"name":"tony","age":14}'
```

## space 参数
`JSON.stringify()` 还有一个可选参数 `space`，用来指定输出的缩进格式。`space` 为正整数时是指定每一级缩进的字符数，他还可以是字符串，此时最前面的十个字符用于每一级的缩进：

```javascript
var foo = {
  name: 'tony',
  age: 14,
  money: '$1000'
};
JSON.stringify(foo, null, 3);
/* 输出：
'{
   "name": "tony",
   "age": 14,
   "money": "$1000"
}'
*/

JSON.stringify(foo, null, '-----');
/* 输出：
'{
-----"name": "tony",
-----"age": 14,
-----"money": "$1000"
}'
*/
```

## 总结
`JSON.stringify()` 并不是强制类型转换。

1. 字符串、数字、布尔值和 `null` 的 `JSON.stringify()` 规则与 `ToString` 基本相同。
2. 如果传递给 `JSON.stringify()` 的对象中定义了 `toJSON()` 方法，那么该方法会在字符化前调用，以便将对象转换为安全的 JSON 值。