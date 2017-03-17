# Airbnb JavaScript Style Guide
https://github.com/airbnb/javascript

## Types
1. 原生类型取值(string, number, boolean, null, undefined)
2. 复杂类型取引用(object, array, function)

## References
1. 对所有变量使用*const*，避免使用*var*
2. 如果必须重新赋值，使用*let*
3. *let*和*const*是块作用域的

## Objects
1. 使用符号而不是new来新建object
2. 通过动态属性名称设置需要计算的属性
```javascript
function getKey(k) {
	return `a key named ${k}`
}

// bad
const obj = {
	id: 5,
	name: 'San Francisco'
}
obj[getKey('enabled')] = true

// good
const obj = {
	id: 5,
	name: 'San Francisco'
	[getKey('enabled')]: true
}
```
3. 使用对象方法的简写
```javascript
// bad
const atom = {
	value: 1,
	addValue: function(value) {
	  return atom.value + value
	}
}

// good
const atom = {
	value: 1,
	addValue(value) {
	  return atom.value + value
	}
}
```
4. 使用属性名简写
```javascript
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  lukeSkywalker: lukeSkywalker,
};

// good
const obj = {
  lukeSkywalker,
};
```
5. 将简写属性放在对象申明的最前面
```javascript
const anakinSkywalker = 'Anakin Skywalker';
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  lukeSkywalker,
  episodeThree: 3,
  mayTheFourth: 4,
  anakinSkywalker,
};

// good
const obj = {
  lukeSkywalker,
  anakinSkywalker,
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  episodeThree: 3,
  mayTheFourth: 4,
};
```
6. 只对无效的标识符标注引号
7. 不直接调用`Object.prototype`，对`hasOwnProperty`，`propertyIsEnumerable`和`isPrototypeOf`也是如此
/因为这些方法很容易被对象的属性给遮蔽掉，比如`{ hasOwnProperty: false }`/
```javascript
// bad
console.log(object.hasOwnProperty(key));

// good
console.log(Object.prototype.hasOwnProperty.call(object, key));

// best
const has = Object.prototype.hasOwnProperty; // cache the lookup once, in module scope.
/* or */
import has from 'has';
// ...
console.log(has.call(object, key));
```
8. 使用`...`操作符而不是`Object.assign`来浅复制对象，使用`...`操作符来从一个对象中获得特定属性
```javascript
// very bad
const original = { a: 1, b: 2 };
const copy = Object.assign(original, { c: 3 }); // this mutates `original` ಠ_ಠ
delete copy.a; // so does this

// bad
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }

// good
const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }

const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
```

## Arrays
1. 使用`[]`而不是`new`新建Array
2. 使用`push`而不是根据index值赋值
3. 使用`...`操作符复制Array
```javascript
// bad
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i += 1) {
  itemsCopy[i] = items[i];
}

// good
const itemsCopy = [...items];
```
4. 使用`Array.from`将一个类似Array的对象转换成Array，比如说NodeList
```javascript
const foo = document.querySelectorAll('.foo');
const nodes = Array.from(foo);
```
5. 在Array的迭代方法中记得使用`return`，如果是单句则无所谓了

## Destructuring
1. 当只需要使用一个Object的部分属性时，使用Object Destructuring(对象解构)
```javascript
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}

// good
function getFullName(user) {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
}

// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```
2. 使用列表解构
```javascript
const arr = [1, 2, 3, 4];

// bad
const first = arr[0];
const second = arr[1];

// good
const [first, second] = arr;
```
3. 当需要多个返回值时，使用Object Destructuring
```javascript
// bad
function processInput(input) {
  // then a miracle occurs
  return [left, right, top, bottom];
}

// the caller needs to think about the order of return data
const [left, __, top] = processInput(input);

// good
function processInput(input) {
  // then a miracle occurs
  return { left, right, top, bottom };
}

// the caller selects only the data they need
const { left, top } = processInput(input);
```

## Strings
1. 对`String`使用单引号
2. 不要对超长的字符进行换行。。。黑人问号
3. 当调用变量生成字符串时，使用模板字符串
4. 永远不要使用eval
5. 不要在`String`中使用不必要的转义符

