[Back to NodeJS Notes](index)

# Introduction

React apps are made out of **components**, which is a UI piece with its own appearence and functionality. 

React components are JavaScript functions that returns markups:

```
function MyButton() {
  return (
    <button>I'm a button</button> 
  );
}
```

These components can be nested from withing other components by calling them like:

```
<MyButton />
```

A convention for naming react components is by capitalizing the first letter.

# JSX

React uses .jsx file extension for writing markup.

JSX is stricter than HTML. A component can't return multiple JSX tags. It needs to be wrapped around a shared parent, like <div></div> or an empty <></> wrapper

```
function AboutPage() {
  return (
    <>
      <h1>About</h1>
      <p>Hello there.<br />How do you do?</p>
    </>
  );
}
```

# Classes for CSS

In react you specify CSS class names with the `className` attribute as in: `<img className="avatar">`

# Variable declaration in markup

To display some javascript variable into a HTML tag using markup on JSX, surround the variable with curly braces {}: `<tag>{myVariable}</tag>`

# Event Handling

To handle events use: `<button onClick={handleClick}></button>`. Note that the function `handleClick` doesn't have the parenthesis, we don't need to call it, just pass it down to the react event handler.

# State

States in React uses the `useState` hook which can be imported by adding `import { useState } from 'react';`. Then to create a state do: `const [ count, setCount ] = useState(0);`. The `useState(0)` function call will return us the actual value of the state and a function that we can use to change the current value of the state. So by calling `setCount(1)`, we are effectively changing the value of the `count` state.

# Hooks 

Functions starting with *use* are called **hooks**. You can only call hooks at the top of your components.
