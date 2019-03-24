#### **基本表现**
DOM2级事件模型为树状结构的通用事件系统设计，所以产生了事件流以及事件捕获(CAPTURING_PHASE)、处理目标(AT_TARGET)、事件冒泡(BUBBLING_PHASE)三部分，事件捕获从document开始，依次寻找子元素，一直到事件目标结束，事件冒泡则相反。  
在事件流的过程当中，不同对象可以进行的操作也不尽相同，可以参考[window、document、html和body的区别](https://github.com/Zenas-He/aboutJS/blob/master/window%E3%80%81document%E3%80%81html%E5%92%8Cbody%E7%9A%84%E5%8C%BA%E5%88%AB.md)。  

在查看文档的过程中，提到事件流是从document开始的，但是在了解事件接口时发现现在的事件流是从window开始的，具体原因会在后面的章节提到。  

#### **事件监听**
DOM0级事件中用<code>onclick</code>来定义事件函数，而在事件流当中则是用事件监听器进行替代。事件注册接口<code>EventTarget</code>为元素提供了添加事件监听的方法<code>addEventListener</code>，在元素上对应的事件被触发时执行之前添加到元素上的监听函数。相应的，事件注册接口也提供了<code>removeEventListener</code>移除事件监听方法。

<code>EventTarget</code>提供了一个<code>dispatchEvent</code>方法用于通过JS分发事件，替代事件的直接实现。如果我创建一个<code>click</code>事件并使用特定元素进行分发，等同于在该元素上进行鼠标点击，实例代码如下：
><code>let evt = document.createEvent("HTMLEvents")</code>  
><code>evt.initEvent("click", true, true)</code>  
><code>document.getElementById('test').dispatchEvent(evt)</code>

在分发事件的过程中，事件初始化函数<code>initEvent</code>的第二个参数叫</code>canBubble</code>，用于决定是否有事件冒泡，而事件捕获是一直都有的，可以看一下这个例子:[dispatchEvent has capturing and bubbling behavior](https://codepen.io/Zenas-He/pen/moaqLp)

#### **事件接口**
事件接口<code>Event</code>用于向上面提到的事件监听的方法提供事件相关的上下文信息，并且会以第一个参数传入方法。  
事件模块有四类：<code>MutationEvent</code>、<code>HTMLEvents</code>、<code>MouseEvents</code>、<code>UIEvents</code>，根据它们创建的事件在继承Event的基础上为方法提供额外的信息。为了了解事件接口的作用，我写了一个例子打印该接口的参数[Interface Event](https://codepen.io/Zenas-He/pen/GePVza)。

参数当中有一个属性<code>path</code>记录了事件树的路径，值为<code>[div#test, body, html, document, Window]</code>，<code>path</code>内是有<code>Window</code>的，所以事件流的最上层应该是<code>window</code>。而根据文档发现，是在最新的DOM Level 3 Events中，<code>window</code>被添加到事件流中进去了。

#### **事件模块**
1.MutationEvent(已在DOM3级事件中被废弃)  
包含事件：DOM节点增删改等

2.MouseEvent  
包含事件：鼠标点击、松开和移动等

3.UIEvent  
包含事件：聚焦、失去焦点和元素被激活

4.HTMLEvent  
包含事件：页面加载和移除，选择或提交文本框，页面滚动和页面大小改变等

*w3cschool中文文档上给的用于<code>createEvent</code>的事件模块是3个，并不完整，这里有一份更完整的可供参阅：[可用的事件模块参数](https://dom.spec.whatwg.org/#dom-document-createevent)*

#### **事件异常与取消**
需要注意的是，任何事件监听器产生的异常都不会影响事件的传播，比如你在事件冒泡时触发了点击事件，通过xhr获取数据出错，即使不捕获异常也不会影响祖先元素触发点击事件。  

事件捕获从上往下，基本没啥特别需要注意的，不过和事件捕获相比，事件冒泡是由下到上的，那么就会出现子元素影响父元素的情况，举个差不多的例子就是在数组遍历的时候往数组里增删元素。  

事件冒泡做的处理就是在处理目标之前确定下来整颗事件树，即使在事件流过程中对元素进行了修改，也不会影响事件冒泡的执行，可以看一下这个子元素响应时删除父元素的例子：[changes of element tree don't affect event bubbling](https://codepen.io/Zenas-He/pen/OqrqEZ)。  

事件捕获和事件冒泡可以通过<code>stopPropagation</code>函数阻止事件流的进一步执行，而<code>preventDefault</code>可以阻止元素默认事件的执行。

**参考**  
[DOM2级事件](https://www.w3.org/TR/DOM-Level-2-Events/events.html)  
[DOM3级事件](https://www.w3.org/TR/DOM-Level-3-Events/)
