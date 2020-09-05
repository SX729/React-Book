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
### 2.render
&emsp;&emsp; 把 React 渲染的
Virtual DOM 渲染到浏览器的 DOM 当中，就要使用 render 方法了，语法：
```
ReactComponent render(
    ReactElement element,
    DOMElement container,
    [function callback]
)
```
&emsp;&emsp;该方法把元素挂载到 container 中，并且返回 element 的实例（即 refs 引用）,当然，如果
是无状态组件，render 会返回 null。当组件装载完毕时，callback 就会被调用。

- 无状态组件：主要用来定义模板，接收来自父组件props传递过来的数据，无状态组件应该保持模板的纯粹性，以便于组件复用。
    ```
        var Header = (props) = (
            <div>{props.xxx}</div>
        );
    ```
- 有状态组件：主要用来定义交互逻辑和业务数据，使用{this.state.xxx}的表达式把业务数据挂载到容器组件的实例上，然后传递props到展示组件，展示组件接收到props，把props塞到模板里面。
    ```
        class Home extends React.Component {
            constructor(props) {
                super(props);
            };
            render() {
                return (
                    <Header title={}/>
                )
            }
        }
    ```
- 有状态组件也可以叫做容器组件，无状态组件也可以叫做展示组件

&emsp;&emsp;比如我们渲染了一个 App 组件到 root 节点下：
```
const myAppInstance = ReactDOM.render(
    <App />, 
    document.getElementById('root')
);
```
&emsp;&emsp;当组件在初次渲染之后再次更新时，React 不会把整个组件重新渲染一次，而会用它高效的DOM diff 算法做局部的更新。

### 3.$refs
&emsp;&emsp;它是 React 组件中非常特殊的 prop，可以附加到任何一个组件上。refs 即 reference，组件被调用时会新建一个该组件的实例，而 refs 就会指向这个实例。
&emsp;&emsp;它可以是一个回调函数，这个回调函数会在组件被挂载后立即执行。
```
<input type="text" ref={(ref) => this.myTextInput = ref} />
```
&emsp;&emsp;也可以使用 refs 获得组件内部的 DOM。
```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';
class App extends Component {
    componentDidMount() {
        // myComp 是 Comp 的一个实例，因此需要用 findDOMNode 转换为相应的 DOM
        const myComp = this.refs.myComp; //实例
        const dom = ReactDOM.findDOMNode(myComp); //对应的dom
    }
    render() {
        return (
        <div>
        <Comp ref="myComp" />
        </div>
        );
    }
}
```
动态组件如何获取?

### 3.React以外的Dom操作
&emsp;&emsp;尽量减少，但是有时无法避免。比如当点击组件以外区域时可以收缩此类组件：
```
componentDidUpdate(prevProps, prevState) {
    if (!this.state.isActive && prevState.isActive) {
        document.removeEventListener('click', this.hidePopup);
    }
    if (this.state.isActive && !prevState.isActive) {
        document.addEventListener('click', this.hidePopup);
    }
}   
componentWillUnmount() {
    document.removeEventListener('click', this.hidePopup);
}
hidePopup(e) {
    if (!this.isMounted()) { return false; }
    const node = ReactDOM.findDOMNode(this);
    const target = e.target || e.srcElement;
    const isInside = node.contains(target);
    if (this.state.isActive && !isInside) {
    this.setState({
    isActive: false,
    });
}}
```