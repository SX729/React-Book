## 1. 采用PureComponent组件
&emsp;&emsp;把继承类从 Component 换成 PureComponent 即可。PureComponent通过prop和state的浅比较来实现shouldComponentUpdate,可以减少不必要的 render 操作的次数，从而提高性能;
## 2. shouldComponentUpdate
&emsp;&emsp;组件挂载之后，每次调用setState后都会调用shouldComponentUpdate判断是否需要重新渲染组件。正确的组件渲染也是性能优化的手段之一;