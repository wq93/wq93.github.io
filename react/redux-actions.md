## Redux-actions

`Redux-actions`有两大法宝`createAction`和`handleAction`

### createAction

`createAction`是用来创建动作创建器的函数。

对比之前创建action

```js
const ACTION = 'ACTION'
const action = () => {type: ACTION};
```

使用redux-actions后创建action:

```js
import { createAction } from "redux-actions"

export const INCREMENT = 'INCREMENT'
export const increment = createAction(INCREMENT)
```

#### 行为

上边代码定义一个动作`INCREMENT`, 然后通过`createAction`创建了对应actionCreator。调用`increment()`就会返回`{ type: 'INCREMENT' }`, 调用`increment(10)`返回`{ type: 'INCREMENT', payload: 10 }`

```js
createAction(
  type, // 动作名
  payloadCreator = Identity, // 用来创建动作对象中的payload值，默认使用lodash的Identity
  ?metaCreator // 用来创建动作对象中元数据
)

const increment = createAction(
  'INCREMENT',
  mount => mount,
  () => ({ admin: true })
);
increment(20);
// {
//   type: 'INCREMENT',
//   payload: 20,
//   meta: { admin: true },
// }
```

### createActions

这个函数用来创建多个动作的。具体用法就是`createActions(actionMap)`

`actionMap`就是一个对象，key值为动作类型，value可以是`payloadCreator`函数、一个数组`[payloadCreator， metaCreator]`、嵌套的`actionMap`。

```js
cosnt {add, remove} = createActions({
  ADD_TODO: todo => ({ todo }), // payload creator
  REMOVE_TODO: [
    todo => ({ todo }), // payload creator
    (todo, warn) => ({ todo, warn }) // meta
  ]
})

// {type: 'ADD_TODO', payload: {todo: 'redux-actions'}}
add('redux-actions')

// {type: 'ADD_TODO', payload: {todo: 'redux-actions'}, meta: {todo: 'redux-actions', warn: 'warn'}}
remove('redux-actions', 'warn') 
```

---

### handleAction

`handleAction`来处理动作,`handleAction`会返回一个`reducer`。

对比之前创建reducer,需要使用switch或者if…else来匹配;

```js
function timer(state = defaultState, action) {
  switch (action.type) {
    case START:
      return { ...state, runStatus: true };
    case STOP:
      return { ...state, runStatus: false };
    case RESET:
      return { ...state, seconds: 0 };
    case RUN_TIMER:
      return { ...state, seconds: state.seconds + 1 };
    default:
      return state;
  }
}
```

使用`handleActions`创建reduces操作state

```js
// reducer.js
import {handleAction} from 'redux-actions'
import {INCREMENT} from './action'

const defaultState = { count: 1 }
const reducer = handleAction(
  INCREMENT,
  (state, action) => ({
    count: state.count + action.payload
  }),
  defaultState
)

export default reducer
```

### handleActions

`handleActions`函数是用来处理多个动作的

```js
handleActions(reducerMap, defaultState)

const timer = handleActions({
  START: (state, action) => ({ ...state, runStatus: true }),
  STOP: (state, action) => ({ ...state, runStatus: false }),
  RESET: (state, action) => ({ ...state, seconds: 0 }),
  RUN_TIMER: (state, action) => ({ ...state, seconds: state.seconds + 1 }),
}, defaultState);
```

reducerMap中key就是动作名, value就是reducer

---

### combineActions

合并多个action和actionCreator

```js
const { increment, decrement } = createActions({
  INCREMENT: amount => ({ amount }),
  DECREMENT: amount => ({ amount: -amount }),
})
const reducer = handleAction(combineActions(increment, decrement), {
  next: (state, { payload }) => ({ ...state, count: state.count + payload }),
  throw: state => ({ ...state, count: 0 }),
}, { count: 10 })

const reduce2 = handleActions(
  {
    [combineActions(increment, decrement)] (state, {payload}) {
      return { ...state, count: state.count + payload }
    }
  },
  { count: 10 }
)
```

---

### Demo

#### create action and state

```js
// actionCreators.js

export const fetchMocks = () => {
  return async dispatch => {
    const data = await fetchMock();
    dispatch(setMockData(data.data));
  };
};
```

```js
// createAction.js

import { createAction } from 'redux-actions';

export const setMockData = createAction(actionTypes.SET_MOCKDATA);
```

```js
// reducer.js

import { handleAction } from 'redux-actions';
const mockReducer = handleAction(
  setMockData,
  (state, { payload }) => {
    return { ...state, ...payload };
  },
  {}
);
```

```js
// combineReducers.js
export default combineReducers({
  mockReducer,
  customReducer,
  config : configReducer,
});
```

#### use

```js
// index.js
import { connect } from 'react-redux';
import { fetchMocks } from './actionCreators.js';

...

componentDidMount(){
  const { fetchMocks } = this.props;
  !isServer && fetchMocks();
}

...

function mapStateToProps(state){
  const {
    mockReducer,
  } = state;
  return { mockReducer };
}

function mapDispatchToProps(dispatch){
  return {
    fetchMocks(){
      dispatch(fetchMocks());
    },
  };
}

export default connect(mapStateToProps, mapDispatchToProps)(Index);
```

