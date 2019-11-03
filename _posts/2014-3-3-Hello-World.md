---
layout: post
title: How to pass global data through the react components tree without state management tools?
---


Imagine that we want to make a simple react app that doesn't require to import any state management tools like Redux or Mobx. What would be the easiest way to share data that can be considered “global” for a tree of React components such as the logged-in user data, translations, theme, or preferred language?

In a typical react application, I used to pass my global data that are required by many components within an application via props. But when the app gets bigger with several stateful components then you will face the ugly code style like below:


```
render(  
	<NavigationBar user={user} theme={Themes.lightblue} />  
	<Link href={user.permalink}>    
	<Avatar user={user} theme={Themes.lightblue} />  
	</Link>  <Profile user={user} theme={Themes.lightblue} />  
	<Reviews user={user} theme={Themes.lightblue} />
);
```

Since I do care about my code style I investigated to find out what's the proper way to get rid of this code style.

[React 16.3.0](https://reactjs.org/blog/2018/03/29/react-v-16-3.html) has been released with several new concepts and introduced the official context API which provides a way to share values between components without having to explicitly pass a prop through every level of the tree. By using context, We can avoid passing props through intermediate elements and absolutely helps you to have a prettier code style.

```
const MyContext = React.createContext(defaultValue);
```

The usage is extremely easy as you see in the code below. **Provider** provides the values and Consumer allows accessing the value. All consumers that are descendants of a Provider will re-render whenever the Provider’s `value` prop changes.

```
const UserContext = React.createContext({});  
const { user } = this.props;render(   
  <UserContext.Provider value={user}>  
    <NavigationBar/>  
    <Link>  
      <Avatar />  
    </Link>  
    <Profile />  
    <Reviews />  
  </UserContext.Provider>  
);

const NavigationBar = () => (  
  <UserContext.Consumer>  
    {value => (  
      <p>{value.first_name}</p>  
    )}  
  </UserContext.Consumer>  
);
```

To find out more magical things about React context API just [RTFM](https://reactjs.org/docs/context.html).
