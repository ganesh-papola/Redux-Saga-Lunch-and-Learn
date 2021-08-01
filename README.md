# Redux-Saga-Lunch-and-Learn

A library that aims to make application side effects (i. e. asynchronous things like data fetching and impure things like accessing the browser cache) easier to manage, more efficient to execute, easy to test, and better at handling failures.
  
 ## Generator Functions
  A generator is a function that can stop midway and then continue from where it stopped.

    function* generateSequence() {
      yield 1;
      yield 2;
      yield 3;
    }
    
    let generator = generateSequence();
    console.log(generator.next());  // { value: 1, done: false }
    console.log(generator.next());  // { value: 2, done: false }
    console.log(generator.next());  // { value: 3, done: false }
    console.log(generator.next());  // { value: undefined, done: true }


## Redux Thunk
A thunk is a function that acts as a wrapper in that it wraps an expression to delay its evaluation

    let x = 1 + 2;
    let foo = () => 1+2;
More - 

	    
    function say (text) {
      console.log(text + '!')
    }
    say('Hello') // 'Hello!'
    
    // Lazy (or "thunked") version
    function thunkedSay (text) {
      return () => {
        console.log(text + '!')
      }
    }
    
    const thunk = thunkedSay('Hello') // no action yet.
    
    // wait for it…
    
    thunk() // 'Hello!'
	
Thunk config for redux - 
	


    import React from 'react';
    import thunk from 'redux-thunk';
    import { createStore, applyMiddleware } from 'redux';
    import { Provider } from 'react-redux';
    import reducer from './reducers';
    
    export const store = createStore(
        reducer,
        applyMiddleware(thunk)
    );
Basic Redux -

    import { store } from "./store"
    
    export const someFunction = () => {
    const { getState, dispatch} = store;
    	dispatch({ type:"SOME_ACTION, value:"Hii" })
    }

Redux Thunk Example - 
    	
	export const login = (user, pass) => async (dispatch) => {
        try {
            dispatch({ type: LOGIN_REQUEST });
            let { data } = await request.post('/login', { user, pass });
            await dispatch(loadUserData(data.uid));
            dispatch({ type: LOGIN_SUCCESS, data });
        } catch(error) {
            dispatch({ type: LOGIN_ERROR, error });
        }
    }
	
Or there may be n no. of calls inside login which may create callback hell.

## Redux Saga
 Config
     
    import { Provider } from 'react-redux'
    import { createStore, applyMiddleware } from 'redux'
    import createSagaMiddleware from 'redux-saga'
    import rootReducer from './reducers'
    import rootSaga from './sagas'
    
    const sagaMiddleware = createSagaMiddleware()
    const store = createStore(rootReducer, applyMiddleware(sagaMiddleware))
    sagaMiddleware.run(rootSaga)
    
    
Redux-saga is a redux middleware library, that is designed to make handling side effects in your redux app nice and simple.

### Common side effects -
- make a HTTP call to a server
- send a WebSocket event
- fetch some data from a GraphQL server
- save something to the cache or browser local storage
.......

A few special helper functions provided by the redux-saga package. To start with, we can list:

- takeEvery()
- takeLatest()
- take()
- call()
- put()

## Example -

### takeEvery()
    import { takeEvery } from 'redux-saga/effects'
    function* watchMessages() {
      yield takeEvery('ADD_MESSAGE', postMessageToServer)
    }
    

	
When the middleware executes the handleNewMessage saga, it stops at the yield takeEvery instruction and waits (asynchronously, of course) until the ADD_MESSAGE action is dispatched. Then it runs its callback, and the saga can resume.

### takeLatest()
It only allows one function handler to run at a time, avoiding concurrency. If another action is fired when the handler is still running, it will cancel it, and run again with the latest data available.

### take()
take() is different in that it only waits a single time. When the action its waiting for occurs, the promise resolves and the iterator is resumed, so it can go on to the next instruction set.

### put()
Dispatches an action to the Redux store. Instead of passing in the Redux store or the dispatch action to the saga, you can just use put():

    yield put({ type: 'INCREMENT' })
    yield put({ type: "USER_FETCH_SUCCEEDED", data: data })
	
### call()
To call some function in a saga.

    import { call } from 'redux-saga/effects'
    
    const fetchUser = (payload) => axios('user/api', payload)
    const users = yield call(fetchUser, data)
	

### Running effects in parallel

### all()

Similar to** promise.all()**


    import { call } from 'redux-saga/effects'
    
    const todos = yield call(fetch, '/api/todos')
    const user = yield call(fetch, '/api/user')
	
	
The second fetch() call wont be executed until the first one succeeds.

    import { all, call } from 'redux-saga/effects'
    
    const [todos, user]  = yield all([
      call(fetch, '/api/todos'),
      call(fetch, '/api/user')
    ])
	
	
### race()

race() differs from all() by not waiting for all of the helpers calls to return. It just waits for one to return, and its done.

Its a race to see which one finishes first, and then it forget about the other participants.

     yield race([
          call(fetch, '/some/api/user/'),
          call(fetch, '/some/api/list/')
        ])
		
