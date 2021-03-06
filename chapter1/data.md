## 1. React 数据流
&emsp;&emsp;在 React 中，数据是自顶向下单向流动的，即从父组件到子组件。state 与 props 是 React 组件中最重要的概念。如果顶层组件初始化 props，那么 React 会向下遍历整棵组件树，重新尝试渲染所有相关的子组件。而 state 只关心每个组件自己内部的状态，这些状态只能在组件内改变。**把组件看成一个函数，那么它接受了 props 作为参数，内部由 state作为函数的内部参数，返回一个 Virtual DOM 的实现.**
## 2. state
&emsp;&emsp;React 把组件看成是一个状态机（State Machines）。通过与用户的交互，实现不同状态，然后渲染 UI，让用户界面和数据保持一致。只需更新组件的 state，然后根据新的 state 重新渲染用户界面（不要操作 DOM）。

&emsp;&emsp;React组件添加一个类构造函数来初始化状态 this.state：
```
    class CreateComponent extends PureComponent {
      constructor(props) {
        super(props)
        this.state = {
        }
      }
    }
```
&emsp;&emsp;当组件内部数据改变，需要更新组件时，使用库内置的 setState 方法，将该组件尝试重新渲染。setState 是一个异步方法，触发一次组件的更新来引发重绘，注意：多次 setState 函数调用产生的效果会合并。

    重绘指的就是引起 React 的更新生命周期函数4个函数：
    shouldComponentUpdate（被调用时this.state没有更新；如果返回了false，生命周期被中断，虽然不调用之后的函数了，但是state仍然会被更新）
    componentWillUpdate（被调用时this.state没有更新）
    render（被调用时this.state得到更新）
    componentDidUpdate

  若在state状态改变后需要做些操作，则需要写在setState回调函数内：
```
    this.state = {foo: 2};
    this.setState({foo: 123}, ()=> {
      console.log(foo);
      // 123
    });
```
注意:通过 addEventListener || setTimeout/setInterval 的方式处理的则会同步更新。但是还是尽量避免直接使用。
```
    componentDidMount() {
      this.setState({val: this.state.val + 1});
      console.log('第 1 次 log:', this.state.val);
      this.setState({val: this.state.val + 1});
      console.log('第 2 次 log:', this.state.val);

    setTimeout(() => {
      this.setState({val: this.state.val + 1});
      console.log('第 3 次 log:', this.state.val);   
      this.setState({val: this.state.val + 1});
      console.log('第 4 次 log:', this.state.val); 
    }, 0);
    }
```
前两次没有更新state, 输出两个0。
后面两次会同步更新， 分别输出2， 3；

## 3. props

&emsp;&emsp;props 是 React 用来让组件之间互相联系的一种机制，通俗地说就像方法的参数一样。React 的单向数据流，主要的流动管道就是 props。props 本身是不可变的。当我们试图改变 props 的原始值时，React 会报出类型错误的警告。
&emsp;&emsp;例如存在一个组件```<Tabs data={data} />```,data就是一个自定义传递的属性props，在Tabs组件内通过```this.props.data```获取该属性。
&emsp;&emsp;React 为 props 同样提供了默认配置，通过 defaultProps 静态变量的方式来定义。
```
    class Toggle extends React.Component {
      static defaultProps = {
        likedText: '取消',
        unlikedText: '点赞'
      }        
      constructor(props) {
        super(props);
        this.state = {};
      }
    }
```
注意：

- 不要直接去操作this.state， 这样会造成不必要的性能问题和隐患。只有通过父组件重新渲染的方式才可以把新的props传入组件中(详见父子组件通信)。
```
    handleTabClick(activeIndex) { 
    // ...
    this.props.onChange({activeIndex, prevIndex});
    }
```

- PropTypes 提供一系列验证器，可用于确保组件接收到的数据类型是有效的。当传入的 prop 值类型不正确时，JavaScript 控制台将会显示警告。出于性能方面的考虑，propTypes 仅在开发模式下进行检查。
```
mport PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name: PropTypes.string
};
export default Greeting
```
常见得有以下类型检查：
> optionalArray: PropTypes.array,
> optionalBool: PropTypes.bool,
> optionalFunc: PropTypes.func,
> optionalNumber: PropTypes.number,
> optionalObject: PropTypes.object,
> optionalString: PropTypes.string,

- 除了组件自定义的props(父子传值)，还有一些react已经封装好的props属性，包括：

**this.props.children** : 

  &emsp;&emsp;表示组件的所有子节点;如果当前组件没有子节点，它就是 undefined ;如果有一个子节点，数据类型是 object ；如果有多个子节点，数据类型就是 array 。

  &emsp;&emsp;React 提供一个工具方法 React.Children 来处理 this.props.children 。我们可以用 React.Children.map 来遍历子节点，而不用担心 this.props.children 的数据类型是 undefined 还是 object。
  ```
      render: function() {
        return (
          <ol>
            {
              React.Children.map(this.props.children, function (child) {
                  return <li>{child}</li>;
              })
            }
          </ol>
        );
    }
  ```

**this.props.dispatch** : 

&emsp;&emsp;当用React Redux的connect进行封装的时候，connect方法会把dispatch放到props中,使用this.props.dispatch方法派发，https://www.jianshu.com/p/6482f4785929:

  Redux：
  ```
    import {connect} from 'react-redux'
    import {setBuildShow} from '@/redux/actions'
    class LayoutDesigner extends React.Component {
      constructor(props) {
        super(props)
        this.state = {}
      }
    }

    const mapStateToProps = state => ({
      layoutSetting: state.configPagw.layoutSetting
    })
    const mapDispatchToProps = dispatch => ({
      setBuildShow: (data) => dispatch(setBuildShow(data))
    })
    export default connect(mapStateToProps, mapDispatchToProps)(LayoutDesigner) ;
  ```

**this.props.history** : 

&emsp;&emsp;实现跳转页面.我们在使用this.props.hostory.push不成功的时候，其实是props没有history这个对象,需要在使用withRouter这个包裹之后方能使用：
  ```
    import React from "react";
    import {withRouter} from "react-router-dom";

    class MyComponent extends React.Component {
      ...
      myFunction() {
        this.props.history.push("/some/Path");
      }
      ...
    }
    export default withRouter(MyComponent);
  ```

  还可以写成如下形式:
  ```
  this.props.history.push(
    { 
      pathname: '/dashboard/groupper', 
      state: { datagroupsperson } 
    });
  ```

  页面取值：
  ```
  this.props.location.state.datagroupsperson
  ```

  在使用 React 开发单页面应用时，一般不会打开新页面。B页面返回，一般采用以下方法：
  ```
  this.props.history.goBack();
  ```

  而使用此方法返回，A 页面的状态是不能记忆的；需要在 A 页面跳转B页面前，将之前的查询条件保存到 React Router 的路由历史中，使用以下方法：
  ```
  this.props.history.replace(this.props.location.pathname, state);
  ```

  以上 repalce 方法，第一个参数为当前页面的 url；第二个参数为要保存的状态。B返回A时，在 A 页面中，可以使用以下方法获取到之前保存的状态，来处理相应的逻辑：
  ```
  let state = this.props.location.state;
  ```