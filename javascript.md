# JavaScript 学习笔记
>    本文来源于网络，可能存在错漏之处，仅供参考。

## 事件冒泡
>    [参考](https://www.cnblogs.com/wuzhiquan/p/5380800.html)

- Example: 点击a的时候，会先弹出‘点击了a’，再弹出‘点击了li’，最后跳转到百度
```bash
<li>
    <a href='http://www.baidu.com'>click a</a>
</li>
<script>
    $('li').click(function () {
        alert('点击了li');
    });
    $('a').click(function () {
        alert('点击了a');
    });
</script>
```

### 1. event.stopPropagation();（阻止冒泡）
-  完美阻止了li元素的冒泡，并且不会影响a的事件。效果是：先弹出‘点击了a’，然后跳转百度。
```bash
<script>
    $('li').click(function () {
        alert('点击了li');
    });
    $('a').click(function (event) {
        alert('点击了a');
        event.stopPropagation();
    });
</script>
```

### 2. event.cancelBubble = true;（阻止冒泡）
- 效果跟上面的是一样：先弹出‘点击了a’，然后跳转百度。
```bash
<script>
    $('li').click(function () {
        alert('点击了li');
    });
    $('a').click(function () {
        alert('点击了a');
        console.log(event);
        event.cancelBubble = true;
    });
</script>
```

### 3. event.preventDefault();（阻止浏览器默认行为)  
- 执行后的效果：先弹出‘点击了a’，再弹出‘点击了li’，但是，不执行跳转。 

```bash
<script>
    $('li').click(function () {
        alert('点击了li');
    });
    $('a').click(function (event) {
        alert('点击了a');
        event.preventDefault();
    });
</script>
```  

- preventDefault并不是阻止事件冒泡，只是阻止浏览器的默认动作。而stopPropagation跟cancelBubble只是阻止事件冒泡，并没有阻止浏览器的默认动作。当我们希望阻止事件冒泡的同时，也阻止浏览器的默认动作时，就可以2者都一起使用。 

```bash
<script>
    $('li').click(function () {
        alert('点击了li');
    });
    $('a').click(function (event) {
        alert('点击了a');
        console.log(event);
        event.stopPropagation();
        event.preventDefault();
    });
</script>
```

### 4. return false;（有很多行为）
- 只弹出‘点击了a’，并不跳转百度链接，也不弹出‘点击了li’。跟（stopPropagation+preventDefault）是一个效果。 return false 不但阻止事件，还可以返回对象, 跳出循环等。
```bash
<script>
    $('li').click(function () {
        alert('点击了li');
    });
    $('a').click(function () {
        alert('点击了a');
        return false;
    });
</script>
```
- addEventListener第三个参数为true是事件捕获，false为事件冒泡。
- IE只支持事件冒泡，不支持事件捕获。





## vue的mvvm模式中的双向数据绑定

>    [参考](https://blog.csdn.net/a419419/article/details/80204111)

### 1. mvvm（Model-View-ViewModel）模式：视图(View)、视图模型(ViewModel)、模型(Model)  
- 实现UI逻辑、呈现逻辑和状态控制、数据与业务逻辑的分离。 

### 2. MVVM模式的优点：
1. 低耦合。View可以独立于Model变化和修改，一个ViewModel可以绑定到不同的View上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。

2. 可重用性。可以把一些视图的逻辑放在ViewModel里面，让很多View重用这段视图逻辑。

3. 独立开发。开发人员可以专注与业务逻辑和数据的开发(ViewModel)。设计人员可以专注于界面(View)的设计。

4. 可测试性。可以针对ViewModel来对界面(View)进行测试。

### 3. 双向数据绑定机制：
3.1 vue中支持mvvm的核心就是双向数据绑定机制。vue数据双向绑定是通过数据劫持结合发布者-订阅者模式的方式来实现的。  
*“数据劫持”：顾名思义指的是当数据发生变化时，“劫持”数据变化行为并指定后续数据流流向。以便动态监听数据变化，并及时相应数据变化。[参考](https://wangfeia.com/2016/12/14/Object.defineProperty()/)*  
*”发布者-订阅者模式“：消息的发送方，叫做发布者（publishers），消息不会直接发送给特定的接收者，叫做订阅者。意思就是发布者和订阅者不知道对方的存在。需要一个第三方组件，叫做信息中介，它将订阅者和发布者串联起来，它过滤和分配所有输入的消息。观察者模式大多数时候是同步的，比如当事件触发，Subject就会去调用观察者的方法。而发布-订阅模式大多数时候是异步的（使用消息队列）。[参考](https://juejin.im/post/5a14e9edf265da4312808d86)[过滤消息的方法：基于主题以及基于内容](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern#Message_filtering)*  
3.2 Object.defineProperty()  
- Object.defineProperty(obj, prop, descriptor)
- 改变prop这个属性的值后，通过set立即更新到view
- [example](https://juejin.im/post/5befeb5051882511a8527dbe?utm_source=gold_browser_extension#heading-3)
```bash
<input id="input"/>
```
```bash
const data = {};
const input = document.getElementById('input');
Object.defineProperty(data, 'text', {
  set(value) {
    input.value = value;
    this.value = value;
  }
});
input.onchange = function(e) {
  data.text = e.target.value;
}
```

## Web Storage  
>    [参考](http://caibaojian.com/web-storage.html)  
### 1. 目标：提供一种在Cookie之外存储会话数据的途径；提供一种存储大量可以跨会话存在的数据的机制。  
*目前的情况是Cookie和localStorage用的比较多，我们也可以尝试用sessionStorage做一些Cookie现在做的事情。*  
### 2. localStorage  
- localStorage是用来做永久性存储的。
- localStorage的作用域是限定在文档源级别的，不同的文档源之间是不能读取和修改对方的数据的，而相同的文档源是可以的。但是不同的浏览器是不共享Storage的，也就是说你在Chorme浏览器里存的数据，在Firefox里是访问不到的，即使它们是同一文档源。  
### 3. sessionStorage  
- sessionStorage是用来做临时性存储的。
- 时效只存在于标签页存在的时间，一旦标签被关闭了，sessionStorage存储的数据也会被删除掉。
- 作用域同样是限定在文档源级别的，不仅如此，它还被限制在标签页中，不同标签页的同一个页面拥有各自的sessionStorage，数据不能共享。如果是一个页面里有两个iframe元素，它们是共享sessionStorage的。  
### 4. Web Storage API
- Storage对象提供了操作key/value对（下面我们称之为item）的方法，key和value都是string类型的值（包括空字符串），如果存的不是字符串，会在存储前被转换成字符串。
### 4.1 length
- 返回Storage对象内item的数量，这是一个只读属性。
### 4.2 key(index)
- 返回第n项的key。当index的值超出了length，返回null。
### 4.3 getItem(key)
- 返回对应key值的value，如果没有，返回null。
### 4.4 setItem(key, value)
- 方法首先检查要设置的item是否存在，如果不存在，在Storage里加入该item；如果存在，更新这个item的value。如果无法存入新item，该方法会抛出QuotaExceedeErrorDOMException异常，不改变Storage内的任何内容（表示Storage已经存满了，Storage目前推荐的存储容量上限为5M）。
### 4.5 removeItem(key)
- 方法会删除指定的item，如果不存在指定的item，什么都不做。
### 4.6 clear()
- 方法会清空Storage里的所有item，如果Storage本来就是空的，什么都不做。  
### 5. Web Storage 存储事件
- 当localStorage或者sessionStorage的数据发生变化的时候，浏览器都会在其他对该数据可见的窗口对象上触发storage事件（本窗口除外）。
-重要：只有当存储数据真正发生变化时才会触发存储事件，比如给一个item重新设置一个和原来一样的value，或者是删除一个不存在的item是不会触发存储事件的。  

## Event loop
- 首先，js是单线程的，主要的任务是处理用户的交互，而用户的交互无非就是响应DOM的增删改，使用事件队列的形式，一次事件循环只处理一个事件响应，使得脚本执行相对连续，所以有了事件队列，用来储存待执行的事件。
- 那么事件队列的事件从哪里被push进来的呢。那就是另外一个线程叫事件触发线程做的事情了，他的作用主要是在定时触发器线程、异步HTTP请求线程满足特定条件下的回调函数push到事件队列中，等待js引擎空闲的时候去执行。
- 当然js引擎执行过程中有优先级之分，首先js引擎在一次事件循环中，会先执行js线程的主任务，然后会去查找是否有微任务microtask（promise），如果有那就优先执行微任务，如果没有，在去查找宏任务macrotask（setTimeout、setInterval）进行执行。

## Js事件流
>    [参考](https://www.cnblogs.com/st-leslie/p/5907556.html)  
![js-eventflow](https://raw.githubusercontent.com/PaulGuo5/Webnotes/master/img/js-eventflow.png)  
### 1. 一个完整的JS事件流是从window开始，最ndow的一个后回到wi过程。
### 2. 事件流被分为三个阶段(1-5)捕获过程、(5-6)目标过程、(6-10)冒泡过程。
### 3. 在冒泡过程中6比7早触发，也就解释了上面那题，为什么btn1,会比content先触发。  
+ 事件流分为两种，捕获事件流和冒泡事件流。
+ 捕获事件流从根节点开始执行，一直往子节点查找执行，直到查找执行到目标节点。
+ 冒泡事件流从目标节点开始执行，一直往父节点冒泡查找执行，直到查到到根节点。
+ DOM事件流分为三个阶段，一个是捕获节点，一个是处于目标节点阶段，一个是冒泡阶段。

- Example：  
```bash
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div style="width:200px;height:200px;background:lightblue" id="content">
        <div style="width:100px;height:100px;background: lightyellow;" id="btn1">
        </div>
    </div>
</body>
<script type="text/javascript">
    var content=document.getElementById("content");
    var btn1=document.getElementById('btn1');
    btn1.onclick=function(){
        alert("btn1");
    };
    content.onclick=function(){
        alert("content");
    }
</script>
</html>
```
- btn1比content先触发





## HTTP 的重定向
>    [参考](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Redirections)

- URL 重定向，也称为 URL 转发，是一种当实际资源，如单个页面、表单或者整个 Web 应用被迁移到新的 URL 下的时候，保持（原有）链接可用的技术。
- 该操作可以应用于多种多样的目标：网站维护期间的临时跳转，网站架构改变后为了保持外部链接继续可用的永久重定向，上传文件时的表示进度的页面，等等。

### 1. 永久重定向
- 这种重定向操作是永久性的。
- 它表示原 URL 不应再被使用，而应该优先选用新的 URL。
- 搜索引擎机器人会在遇到该状态码时触发更新操作，在其索引库中修改与该资源相关的 URL 。

### 2. 临时重定向
- 有时候请求的资源无法从其标准地址访问，但是却可以从另外的地方访问。
- 在这种情况下可以使用临时重定向。搜索引擎不会记录该新的、临时的链接。
- 在创建、更新或者删除资源的时候，临时重定向也可以用于显示临时性的进度页面。

### 3. 特殊重定向
- 除了上述两种常见的重定向之外，还有两种特殊的重定向。
- 304(Not Modified，资源未被修改)会使页面跳转到本地陈旧的缓存版本当中(该缓存已过期(?)）
- 300(Multiple Choice，多项选择)则是一种手工重定向：以 Web 页面形式呈现在浏览器中的消息主体包含了一个可能的重定向链接的列表，用户可以从中进行选择。

### 设定重定向映射的其他方法
1. HTML 重定向机制
- HTTP 协议中重定向机制是应该优先采用的创建重定向映射的方式，但是有时候 Web 开发者对于服务器没有控制权，或者无法对其进行配置。
- 针对这些特定的应用情景，Web 开发者可以在精心制作的 HTML 页面的 <head>  部分添加一个 <meta> 元素，并将其 http-equiv 属性的值设置为 refresh 。
- 当显示页面的时候，浏览器会检测该元素，然后跳转到指定的页面。
```bash
<head> 
  <meta http-equiv="refresh" content="0;URL=http://www.example.com/" />
</head>
```
- content 属性的值开头是一个数字，指示浏览器在等待该数字表示的秒数之后再进行跳转。建议始终将其设置为 0 来获取更好的可访问性。
- 显然，该方法仅适用于 HTML 页面（或类似的页面），然而并不能应用于图片或者其他类型的内容。
2. JavaScript 重定向机制
- 在 JavaScript 中，重定向机制的原理是设置 window.location 的属性值，然后加载新的页面。
```bash
window.location = "http://www.example.com/";
```
- 与 HTML 重定向机制类似，这种方式并不适用于所有类型的资源，并且显然只有在支持 JavaScript 的客户端上才能使用。
- 另外一方面，它也提供了更多的可能性，比如在只有满足了特定的条件的情况下才可以触发重定向机制的场景。

### 优先级
1. HTTP 协议的重定向机制永远最先触发，即便是在没有传送任何页面——也就没有页面被（客户端）读取——的情况下。
2. HTML 的重定向机制 (<meta>) 会在 HTTP 协议重定向机制未设置的情况下触发。
3. JavaScript 的重定向机制总是作为最后诉诸的手段，并且只有在客户端开启了 JavaScript 的情况下才起作用。
- 任何情况下，只要有可能，就应该采用 HTTP 协议的重定向机制，而不要使用  <meta> 标签。假如开发人员修改了 HTTP 重定向映射而忘记修改 HTML 页面的重定向映射，那么二者就会不一致，最终结果或者出现无限循环，或者导致其他噩梦的发生。





## 闭包
>    [参考](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)
- 闭包是函数和声明该函数的词法环境的组合。
```bash
function init() {
    var name = "Mozilla"; // name 是一个被 init 创建的局部变量
    function displayName() { // displayName() 是内部函数,一个闭包
        alert(name); // 使用了父函数中声明的变量
    }
    displayName();
}
init();
```
- init() 创建了一个局部变量 name 和一个名为 displayName() 的函数。displayName() 是定义在 init() 里的内部函数，仅在该函数体内可用。displayName() 内没有自己的局部变量，然而它可以访问到外部函数的变量，所以 displayName() 可以使用父函数 init() 中声明的变量 name 。但是，如果有同名变量 name 在 displayName() 中被定义，则会使用 displayName() 中定义的 name 。
- 词法作用域中使用的域，是变量在代码中声明的位置所决定的。嵌套的函数可以访问在其外部声明的变量。
```bash
function makeFunc() {
    var name = "Mozilla";
    function displayName() {
        alert(name);
    }
    return displayName;
}

var myFunc = makeFunc();
myFunc();
```
- JavaScript中的函数会形成闭包。 
- 闭包是由函数以及创建该函数的词法环境组合而成。这个环境包含了这个闭包创建时所能访问的所有局部变量。在我们的例子中，myFunc 是执行 makeFunc 时创建的 displayName 函数实例的引用，而 displayName 实例仍可访问其词法作用域中的变量，即可以访问到 name 。由此，当 myFunc 被调用时，name 仍可被访问，其值 Mozilla 就被传递到alert中。

```bash
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  // 7
console.log(add10(2)); // 12
```
- 在这个示例中，我们定义了 makeAdder(x) 函数，它接受一个参数 x ，并返回一个新的函数。返回的函数接受一个参数 y，并返回x+y的值。
- 从本质上讲，makeAdder 是一个函数工厂(他创建了将指定的值和它的参数相加求和的函数)。
- 在上面的示例中，我们使用函数工厂创建了两个新函数 — 一个将其参数和 5 求和，另一个和 10 求和。
- add5 和 add10 都是闭包。它们共享相同的函数定义，但是保存了不同的词法环境。在 add5 的环境中，x 为 5。而在 add10 中，x 则为 10。

### 实用的闭包
- 闭包很有用，因为它允许将函数与其所操作的某些数据（环境）关联起来。这显然类似于面向对象编程。在面向对象编程中，对象允许我们将某些数据（对象的属性）与一个或者多个方法相关联。
- 因此，通常你使用只有一个方法的对象的地方，都可以使用闭包。
- 在 Web 中，你想要这样做的情况特别常见。大部分我们所写的 JavaScript 代码都是基于事件的（ 定义某种行为），然后将其添加到用户触发的事件之上（比如点击或者按键）。我们的代码通常作为回调：为响应事件而执行的函数。
- Example:文本尺寸调整按钮可以修改 body 元素的 font-size 属性
```bash
function makeSizer(size) {
  return function() {
    document.body.style.fontSize = size + 'px';
  };
}

var size12 = makeSizer(12);
var size14 = makeSizer(14);
var size16 = makeSizer(16);
```

### 用闭包模拟私有方法
- 编程语言中，比如 Java，是支持将方法声明为私有的，即它们只能被同一个类中的其它方法所调用。
- 而 JavaScript 没有这种原生支持，但我们可以使用闭包来模拟私有方法。私有方法不仅仅有利于限制对代码的访问：还提供了管理全局命名空间的强大能力，避免非核心的方法弄乱了代码的公共接口部分。
- 下面的示例展现了如何使用闭包来定义公共函数，并令其可以访问私有函数和变量。这个方式也称为 模块模式（module pattern）：
```bash
var Counter = (function() {
  var privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  }   
})();

console.log(Counter.value()); /* logs 0 */
Counter.increment();
Counter.increment();
console.log(Counter.value()); /* logs 2 */
Counter.decrement();
console.log(Counter.value()); /* logs 1 */
```
- 在之前的示例中，每个闭包都有它自己的词法环境；而这次我们只创建了一个词法环境，为三个函数所共享：Counter.increment，Counter.decrement 和 Counter.value。
- 该共享环境创建于一个立即执行的匿名函数体内。这个环境中包含两个私有项：名为 privateCounter 的变量和名为 changeBy 的函数。这两项都无法在这个匿名函数外部直接访问。必须通过匿名函数返回的三个公共函数访问。
- 这三个公共函数是共享同一个环境的闭包。多亏 JavaScript 的词法作用域，它们都可以访问 privateCounter 变量和 changeBy 函数。

+ 你应该注意到我们定义了一个匿名函数，用于创建一个计数器。我们立即执行了这个匿名函数，并将他的值赋给了变量counter。我们可以把这个函数储存在另外一个变量makeCounter中，并用他来创建多个计数器。
```bash
var makeCounter = function() {
  var privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  }  
};

var Counter1 = makeCounter();
var Counter2 = makeCounter();
console.log(Counter1.value()); /* logs 0 */
Counter1.increment();
Counter1.increment();
console.log(Counter1.value()); /* logs 2 */
Counter1.decrement();
console.log(Counter1.value()); /* logs 1 */
console.log(Counter2.value()); /* logs 0 */
```
- 请注意两个计数器 counter1 和 counter2 是如何维护它们各自的独立性的。每个闭包都是引用自己词法作用域内的变量 privateCounter 。
- 每次调用其中一个计数器时，通过改变这个变量的值，会改变这个闭包的词法环境。然而在一个闭包内对变量的修改，不会影响到另外一个闭包中的变量。
- 以这种方式使用闭包，提供了许多与面向对象编程相关的好处 —— 特别是数据隐藏和封装。

> [参考](https://www.jianshu.com/p/d6365d449745)
- 闭包是指一个函数的内部函数被返回之后，在函数外部被另一个变量引用的时候，就形成了闭包。
- 通常用于创建一个内部变量（私有变量），使得这个内部变量不能被外部修改。但可以通过传递的指定函数借口来进行修改。
- 注意，由于闭包内的部分资源无法自动释放，容易造成内存泄漏





## JS原型和原型链
> [参考](https://github.com/mqyqingfeng/Blog/issues/2)
- 原型：每一个JavaScript对象(null除外)在创建的时候就会与之关联另一个对象，这个对象就是我们所说的原型，每一个对象都会从原型"继承"属性。
- __proto__：隐式原型，每个引用类型都有，每一个对象的私有属性，天生的。
- prototype：显示原型，所有的函数（构造函数？）都有，是后天赋予的。
- 原型链查找：调用一个对象的属性或方法，一旦这个对象没有，就去这个对象的__proto__中查找，对象的__proto__指向自己的构造函数的prototype属性。
- JavaScript中每个对象都有一个内置属性prototype，ES5之前没有方法可以得到这个属性，大多数浏览器都支持通过__proto__来访问。
- 
>[参考](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
- 基于原型链的继承：JavaScript 对象是动态的属性“包”（指其自己的属性）。JavaScript 对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。


## 网站的性能优化
- 两大原则：
1. 多使用内存、缓存或其他方法
2. 减少CPU计算、减少网络请求
- 从哪入手：
1. 加载页面和静态资源
+ 静态资源的压缩合并
+ 静态资源缓存
+ 使用CDN让资源加载更快
+ 使用SSR（服务端渲染），数据直接输出到HTML中
2. 页面渲染
+ CSS放前面，JS放后面
+ 懒加载（图片懒加载，下拉加载更多）
+ 减少DOM查询，对DOM查询做缓存
+ 减少DOM操作，多个操作尽量合并在一起
+ 事件节流
+ 尽早执行函数


## CDN（Content Delivery|Distribute Network）
- CDN是将源站内容分发至最接近用户的节点，使用户可就近取得所需内容，提高用户访问的响应速度和成功率。
- 解决因分布、带宽、服务器性能带来的访问延迟问题，适用于站点加速、点播、直播等场景。

## 怎样减少首屏加载时间
- 图片懒加载：先指向一张较小的或者不指向图片，将真正的图片地址放到元素属性如data-src中，监听页面的滚动事件，到滚动到时才加载。（可以给页面滚动事件加一个节流函数）

> [参考](https://juejin.im/post/583b10640ce463006ba2a71a)
- 将页面中的img标签src指向一张小图片或者src为空，然后定义data-src（这个属性可以自定义命名，我才用data-src）属性指向真实的图片。
- src指向一张默认的图片，否则当src为空时也会向服务器发送一次请求。
- 可以指向loading的地址。
> 注：图片要指定宽高
```bash
<img src="default.jpg" data-src="http://ww4.sinaimg.cn/large/006y8mN6gw1fa5obmqrmvj305k05k3yh.jpg" />
```
- 当载入页面时，先把可视区域内的img标签的data-src属性值负给src，然后监听滚动事件，把用户即将看到的图片加载。这样便实现了懒加载。


## 浏览器缓存机制，怎么处理浏览器缓存问题
- 主要是HTTP协议定义的缓存机制。浏览器依靠请求和相应中的头信息来控制缓存。
- 头信息中的Cache-Control的值可以是public、private、no-cache、no-store、no-transform等等等
+ public:所有内容都将被缓存。
+ private:内容只缓存到私有缓存中。
+ no-cache:所有内容都不会被缓存。
+ no-store:所有内容都不会被缓存到Internet临时文件中。
+ must-revalidate/proxy-revalidation:如果缓存的内容失败，请求必须发送到服务器/代理进行重新验证。
+ max-age=xxx:缓存的内容将在xxx秒失效，在HTTP1.1中可用，比expires、last-modified优先级高。（public, max-age:86400（一天60*60*24=86400））

> [参考](https://harttle.land/2017/04/04/using-http-cache.html)
- 整个Web系统架构在HTTP协议之上，利用HTTP的缓存机制不仅可以极大地减少服务器负载， 更重要的是加速页面的载入，以及减少用户的流量消耗。
- 浏览器询问服务器缓存是否有效，服务器返回 304 指示浏览器使用缓存。
- 资源仍然处于有效期时，浏览器会直接使用磁盘缓存（在刷新时稍有不同，见下文）。
- favicon.ico 直接来自磁盘缓存，而 localhost 文档则来自 304 缓存。
- Http响应头字段
+ Cache-Control 响应头表示了资源是否可以被缓存，以及缓存的有效期。
+ Etag 响应头标识了资源的版本，此后浏览器可据此进行缓存以及询问服务器。
+ Last-Modified 响应头标识了资源的修改时间，此后浏览器可据此进行缓存以及询问服务器。





## js和css的缓存
- localStorage相比cookie，可以缓存大体积的数据，而且是永久有效。所以，如果把js资源和css资源存储在localStorage中，则可以省去发送http请求所消耗的时间，大大提高用户的浏览体验。
- localStorage中的信息，客户端是可以任意修改的。如果哪个黑客想练手一下，可以任意注入js代码。那么，在页面刷新的时候，注入的代码也将会被执行。
- 应用场景
1. 非首屏渲染需要的css文件，可以做LS缓存。首屏渲染需要的css，需要按常规方式输出，因为SEO需要，不然爬虫爬取页面的时候，页面效果会很不好。而非首屏的css，则可以用LS缓存，减少资源下载时间。
2. 展示类、动画类等非业务主要逻辑的代码，可以做LS缓存。这样，可以一定程度上避免业务层的安全漏洞。当然，前端再怎么做防护都是一层薄纸。重要的，还是后台接口要做好安全保护。
3. 移动端可以做LS缓存。PC端做LS缓存，起到的优化作用不大。





## 更新http缓存
> [参考](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn)
- 可以在资源内容发生变化时更改其网址，强制用户下载新响应。 
- 通常情况下，可以通过在文件名中嵌入文件的指纹或版本号来实现—例如 style.x234dff.css。






## Cookie
> [参考](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)
- HTTP Cookie（也叫Web Cookie或浏览器Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。
- 通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。Cookie使基于无状态的HTTP协议记录稳定的状态信息成为了可能。
- Cookie主要用于以下三个方面：
1. 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
2. 个性化设置（如用户自定义设置、主题等）
3. 浏览器行为跟踪（如跟踪分析用户行为等）
- Cookie曾一度用于客户端数据的存储，因当时并没有其它合适的存储办法而作为唯一的存储手段，但现在随着现代浏览器开始支持各种各样的存储方式，Cookie渐渐被淘汰。由于服务器指定Cookie后，浏览器的每次请求都会携带Cookie数据，会带来额外的性能开销（尤其是在移动环境下）。新的浏览器API已经允许开发者直接将数据存储到本地，如使用 Web storage API （本地存储和会话存储）或 IndexedDB 。





## 浏览器加载过程，一个网站的网页是如何加载的（解析文本、树节点...）
- 加载一个资源的过程
1. 浏览器通过DNS(域名系统Domain name system)得到域名的IP地址
2. 向这个IP的服务器发送HTTP请求
3. 服务器收到，处理并返回HTTP请求
4. 浏览器得到返回内容
- 页面加载过程
1. 根据HTML结构生成DOM树
2. 根据CSS生成CSSOM（CSS Object Model）
3. 结合DOM和CSSOM，生成一颗渲染树
4. 根据渲染树开始渲染和展示
5. 遇到script时，会执行并阻塞渲染（JS可能会修改DOM树）






## apply, call, bind
> [参考](https://www.cnblogs.com/coco1s/p/4833199.html)
- 在 javascript 中，call 和 apply 都是为了改变某个函数运行时的上下文（context）而存在的，换句话说，就是为了改变函数体内部 this 的指向。
```bash
function fruits() {}
 
fruits.prototype = {
    color: "red",
    say: function() {
        console.log("My color is " + this.color);
    }
}
 
var apple = new fruits;
apple.say();    //My color is red


banana = {
    color: "yellow"
}
apple.say.call(banana);     //My color is yellow
apple.say.apply(banana);    //My color is yellow
```
- apply、call 的区别：
- 对于 apply、call 二者而言，作用完全一样，只是接受参数的方式不太一样。例如，有一个函数定义如下
```bash
var func = function(arg1, arg2) {
     
};
```
- 调用方式
```bash
func.call(this, arg1, arg2);
func.apply(this, [arg1, arg2])
```
- 其中 this 是你想指定的上下文，他可以是任何一个 JavaScript 对象(JavaScript 中一切皆对象)，call 需要把参数按顺序传递进去，而 apply 则是把参数放在数组里。　　
- JavaScript 中，某个函数的参数数量是不固定的，因此要说适用条件的话，当你的参数是明确知道数量时用 call 。
- 而不确定的时候用 apply，然后把参数 push 进数组传递进去。当参数数量不确定时，函数内部也可以通过 arguments 这个伪数组来遍历所有的参数。  

- bind() 方法与 apply 和 call 很相似，也是可以改变函数体内 this 的指向。
- bind()方法会创建一个新函数，称为绑定函数，当调用这个绑定函数时，绑定函数会以创建它时传入 bind()方法的第一个参数作为 this，传入 bind() 方法的第二个以及以后的参数加上绑定函数运行时本身的参数按照顺序作为原函数的参数来调用原函数。

+ apply、call、bind比较
```bash
var obj = {
    x: 81,
};
 
var foo = {
    getX: function() {
        return this.x;
    }
}
 
console.log(foo.getX.bind(obj)());  //81
console.log(foo.getX.call(obj));    //81
console.log(foo.getX.apply(obj));   //81
```
*当你希望改变上下文环境之后并非立即执行，而是回调执行的时候，使用 bind() 方法。而 apply/call 则会立即执行函数。*
+ apply 、 call 、bind 三者都是用来改变函数的this对象的指向的；
+ apply 、 call 、bind 三者第一个参数都是this要指向的对象，也就是想指定的上下文；
+ apply 、 call 、bind 三者都可以利用后续参数传参；bind 是返回对应函数，便于稍后调用；apply 、call 则是立即调用 。






## Js继承
- JavaScript 对象是动态的属性“包”（指其自己的属性）。JavaScript 对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。
```bash
// 让我们从一个自身拥有属性a和b的函数里创建一个对象o：
let f = function () {
   this.a = 1;
   this.b = 2;
}
/* 你要这么写也没区别
function f(){
  this.a = 1;
  this.b = 2;
}
*/
let o = new f(); // {a: 1, b: 2}
// 在f函数的原型上定义属性
f.prototype.b = 3;
f.prototype.c = 4;

// 不要在f函数的原型上直接定义 f.prototype = {b:3,c:4};这样会直接打破原型链
// o.[[Prototype]] 有属性 b 和 c   (其实就是o.__proto__或者o.constructor.prototype)

// o.[[Prototype]].[[Prototype]] 是 Object.prototye.
// 最后o.[[Prototype]].[[Prototype]].[[Prototype]]是null
// 这就是原型链的末尾，即 null，
// 根据定义，null 没有[[Prototype]].
// 综上，整个原型链如下: 
// {a:1, b:2} ---> {b:3, c:4} ---> Object.prototye---> null

console.log(o.a); // 1
// a是o的自身属性吗？是的，该属性的值为1

console.log(o.b); // 2
// b是o的自身属性吗？是的，该属性的值为2
// 原型上也有一个'b'属性,但是它不会被访问到.这种情况称为"属性遮蔽 (property shadowing)"

console.log(o.c); // 4
// c是o的自身属性吗？不是，那看看原型上有没有
// c是o.[[Prototype]]的属性吗？是的，该属性的值为4

console.log(o.d); // undefined
// d是o的自身属性吗？不是,那看看原型上有没有
// d是o.[[Prototype]]的属性吗？不是，那看看它的原型上有没有
// o.[[Prototype]].[[Prototype]] 为 null，停止搜索
// 没有d属性，返回undefined
``` 

- 继承方法
- JavaScript 并没有其他基于类的语言所定义的“方法”。在 JavaScript 里，任何函数都可以添加到对象上作为对象的属性。函数的继承与其他的属性继承没有差别，包括上面的“属性遮蔽”（这种情况相当于其他语言的方法重写）。
- 当继承的函数被调用时，this 指向的是当前继承的对象，而不是继承的函数所在的原型对象
```bash
var o = {
  a: 2,
  m: function(){
    return this.a + 1;
  }
};

console.log(o.m()); // 3
// 当调用 o.m 时,'this'指向了o.

var p = Object.create(o);
// p是一个继承自 o 的对象

p.a = 4; // 创建 p 的自身属性 a
console.log(p.m()); // 5
// 调用 p.m 时, 'this'指向 p. 
// 又因为 p 继承 o 的 m 函数
// 此时的'this.a' 即 p.a，即 p 的自身属性 'a'
```




## 跨域的解决办法（实现方式）cross-domain
- 跨域：浏览器不能执行其他网站的脚本。它是由浏览器的同源策略造成，是浏览器对JavaScript施加的安全限制。
- 同源指域名，协议，端口均相同。





## JS事件代理，怎么代理，什么好处（判断e.target）
- 事件代理就是利用事件冒泡，只指定一个事件处理程序。





## HTTP状态码
- 200：请求成功
- 301：资源（网页等）被永久转移到其他URL，浏览器会将网址和内容的抓取都转移到新的网址中；302为暂时性转移，浏览器会抓取新的网址的内容，但地址仍旧是旧网址的。
- 304：Not Modified，客户端发送附带条件的请求时（if-matched,if-modified-since,if-none-match,if-range,if-unmodified-since任一个）服务器端允许请求访问资源，但因发生请求未满足条件的情况后，直接返回304Modified（服务器端资源未改变，可直接使用客户端未过期的缓存）。304状态码返回时，不包含任何响应的主体部分。304虽然被划分在3xx类别中，但是和重定向没有关系。
- 404：请求的资源（网页等）不存在
- 500：内部服务器错误

> [参考](https://blog.csdn.net/wangjun5159/article/details/51239960)了新u
- 301 表示永久重定向（301 moved permanently），表示请求的资源分配rl，以后应使用新url。
- 302 表示临时性重定向（302 found），请求的资源临时分配了新url，本次请求暂且使用新url。
- 303 表示请求的资源路径发生改变，使用GET方法请求新url。她与302的功能一样，但是明确指出使用GET方法请求新url。




## www(.vip.)com 怎么把括号里的字符串取出来。（正则，如何创建正则式|对象 ，g和i分别代表什么）
```bash
/www(\.vip\.)com/

new RegExp('www\.vip\.com',"gi")
```

> [参考](https://blog.csdn.net/ssisse/article/details/52529372)
- /i (忽略大小写)
- /g (全文查找出现的所有匹配字符)
- /m (多行查找)
- /gi(全文查找、忽略大小写)
- /ig(全文查找、忽略大小写)



## 设计模式（工厂模式、单体模式、观察者模式）
> [参考](http://www.runoob.com/design-pattern/factory-pattern.html)
### 1. 工厂模式
- 概念：在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。
- 意图：定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行。
- 主要解决：主要解决接口选择的问题。
- 何时使用：我们明确地计划不同条件下创建不同实例时。
- 如何解决：让其子类实现工厂接口，返回的也是一个抽象的产品。
- 关键代码：创建过程在其子类执行。
- 应用实例： 
1. 您需要一辆汽车，可以直接从工厂里面提货，而不用去管这辆汽车是怎么做出来的，以及这个汽车里面的具体实现。 
2. Hibernate 换数据库只需换方言和驱动就可以。
- 优点： 
1. 一个调用者想创建一个对象，只要知道其名称就可以了。 
2. 扩展性高，如果想增加一个产品，只要扩展一个工厂类就可以。 3、屏蔽产品的具体实现，调用者只关心产品的接口。
- 缺点：每次增加一个产品时，都需要增加一个具体类和对象实现工厂，使得系统中类的个数成倍增加，在一定程度上增加了系统的复杂度，同时也增加了系统具体类的依赖。这并不是什么好事。
- 使用场景： 
1. 日志记录器：记录可能记录到本地硬盘、系统事件、远程服务器等，用户可以选择记录日志到什么地方。 
2. 数据库访问，当用户不知道最后系统采用哪一类数据库，以及数据库可能有变化时。 
3. 设计一个连接服务器的框架，需要三个协议，"POP3"、"IMAP"、"HTTP"，可以把这三个作为产品类，共同实现一个接口。
- 注意事项：作为一种创建类模式，在任何需要生成复杂对象的地方，都可以使用工厂方法模式。有一点需要注意的地方就是复杂对象适合使用工厂模式，而简单对象，特别是只需要通过 new 就可以完成创建的对象，无需使用工厂模式。如果使用工厂模式，就需要引入一个工厂类，会增加系统的复杂度。

### 2. 单体模式
- 单例模式（Singleton Pattern）是 Java 中最简单的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
- 这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。
- 注意：
1. 单例类只能有一个实例。
2. 单例类必须自己创建自己的唯一实例。
3. 单例类必须给所有其他对象提供这一实例。
- 意图：保证一个类仅有一个实例，并提供一个访问它的全局访问点。
- 主要解决：一个全局使用的类频繁地创建与销毁。
- 何时使用：当您想控制实例数目，节省系统资源的时候。
- 如何解决：判断系统是否已经有这个单例，如果有则返回，如果没有则创建。
- 关键代码：构造函数是私有的。
- 应用实例：
1. 一个班级只有一个班主任。
2. Windows 是多进程多线程的，在操作一个文件的时候，就不可避免地出现多个进程或线程同时操作一个文件的现象，所以所有文件的处理必须通过唯一的实例来进行。
3. 一些设备管理器常常设计为单例模式，比如一个电脑有两台打印机，在输出的时候就要处理不能两台打印机打印同一个文件。
- 优点：
1. 在内存里只有一个实例，减少了内存的开销，尤其是频繁的创建和销毁实例（比如管理学院首页页面缓存）。
2. 避免对资源的多重占用（比如写文件操作）。
- 缺点：没有接口，不能继承，与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心外面怎么样来实例化。
- 使用场景：
1. 要求生产唯一序列号。
2. WEB 中的计数器，不用每次刷新都在数据库里加一次，用单例先缓存起来。
3. 创建的一个对象需要消耗的资源过多，比如 I/O 与数据库的连接等。
注意事项：getInstance() 方法中需要使用同步锁 synchronized (Singleton.class) 防止多线程同时进入造成 
- instance 被多次实例化。

### 3. 观察者模式
- 当对象间存在一对多关系时，则使用观察者模式（Observer Pattern）。比如，当一个对象被修改时，则会自动通知它的依赖对象。观察者模式属于行为型模式。
- 意图：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。
- 主要解决：一个对象状态改变给其他对象通知的问题，而且要考虑到易用和低耦合，保证高度的协作。
- 何时使用：一个对象（目标对象）的状态发生改变，所有的依赖对象（观察者对象）都将得到通知，进行广播通知。
- 如何解决：使用面向对象技术，可以将这种依赖关系弱化。
- 关键代码：在抽象类里有一个 ArrayList 存放观察者们。
- 应用实例： 
1. 拍卖的时候，拍卖师观察最高标价，然后通知给其他竞价者竞价。 
2. 西游记里面悟空请求菩萨降服红孩儿，菩萨洒了一地水招来一个老乌龟，这个乌龟就是观察者，他观察菩萨洒水这个动作。
- 优点： 
1. 观察者和被观察者是抽象耦合的。 
2. 建立一套触发机制。
- 缺点： 
1. 如果一个被观察者对象有很多的直接和间接的观察者的话，将所有的观察者都通知到会花费很多时间。 
2. 如果在观察者和观察目标之间有循环依赖的话，观察目标会触发它们之间进行循环调用，可能导致系统崩溃。 
3. 观察者模式没有相应的机制让观察者知道所观察的目标对象是怎么发生变化的，而仅仅只是知道观察目标发生了变化。
- 使用场景：
1. 一个抽象模型有两个方面，其中一个方面依赖于另一个方面。将这些方面封装在独立的对象中使它们可以各自独立地改变和复用。
2. 一个对象的改变将导致其他一个或多个对象也发生改变，而不知道具体有多少对象将发生改变，可以降低对象之间的耦合度。
3. 一个对象必须通知其他对象，而并不知道这些对象是谁。
- 需要在系统中创建一个触发链，A对象的行为将影响B对象，B对象的行为将影响C对象……，可以使用观察者模式创建一种链式触发机制。
- 注意事项： 
1. JAVA 中已经有了对观察者模式的支持类。 
2. 避免循环引用。 
3. 如果顺序执行，某一观察者错误会导致系统卡壳，一般采用异步方式。



## AppCan是什么框架来的
- 移动应用开发，混合应用开发，web+原生。


## JS定时器，写定时器用到了哪些函数模块
- setInterval


## jQuery选择器哪些加载起来效率更高
- ID选择器加载最高效，因为使用了js的getElementById函数。其次是类型选择器，然后是class选择器。
- 使用类选择器时结合类型选择器，例如input.class


## AJAX
- Ajax是异步JavaScript和XML，用于在Web页面中实现异步数据交互。
- 通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。
- 传统的网页（不使用 Ajax）如果需要更新内容，必须重载整个网页页面。
> [参考](https://blog.csdn.net/sinat_35861727/article/details/78809986)
- 一个完整的AJAX请求包括五个步骤:
1. 创建XMLHTTPRequest对象
2. 使用open方法创建http请求,并设置请求地址
3. 设置发送的数据，开始和服务器端交互
4. 注册事件
5. 获取响应并更新界面



## 重绘（redraw）和重排(reflow)有什么区别
- 重绘：是一个元素的外观变化所引发的浏览器行为；例如改变visibility、outline、背景色等属性。
- 重排：是引起DOM树重新计算的行为。引发重排的行为：添加、删除可见的DOM；元素位置改变；元素的尺寸改变；页面渲染初始化；浏览器尺寸改变。
- 浏览器会维护一个队列，把所有引起重排、重绘的操作放入这个队列中。等队列的操作达到一定的事件间隔，浏览器就会flush队列。如果向浏览器请求一些style信息，会让浏览器flush队列。
- 让要操作的元素进行“离线处理”，处理完后一起更新
```bash
使用DocumentFragment进行缓存操作，引发一次回流和重绘。

使用display:none技术，只引发两次回流和重绘。

使用cloneNode(true or false)和replaceChild技术，引发一次回流和重绘。
```



## 用js数组实现数据结构-堆栈和队列
> [参考](https://blog.csdn.net/gdut_2012/article/details/25010863)
- 堆栈
```bash

function Stack(){
    //存储元素数组
    var aElement = new Array();
    /*
    * @brief: 元素入栈
    * @param: 入栈元素列表
    * @return: 堆栈元素个数
    * @remark: 1.Push方法参数可以多个
    *    2.参数为空时返回-1
    */
    Stack.prototype.Push = function(vElement){
      //arguments对象是所有（非箭头）函数中都可用的局部变量。你可以使用arguments对象在函数中引用函数的参数。
      //此对象包含传递给函数的每个参数，第一个参数在索引0处。
        if (arguments.length == 0)
            return - 1;
        //元素入栈
        for (var i = 0; i < arguments.length; i++){
            aElement.push(arguments[i]);
        }
        return aElement.length;
    }
    /*
    * @brief: 元素出栈
    * @return: vElement
    * @remark: 当堆栈元素为空时,返回null
    */
    Stack.prototype.Pop = function(){
        if (aElement.length == 0)
            return null;
        else
            return aElement.pop();
    }
    /*
    * @brief: 获取堆栈元素个数
    * @return: 元素个数
    */
    Stack.prototype.GetSize = function(){
        return aElement.length;
    }
    /*
    * @brief: 返回栈顶元素值
    * @return: vElement
    * @remark: 若堆栈为空则返回null
    */
    Stack.prototype.GetTop = function(){
        if (aElement.length == 0)
            return null;
        else
            return aElement[aElement.length - 1];
    }
    /*
    * @brief: 将堆栈置空  
    */
    Stack.prototype.MakeEmpty = function(){
        aElement.length = 0;
    }
    /*
    * @brief: 判断堆栈是否为空
    * @return: 堆栈为空返回true,否则返回false
    */
    Stack.prototype.IsEmpty = function(){
        if (aElement.length == 0)
            return true;
        else
            return false;
    }
    /*
    * @brief: 将堆栈元素转化为字符串
    * @return: 堆栈元素字符串
    */
    Stack.prototype.toString = function(){
        var sResult = (aElement.reverse()).toString();
        aElement.reverse()
        return sResult;
    }
}
```

- 队列
```bash
function Queue(){
    //存储元素数组
    var aElement = new Array();
    /*
    * @brief: 元素入队
    * @param: vElement元素列表
    * @return: 返回当前队列元素个数
    * @remark: 1.EnQueue方法参数可以多个
    *    2.参数为空时返回-1
    */
    Queue.prototype.EnQueue = function(vElement){
        if (arguments.length == 0)
            return - 1;
        //元素入队
        for (var i = 0; i < arguments.length; i++){
            aElement.push(arguments[i]);
        }
        return aElement.length;
    }
    /*
    * @brief: 元素出队
    * @return: vElement
    * @remark: 当队列元素为空时,返回null
    */
    Queue.prototype.DeQueue = function(){
        if (aElement.length == 0)
            return null;
        else
            return aElement.shift();
 
    }
    /*
    * @brief: 获取队列元素个数
    * @return: 元素个数
    */
    Queue.prototype.GetSize = function(){
        return aElement.length;
    }
    /*
    * @brief: 返回队头素值
    * @return: vElement
    * @remark: 若队列为空则返回null
    */
    Queue.prototype.GetHead = function(){
        if (aElement.length == 0)
            return null;
        else
            return aElement[0];
    }
    /*
    * @brief: 返回队尾素值
    * @return: vElement
    * @remark: 若队列为空则返回null
    */
    Queue.prototype.GetEnd = function(){
        if (aElement.length == 0)
            return null;
        else
            return aElement[aElement.length - 1];
    }
    /*
    * @brief: 将队列置空  
    */
    Queue.prototype.MakeEmpty = function(){
        aElement.length = 0;
    }
    /*
    * @brief: 判断队列是否为空
    * @return: 队列为空返回true,否则返回false
    */
    Queue.prototype.IsEmpty = function(){
        if (aElement.length == 0)
            return true;
        else
            return false;
    }
    /*
    * @brief: 将队列元素转化为字符串
    * @return: 队列元素字符串
    */
    Queue.prototype.toString = function(){
        var sResult = (aElement.reverse()).toString();
        aElement.reverse()
        return sResult;
    }
}
```
- 测试：
```bash
var oStack = new Stack();
oStack.Push("abc", "123", 890);
console.log(oStack.toString());
oStack.Push("qq");
console.log(oStack.toString());
//  alert(oStack.GetSize());
//  alert(oStack.Pop());
//  alert(oStack.GetTop());
//  oStack.MakeEmpty();
//  alert(oStack.GetSize());
//  alert(oStack.toString());
delete oStack;
var oQueue = new Queue();
oQueue.EnQueue("bbs", "fans", "bruce");
console.log(oQueue.toString());
oQueue.EnQueue(23423);
console.log(oQueue.toString());
//  alert(oQueue.DeQueue());
//  alert(oQueue.GetSize());
//  alert(oQueue.GetHead());
//  alert(oQueue.GetEnd());
//  oQueue.MakeEmpty();
//  alert(oQueue.IsEmpty());
//  alert(oQueue.toString());
delete oQueue;
```


## 缓存
> [参考](https://heyingye.github.io/2018/04/16/%E5%BD%BB%E5%BA%95%E7%90%86%E8%A7%A3%E6%B5%8F%E8%A7%88%E5%99%A8%E7%9A%84%E7%BC%93%E5%AD%98%E6%9C%BA%E5%88%B6/)
- 根据是否需要向服务器重新发起HTTP请求将缓存过程分为两个部分，分别是强制缓存和协商缓存 。
- 强制缓存就是向浏览器缓存查找该请求结果，并根据该结果的缓存规则来决定是否使用该缓存结果的过程，强制缓存的情况主要有三种(暂不分析协商缓存过程)，如下：
1. 不存在该缓存结果和缓存标识，强制缓存失效，则直接向服务器发起请求（跟第一次发起请求一致）
2. 存在该缓存结果和缓存标识，但该结果已失效，强制缓存失效，则使用协商缓存(暂不分析)
3. 存在该缓存结果和缓存标识，且该结果尚未失效，强制缓存生效，直接返回该结果
- 协商缓存就是强制缓存失效后，浏览器携带缓存标识向服务器发起请求，由服务器根据缓存标识决定是否使用缓存的过程，主要有以下两种情况：
1. 协商缓存生效，返回304
2. 协商缓存失效，返回200和请求结果结果