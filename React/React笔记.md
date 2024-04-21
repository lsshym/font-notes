#### 组件

```javascript
// 两种方式，一种使用JavaScript，
const e = React.createElement;

class LikeButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = { liked: false };
  }

  render() {
    if (this.state.liked) {
      return 'You liked this.';
    }

    return e(
      'button',
      { onClick: () => this.setState({ liked: true }) },
      'Like'
    );
  }
}

const domContainer = document.querySelector('#like_button_container');
ReactDOM.render(e(LikeButton), domContainer);
```

使用React.createElement生成，这种方式在Vue官网中叫渲染函数，花里胡巧

或者使用JSX的方式，这种方式跟使用模板差不多，

```javascript
return (
  <button onClick={() => this.setState({ liked: true })}>
    Like
  </button>
)
```

#### 为什么React的evetn.target.value是null

setState是异步的

#### 为什么事件的this会丢失

异步函数的调用者是window

[React]: https://juejin.im/post/5afa6e2f6fb9a07aa2137f51

```react
class Title extends Component {
  handleClickOnTitle (e) {
    console.log(this)
  }

  render () {
    return (
      <h1 onClick={this.handleClickOnTitle.bind(this)}>React 小书</h1>
    )
  }
}
// 这种其实是相当于直接调用handleClickOnTitle
```

页面一旦生产就无法更改，就跟电影一样，除非重新替换

*当我们调用这个函数的时候，React.js 会更新组件的状态 `state` ，并且重新调用 `render` 方法，然后再把 `render` 方法所渲染的最新的内容显示到页面上*。

