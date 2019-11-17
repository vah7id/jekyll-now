---
layout: post
title: Manage your state in react without any external tool
---

### Introduction

Since I started to combine redux to my react applications, always I was kind of curious about how state management works inside its base-code! Redux actually is a great library and the idea of writing this article is just about to make it clear how exactly redux works.

Last weekend I got some spare time to dig into it and turns out react had those reducer and action concepts like redux inside itself as they were in experiment state. Since React Hooks API official release in [React 16.8](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html) we have access to [**useState**](https://reactjs.org/docs/hooks-reference.html#usestate)**,** [**useReducer**](https://reactjs.org/docs/hooks-reference.html#usereducer)**,** [**useContext**](https://reactjs.org/docs/hooks-reference.html#usecontext) **API** which is all we need to manage our global state as redux do. let’s get this straight!

### useState

useState is a hook that lets you to add a react state to function components (state-less). Just pass your initialState and it returns the state object, and a function to update it (setState) as below.

```
function Increment(initialCount) {  
  const [state, setState] = useState(initialState);  
  return (
    <button onClick={() => setState(state + 1)}>{state}</button>
  );
}
```

### useReducer

> An alternative to `[useState](https://reactjs.org/docs/hooks-reference.html#usestate)`. Accepts a reducer of type `(state, action) => newState`, and returns the current state paired with a `dispatch` method.)

If you’re familiar with Redux you already know how useReducer works. In official docs mentioned alternative because useState implemented by useReducer and it can apply for many cases. Let have a quick look at a simple reducer as below:

```
const initialState = 0;  
const reducer = (state, action) => {  
  switch (action) {  
    case 'increment': return state + 1;  
    case 'decrement': return state - 1;  
    case 'reset': return 0;  
  }  
};
```

Reducers specify how the application’s state changes in response to the action and always return the new state value. Here’s a simple usage of reducer in the state-less component.

```
import React, { useReducer } from "react";

const initialState = 0;

const reducer = (state, action) => {
  switch (action) {
    case "increment":
      return state + 1;
    case "decrement":
      return state - 1;
    case "reset":
      return 0;
  }
};

const Counter = () => {
  const [count, dispatch] = useReducer(reducer, initialState);
  return (
    <div>
      {count}
      <button onClick={() => dispatch("increment")}>+1</button>
      <button onClick={() => dispatch("decrement")}>-1</button>
      <button onClick={() => dispatch("reset")}>reset</button>
    </div>
  );
};

export default Counter;

```

### useContext

useContext provides a way to share values between components without having to explicitly pass a prop through every level of the tree. We using context when some data needs to be accessible by many components. Therefore to make the global state accessible for all components we just need to wrap our root component by context provider. Let’s quickly check how it works in the code below.

```
import React, { useContext } from "react";
import ReactDOM from "react-dom";

const state = {
  user: {
    first_name: "Fat",
    last_name: "Joe"
  }
};

const AppContext = React.createContext(state);

function App() {
  return (
    <AppContext.Provider value={state}>
      <NavigationBar />
    </AppContext.Provider>
  );
}

function NavigationBar(props) {
  const state = useContext(AppContext);
  return (
    <>
      {state.user.first_name} {state.user.last_name}
    </>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

### Let the magic happen

By these three hooks, we can easily combine them together and make our simple state management just by defining our own reducer and passing the current state to our AppContext wrapper.

Then we have access to our global stare by props in any class component and function component. Look at the simple implementation as below.

```
import React, { createContext, useContext, useReducer } from "react";
import ReactDOM from "react-dom";

const initialState = {
  name: "Fat Joe",
  logged_in: false
};

const rootReducer = (state, action) => {
  switch (action) {
    case "init":
      return state;
  }
};

export const AppContext = createContext(initialState);

export const AppProvider = ({ reducer, initialState, children }) => (
  <AppContext.Provider value={useReducer(reducer, initialState)}>
    {children}
  </AppContext.Provider>
);

export const useStateValue = () => useContext(AppContext);

function App() {
  return (
    <AppProvider
      reducer={rootReducer}
      initialState={initialState}
      children={<Profile />}
    />
  );
}

function Profile() {
  const state = useStateValue();
  return <>{state[0].name}</>;
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);

```

First, we defined out most simple initial state and reducer then AppContext with initialState defined as a wrapper for our root component.

We pass the value of useReducer to AppProvider prop and all children component tree as other props. Then we defined our get global state function which returns the value of our AppContext.

Therefore we have an access to the current state in the children component and for update the state we just need to dispatch an action inside the component by using useReducer.

### Conclusion

Sometimes I used to include redux to my react app to manage only one reducer and less than 2 actions and I had to define all actions and reducers and map them to my component by `connect`, `mapStateToProps` which was quite hassle. Now when we know how to make our state management easily it makes sense to use our own simple state management for those use cases.    



