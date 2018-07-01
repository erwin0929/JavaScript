# Javascript学习总结
这里我们先从Javascript的难点开始说起,简单的如流程控制语句,循环操作变量声明就不再提及了,这里难点由浅入深,总结自己的学习情况。  

## Javascript (es5)

## 6大数据类型
* 5种基础类型: Number,String,Boolean,Null,Undefined
* 1种复杂数据类型(引用类型): Object(Array,Object,Function)

## 数据类型转换 (javascript权威指南得知,俗称犀牛书)
* 由于JS是弱类型语言,在计算时会被隐式的转换成系统想要的类型,当然也可以手动进行类型转换
* Number('23') 将字符串转换成数值类型
    1. 转换null时,返回0
    2. 转换undefined,返回NaN(Not a Number)
    3. 空白字符串,转换成0
    4. 字符串数字转换成数值
    5. 如果是对象先调用valueOf方法,再使用toString转换,再根据上述规则转换
    6. parseInt和parseFloat同样可以转换为数字
    7. 一元加一元减,加减乘除法都将隐式转换为数字
* String(true) 将布尔值转换成字符串类型
    1. 也可通过toString, ''+变量来转换成字符串,null和undefined没有toString方法,将报错
    2. 对象转为字符串将显示[object Object]
* Boolean(101) 将数值转换成布尔类型
    1. 0,null,undefined,空字符串,NaN都转换为false
* Object('hello') 将字符串转换成对象类型
    1. 将转换成包装对象

## 事件 (CSDN中得知)
* 事件冒泡: 当多个div绑定了点击事件,且dom关系为父子关系,此时点击最内层的div会将事件由内向外传播到最外层。
* 事件捕获: 场景如上,当点击最外层的div,会将事件自外而内传播到最里面的div。
* 以上2中情况可通过事件对象里的方法来禁止
* 事件委托: 当子元素出现成千上万个的时候,此时子元素如果绑定事件会造成很不好的性能问题。可以利用事件冒泡的原理,为父元素绑定一个事件,点击子元素时会冒泡到父元素身上从而形成一种点击了子元素的假象,然后使用事件对象中的target来锁定正真被点击的目标,这样就能精确定位到点击的元素


## 预解析,变量提升(javascript高级程序设计得知,俗称红宝石)

* Js在代码执行之前会进行预解析,将变量声明和函数声明以及参数提升到当前作用域最顶端
```
// 代码执行之前,将变量a,函数a提升到顶端,这里变量a和函数a重名,Js引擎认为函数的优先级更高,所以这里的a以函数形式保存,而代码中出现2个a函数,我们知道程序是从上至下执行,所以后声明的覆盖前声明的,所以这里以后声明的函数保存到内存中。
提升行为结束,开始执行代码。
<script>
    var a;                  
    function a(){
        return false;
    };                      

    alert(a); //内存中的a是函数,所以这里打印函数体,注意,这里打印后一个声明的a函数
    a = 1;    //此时a被赋值             
    alert(a); //a被赋值为1,所以这里打印1              
    function a(){           
        return true
    };

    function fn1(a){  
        alert(a); // 预解析完成后开始执行,虽然在全局作用域下也有a变量,但是该函数体内总会优先使用当前作用域的变量,所以此时a打印undefined
        a = 2;    // a被赋值,如果再打印a,就是2
    }；
    fn1(); //执行该函数前,首先进行预解析,将实参存到内存中,由于调用函数没有传值,所以这里的a为undefined
</script>
```
* 总结: js总是先解析后执行,当执行到函数出,又要进入函数体开始预解析,而变量提升的优先级 函数>变量>参数

## this的四种指向逐一击杀 (segmentfalut中得知,没有复制,手写)

> 一. 单独使用的this,最容易理解的

```
console.log(this) // 非严格模式下,全局作用域下的this指向window,因为this默认指向window

function foo () {
    console.log(this)   
}
foo()  // 非严格模式的局部作用域下,单独使用的this仍指向window
```

> 二. 作为方法被调用

