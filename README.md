# RTK-Redux-Tool-Kit

- Redux is Core library.
- Wheres React-Redux is a implementation to wiring the Redux with React, so they both can communicate with each other.

![alt text](image.png)

  
  ![alt text](image-2.png)

- RTK first start with its store.
  - **app -> store.js**
  - Every RTK implemented Application has only one store.(we can made multiple but always preffered to make a store only.)
  - 'store' also known as 'single source of truth'.
  
   <img src="image-1.png" width="400px"> 

  - store has `configureStore` which is imported from `@reduxjs/toolkit`
  - `configureStore` contain objects in form of key value pair.
  - `configureStore` could have multiple key value pair, but in above example their is only one key value pair.
  - Inside `configureStore` we introduce all the reducer inside it, in form of key value pair.
  - Why their we have to introduce `reducer` inside `configureStore`
   - Large applications typically have multiple `reducers` to manage different parts of the state. configureStore allows you to combine these reducers into a `single root reducer`, which can then be passed to the `store`.


- Lets know about `reducer`.
  - **features -> todo -> todoSlice.js**
  - its is syntax we call every feature a slice, it could be any name but it is suggested to give name with slice suffix.

  - To Create a slice we use `createSlice` which is again imported from `@reduxjs/toolkit`.
  - To create a slice we need majorly three things
    - name (is is string)
    - intialState (it is a object)
    - reducers (it is list of all reducers)

  - A reducer have to parameter `state` and `action`
    - a state always `initial state` for it
    - Whereas `action` is payload send to it by invoking it.
    - In `addTodo` initial state will be array of object of all todos.
    - And `action` will be a new Todo send to it.
    
    <img src="image-4.png" width="400px">
    
    - Here we get text of todo from `action.payloady` we directly make object using it an uninue `nanoid` which is from again `@reduxjs/toolkit`
    - after it we directly push this todo into our current state to update todos.
    - Here one thing we notice we don't have to use iterator `...` as like we use in context API first itrate over the existing values then push new one.
     
    ```javascript    
      const addTodo = (todo) => {
        setTodos((prev) => [{ id: Date.now(), ...todo }, ...prev]); 
      };
    ```
    - This above thing automatically managed by redux-toolkit.



### Code of reducer

```javascript
import {createSlice, nanoid } from '@reduxjs/toolkit';

const initialState = {
    todos: [{id: 1, text: "Hello world"}]
}



export const todoSlice = createSlice({
    name: 'todo',
    initialState,
    reducers: {
        addTodo: (state, action) => {
            const todo = {
                id: nanoid(), 
                text: action.payload
            }
            state.todos.push(todo)
        },
        removeTodo: (state, action) => {
            state.todos = state.todos.filter((todo) => todo.id !== action.payload )
        },
    }
})

export const {addTodo, removeTodo} = todoSlice.actions

export default todoSlice.reducer

```


  - All reducers must be export because you don't which component will use which reducer

```javascript
export const {addTodo, removeTodo} = todoSlice.actions
```

  - Main soruce of reducer also export due to this use by our store.
```javascript
export default todoSlice.reducer
```

- As last step Wrap the <App/> with provider as like we do into context API.
  - Also provide `store` to this provider.
  - But `provider` is imported from `react-redux`

```javascript
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import './index.css'
import { Provider } from 'react-redux'
import {store} from './app/store'

ReactDOM.createRoot(document.getElementById('root')).render(
  <Provider store={store}>
    <App />
  </Provider>,
)

```


- Now we can use functionality of RTK
 - To send the value to the store we use `useDispatch()`

   <img src="image-3.png" width="400px">

 - To dispatch values to particular reducer we pass import this reducer and pass it into `dispatch` and from their our reducer invoke particular functionality by sending `action` to it, here action is our input.

- To take the values from store we use `useSelector()` this return the current state for particlar slice.
- To store values we have iterate over all the state of it and store in into a new variable

- as like here `todos` is storing all the object from `state` and making a array of object containing all todos.

    <img src="image-5.png" width="400px">

---

## Redux Toolkit: Using Computed Property Names

### Introduction

In Redux Toolkit, it's common to encounter scenarios where you need to use computed property names. This is particularly useful when defining reducers dynamically. This guide explains how to correctly use computed property names in your Redux reducers.

### Understanding Computed Property Names

#### What are Computed Property Names?

Computed property names allow you to dynamically assign keys to an object based on variable values. In JavaScript, this is done using square brackets `[]`.

#### Example

Suppose you have an `authSlice` defined using Redux Toolkit's `createSlice`:

```javascript
import { createSlice } from '@reduxjs/toolkit';

const authSlice = createSlice({
  name: 'auth',
  initialState: { loggedIn: false },
  reducers: {
    login: state => { state.loggedIn = true; },
    logout: state => { state.loggedIn = false; }
  }
});

export default authSlice;
```

Here, `authSlice.name` would be `'auth'`.

### Using Computed Property Names in Reducers

#### Incorrect Usage

If you directly use `authSlice.name` without square brackets in your root reducer, it will be interpreted as a literal string:

```javascript
import { combineReducers } from 'redux';
import authSlice from './authSlice';

const rootReducer = combineReducers({
  authSlice.name: authSlice.reducer, // Incorrect usage
});

export default rootReducer;
```

In this case, `authSlice.name` is treated as a literal key, not the value of `authSlice.name`.

#### Correct Usage

To use the value of `authSlice.name` as the key, you need to use square brackets:

```javascript
import { combineReducers } from 'redux';
import authSlice from './authSlice';

const rootReducer = combineReducers({
  [authSlice.name]: authSlice.reducer, // Correct usage
});

export default rootReducer;
```

This ensures that the key in the `combineReducers` object is dynamically set to the value of `authSlice.name`.

### What is a Literal String?

A literal string is a fixed sequence of characters enclosed in quotes. For example:

- Double quotes: `"hello"`
- Single quotes: `'world'`
- Backticks (template literals): \`backticks\`

#### Illustration of Literal Strings vs Computed Property Names

1. **Literal String Key**:
   ```javascript
   const obj = {
     "authSlice.name": "some value"
   };
   console.log(obj); // Output: { authSlice.name: "some value" }
   ```

2. **Computed Property Name**:
   ```javascript
   const key = "dynamicKey";
   const obj = {
     [key]: "some value"
   };
   console.log(obj); // Output: { dynamicKey: "some value" }
   ```

In the second example, `key` is a variable containing the string `"dynamicKey"`, and `[key]` evaluates to `"dynamicKey"`, making it the key of the object.

### Conclusion

Using computed property names in Redux Toolkit allows for more dynamic and maintainable code. By understanding the difference between literal strings and computed property names, you can avoid common pitfalls and write more flexible reducers.

---

  
 


