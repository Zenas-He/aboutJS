有误，待修改。

&emsp;<code>mounted</code>钩子函数是由<code>core/vdom/patch.js</code>的<code>createPatchFunction</code>函数返回的<code>patch</code>函数调用的。其中最简单的逻辑是在页面初始化时渲染原生节点(appendChild)之后调用<code>vonde</code>的<code>insert</code>钩子调用<code>mounted</code>。  
&emsp;<code>mounted</code>钩子主要用于获取已渲染的元素的属性，而什么时候才能拿到元素的属性，在<code>appendChild</code>之后立即调用<code>mounted</code>钩子会不会元素还没有渲染好呢？  
&emsp;这就要涉及到浏览器的重绘和重排了，当页面上的元素被修改之后，会进行重绘、重排，如果去获取元素的属性，JS线程会被渲染线程阻塞，在浏览器完成重绘、重排之后获取属性的值，并继续执行js代码。  

&emsp;PS：一般大家都会提到js线程执行时会阻塞渲染线程，只有在宏观任务和微观任务都执行完成之后才进行渲染，但是如果有了解到重绘、重排的优化。  
&emsp;里面有一个广泛提到的例子是：修改元素的一个高并且打印出来，再修改元素的宽并且打印出来，这种情况下触发了两次重排，如果js线程阻塞渲染线程的规则在这里适用的话，那么打印出来的应该都是修改之前的值。  
