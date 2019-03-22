首先，标题上的四个对象是包含关系，window包含了document，document包含了html，那这篇文章的区别讲的就是window有而document没有的地方了，html和body依此类推。  

1.window和document  
&emsp;1）如果在页面内添加一个iframe标签，那么浏览器会在现有window下内嵌一个window，所有的数据加载也是基于新window实现的，而不是document。  
&emsp;2) window包含history，记录了该窗口内不断变化的document的路径情况。  
&emsp;3) window内包含创建该窗口的浏览器的信息以及操作系统信息，可以通过window.Navigator进行查看。  
&emsp;4）window.Screen能够获取用户设备显示屏幕的信息，包括色深、DPI、颜色分辨率和屏幕刷新率。  
&emsp;5）window.innerHeight等同于document.documentElement.clientHeight，会记录document的高度，而window.outerHeight则会记录window的高度，还包含了工具栏、菜单栏、地址栏和标签栏(以PC版chrome为例)。举个例子，我调用<code>window.close()</code>会关闭当前标签，调用<code>window.open('','','toolbar=no,menubar=no')</code>会创建一个没有菜单栏，也没有工具栏的新标签出来。  
&emsp;在我看来，window比较实用的一点是能够对浏览器进行控制，实现更多定制化的开发。当然，Electron、PWA这样的技术对浏览器的修改更加彻底了。  

2.document和html  
&emsp;1）document是浏览器整个画布的范围，而html是整个网页的范围，比如一个1920 1080的显示器，document的大小差不多1900 800，如果我限定整个body的宽为800，那html的宽也就只有800了，页面的左右两边空白用鼠标点击是不会触发标签内的事件的。  
&emsp;2）<code>document.getElementsByTagName('html')[0] === window.document.documentElement</code>的结果为true，<code>window.document.children[0] === window.document.documentElement</code>的结果也为true，打印<code>document.nodeType</code>为9，<code>document.documentElement.nodeType</code>为1，说明document表示整个文档，而html是它的第一个子元素，应该也是唯一一个子元素，因为为了方便使用，给了html<code>document.documentElement</code>这个别名便于调用。  
&emsp;3）document除了HTML元素集合外，还有cookie、域名、referrer和标题等信息。  

3.html和body  
&emsp;1）正常情况下，head和body是html唯一的两个子元素,head里包含页面配置参数、名称，打开页面时加载的脚本和样式。  
&emsp;2）为了移动端网页适配，一般会以rem作为css里的大小单位，而为了统一使用，rem是以html的font-size属性来定的。  
