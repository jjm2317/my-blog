# [React] 리덕스 미들웨어를 이용한 비동기 작업 처리



안녕하세요. 도롱뇽 개발자입니다. 

오늘은 리덕스 미들웨어(redux middleware)를 이용하여 비동기 작업을 처리하는 법에 대해 알아보도록 하겠습니다. 

## **먼저, 리덕스 미들웨어란?**

리덕스 스토어의 기능을 확장하는 방법 중 하나입니다. 미들웨어를 사용하면 dispatch 함수를 커스터마이징하여

 **1.** **액션(action)이 디스패치(dispatch)** 된 이후, 

 **2. 액션이 리듀서(reducer)에 도달**하기 전에 

추가적인 작업을 수행할 수 있습니다. 



**미들웨어는 언제 사용될까요?**? 

간단하게만 설명드리자면,,, side effect를 처리하는데 필요합니다!

어플리케이션 개발시, 서버로부터 데이터를 받기위해 http 요청을 보내거나, 콘솔에 로깅을 하는 등의 작업이 필요합니다. 이러한 작업들을 side effect 라고 합니다. 리덕스 어플리케이션 개발시에도 당연히 side effect 가 수반되겠죠? 액션을 로깅하거나, 액션을 변경하거나, 액션을 딜레이 시키거나, API 요청을 보내는 등 말이죠!