## Functions
1. 使用命名函数表达式，不要使用函数声明
`function声明的函数具有全局作用域，伤害了程序的可读性和可维护性，而匿名函数在回调的时候非常难以追踪`
```javascript
// bad
function foo() {
  // ...
}

// bad
const foo = function () {
  // ...
};

// good
const foo = function bar() {
  // ...
};
```
2. 将立即执行的function包在`()`中
```javascript
// immediately-invoked function expression (IIFE)
(function () {
  console.log('Welcome to the Internet. Please follow me.');
}());
```
3. 不要在`if`、`for`等语句中命名函数
4. ECMA-262规定，block中应该是一系列的`statements`，而函数声明不是`statements`
5. 不要在入参中使用`arguments`变量，这会覆盖每一个function命名域中的`arguments`变量
6. 永远不要使用`arguments`，使用`...`操作符。`...`显式地表示你想要剩下的参数，另外这样收纳的参数是真正的array，而`arguments`不是array
7. 使用默认参数而不是更改函数入参
```javascript
// really bad
function handleThings(opts) {
  // No! We shouldn't mutate function arguments.
  // Double bad: if opts is falsy it'll be set to an object which may
  // be what you want but it can introduce subtle bugs.
  opts = opts || {};
  // ...
}

// still bad
function handleThings(opts) {
  if (opts === void 0) {
    opts = {};
  }
  // ...
}

// good
function handleThings(opts = {}) {
  // ...
}
```
8. 默认参数应该避免副作用
9. 永远把默认参数放在最后
10. 不要使用`Function Constructor`来new一个`function`，这和eval一样危险
11. `function`与`()`与`{}`之间应该留空格
12. 不要修改入参
13. 永远不要修改入参
14. 对于可变参数的函数，使用`...`操作符
```javascript
// bad
const x = [1, 2, 3, 4, 5];
console.log.apply(console, x);

// good
const x = [1, 2, 3, 4, 5];
console.log(...x);

// bad
new (Function.prototype.bind.apply(Date, [null, 2016, 8, 5]));

// good
new Date(...[2016, 8, 5]);
```
15. 多参数的函数，缩进应该与其他行的缩进规则保持一致。。。黑人问号
```javascript
// bad
function foo(bar,
             baz,
             quux) {
  // ...
}

// good
function foo(
  bar,
  baz,
  quux,
) {
  // ...
}

// bad
console.log(foo,
  bar,
  baz);

// good
console.log(
  foo,
  bar,
  baz,
);
```

## Arrow Functions
1. 当需要使用匿名函数时，应该使用箭头函数。箭头函数会使用当前的上下文，这很用，而如果函数太复杂，就不应该使用匿名函数。
2. 如果只有单行内容，则直接返回
```javascript
// 返回Object的方式
[1, 2, 3].map((number, index) => ({
  [index]: number,
}));
```
3. 当单行太长时，为了可读性应该使用`()`
```javascript
// bad
['get', 'post', 'put'].map(httpMethod => Object.prototype.hasOwnProperty.call(
    httpMagicObjectWithAVeryLongName,
    httpMethod,
  )
);

// good
['get', 'post', 'put'].map(httpMethod => (
  Object.prototype.hasOwnProperty.call(
    httpMagicObjectWithAVeryLongName,
    httpMethod,
  )
));
```
4. 除非使用单行返回，否则永远给参数带括号。。。黑人问号
```javascript
// bad
[1, 2, 3].map((x) => x * x);

// good
[1, 2, 3].map(x => x * x);

// good
[1, 2, 3].map(number => (
  `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
));

