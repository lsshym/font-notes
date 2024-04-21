## Redux流程

创建Store

```javascript
// index.js
// 从redux引入createStore
import { createStore } from 'redux';

// 创建store，同时要引入一个管理方法reducer
const store = createStore(reducer,
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
);

export default store;
```

```javascript
// reducer.js
// 其实就是处理数据的方法
const defaultState = {
    inputValue: '666',
    list: ['1', '2']
}
export default (state = defaultState, action) => {
    if (action.type === 'addItem') {
        const newState = JSON.parse(JSON.stringify(state))
        newState.list.push(action.value)
        return newState
    }
    // state是整个数据
    return state
}
```

```javascript
// 组件之中
constructor(props) {
    super(props);
    this.state = store.getState()
    store.subscribe(this.handleStoreChange)
}
//
handleChange = (event) => {
    const action = {
        type: 'change',
        value: event.target.value
    }
    store.dispatch(action)
}
handleStoreChange = () => {
    this.setState(store.getState())
}
```

## Vuex就比较简单了

```javascript
export default new Vuex.Store({
  state: {
    columns: []
  },
  mutations: {
    saveColums (state, res) {
      state.columns = res
    }
  },
  getters: {
    getColmuns: (state) => {
      return state.columns
    }
  },
  modules: {
    app,
    moduleDistrict
  }
})

// 
store.commit('saveColums')

```

