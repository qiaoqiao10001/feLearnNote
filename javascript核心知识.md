基本数据类型
```javascript
    null undefined number string boolean
    typeof 返回值类型
    typeof null == 'object' 
    typeof undefined === 'undefined'
    typeof 12 === 'number'
    typeof '123' === 'string'
    typeof true === 'boolean'
```

引用数据类型：

```javascript
    // object function 
    typeof [123] === 'object'
    typeof {} === 'object'
    typeof function(){} === 'function'
```

## 堆、栈内存
- alert出来的结果都要toString()
 ```javascrip
    let a = 0;
    let b = a;
    b++
    alert(a)  //==> '0'

 ```

- 浏览器渲染的时候(编译器)
    - 词法解析
    - AST抽象语法树
    - 构建能够执行的代码
    - 引 擎（v8）
    - 变量提升、作用域，闭包 、堆栈内存。
    - GO、VO、AO、EC、ECStack
- 栈内存
    - 存储基本类型的值、执行代码

- 基本类型变量声明
    - 创建变量
    - 创建值
    - 把值和变量关联
    - 变量和值的关联就是指针概念
    - const 声明的是指针不可变的变量

- 引用类型变量
    - 引用类型是复杂的，所以特殊处理
    - 首先会开辟一个存储对象键值对的内存空间（堆内存）
    - 堆内存中有一段可被后续查找的地址（16进制）
    - 在栈内存中，把堆内存地址给了声明的变量

- null、undefined
    - a = null 是把变量指针指向空指针、解除这个变量的引用。赋值，暂时不想赋值，后期要赋值，则赋值null
    - undefined 声明了。

- EC （执行上下文）
    - 某个域下面的代码执行都胡形成自己的执行上下文
    - 全局 EC(G) 全局
    - EC 某个函数
    - 执行时候，进栈，执行完之后，出栈
    - 有的还要用，比如闭包，会把其压入栈底，以便再次使用
    
- GO (全局对象)： Global Object、存储浏览器api对象 
    -  浏览器端会把变量赋值给window
- VO(G) 全局变量对象： 全局上下文存储全局变量的空间，不是GO

## 执行上下文

- JS代码首次运行,都会先创建一个全局*执行上下文*并压入到执行栈中,之后每当有函数被调用,都会创建一个新的函数*执行上下文*并压入栈内;
- 每当一个函数被调用时都会创建一个函数上下文；需要注意的是，同一个函数被多次调用，都会创建一个新的上下文。

## 作用域

- 创建函数的时候就形成了作用域，如果全局上下文当中创建一个函数，当前函数的作用域就是全局上下文，在哪创建的函数，他的作用域就是此时的上下文

- 每个函数执行的时候，里头都是该函数私有的执行上下文

- ```javascript
  function A(){
  	function B(){}
  }
  A();
  //B的执行上下文就就是A的作用域
  ```



## 函数声明&执行

1. 函数执行时候，形成自己的私有执行上下文，供代码执行，然后放到栈顶进行执行
2. 函数声明的时候，就会有自己的作用域，如果全局声明那么其作用域是EC(G)，全局执行上下文；其次， 函数会有自己的私有变量对象 也就是AO(FN) ；还会初始化作用域链 (scope chain) <EC(FN)  ，EC(G)>; 所以访问函数内的变量会首先找函数私有作用域中的变量对象，然后通过作用域去查找其执行环境中的标识符；  函数上下文取决于函数在哪创建的
3. 初始化this  arguments  形参赋值



### 函数创建的时候发生的()

- 创建一个堆空间（存储代码字符串和对应的键值对）
- 初始化了当前函数的作用域
  - [[scope]] = 所在上下文EC中的变量对象VO/AO

### 函数执行的时候

- 创建一个新的执行上下文EC(压缩到栈ECStack中执行)
- 初始化this指向
- 初始化作用域链 [[scope-chain]]
- 创建AO活动对象来存储变量
  - Arguments ，形参赋值 ==> 如果有变量提升  ==> 代码执行
- 默认返回undefined



## 作用域链、闭包、this
- this指向 理解为执行主体
1.  函数执行，看前面有无点运算符 入 obj.fn() this指向obj
2.  给元素绑定时间方法（dom0 、dom2级）事件触发，this指向当前元素本身

```javascript
    var x = 10;
    var obj = {
        x: 20,
        fn:function(){
            console.log(x)
        }
    }
    
    var fn = obj.fn();
    fn();  // 10
    obj.fn(); // 20
    var box = document.getElementById('box')
    box.x = 30;
    box.onclick = function(){
      // this => box
        obj.fn(); // => 20
    }
    
    box.onclick = obj.fn; // => 30
```
### 变量提升
- 代码执行之前，会把var 或者function关键字的声明或者定义（带var的只提前声明，带function的会提前声明+定义【赋值】）

- {}中出现let/const/function，才会形成块级作用域

- ```js
  {
    function foo(){}  
    foo = 1
    function foo(){}
  }
  console.log(foo)
  
  
  
  
  ```