```
// 我们定义一个对象,在对象里申请一个getValue属性,其值为函数,此时getValue被称为方法  
// 他需要由obj来调用
// 当obj调用getValue方法时,此时this不再指向window,指向了调用该方法的obj对象
var obj = {
    value: 1,
    getValue: function() {
        alert(this.value); //此时this指向obj,而obj中有个value属性,所以打印1
    }
};
obj.getValue(); 

// 分支  
// 如下代码
var a = 1
function test () {
    console.log(this.a)
}
var obj = {
    a: 2,
    test
}
var obj0 = {
    a: 3,
    obj 
}
obj0.obj.test() // 这里形成了链式操作,但是不论链式再长再复杂,this指向的是他的直属调用者,而test的调用者是obj,所以这里打印2

// 分支,this的隐式丢失
var a = 1
function test () {
    console.log(this.a)
}
var obj = {
    a: 2,
    test
}
var b = obj.test // 注意这里没有执行test方法,而是将方法赋给了b变量,此时的this已经隐式丢失
b() // 绑定到了默认的window上,window下的a=1,所以这里打印1
等价于
function b () {
    console.log(this.a) // 使用默认绑定指向window
}
```

> 三 apply,call强制更改this指向

```
var a = 0;
function foo(){
    console.log(this.a);
}
var obj = {
    a:2
};
foo(); //0
foo.call(obj); // 这里间接调用foo函数,将foo函数this指向强制转移到obj上,所以打印obj里的a属性,为2
```

> 四 new 构造函数更改this指向

```
function fn(){ //注意这里只是定义了一个函数,还不能决定this的指向
    this.a = 2;
}
var test = new fn(); //new一个fn构造函数,使得函数内的this指向为实例对象test
console.log(test.a);
```

* 四种绑定方式有个通用的规则,只有在被调用的时候才能确定this的指向


## 作用域链,闭包 ,执行期上下文 (<高性能javascript>一书中得知)

* 这里很重要,上面的你可以不看,但这章请听我仔细分析,这里的内容直接反应了对JS的理解,很多JS多年从业者都没搞清楚。
* 作用域分全局和局部,我们知道最外层定义的变量是全局变量,在任何角落都能访问到。  
而函数内部定义的变量在最外层是访问不到的,称之为局部作用域。  
* 那么问题来了,为什么全局定义的能在任何地方访问,而局部定义的不能再函数外层访问?
* 先了解一下[[Scope]],由于函数也是对象,所以函数也能拥有属性,而每个函数都有个隐藏属性[[Scope]],该属性仅供Js引擎使用,我们无法使用,函数被创建时这个隐藏属性存放了,这个集合呈链式,被称为作用域链,作用域链上的每个对象被称为变量对象

```
var a = 1;
function foo(x, y){
    var b = 2;
    function bar(){
        var c = 3;
    }
    bar();
}
foo(100, 200);

// foo函数被创建时,他的[[Scope]](作用域链)中首先添加了一个全局对象GO,包含了全局下所有的变量,请看以下代码

foo.[[Scope]] = [
    {
        // 该全局对象下标为0,里面存放了window,this,和变量a
        GO: {
            this: window ,
            window,
            document,
            a: undefined, // 预编译阶段还不知道a值是多少
            foo: function(){...}, // 以及函数自身
        }
    }
]
ok,这是创建时所发生的。  
当函数执行时,创建一个叫做执行期上下文的对象,函数每执行一次就会创建一次,函数执行完毕后会被销毁。  
注意了,作用域链与执行期上下文是2个不同的概念。
好了,foo函数执行前会复制一份foo的作用域链,也就是[[Scope]],并且生成一个叫做AO的活动对象,该对象在执行期上下文的最顶端,请看以下实例代码

foo.执行期上下文 = {
    // AO对象由预编译阶段生成,并推入最顶端,优先使用AO里的变量
    AO: {
        this: window,
        arguments: [100,200],
        x: 100, // 这是foo函数接受的参数x
        y: 200, // 这是foo函数接受的参数y
        b: undefined, // 这是foo函数作用域下定义的变量b
        bar: function(){...}  // 这是foo函数下定义的bar函数,bar函数也创建了[[Scope]]
    },
    // 执行期上下文复制了foo函数的作用域链
    GO: {
        this: window ,
        window: ... ,
        document: ... ,
        a: 1,
        foo: function(){...},
        ......
    }
}

foo函数查找变量的规则优先从AO里找,如果AO里没有,往下去找GO里的变量,如果再没有找到则undefined,这就是局部作用域能访问全局变量的原因。注意foo函数里又执行了bar函数, bar函数预解析时会复制一份foo函数的执行期上下文到自己的执行期上下文中,
预解析完毕后,bar函数开始执行,生成一个AO对象,AO对象保存了自己作用域下的变量,优先从自己的作用域下查找变量,如果没有找到,则继续往下查找刚才复制的foo函数的执行期上下文,这样的行为称之为闭包,如果一个函数拥有对其他作用域的访问权,这样的函数称为闭包。
这是我总结的概念,绝非复制粘贴!
```

