## React与Dom
### 1.findDOMNode
&emsp;&emsp;DOM 真正被添加到 HTML 中的生命周期方法是 componentDidMount 和 componentDidUpdate 方法。在这两个方法中，我们可以获取真正的 DOM 元素。React 提供的获取 DOM 元素的方法有两种，其中一种就是 ReactDOM 提供的 findDOMNode:
```
    import React, { Component } from 'react'; 
    import ReactDOM from 'react-dom'; 
    class App extends Component { 
    componentDidMount() { 
    // this 为当前组件的实例
    const dom = ReactDOM.findDOMNode(this); 
    } 
    render() {} 
    }
```
&emsp;&emsp;dom 会返回render中的内容，实际应用中**搭配选择器或refs使用**。其中refs是访问到组件内部DOM节点可靠的方法：
```
    this.refs.btn.style.color = 'red'
```
如何搭配运用？
```
    let domRight = document.getElementsByClassName('target-container')[0]
    let resultRight = findDOMNode(domRight).contains(e.target); //组件外部用选择器
    let result = findDOMNode(this.refs.refTest).contains(e.target); //组件内部用refs
```
### 2.refs
&emsp;&emsp; ReactDOM 的 render 方法，比如我们渲染了一个 App 组件到 root 节点下：
```
const myAppInstance = ReactDOM.render(<App />, document.getElementById('root'));
```