- 

### 闭包

- 函数执行会**形成全新的私有上下文**，这个上下文可能被释放，也可能不被释放，不论是否被释放，它的作用是：

1. 保护：划分一个独立的代码执行区域，在这个区域中有自己私有变量存储的空间，而用到的私有变量和其它区域中的变量不会有任何的冲突（防止全局变量污染）

2. 保存：如果上下文不被销毁，那么存储的私有变量的值也不会被销毁，可以被其下级上下文中调取使用

   ps:我们把函数执行，形成私有上下文，来保存和保护私有变量的机制，称之为“闭包”  =>它是一种机制

### 闭包理解

- 市面上一般认为只有形成的私有上下文不被释放，才算是闭包（因为如果一但释放，之前的东西也就不存在了）；还有人认为，只有一下级上下文用到了此上下文中的动西才算闭包；









### let var const 
- var 声明的会给全局对象GO增加一个对应的属性；
- 但是let变量不会给全局增加变量
- let 不能重复声明
- 不带var，let,const声明的变量会在代码解析阶段给当前环境声明一个属性
```javascript
    let x =1;
    let x=2; //Uncaught SyntaxError: Identifier 'x' has already been declared
```
- let const 声明
    - let 声明的变量是可以更改指针指向的，可以重新赋值；
    - const声明的变量是不允许改变指针指向的




### 函数执行和new 函数执行

- 创建一个新的执行上下文EC(压缩到栈ECStack中执行)

- 初始化this指向

- 初始化作用域链 [[scope-chain]]

- 创建AO活动对象来存储变量

  - Arguments ，形参赋值 ==> 如果有变量提升  ==> 代码执行

   new 执行不同的地方

  - 【new】默认创建一个对象，而这个对象就是当前类的实例
  - 【new】声明this指向，指向新创建的实例
  - 【new】不论是否有return语句，默认返回新创建的实例，如果返回的引用类型的值则覆盖默认实例
  

### new执行的时候的步骤
- 默认创建一个实例对象（当前类的实例）
- 也会把类当做普通函数执行，接受这个函数的返回结果
- 执行的时候保证函数中的this指向创建的实例
- 如果自己返回引用值，则返回自己的引用值，否则返回创建的实例

```javascript
    function _new(fn,...args){
        // let obj = {};
        // obj.__proto__ = fn.prototype;
        // 创建一个空对象让其__proto__属性指向fn的prototype
        let obj = Object.create(fn.prototype)
        let result = fn.call(obj, ...args);
        if((typeof result !==null && typeof result === 'object') || typeof result ==='function'){
            return result;
        }
        return obj;
    }
    
    function Dog(name){
        this.name = name
    }
    Dog.prototype.bark = function(){
        console.log('汪汪汪')
    }
    Dog.prototype.sayName = function(){
        console.log('my name is '+ this.name)
    }
    let sanmao = _new(Dog,'三毛');
```



### 变量赋值都是指针

```javascript
fn();
function fn(){ console.log(1); }
fn();
function fn(){ console.log(2); }
fn();
var fn = function(){ console.log(3); }  // 这里开始重新赋值之后才执行
fn();
function fn(){ console.log(4); }
fn();
function fn(){ console.log(5); }
fn();
```

#### 执行过程分析  function fn(){}  （声明fn，指向一个地址）

fn是指向一个地址，这时不断改变fn的指针指向

代码执行fn



## 原型原型链

- 每一个类（函数）都具备prototype属性，并且属性是一个对象
- 对象天生具备一个属性constructor，指向类本身
- 所有对象都具备：\__proto__，属性值是当前实例所属类的原型

### new Fn和new Fn()

- 都是创建类的实例
- new Fn ()可以传参,没有其他区别





MVVM

proxy不用递归数据，加强性能



重写数组方法，

对对象和数组分别观测

# 对象做属性名的时候转为"[object Object]"

页面渲染



如果传入了el则需要实现挂载流程；

写的是模板就要进行编译，render就不用编译

render函数 把模板编译成对象

ast语法树是用对象来描述原生语法的，

虚拟dom是对象描述dom节点

nodeType: 

nodeType 属性返回以数字值返回指定节点的节点类型。

如果节点是元素节点，则 nodeType 属性将返回 1。

如果节点是属性节点，则 nodeType 属性将返回 2。

```html
<div id="#app">
  <p>
    heelo
  </p>
</div>
```

```javascript
// ast语法树
let root = {
  tag: 'div',
  attrs: [{name:'id',value: 'app'}],
  parent: null,
  children: {
    tag: 'p',
    attr: [],
    parent: root,
    type: 1,  //nodeType
    children:[
      {
        
      }
    ]
  }
}
```

ast生成最终的render函数（就是模板引擎）

先把html转成ast语法树，然后再转成字符串

模板编译->AST语法树->render()->VNode->DOM-Diff



### 惰性函数：只会在执行的第一个分支调用函数

