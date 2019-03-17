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
+ max-age=xxx(xxx is numeric):缓存的内容将在xxx秒失效，在HTTP1.1中可用，比expires、last-modified优先级高。

> [参考](https://harttle.land/2017/04/04/using-http-cache.html)
- 整个Web系统架构在HTTP协议 之上， 利用HTTP的缓存机制不仅可以极大地减少服务器负载， 更重要的是加速页面的载入，以及减少用户的流量消耗。
- 浏览器询问服务器缓存是否有效，服务器返回 304 指示浏览器使用缓存。
- 资源仍然处于有效期时，浏览器会直接使用磁盘缓存（在刷新时稍有不同，见下文）。
- 



