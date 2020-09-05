## React 生命周期
&emsp;&emsp;React 组件的生命周期可以分为**挂载、渲染和卸载**这几个阶段。

生命周期的方法有：

- **componentWillMount** 在渲染前调用。
- **componentDidMount** : 在第一次渲染后调用。之后组件已经生成了对应的DOM结构。 可以在这个方法中调用setTimeout, setInterval或者发送AJAX请求等操作(防止异步操作阻塞UI)。

- **componentWillReceiveProps** 在组件接收到一个新的 prop (更新后)时被调用。这个方法在初始化render时不会被调用。

- **shouldComponentUpdate** 返回一个布尔值。在组件接收到新的props或者state时被调用。在初始化时或者使用forceUpdate时不被调用。可以在你确认不需要更新组件时使用。

- **componentWillUpdate**在组件接收到新的props或者state但还没有render时被调用。在初始化时不会被调用。

- **componentDidUpdate**在组件完成更新后立即调用。在初始化时不会被调用。

- **componentWillUnmount**在组件从 DOM 中移除之前立刻被调用。
![](/images/图例4.png)
因此，我们可以把 React 生命周期分成两类：

1. 当组件在挂载或卸载时；
2. 当组件接收新的数据时，即组件更新时。

## 组件的挂载或卸载

&emsp;&emsp;这个过程主要做组件状态的初始化。
- **componentWillMount()** 组件挂载之前、渲染前：

&emsp;&emsp;在组件挂载之前调用且全局只调用一次。如果在这个钩子里可以setState，render后可以看到更新后的state，不会触发重复渲染。该生命周期可以发起异步请求，并setState。（React v16.3后废弃该生命周期，可以在constructor中完成设置state）
- **render()**  渲染组件

&emsp;&emsp;render是一个React组件必须定义的生命周期，用来渲染dom。render必须返回reactDom:
```
  render() {
    return (
      <div className="workspace-dialog-result">
      </div>
    );
```
- **componentDidMount()** 组件挂载完成后

&emsp;&emsp;在组件挂载完成后调用，且全局只调用一次。可以在这里使用refs，获取真实dom元素。该钩子内也可以发起异步请求，并在异步请求中可以进行setState。同步使用setState 方法,组件会再次更新，不过在初始化过程就渲染了两次组件并不是一件好事。
- **componentWillUnmount()**  组件即将被卸载

&emsp;&emsp;组件被卸载的时候执行一些清理方法。一般在componentDidMount里面注册的事件需要在这里删除。如事件回收或是清除定时器。

## 组件的更新
&emsp;&emsp;更新过程指的是父组件向下传递 props 或组件自身执行 setState 方法时发生的一系列更新动作。如果组件自身更新了，会依次执行 （componentWillReceiveProps） 、shouldComponentUpdate、componentWillUpdate 、
render 和 componentDidUpdate
- **componentWillReceiveProps(nextProps)**  props即将变化之前：

&emsp;&emsp;props发生变化以及父组件重新渲染时都会触发该生命周期，在该钩子内可以通过参数nextProps获取变化后的props参数，通过this.props访问之前的props。该生命周期内可以进行setState。

- **shouldComponentUpdate(nextProps, nextState)** 是否重新渲染：

&emsp;&emsp;组件挂载之后，每次调用setState后都会调用shouldComponentUpdate判断是否需要重新渲染组件。默认返回true，需要重新render。返回false则不触发渲染。而默认情况下，React 会渲染所有的节点，因为 shouldComponentUpdate 默认返回 true。正确
的组件渲染从另一个意义上说，也是性能优化的手段之一。（使用PureComponent）
- **componentWillUpdate(nextProps, nextState)** 

&emsp;&emsp;shouldComponentUpdate返回true或者调用forceUpdate之后，componentWillUpdate会被调用。不能在该钩子中setState，会触发重复循环。

- **componentDidUpdate()** 完成组件更新渲染：

&emsp;&emsp;除了首次render之后调用componentDidMount，其它render结束之后都是调用componentDidUpdate。该钩子内setState有可能会触发重复渲染，需要自行判断，否则会进入死循环。

### 常用：componentDidMount异步请求后setState, componentWillReceiveProps 获取props更新



### 了解：React v16.3 新加入的生命周期
react v16.3删掉以下三个生命周期
> componentWillMount<br/>
> componentWillReceiveProps<br/>
> componentWillUpdate<br/>

新增两个生命周期
- static getDerivedStateFromProps(nextProps, prevState)

>- 触发时间：在组件构建之后(虚拟dom之后，实际dom挂载之前) ，以及每次获取新的props之后。
>- 每次接收新的props之后都会返回一个对象作为新的state，返回null则说明不需要更新state.
>- 配合componentDidUpdate，可以覆盖componentWillReceiveProps的所有用法

- getSnapshotBeforeUpdate(prevProps, prevState)

>- 触发时间: update发生的时候，在render之后，在组件dom渲染之前。
>- 返回一个值，作为componentDidUpdate的第三个参数。
>- 配合componentDidUpdate, 可以覆盖componentWillUpdate的所有用法。
示例