```js
function addEvent (type, element, fun) {
  // 只检测了了一次能力，来返回这个函数
  if (element.addEventListener) {
    addEvent = function (type, element, fun) {
      element.addEventListener(type, fun, false);
    }
  }
  else if(element.attachEvent){
    addEvent = function (type, element, fun) {
      element.attachEvent('on' + type, fun);
    }
  }
  else{
    addEvent = function (type, element, fun) {
      element['on' + type] = fun;
    }
  }
  return addEvent(type, element, fun);
}

```



### 柯里化函数： 将多个参数的函数转换为单一参数

```js
// 正常正则验证字符串 reg.test(txt)

// 函数封装后
function check(reg, txt) {
    return reg.test(txt)
}

check(/\d+/g, 'test')       //false
check(/[a-z]+/g, 'test')    //true

// Currying后
function curryingCheck(reg) {
    return function(txt) {
        return reg.test(txt)
    }
}

var hasNumber = curryingCheck(/\d+/g)
var hasLetter = curryingCheck(/[a-z]+/g)

hasNumber('test1')      // true
hasNumber('testtest')   // false
hasLetter('21212')      // false
```



### 深拷贝



```js
var deepClone = (target) => {
    if(typeof target ==='object' &&target !== null){
        const cloneTarget = Array.isArray(target) ? [] : {};
        for(let prop in target){
            if(target.hasOwnProperty(prop)){
                cloneTarget[prop] = deepClone(target[prop])
            }
        }
    		return cloneTarget;
    }else{
      return target;
    }
}
```



## 类型转换规律小结

**在加号两边出现字符串（或者对象）的情况下，加号一定是字符串拼接** ==>  <u>对象本身是要转数字进行计算的，转为数字的过程中需要先转字符串，因为字符串所以一定先拼接</u>

null 转数字是0

undefined转数字是NaN

**对象转字符串先调用valueOf获取原始值（一般是基本类型值），<u>如果不是原始值</u>则继续调用toString();**

==规则

1. 对象==字符串   对象转换为字符串
  [10] == '10'    true

2. null == undefined  （三个等号下不相等），但是和其它任何的值都不相等
  0 == null   false

3. NaN和谁（包括自己）都不相等

4. 剩下的情况都是转换为数字在做比较的

5. ![]==false
   运算符优先级   ![]   再算比较
   ![]  转换为布尔值进行取反（把其它类型转换为布尔类型遵循的规律： 只有 0/NaN/null/undefined/'' 五个值是false，其余的都是true）  => false

   false == false    true

6. +值是数学操作符  和+类似  {}出现在+前面的时候{}是代码块  {}+0 是把前面当做一个代码块

7. 0+{} 是数学运算了 得到 0[object Object] 

   大括号在运算符前面

   1在没有使用小括号处理优先级的情况下  不认为是数学运算，加小括号才算

   2出现在运算符的后面  认为是数学运算



1. 对象属性名： 可以是基本数据类型值；可以支持Map数据结构作为属性名

2. 对象属性名不能是对象  所以遇到对象的时候变为字符串  toString

3. ```js
   var b = {n:'1'};
   var a = {}
   a[b] = 2
   //此时a ==> {[object Object]:2}
   ```

4. a = a.x = {n:1}

5. ```js
   var a = {n:1}
   var b = a;
   a.x = a = {n:2}  //此时先让对象赋值给a.x 再让对象赋值给 a
   // 先让   a.x  = 这个值的地址
   // 再 让  a = 这个地址
   ```

6. 把函数体中的代码当做字符串存储到堆中 “代码字符串”  =>创建函数不执行，函数没啥用

7. 函数也是对象，他也有自己的键值对   

8. 定义了所在作用域 = 当前创建函数的上下文





### 数组方法整理

- isArray() 判断是否是数组
- from()  类数组转为数组
- Array.of()  和数组构造器一样  但是规避了副作用
- concat()  连接2个或者多个数组
- copyWithin 浅复制
- entries   返回数组的迭代器对象
- every 遍历所有元素看是否满足一个回调函数
- fill 
- filter
- find
- findIndex 
- flat 数组扁平化  [[12]].flat(infinity)
- flatMap  类似map
- forEach
- Includes 
- indexOf 
- join
- Keys 返回索引的数组
- lastIndexOf
- map
- pop
- push
- reduce
- reduceRight
- reverse
- shift
- unshift
- Slice
- some 
- Sort
- splice
- toString
- Unshift
- Values()



parseInt ：parseInt([value],[radix]) 多支持一个进制基数

parseFloat : 多识别一个小数点

0x是16进制标识



### VO  AO  

- GO 全局对象window    堆内存   浏览器内置的API
- VO(G) 全局变量对象   上下文中的空间   全局上下文中创建的变量
- 基于VAR/FUNCTION在全局上下文中声明的全局变量也会给GO赋值一份（映射机制）
- 但是就LET/CONST等ES6方式在全局上下文中创建的全局变量和GO没有关系











