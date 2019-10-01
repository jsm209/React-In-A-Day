# React Notes

*This guide is based off of the online lessons by Bob Ziroll, which can be found [here](https://scrimba.com/playlist/p7P5Hd)*
## Basics
In React you build things out of components.

There are several import statements that if used, allows you to do different things:
```
import React from "react" 		 // allows you to actually use react library and JSX

import ReactDOM from "react-dom" 
								 // Allows you to use reactDOM object methods, 
								 // which are important for example, rendering 
								 // components into from the virtural DOM tree 
								 // to the actual page DOM.
```

In a nutshell, your index.js file will have both import statements, as well as
import any components you need (probably only the App component), and render
an App component to the "root" element on an HTML page.

```
// Example reactDOM.render method...
ReactDOM.render(<App />, document.getElementById("root"))

```

## Basic Organization
What does the App component have then? The app component is basically a component
to render all the other components of the app. Because it itself isn't rendering 
from the virtual DOM to the real DOM (it's basically organizing a virtural DOM)
it doesn't need to import reactDOM. However it does need to import all the 
components used to build the App. A good practice to get into is storing all
these components into folders.

## React Components
Ok so what is a component? It is essentially a piece of HTML code that is adaptable like a function. To distinguish react components from HTML elements, we denote react components with \<UppercaseTags /> rather than regular HTML tags like \<header>. Additionally, they're self closing. They can also take "props" which are essentially attributes that are passed into the component and treated as if they were the parameters of a function. 

Example of a react component:

```
import React from "react"

function Joke(props) {
    if (props.question != null) {
        return(
            <div>
                <p>Question: {props.question}</p>
                <p>Answer: {props.punchline}</p>
            </div>
        )  
    } else {
        return(
            <div>
                <p>Answer: {props.punchline}</p>
            </div>
        )  
    }
}

export default Joke
```

Notice that components (even App) always return a single div, but inside of this div can be any amount of complexity. Additionally, notice how props is treated like an object, because technically when you specify the attributes of a react component, you're assigning state to the props object.

Example of an App component using the Joke component:

```
import React from "react"

import Joke from "./Joke"

function App() {
    return(
        <div>
            <Joke
                question="This is the question of the joke."
                punchline="This is the punchline of the joke."
            />
            <hr />
            <Joke
                punchline="Sometimes jokes only need punchlines."
            />
        </div>
    )
}

export default App
```

There is a practice called *lifting up the state*, which means processing 
variables outside of the component, then only passing in final values to 
the component, as opposed to the component itself computing or processing 
anything. This is useful and important because it keeps the entire app in 
sync with both values and time.

## Higher Order Functions with External Data for Component Creation

We can use higher order functions like map to loop over a collection of data 
(for example JSON) to build components without having to manually write out
every single component:
```
import React from "react"

import Joke from "./Joke"
import jokesData from "./jokesData"

function App() {
    const jokeComponents = jokesData.map(function joke(item) {
        return (
            <Joke key={item.key} question={item.question} punchLine={item.punchLine} />
        )
    })
	
    return (
        <div>
            {jokeComponents}            
        </div>
    )
}
```
There is a lot going on here. For one, we're simulating an http request by 
already having and importing jokesData, however it is formatted like typical 
JSON. Basically, we're using the higher order function *map*, to store an
array of components inside jokeComponents by using a function called *joke*
to create a joke component using the data of the specific item in jokesData
that we're currently iterating over.

After that, the App component returns jokesComponent, because our entire app
is basically just the whole collection of newly constructed joke components.

Another example:
```
import React from "react"
import Product from "./Product"
import productsData from "./vschoolProducts"

function App() {
    const productComponents = productsData.map(item => <Product key={item.id} product={item}/>)
    
    return (
        <div>
            {productComponents}
        </div>
    )
}

export default App
```

## Functional Components vs. Class-based Components
Up until now, we've been writing functional components:
```
function App() {
    return (
        <div>
            <h1>Code goes here</h1>
        </div>
    )
}

export default App
```

This is the equivalent as a class based component:
```
class App extends React.Component {
	render() {
		return(
			<div>
				<h1>Code goes here</h1>
			</div>
		)
	}
}
```

All class based components require a render() method. If there is code that
determines the display logic/conditional rendering (like style or variables),
they can be calculated in the render method before the return statement.

Props are referenced differently in class based components too.
You have to use *this.props* as opposed to referencing just *props*
```
class App extends React.Component {
	render() {
		return(
			<div>
				<h1>{this.props.whatever}</h1>
			</div>
		)
	}
}
```

## State
State is important because it allows components to change each other. This is
important because props are immutable, and therefore cannot be used to manage
changes made by components.

The constructor method is where we can intialize our state.

Example constructor method in an app:
```
class App extends React.Component {
    constructor() {
        super()
        this.state = {
            answer: "Yes"
        }
    }
    
    render() {
        return (
            <div>
                <h1>Is state important to know? {this.state.answer}</h1>
                <ChildComponent answer={this.state.answer}/>
            </div>
        )
    }
}

export default App
```
Notice how the constructor has *super()* and that *this.state is an object*.
The super() function is necessary because we need to use things that are a
part of its parent, React.Component.

Also notice how we can pass the state of one component to another through
giving the props of another component this component's state.

## Handling Events in React
Regular HTML event handling:
```
<button onclick="myFunction()">Click me</button>
```

React event handlers are the JavaScript version/camel cased:
```
<button onClick={() => console.log("I was clicked!")}>Click me</button>
```

Why? It uses the same convention that regular javascript does if you were
to add these events to elements on the page.

Here is a link to more event handler documentation:
<https://reactjs.org/docs/events.html#supported-events>

## Changing State
You know how to initialize state, and use event handlers to call functions you write, so how do we go about changing the state?

*Our render method looks like this right now. How do we make the count update by changing the state when we click on the button..?*
```
    render() {
        return (
            <div>
                <h1>{this.state.count}</h1>
                <button onClick={this.handleClick}>Change!</button>
            </div>
        )
    }
```

It may be tempting, for example in the case of a button, to write a function called *handleClick()* that will literally change *this.state.property* to something else, **however you will never modify state directly.**

*Don't do this...*
```
    handleClick() {
        this.state.count++
    }
```

Instead, extending React.Component gave us access to a function called *setState()*, which will re-set the state to the given state. We get a new version of state.

```
    handleClick() {
        this.setState({ count: 1 })
    }
```

**Anyime you write a function that uses setState(), you want to bind your function to your class.** It make sure the handleClick() method for example, is bound to the context of this particular instance of this class.

```
class App extends React.Component {
    constructor() {
        super()
        this.state = {
            count: 0
        }
        this.handleClick = this.handleClick.bind(this) // bound.
    }
```

If you noticed, we're changeing the state of count to 1, after which it will just continuously change itself to 1 and never update. How do we change the count based on what it was previously?

You pass in the previous state to the setState function as the parameter for another function. This other function should return the new version of state.

```
    handleClick() {
        this.setState((prevState) => {
            return {
				count: prevState.count + 1
			}
        })
    }
```

Example in its entirety:
```
import React from "react"

class App extends React.Component {
    constructor() {
        super()
        this.state = {
            count: 0
        }
        this.handleClick = this.handleClick.bind(this)
    }
    
    handleClick() {
        this.setState(prevState => {
            return {
                count: prevState.count + 1
            }
        })
    }
    
    render() {
        return (
            <div>
                <h1>{this.state.count}</h1>
                <button onClick={this.handleClick}>Change!</button>
            </div>
        )
    }
}
```

This is **SUPER POWERFUL** because it allows components to track and change their own state. It allows components to update themselves whenever the state of its parent changes, and thanks to React, these components will re-render automatically.

## Example of Todo List React App
This is a quick example of a todo list app in react that takes advantage of using class based components to use handling events to change the state of other components. There are three main steps to be aware of in this example. *It will be useful to first scan through the code, then refer back to these steps to ensure understanding:*
1. *handleChange(id)* uses the higher order function *map* to loop over the current version of our todos data (originally imported in the form of the object seen below). For each todo item, the anonymous function given to map returns the original item unless the particular item we're iterating over matches the given id given in the parameter of this method. If that's the case, we will change this particular todo item property *completed* to the opposite of what it was earlier. This is the behavior for a check box.
2. Everytime we construct a todo item component, we give this method as a prop called *handleChange*. The attribute could be named whatever, but the important thing to see is that we're giving *this.handleChange* method to each of our todo item components.
3. Each todo item component will reference the given handleEvent prop every time it is changed. When it is changed, it calls an anonymous function that will simply call handleChange, and give it *props.item.id*. Something that confused me is why couldn't I give the handleChange function in this case, *props.key* instead (notice that is also another attribute we gave when we constructed todo items in the App Component) and the reason is that React thinks of component keys as special, and not techincally a prop, so if you need to reference something by an id, you need to give it to the component in another attribute besides specifying *key*.

### Original Todos Data
```
const todosData = [
    {
        id: 1,
        text: "Take out the trash",
        completed: true
    },
    {
        id: 2,
        text: "Grocery shopping",
        completed: false
    },
    {
        id: 3,
        text: "Clean gecko tank",
        completed: false
    },
    {
        id: 4,
        text: "Mow lawn",
        completed: true
    },
    {
        id: 5,
        text: "Catch up on Arrested Development",
        completed: false
    }
]

export default todosData
```

### App Component
```
import React from "react"
import TodoItem from "./TodoItem"
import todosData from "./todosData"

class App extends React.Component {
    constructor() {
        super()
        this.state = {
            todos: todosData
        }
        this.handleChange = this.handleChange.bind(this)
    }
    
    handleChange(id) {
        this.setState(prevState => {
            const updatedTodos = prevState.todos.map(todo => {
                if (todo.id === id) {
                    todo.completed = !todo.completed
                }
                return todo
            })
            return {
                todos: updatedTodos
            }
        })
    }
    
    render() {
        const todoItems = this.state.todos.map(item => <TodoItem key={item.id} item={item} handleChange={this.handleChange}/>)
        
        return (
            <div className="todo-list">
                {todoItems}
            </div>
        )    
    }
}

export default App
```

### Todo Item Component
```
import React from "react"

function TodoItem(props) {
    return (
        <div className="todo-item">
            <input 
                type="checkbox" 
                checked={props.item.completed} 
                onChange={() => props.handleChange(props.item.id)}
            />
            <p>{props.item.text}</p>
        </div>
    )
}

export default TodoItem
```

## Lifecycle Methods
To talk about lifecycle methods of react components, it is useful to compare
a react component to a human, both of which undergo milestones throughout
their lives. Lifecyle methods for react components are milestones that when
reached, will make the component behave a certain way.

Here is an article about some of these methods, which includes notes about
the ones being deprecated in React 16:
<https://engineering.musefind.com/react-lifecycle-methods-how-and-when-to-use-them-2111a1b692b1>.

Here is the React team's official blog post about the update: 
<https://reactjs.org/blog/2018/03/29/react-v-16-3.html#component-lifecycle-changes>.

- One example is the *render()* method. Not typically thought of as a lifecycle
method, but one analogy to help understand how this method plays in a
components life, is the act of getting dressed for the day. Every time the
component is presenting itself to the world, it "dresses" itself visually.
- Another is *componentDidMount()*, which is ran basically when the component
is born, or first "mounted" to the application. A common use for this is making
an API call, where you only need to make a single request to get data.
- Another is *shouldComponentUpdate(nextProps, nextState)* gives us a chance
to optimize our app. It is analogous to deciding if one should change clothes,
or re-render. This is important because React will sometimes automatically
re-render components, even if nothing changes about them, in order to be safe,
but this can be a costly action. We can determine the logic if it should change
or not here. It should return true if we want the component to update, and
false otherwise. *Be sure that if you're returning false, that you definitely
do not want the component to re-render, because it can be hard to debug why
components are not changing if there is a flaw in your logic.*
- Another is *componentWillUnmount()*, will run when the component dies, or
unmounts from the screen. This is useful for cleanup or teardown before a
component disappears, such as removing event listeners it may have placed.

There are some deprecated lifecycle methods, which are useful to know about
if you're reviewing code before React 16:
- **(This is deprecated)** Another is *componentWillRecieveProps(nextProps)*,
will run this method everytime the component gets props from a parent
component. It occurs whenever the component is mounted, or when a parent
passes down props (even if those props remained unchanged). A use for this
is checking if the props have changed, then doing some sort of calculation.
- **(This is deprecated)** *componentWillMount()*
- **(This is deprecated)** *componentWillUpdate()*


These are being replaced by some new lifecycle methods, which we should
know about:
- *static getDerivedStateFromProps(props, state)* is a static method (so we
need to include the static keyword) that returns a new updated state based
on the props. Read more about **how you don't need this** here: 
<https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html>.
This is method for rare cases, and is generally discouraged for use since
there are other ways to go about setting a component's own state from props.
- *getSnapshotBeforeUpdate()* creates a backup of the way current things
area. Not a super common lifecyle method either, but here is documentation
if you're curious: 
<https://reactjs.org/docs/react-component.html#getsnapshotbeforeupdate>.

## Functional or Class Based Component?
Use a class based component when you need to manage state or use a lifecycle
method. For all other cases, use a functional component. The reason for this
is that React is moving more towards functional components and away from
class based components, so to best preserve your code for the future, it should
also follow this trend.

## Conditional Rendering with Ternary Operations
A ternary operation has the following format:

*condition ? statement if true : statement if false*

Example:
```
props.isLoading === true ? <h1>Loading...</h1> : <h1>Some cool stuff about conditional rendering</h1>
```

A common way to do conditional rendering in React is with a ternary operation.
Since we need to return a div, we need React to interpret our ternary operation
as JavaScript, so we need to wrap in with "{}". 

```
import React from "react"

function Conditional(props) {
    return (
        <div>
            <p>Something consistent here.</p>
            
            {props.isLoading === true ? <h1>Loading...</h1> : <h1>Some cool stuff about conditional rendering</h1>}
            
        </div>
    )
    
}

export default Conditional
```

Notice how we also have other elements inside the div that are also being
returned. This is a neat way to do conditional rendering, because only
the middle section will update while the other sections remain the same.

There's honestly so many ways to do conditional rendering. For example,
instead of having this component decide what to render, the parent App
component may be a better choice, so we might put the ternary operation
inside its render method instead.

Another cool way is with the && operator.
Typically we use this to determine the truthiness of two statements,
but in reality what is happening is that JavaScript will check if
the first condition is true, then return the second statement,
and if false, return false:

(true && true) returns true.

(true && false) returns false, because it literally returns the second 
condition.

(false && true) returns false, because the first condition was false.

Here is an example, where if we have more than 0 unread message, we'll
return the second statement, but if not, return nothing at all. 

```
    render() {
        return (
            <div>
                {
                    this.state.unreadMessages.length > 0 && 
                    <h2>You have {this.state.unreadMessages.length} unread messages!</h2>
                }
            </div>
        )
    }
```

## Quick Conditional Rendering Example
Here is an example with conditional rendering, that allows a button to log
a user in if they're logged out, and log out if they're logged in.
It combines state, ternary operators, and conditional rendering into a single
example:

```
class App extends React.Component {
    constructor() {
        super()
        this.state = {
            isLoggedIn: false
        }
        this.toggleLogin = this.toggleLogin.bind(this)
    }
    
    toggleLogin() {
        this.setState(prevState => { 
            return {
                isLoggedIn: !prevState.isLoggedIn
            }
        })
    }
    
    render() {
        return (
            <div>
                {this.state.isLoggedIn ? <p>Logged In</p> : <p>Logged Out</p>}
                <button onClick={this.toggleLogin}>{this.state.isLoggedIn ? "Log Out" : "Log In"}</button>
            </div>
        )
    }

}

export default App
```


## Forms with React
Typical forms with regular vanilla JavaScript DOM involves a form with input
elements, where all the data is collected at once in the end and submitted.

In React, a core practice is that the state of an object should be a single
source of truth. In other words, there shouldn't be any differences between
the state and the rest of the page, and that the state shouldn't rely on other
sources for its "truth".

This is important for forms, because input elements are always changing what
they're displaying. One way we might go about implementing a form in React is
by having the input elements of a form dictate the state of the parent object.

This would violate the state being the single source of truth, because now
it's relying on an input for its value, as opposed to the input relying on the
state. This is an example where the input is determining the state:

*Here are inputs, where when it's changed it will call a handleChange method.
```
    render() {
        return (
            <form>
                <input type="text" name="firstName" placeholder="First Name" onChange={this.handleChange} />
                <br />
                <input type="text" name="lastName" placeholder="Last Name" onChange={this.handleChange} />
                <h1>{this.state.firstName} {this.state.lastName}</h1>
            </form>
        )
    }
```
It is important to see that the input has a "name" property, and this matches
the name of it's corresponding state object property.

*This is the handleChange method*
```
    handleChange(event) {
        this.setState({
            [event.target.name]: event.target.value
        })
    }
```

*This is a better way to write the handleChange method*
```
    handleChange(event) {
        const {name, value} = event.target
        this.setState({
            [name]: event.target.value
        })
    }
```
What is happening, is that every time the user changes the form, the input
calls the handleChange method, which always passes in an event object from
whatever called the event. This event object has another object called target,
which refers to the specific element that called the method. This allows us
to reference and assign the state according to the input that called it.

However, at this point, the state is relying on the input. To make the input
rely on the state, we simply alter the input elements to be this instead:

*The input is used to assign the state, and the state updates the input*
```
                <input 
                    type="text" 
                    value={this.state.firstName} 
                    name="firstName" 
                    placeholder="First Name" 
                    onChange={this.handleChange} 
                />
                <br />
                <input 
                    type="text" 
                    value={this.state.lastName} 
                    name="lastName" 
                    placeholder="Last Name" 
                    onChange={this.handleChange} 
                />
```
We simply make the value of the input be dictated by the state.

What if it wasn't a text input though? How wold handleChange work then?
I'm not going to explain all the syntax because one can simply look at
the following example and reason it through.

The most important part of the example is this handleChange method,
which can accomodate a bunch of different input types:
```
    handleChange(event) {
        const {name, value, type, checked} = event.target
        type === "checkbox" ? this.setState({ [name]: checked }) : this.setState({ [name]: value })
    }
```

**Great example of a form using React**
```
import React, {Component} from "react"

class App extends Component {
    constructor() {
        super()
        this.state = {
            firstName: "",
            lastName: "",
            isFriendly: false,
            gender: "",
            favColor: "blue"
        }
        this.handleChange = this.handleChange.bind(this)
    }
    
    handleChange(event) {
        const {name, value, type, checked} = event.target
        type === "checkbox" ? this.setState({ [name]: checked }) : this.setState({ [name]: value })
    }
    
    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                <input 
                    type="text" 
                    value={this.state.firstName} 
                    name="firstName" 
                    placeholder="First Name" 
                    onChange={this.handleChange} 
                />
                <br />
                <input 
                    type="text" 
                    value={this.state.lastName} 
                    name="lastName" 
                    placeholder="Last Name" 
                    onChange={this.handleChange} 
                />
                
                {
                    /**
                     * Other useful form elements:
                     * 
                     *  <textarea /> element
                     *  <input type="checkbox" />
                     *  <input type="radio" />
                     *  <select> and <option> elements
                     */
                }
                
                <textarea 
                    value={"Some default value"}
                    onChange={this.handleChange}
                />
                
                <br />
                
                <label>
                    <input 
                        type="checkbox" 
                        name="isFriendly"
                        checked={this.state.isFriendly}
                        onChange={this.handleChange}
                    /> Is friendly?
                </label>
                <br />
                <label>
                    <input 
                        type="radio" 
                        name="gender"
                        value="male"
                        checked={this.state.gender === "male"}
                        onChange={this.handleChange}
                    /> Male
                </label>
                <br />
                <label>
                    <input 
                        type="radio" 
                        name="gender"
                        value="female"
                        checked={this.state.gender === "female"}
                        onChange={this.handleChange}
                    /> Female
                </label>
                <br />
                
                <label>Favorite Color:</label>
                <select 
                    value={this.state.favColor}
                    onChange={this.handleChange}
                    name="favColor"
                >
                    <option value="blue">Blue</option>
                    <option value="green">Green</option>
                    <option value="red">Red</option>
                    <option value="orange">Orange</option>
                    <option value="yellow">Yellow</option>
                </select>
                
                <h1>{this.state.firstName} {this.state.lastName}</h1>
                <h2>You are a {this.state.gender}</h2>
                <h2>Your favorite color is {this.state.favColor}</h2>
                <button>Submit</button>
            </form>
        )
    }
}

export default App
```

A library called "Formik" reduces the amount of tears you have to spend 
on forms in React.
<https://jaredpalmer.com/formik/>

## Container/Component Architecture
The last form example is jarring to read, and honestly too long for a component
for programmers to easily read and process, so a common practice is to split
up its parts between the *business* and *display logic*. 

Basically, one component will be in charge of maintaining state, and handling
changes to state, then passing it off to another component that handles all
the display logic.

You acomplish this by literally copying and pasting (seperating) the code into
seperate components, then taking the state calculated by the business logic
component, and passing it in, including functions like the handleChange 
function, as props to the display logic component.

## Modern React Practices
Instead of binding the methods of a class based component, you can instead
declare the method as an arrow function so you don't have to bind it.

```
    // Change to use arrow functions
    handleChange = (event) => {
        const { name, value } = event.target
        this.setState({
            [name]: value
        })
    }
```

You can also just declare state as a regular javascript object outside of the
constructor method.


 Other modern/advanced React features/topics to learn:
 * Official React Context API - <https://reactjs.org/docs/context.html>
 * Error Boundaries - <https://reactjs.org/docs/error-boundaries.html>
 * render props - <https://reactjs.org/docs/render-props.html>
 * Higher Order Components - <https://reactjs.org/docs/higher-order-components.html>
 * React Router - <https://reacttraining.com/react-router/core/guides/philosophy>
 * React Hooks - <https://reactjs.org/docs/hooks-intro.html>
 * React lazy, memo, and Suspense - <https://reactjs.org/blog/2018/10/23/react-v-16-6.html>
 
 
## Steps for creating react applications
1. npm install create-react-app
2. create-react-app my-app-name
3. npm start

## Steps for using reactstrap
1. npm install --save bootstrap reactstrap
2. Include the following in your index.js file: 
```import 'bootstrap/dist/css/bootstrap.min.css';```
3. Anytime you need to use a reactstrap component, import it with 
```import { Button } from 'reactstrap';``` 
in either App.js or your desired component file.
 

