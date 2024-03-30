# React + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/README.md) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

# USEREF

Three scenarios for using useRef is possible

## Referencing DOM elements

### react-sandbox-vite setup for bootstrap

- install vite and clean out App.css
- npm install bootstrap
- To apply Bootstrap styles, you need to import the Bootstrap CSS into your project. A common approach is to import it in your main entry file (e.g., main.jsx or index.js) or directly in the component where you want to use Bootstrap styles. You can do this by adding the following import statement:

import 'bootstrap/dist/css/bootstrap.min.css';

- Import useRef, initiate it and refer it in the return statement
  import { useRef } from "react";

export default function UseRefExample1() {
const inputRef = useRef();

return (

<div>
  <form onSubmit={onSubmit}>
    <label htmlFor="name">Name</label>
        <input type="text" ref={inputRef} id="name" className="form-control mb-2" />
        <button type="submit" className="btn btn-primary">
          Submit
        </button>
  </form>
</div>
);
}

- create an onSubmit event and function to take in the element the useRef is referring to
- import { useRef } from "react";

export default function UseRefExample1() {
const inputRef = useRef();

e.preventDefault();
const onSubmit = (e) => {
};

return (

<div>
<form onSubmit={onSubmit}>
<label htmlFor="name">Name</label>
<input type="text" ref={inputRef} id="name" className="form-control mb-2" />
<button type="submit" className="btn btn-primary">
Submit
</button>
</form>
</div>
);
}

- import UseRefExample1 from "./components/UseRefExample1";

function App() {
return (

<div className="container mt-5">
<UseRefExample1 />
</div>
);
}

export default App;

- inputRef will log out an object with a property called 'current'. Inside the 'current' is the DOM element
- So if you console log inputRef.current you will get the input element
- console.log(inputRef.current.value) will output the contents of the input
  console.log(123);
- You cana use this instead of state
- You can also set the value like this:
  inputRef.current.value="Hello"
- All vanilla js is available:

  const onSubmit = (e) => {
  e.preventDefault();
  console.log(inputRef.current.value);
  inputRef.current.value = "Hello";
  inputRef.current.style.backgroundColor = "red";
  };

  - THIS DOES NOT RERENDER YOUR COMPONENT LIKE STATE DOES
  - Example for a paragraph
    import { useRef } from "react";

export default function UseRefExample1() {
const inputRef = useRef();
const paraRef = useRef();

const onSubmit = (e) => {
e.preventDefault();
console.log(inputRef.current.value);
inputRef.current.value = "Hello";
inputRef.current.style.backgroundColor = "red";
paraRef.current.innerText = "Goodbye";
};

return (

<div>
<form onSubmit={onSubmit}>
<label htmlFor="name">Name</label>
<input type="text" ref={inputRef} id="name" className="form-control mb-2" />
<button type="submit" className="btn btn-primary">
Submit
</button>
<p ref={paraRef}></p>
</form>
</div>
);
}

- Or to focus on the input if the paragraph is clicked

...
</button>

<p onClick={() => inputRef.current.focus()} ref={paraRef}>
Hello
</p>

  </form>
...

### USEREF FOR GETTING PREVIOUS STATE

import { useState, useEffect, useRef } from "react";

function UseRefExample2() {
const [name, setName] = useState("");

const renders = useRef(1);
const prevName = useRef("");

useEffect(() => {
renders.current = renders.current + 1;
prevName.current = name;
}, [name]);

return (

<div>
<h1>Renders: {renders.current}</h1>
<h2>Prev Name State: {prevName.current}</h2>
<input type="text" value={name} onChange={(e) => setName(e.target.value)} className="form-control mb-3" />
</div>
);
}

export default UseRefExample2;

- The useRef hook in React is used for accessing and interacting with a DOM element directly, but it can also be used to store any mutable value that does not cause a re-render when updated. This is precisely how it's being used in the code snippet you've shared.

In the UseRefExample2 component, useRef is utilized in two different ways, neither of which involves direct reference to a DOM element within the return statement:

Tracking the number of renders:

const renders = useRef(1); initializes a ref object with a current property of 1. This is used to keep track of how many times the component has rendered.
Inside the useEffect hook, which runs after every render when the name state changes ([name] dependency array), the code renders.current = renders.current + 1; increments this value. This does not trigger a re-render because updating a ref does not inform React of a change in the component's state or props.
Storing the previous state value:

const prevName = useRef(""); similarly initializes another ref to hold the previous name state value.
Within the same useEffect hook, prevName.current = name; updates this ref to always hold the previous value of name after each render triggered by a change to name.
The crucial aspect to understand here is that useRef is being used as a persistent container that holds a value across renders without causing additional renders when its content changes. This is why you don't see ref={someRef} in the return element: the refs (renders and prevName) are not used to reference DOM elements directly but rather to store arbitrary values (the number of renders and the previous name state) across component re-renders.

This pattern is useful for keeping track of values over time without affecting the render cycle, different from how refs are typically introduced to newcomers (e.g., accessing a DOM element directly).

- The .current property is a characteristic of the object returned by React's useRef hook. When you initialize a ref using useRef, you get back a mutable ref object with a property named .current. This property is initialized to the argument passed to useRef. Here's a breakdown of how this works:

Initialization: When you do something like const myRef = useRef(initialValue), React creates an object that looks like { current: initialValue }. This object is then persisted for the entire lifetime of the component.

Accessing and Modifying: You can access the current value of the ref by using myRef.current, and you can modify it directly by assigning a new value to myRef.current. Unlike state updates with useState, changing the .current property does not trigger a component re-render.

Persistence Across Renders: The ref object (including its .current property) is persisted across renders. This means that if you update myRef.current, the updated value will be available in the next render, but React won't re-render the component just because a ref's .current property was modified.

This mechanism is what allows useRef to be used for storing a mutable value that doesn't cause component re-renders when it changes, which is particularly useful for things like:

Storing a previous state value across renders (without causing additional renders when it changes).
Keeping track of how many times a component has rendered.
Accessing DOM elements directly for focus management, measuring dimensions, and more, without involving React's re-render cycle.
In your code snippet, useRef is used to track the number of renders and the previous name value without causing a re-render when these are updated, thanks to the mutable .current property that useRef provides.

### USEREF WHEN THE COMPONNENT UNMOUNTS WHEN HANDLING A RESPONSE

- This handles as a memory leak error
- In addition to the example3 we need a fetch request that we can unmount before we get the response back

* NOTE: If you want something to happen after a component has unmounted like here when you toggle before three seconds have expire, you might experience a memory leak warning. Nowadays react automatically runs a cleanup and just dont update state after you have toggled like this. However, should you experience such a memory leak you should do the following:

You return it in the useEffect, but outside of the fetch()

import { useState, useEffect, useRef } from "react";

function ToDo() {
const [loading, setLoading] = useState(true);
const [todo, setTodo] = useState({});

const isMounted = useRef(true);

useEffect(() => {
fetch("https://jsonplaceholder.typicode.com/todos/1")
.then((res) => res.json())
.then((data) => {
setTimeout(() => {
if (isMounted.current) {
setTodo(data);
setLoading(false);
}
}, 3000);
});

    // Runs when component is unmounted
    return () => {
      console.log(123);
      isMounted.current = false;
    };

}, []);

return loading ? <h3>Loading...</h3> : <h1>{todo.title}</h1>;
}
export default ToDo;