// bad
[1, 2, 3].map(x => {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
```
5. 为了避免混淆，不要在箭头函数中使用大于小于号
```javascript
// bad
const itemHeight = item => item.height > 256 ? item.largeSize : item.smallSize;

// bad
const itemHeight = (item) => item.height > 256 ? item.largeSize : item.smallSize;

// good
const itemHeight = item => (item.height > 256 ? item.largeSize : item.smallSize);

// good
const itemHeight = (item) => {
  const { height, largeSize, smallSize } = item;
  return height > 256 ? largeSize : smallSize;
};
```

## Classes & Constructor
1. 永远使用`class`，避免直接操作`prototype`
```javascript
// bad
function Queue(contents = []) {
  this.queue = [...contents];
}
Queue.prototype.pop = function () {
  const value = this.queue[0];
  this.queue.splice(0, 1);
  return value;
};


// good
class Queue {
  constructor(contents = []) {
    this.queue = [...contents];
  }
  pop() {
    const value = this.queue[0];
    this.queue.splice(0, 1);
    return value;
  }
}
```
2. 使用`extends`来完成继承，这样不会破坏`instanceOf`方法
```javascript
// bad
const inherits = require('inherits');
function PeekableQueue(contents) {
  Queue.apply(this, contents);
}
inherits(PeekableQueue, Queue);
PeekableQueue.prototype.peek = function () {
  return this.queue[0];
};

// good
class PeekableQueue extends Queue {
  peek() {
    return this.queue[0];
  }
}
```
3. 函数可以返回`this`，用来串联函数
4. 在保证正确的情况下，可以覆盖`toString`函数
5. `class`默认有`constructor`，没必要用一个空的或者仅仅代理父类的`constructor`

## Modules
1. 永远使用`import / export`
```javascript
// bad
const AirbnbStyleGuide = require('./AirbnbStyleGuide');
module.exports = AirbnbStyleGuide.es6;

// ok
import AirbnbStyleGuide from './AirbnbStyleGuide';
export default AirbnbStyleGuide.es6;

// best
import { es6 } from './AirbnbStyleGuide';
export default es6;
```
2. `import`时不要使用`*`
3. 不要直接在`import`中`export`。。。还能这么用
```javascript
// bad
// filename es6.js
export { es6 as default } from './AirbnbStyleGuide';

// good
// filename es6.js
import { es6 } from './AirbnbStyleGuide';
export default es6;
```
4. 不用重复`import`
5. 只`export const`
6. 对于单独的`export`，使用`export default`
7. 所有`import`都应该在最上方
8. 多行`import`应该像`array`或者`object`那样缩进
9. 不用使用webpack loader的语法

## Iterators and Generators
1. 不要使用*Iterator*，优先使用高阶的函数而不是`for-in`或者`for-of`
2. 先不要使用*generator*，目前它还不能很好地编译到ES5
3. 如果一定要使用*generator*，确保它的格式正确
```javascript
// bad
function * foo() {
  // ...
}

// bad
const bar = function * () {
  // ...
};

// bad
const baz = function *() {
  // ...
};

// bad
const quux = function*() {
  // ...
};

// bad
function*foo() {
  // ...
}

// bad
function *foo() {
  // ...
}

// very bad
function
*
foo() {
  // ...
}

// very bad
const wat = function
*
() {
  // ...
};

// good
function* foo() {
  // ...
}

// good
const foo = function* () {
  // ...
};
```

## Properties
1. 访问属性时使用`obj.prop`而不是`obj[‘prop’]`
2. 当prop是一个变量时，使用`obj[prop]`

## Variables
1. 使用`const`和`let`来声明变量
2. 每一个变量都需要使用`const`或者`let`
3. 所有`const`和`let`最好都放在一起
4. 确实有需要的时候，再声明一个变量，不要把所有变量声明都放在block的最前端
5. 不要连续地给变量赋值，很容易将变量泄露到全局空间
```javascript
// bad
(function example() {
  // JavaScript interprets this as
  // let a = ( b = ( c = 1 ) );
  // The let keyword only applies to variable a; variables b and c become
  // global variables.
  let a = b = c = 1;
}());

console.log(a); // undefined
console.log(b); // 1
console.log(c); // 1

// good
(function example() {
  let a = 1;
  let b = a;
  let c = a;
}());

console.log(a); // undefined
console.log(b); // undefined
console.log(c); // undefined

// the same applies for `const`
```
6. 避免使用`++`和`--`
7. 声明函数会泄露到全局空间，而将函数赋值到一个变量，就不会有类似的情况了

## Comparison Operators & Equality
1. 使用`===`和`!==`而不是`==`和`!=`
2. 对于`if`的判定，有如下要注意的
> Object === true  
> Undefined === false  
> Null === false  
> +0, -0, NaN === false  
> ‘’ === false  
> []都是Object，所以为true  
3. 三元运算符语句，最好都在一行并且不应该嵌套
4. 避免不必要的三元运算符
```javascript
// bad
const foo = a ? a : b;
const bar = c ? true : false;
const baz = c ? false : true;

// good
const foo = a || b;
const bar = !!c;
const baz = !c;
```

## Blocks
1. 多行的block应该使用`{}`框起来
2. `else`应该和上一个`if`的`}`连起来

## Comments
1. 使用`/**  */`用来多行注释
2. 在语句后面不要跟注释。。。
3. 注释前面加空行

## Whitespace
1. 使用两格空格

## Type Casting && Coercion
1. 转型直接可以使用String
```javascript
// bad
const totalScore = this.reviewScore + ''; // invokes this.reviewScore.valueOf()

// bad
const totalScore = this.reviewScore.toString(); // isn't guaranteed to return a string

// good
const totalScore = String(this.reviewScore);
```
2. 使用Number
3. 使用Boolean