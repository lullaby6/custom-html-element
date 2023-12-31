# custom-html-element

Create reusable and reactive Web Components using Custom Element API.

# Installation

Using NPM:

```sh
npm install custom-html-element
```

Using the CDN:

```html
<script defer src='https://cdn.jsdelivr.net/npm/custom-html-element/dist/cdn.js'></script>
```

# Usage

## Sintax showcase

JavaScript:

```js
// if you are using the CDN, you don't need import anything
import {customHTMLElement} from 'custom-html-element'

function MyElement(){
    return `
        <h1>Hello World</h1>
    `
}

// use the customHTMLElement method to define the custom element
customHTMLElement(MyElement)
```

HTML:

```html
<my-element></my-element>
```

## Using children elements

the inner HTML of the element ```slot``` inside of your custom element will be setted by the original inner HTML of the custom element.

JavaScript:

```js
function MyElement({count}){
    return `
        <h1>World</h1>
        <slot></slot>
    `
}
```

HTML:

```html
<my-element>
    <h1>World</h1>
</my-element>
```


## Let's create a simple button counter

JavaScript:

```js
import {customHTMLElement, HTMLElementAddEventListener} from 'custom-html-element'

function MyCounter({count}){

    // creating a reference to hit the button
    const ref = crypto.randomUUID()

    /*
    use the HTMLElementAddEventListener method for add a
    event listener to elements inside custom elements
    */
    HTMLElementAddEventListener(ref, 'click', () => {

        // when any attribute of the custom element is updated, it will be re-rendered
        this.setAttribute('count', parseInt(count) + 1)

    })

    return `
        <button id="${ref}">${count}</button>
    `
}

customHTMLElement(MyCounter)
```

HTML:

```html
<!--
you need to specify the attributes to be used in the
custom element function, otherwise it will throw an error
 -->
<my-counter count="0"></my-counter>
```

## Now let's create a simple ToDo List App

JavaScript:

```js
import {createHTMLElement, HTMLElementAddEventListener} from './index.js'

function TodoList({todos}) {
    todos = JSON.parse(todos)

    const formRef = crypto.randomUUID()

    HTMLElementAddEventListener(formRef, 'submit', (event) => {
        event.preventDefault()
        const formData = new FormData(event.target)
        const formProps = Object.fromEntries(formData)

        todos.push({id: crypto.randomUUID(), text: formProps['todo']})

        this.setAttribute('todos', JSON.stringify(todos))
    })

    todos.forEach(todo => {
        HTMLElementAddEventListener(todo.id, 'click', (event) => {
            todos.splice(todos.findIndex(todo => todo.id === event.target.id), 1)

            this.setAttribute('todos', JSON.stringify(todos))
        })
    })

    return `
        <div>
            <form id="${formRef}">
                <input type="text" placeholder="todo" name="todo">
                <input type="submit" value="add todo">
            </form>
            <ul>
                ${todos.map(todo => `
                    <li id="${todo.id}">${todo.text}</li>`
                ).join('')}
            </ul>
        </div>
    `
}

createHTMLElement(TodoList)
```

HTML:

```html
<todo-list todos='[]'></todo-list>
```