#### **1.事件处理机制**  
DOM0级事件为事件绑定方法，例如<code>window.onclick = () => {}</code>，如果想要为元素的同一事件绑定新的方法，则会将现有的方法替换掉。而DOM2级事件为事件添加监听器，例如<code>window.addEventListener('click', () => {})</code>，元素的同一事件上可以添加多个方法。  

<code>onclick</code>使用钩子函数绑定方法，并且有且只有一个，可以说是写起来方便，用起来方便，但是不利于扩展。<code>addEventListener</code>使用回调函数添加方法，写起来更复杂一些，但是不论是动态提供事件名称，还是可以添加多个方法，特性都更加的友好。

#### **2.混合使用**
如果一个元素中既有<code>onclick</code>，也有<code>addEventListener</code>，那么事件捕获会先触发，然后事件冒泡和DOM0级事件会同时触发。如果该元素是目标元素，那么事件冒泡也会和后两者同时触发。事件方法同时触发时顺序为：按照方法添加顺序依次触发（*该触发排序规则在DOM3级事件中被制定*）。

想要有更直观的了解事件触发顺序可以看一下这个例子：[hook and callback](https://codepen.io/Zenas-He/pen/Oqdjjd)
