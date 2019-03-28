在<code>new Vue</code>的时候，vue会调用core/instance/index.js里的Vue函数来进行初始化。而在该js内可以看到的是，Vue函数被创建出来之后会进行一系列的混入，然后才会被导出被我们用于创建Vue对象。  

而这些混入里有提供_init原型函数，初始化$data、$props和$watch原型属性，提供$on、$once、$off和$emit原型函数，提供_update和$destroy原型函数以及一系列用于渲染的函数。  

在声明方法之后，Vue可以进行初始化了，初始化的第一步是对options进行整理(-)，然后初始化代理(-)。  

接着初始化生命周期，首先会判断是否有父组件，如果自身是非抽象组件，而父组件是抽象组件，如keep-live，那么会一直向上追溯，直到遇到非抽象组件，将自身作为该组件的子组件，以便于渲染和生命周期的作用。处理完之后把$children、$refs和_isDestroyed这样的生命周期位清空。  

生命周期初始化之后是事件初始化，首先将_events保存事件的数组清空，然后将父组件传进来的方法($listeners)更新为事件，更新的实现在vdom/helpers/update-listeners.js里，这样在beforeCreate的时候也可以触发父组件的事件了。  

$listeners和$props都由父组件提供，那么在调用beforeCreate钩子之前，需要进行渲染初始化，$listeners和$props都会变成可交互的。如果有了解过HOC，就知道除了前面提到的两个属性，slot也会进行处理(-)，以属性$slots保存。除了处理父组件传进来的数据，也会提供$createElement用于根据上下文创建元素，和_c用于Vue内部创建元素。  

在上面的这些处理全部完成之后，beforeCreate钩子被调用。  

在调用beforeCreate之后，Vue会对数据和方法进行处理。  
