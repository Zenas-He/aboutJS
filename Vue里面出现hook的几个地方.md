- lifecycle hooks  
- vdom modules hooks  
- component hooks  
在/core/vdom/patch.js的patchVnode函数中，如果节点可更新，则会按顺序调用vdom钩子和component钩子对vnode进行更新。
