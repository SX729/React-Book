## 1. 采用PureComponent组件
把继承类从 Component 换成 PureComponent 即可。PureComponent通过prop和state的浅比较来实现shouldComponentUpdate,可以减少不必要的 render 操作的次数，从而提高性能