그럼 이러한 side effect들을 어디서 처리하느냐? [리덕스 공식문서](https://redux.js.org/tutorials/fundamentals/part-6-async-logic)에서도 나와있듯이,  액션을 처리하는 리듀서(reducer)함수에는 side effect가 있으면 안됩니다.  그 대신, side effect 처리에 대해 다음과 같이 설명하고 있습니다.

> **Redux middleware were designed to enable writing logic that has side effects**.

설명과 같이 **리덕스 미들웨어**는 side effect를 처리하기 위해서 만들어졌습니다. 특히 비동기 작업의 경우 대부분의 프로젝트에서 미들웨어를 통해 처리됩니다.

오늘은!  리덕스 미들웨어가 어떻게 비동기 작업을 수행하는지에 대해 다뤄볼 예정입니다.  



## 미들웨어에서 비동기 작업 처리하기

대표적인 비동기 작업으로 다음을 꼽을 수 있습니다.

- setTimeout 등의 타이머 함수
- Promise 기반의 api 요청

이런 작업들을 어떻게 미들웨어에서 처리할 수 있을까요? 

가장 쉬운 방법은 비동기 작업마다 미들웨어 함수를 직접 작성하는 것입니다. 

**미들웨어를 직접 정의해보겠습니다.**

다음과 같이 미들웨어 두가지를 작성하였습니다. 

- 특정 액션에 대해 다음 액션을 딜레이 시키는 미들웨어
- 특정 액션에 대해 API 요청을 보내고 resolve되면 액션을 디스패치 시키는 미들웨어 

```react
import { client } from '../api/client'

const delayedActionMiddleware = storeAPI => next => action => {
  if (action.type === 'todos/todoAdded') {
    setTimeout(() => {
      next(action)
    }, 1000)
    return
  }

  return next(action)
}

const fetchTodosMiddleware = storeAPI => next => action => {
  if (action.type === 'todos/fetchTodos') {
    client.get('todos').then(todos => {
      storeAPI.dispatch({ type: 'todos/todosLoaded', payload: todos })
    })
  }

  return next(action)
}
```

위와 같은 방법을 통해서 액션을 지연시키고 http 요청을 보내는 작업을 스토어 자체에서 처리할 수 있게 되었습니다.  즉, side effect 를 처리하는 미들웨어의 역할에 대해 알게된 것입니다. 하지만 몇가지 짚고 넘어가야 될 점이 있습니다. 

위 코드는 정상적으로 동작하지만 **특정 액션에 대해서만** 비동기 작업을 처리할 수 있습니다. 또한  **매번 미들웨어를 작성**해서  applyMiddleware 함수에 인수로 넘겨주어야 한다는 번거로움이 있네요. 

**더 좋은 방법은 없을까?**

여러개의 미들웨어를 작성하지 않고, 하나의 미들웨어로 비동기 작업을 처리할 수는 없을까요? 

그러기 위해서는 비동기 로직을 미들웨어 자체에서 분리해야 될 것 같습니다. 그러면서도, 액션을 처리하고 상태를 조회하기 위해 store API의 dispatch, getState 함수를 이용할 수 있어야 될 것 같습니다. 

이러한 방법을 제공해주는 범용적인 미들웨어가 있습니다! 바로 **Redux-Thunk** 라는 라이브러리인데요.

비동기 작업을 위한 리덕스 미들웨어 중 가장 많이 사용되는 라이브러리입니다. 

Redux-Thunk 미들웨어는 다음과 같은 기능을 제공합니다. 

- dispatch 의 인수로 함수를 넘겨줄 수 있도록 합니다. 
- 인수로 전달되는 함수에서는 store의 상태를 조회하거나 새로운 액션을 디스패치할 수 있습니다. 

무엇인가 좋은 기능인 것은 알겠는데, 아직 긴가 민가 합니다. 

한번 Redux-Thunk 라이브러리를 이용해서 비동기 작업을 처리하는 코드를 직접 보겠습니다. 



먼저,  Redux-Thunk 라이브러리를 설치해줍니다. 

```bash
npm install redux-thunk
```

스토어를 설정해줍니다.

```react
import { createStore, applyMiddleware } from 'redux'
import thunkMiddleware from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'
import rootReducer from './reducer'

const composedEnhancer = composeWithDevTools(applyMiddleware(thunkMiddleware))

// The store now has the ability to accept thunk functions in `dispatch`
const store = createStore(rootReducer, composedEnhancer)
export default store
```

applyMiddleware 함수를 사용하면 미들웨어를 스토어에 적용할 수 있습니다. 

여기까지 스토어에 Redux-Thunk 미들웨어를 적용하였으니, 다음으로는 thunk 함수를 작성해보겠습니다.

``` react
export async function fetchTodos(dispatch, getState) {
  const response = await client.get('/fakeApi/todos')
  dispatch({ type: 'todos/todosLoaded', payload: response.todos })
}
```

보시는 바와 같이 thunk 함수는 dispatch와 getState를 인자로 받습니다. http 요청을 보내고, promise가 resolve 되었을 때 응답받은 데이터와 함께 액션을 발생시키죠.

해당 thunk함수는 일반적으로 컴포넌트 내의 useEffect 훅 안에서 사용됩니다.

```react
//...some codes
const Component = () => {
const dispatch = useDispatch();

useEffect(() => {
    dispatch(fetchTodos)
},[dispatch])
    
   
//... some render codes
}
```

컴포넌트 내에서 dispatch 의 인수로 액션 객체( thunk함수)를 전달하는 것만으로 까다로운 비동기로직이 처리되었습니다!  컴포넌트는 side effect 를 처리하는 과정에 대해 전혀 신경쓰지 않아도 됩니다!

여기서 side effect를 처리하는 미들웨어의 유용함을 느낄 수 있는 것 같습니다! 

그렇다면 get 메서드 이외의 메서드는 어떤식으로 다룰 수 있을까요?

새로운 데이터를 생성하는 POST요청을 보낸 뒤 응답을 받아 dispatch 해보겠습니다. 

다음은 새로운 TODO 아이템을 생성하는 thunk 함수입니다.

```react
async function saveNewTodo(dispatch, getState) {
  // ❌ We need to have the text of the new todo, but where is it coming from?
  const initialTodo = { text }
  const response = await client.post('/fakeApi/todos', { todo: initialTodo })
  dispatch({ type: 'todos/todoAdded', payload: response.todo })
}
```

thunk함수를 작성했으나 한가지 문제가 있네요. 코드를 보시면 text 를 값으로 갖는 todo 아이템을 만들고 싶지만, thunk함수의 인자로는 받아올 수 없는 상황입니다. 어떻게 해야될까요?

이럴 때! **action creator** 패턴이 사용됩니다. async creator 패턴이란, 외부 함수로 액션객체를 캡슐화해서 반환하는 방식인데요. 외부함수의 인수로 액션 객체에 필요한 값을 넘겨줄 수도 있습니다. 

thunk 함수는 다음과 같이 작성될 수 있습니다. 

```react
// Write a synchronous outer function that receives the `text` parameter:
export function saveNewTodo(text) {
  // And then creates and returns the async thunk function:
  return async function saveNewTodoThunk(dispatch, getState) {
    // ✅ Now we can use the text value and send it to the server
    const initialTodo = { text }
    const response = await client.post('/fakeApi/todos', { todo: initialTodo })
    dispatch({ type: 'todos/todoAdded', payload: response.todo })
  }
}
```

그리고 컴포넌트에서 다음과 같이 사용될 수 있습니다.

```react
import React, { useState } from 'react'
import { useDispatch } from 'react-redux'

import { saveNewTodo } from '../todos/todosSlice'

const Header = () => {
  const [text, setText] = useState('')
  const dispatch = useDispatch()

  const handleChange = e => setText(e.target.value)

  const handleKeyDown = e => {
    // If the user pressed the Enter key:
    const trimmedText = text.trim()
    if (e.which === 13 && trimmedText) {
      dispatch(saveNewTodo(trimmedText))
      setText('')
    }
  }

  // omit rendering output
}
```

컴포넌트에서 비동기 로직이 쓰였나 의심이 들정도로 깔끔하게 처리가 되었습니다.  컴포넌트에서 신경써야 될 것은 단지 action creator 인 saveNewTodo로 액션을 생성하여 dispatch 하는 것 뿐입니다. 내부적인 로직은 신경쓰지 않고 말이죠.





