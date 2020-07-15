### 1、概述
1、Symbol是一种新的原始数据类型，表示独一无二的值。
2、Symbol值通过Symbol函数生成
```
let s = Symbol();
typeof s;   //symbol
```
3、Symbol函数不能使用new命令，因为生成的Symbol是一个原始数据类型，不是一个对象。
4、Symbol函数可以接受一个字符串做为参数，表示对Symbol实例的描述
```
let s1 = Symbol('foo');
let s2 = Symbol('bar');
console.log(s1);    // Symbol(foo)
console.log(s2);    // Symbol(bar)
```
5、如果Symbol的参数是一个对象，就会调用该对象的toString()方法，将其转为字符串。
```
const obj1 = {
  toString() {
    return 'abc';
  }
};
const sym = Symbol(obj1);
console.log(sym);   // Symbol(abc)

const obj = { name: 'hi', age: '1'};
let s2 = Symbol(obj);
console.log(s2);    //Symbol([object Object])
```
6、Symbol的参数只表示当前Symbol实例的描述，相同参数的Symbol函数返回值是不相等的
7、Symbol值不能与其他类型的值进行运算，会报错。但是，可以显式地转为字符串、转为布尔值。
```
let sym = Symbol('My symbol');

"your symbol is " + sym
// TypeError: can't convert symbol to string
`your symbol is ${sym}`
// TypeError: can't convert symbol to string

String(sym) // 'Symbol(My symbol)'
sym.toString() // 'Symbol(My symbol)'

Boolean(sym);   //true
```
8、获取Symbol的描述
```
sym.description
```

### 2、作为属性名的Symbol
1、有3种写法
```
let mySymbol = Symbol();
//第一种写法
let a = {};
a[mySymbol] = 'Hello';

//第二种写法
let a = {
    [mySymbol]: 'Hello'
};

//第三种写法
let a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });

//以上3种写法都得到相同结果
a[Symbol] // "Hello"
```
2、Symbol值作为对象属性名时，不能用点运算符。因为点运算符后面总是字符串。
```
const mySymbol = Symbol();
const a = {};

a.Symbol = 'Hello';
a[mySymbol]     //undefined
a['mySymbol']   //'Hello'
```
3、使用Symbol定义对象的属性时，Symbol值必须放在方括号之中。
```
let s = Symbol();

let obj = {
    [s]: fucntion (arg){ ... }
};

//增强的对象写法
let obj = {
    [s](arg) { ... }
}
```
4、Symbol值作为属性名时，该属性还是公开属性，不是私有属性。

### 3、属性名的遍历
1、Symbol作为属性名，该属性不会出现在for...in、for...of循环中，也不会被Object.keys()、Object.getOwnPropertyNames()、JSON.stringify()返回。尽管如此，该属性也不是私有属性。
2、`Object.getOwnPropertySymbols()`方法，可以获取指定对象的所有Symbol属性名。
3、`Reflect.ownKeys()`方法，可以返回所有类型的键名，包括常规键名和Symbol键名。
```
let obj = {
	[Symbol('my_key')]: 1,
	enum: 2,
	nonEnum: 3
};
Reflect.ownKeys(obj);   // ["enum", "nonEnum", Symbol(my_key)]
```
4、可以用Symbol达到定义非私有但只用于内部的属性、方法。

### 4、Symbol.for(), Symbol.keyFor()
1、Symbol.for()接受一个字符串作为参数，然后搜索有没有以这个参数作为名称的Symbol值。如果有，就返回这个Symbol值，否则就新建并返回一个以该字符串为名称的Symbol值。
```
Symbol.for("bar") === Symbol.for("bar")   //true
Symbol("bar") === Symbol("bar")   //false
```
2、Symbol.for()为Symbol值登记的名字，是全局环境的，可以在不同的ifram或service.worker中取到同一个值。
3、Symbol.keyFor方法返回一个已登记的Symbol类型值的key。
```
let s1 = Symbol.for("foo");
Symbol.keyFor(s1) // "foo"

let s2 = Symbol("foo");
Symbol.keyFor(s2) // undefined
// s2属于未登记的Symbol值，所以返回undefined
```

### 5、内置的Symbol值
1、`Symbol.hasInstance`属性，当其他对象使用instanceof运算符，判断是否为该对象的实例时，会调用这个方法。
```
class MyClass {
  [Symbol.hasInstance](foo){
    return foo instanceof Array;
  }
}
[1,2,3] instanceof new Class()  //true
```
2、`Symbol.isConcatSpreadable`属性等于一个布尔值，表示该对象用于`Array.prototype.concat()`时，是否可以展开。
```
let arr1 = ['c', 'd'];
['a', 'b'].concat(arr1, 'e'); // ['a', 'b', 'c', 'd', 'e']
arr1[Symbol.isConcatSpreadable] // undefined
//Symbol.isConcatSpreadable默认等于undefined，能展开。该属性等于true时，也有展开效果。

let arr2 = ['c', 'd'];
arr2[Symbol.isConcatSpreadable] = false;
['a', 'b'].concat(arr2, 'e'); // ['a', 'b', ['c','d'], 'e']


let obj = {length: 2, 0: 'c', 1: 'd'};
['a', 'b'].concat(obj, 'e'); // ['a', 'b', obj, 'e']
//默认不展开，显式地设置为true时才可以展开

obj[Symbol.isConcatSpreadable] = true;
['a', 'b'].concat(obj, 'e'); // ['a', 'b', 'c', 'd', 'e']
```