## 原型与原型链,继承方法 (犀牛书中得知)

* 每个函数都有一个prototype属性,指向他的原型。
* 每个对象都有一个__proto__的隐藏属性,指向其构造函数的原型即 obj.__proto__ === Object.protpotype

```
// 定义一个构造函数
var parent = function(name){
 this.name = '你好哦';
}
// 为构造函数的原型添加一个getName方法
parent.prototype.getName = function(){
 return this.name;
}
// 实例化构造函数
var son = new parent()

// 构造函数里没有getName方法就从原型中查找
son.getName()
// 调用一个没有定义的方法,该方法也不存在原型中,那么再去查找parent.prototype.__proto__  
// 因为 parent.prototype.__proto__  === Object.prototype,所以在Object的原型中查找,而Object的原型中由toString方法,这样的查找形式形成了一条链子,称之为原型链
son.toString()

// 原型链关系如下
son.__proto__ === parent.prototype
parent.prototype.__proto__ === Object.prototype
Object.prototype.__proto__ === null //null不再拥有属性和原型
```

> js的继承方式 (CSDN中得知)

* 一.原型链继承

```
// 定义一个类
function Animal (name) {
    this.name = 'Jack'
    this.sleep = function(){
        console.log(`${this.name}在吃饭!`);
    }
}

Animal.prototype.eat = function(food) {
};

// 再定义一个构造函数
function Cat(){ 
}
// 核心: 将Animal的实例作为Cat构造函数的原型对象
Cat.prototype = new Animal();
// 实例化Cat,cat对象继承了Animal实例的所有方法和属性
var cat = new Cat()

cat.sleep() //调用成功
cat.eat() //调用成功

// 对应关系
cat.__proto__ === Cat.prototype
Cat.prototype.__proto__ === Animal.prototype
Animal.prototype.__proto__ === Object.prototype

```

* 二. call继承
```
// 定义一个test构造函数
function test(str){
    // 定义属性和方法
    this.name = 123
    this.abc = function () {
    }
}
// 定义一个普通对象
var object = {};
// 为对象添置属性
object.name = "zhangsan";
间接调用test,使其this指向变更为object,此时object劫持了所有test的属性和方法从而完成继承
test.call(object)
```


## 异步和同步(CSDN中得知)

* js是同步执行的,也就是同一时间只做一件事,当这件事没有做完,其他事情必须安静等待,等这件事做完才能做接下来的事。  
* 如果一件事消耗了大量的时间也没有返回结果,此时可能网页出现一片空白,出现假死现象,为了解决这种情况,异步应运而生。  
* 常见的异步有setTimeout,setInterval,ajax
* 当主程序遇到异步操作时,认为他是一个耗时程序,所以先将其放入任务队列,等我的主程序执行完再来打开任务队列里依次执行异步操作

## jsonp跨域 (SegmentFault中得知)

* 由于浏览器的同源策略,我们无法用ajax访问另一个域的数据接口。不过目前可以在后端设置某个参数可以访问了(好像是这么回事)。
* 但是script中的src标签是可以跨域访问的,该远程文件调用本地定义的一个函数,并且将传递的数据以参数形式传到函数体内
* 此时本地函数接受一个参数来接受远程js发送的数据

```
// 本地定义一个函数
var localHandle = function (getData) {
    // 当该函数被执行时,getData就获取到了远程传递的数据,这就是jsonp的基本原理,利用了src的跨域和函数传参的方式来接受远程数据,从而绕过同源策略的限制,他与ajax没有任何关系,ajax是创建xmlhttprequest对象来访问远端
    console.log(getData)
}

// 为方便展示,将远程代码写到标签里
<script src="http://www.badu.com/remote.js">
// 这是要发送的数据
var data = [
    {
        name: 123,
        age: 2
    },
    {
        name: 343,
        age: 100
    }
]
// 执行函数,将数据传到函数里,请看函数定义的地方
localHandle(data)
</script>
```
