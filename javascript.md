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