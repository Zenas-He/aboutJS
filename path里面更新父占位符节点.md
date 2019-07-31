patch方法被vm._update调用，根据vnode生成DOM节点，其中的主要逻辑有三处：1.创建DOM节点，2.如果vnode一样，就根据js对象进行比较并生成新的DOM节点，3.如果vnode不一致，创建新DOM节点，更新父占位符节点，再删除旧DOM节点。  
更新父占位符节点时调用的cbs.destroy和cbs.create都是对vnode.parent的elm或者componentInstance进行操作，vnode.parent.elm等于vnode.elm，那么这里的实现就很明显了，看代码是对占位符节点进行模块更新，实际上是对生成的DOM节点的根节点(或者组件实例)进行操作。   
为什么这里要对DOM根节点进行操作呢？因为模块更新内包含了class、style等，都是对DOM节点进行操作，当vnode不一样时，认为DOM节点也不一样了，之前根节点是div，现在是ul，那么类和样式这些vue里面的模块必然要重新设置了。
