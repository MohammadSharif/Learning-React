# Learning Redux
This document will be a collection of my notes as I learn Redux, I will also post the supplements that I have used to form this document below. Almost all of the information will be repeated within the supplements, it's just that I have noted them down in a way to gather the information collectively and written in a way to make it more understandable for myself.

## Supplements
http://redux.js.org/

https://egghead.io/series/getting-started-with-redux

Redux is a tool that was made to help write web apps that have consistent behavior, can run in different environments, and are pretty simple when it comes to testing.

Redux doesn't have to be used with React, it can be used with any of the other similar libraries and takes up a extremely small amount of space.

### Installation

Assuming npm is installed on the machine, redux is a simple install with the following command.

```
npm install --save redux
```

#### Useful Extras

Most likely the React bindings and the developer tools will also be needed and can be installed as follows:

```
npm install --save react-redux
npm install --save-dev redux-devtools
```

## The Big Picture

Redux has a specific way of approaching how states are handled. The entire state of the application is stored inside of an object tree inside of a since *store*. A store is an object, not necessarily a class, that has a few methods within it. The state tree of the application can only be modified through emitting an *action*, which is an object describing the changes that occured.

## Redux Basics

#### The State Tree

The state of the application is represented by an immutable state tree. This tree will represent each change in the state of the application. This single object will hold the entire state of the application, for a simple application that has one stateful component maintaining a counter it can simply be something such as:
```javascript
[0] // Original State
[1] // Increment
[0] // Decrement
```
For a more complex state tree, which tends to be the case for many web applications it would look something like the following:
```javascript
[object Object] {
  people: [[object Object] {
    friend: false,
    id: 0,
    name: "Mohammad Sharif"
  }]
}
```

#### State changes

Anytime the state needs to be changed we must dispatch an action. The action represents the change that has occurred to the data on the page. Actions are also plain javascript objects; however, they require a type property that will indicate what kind of action is going to occur. If we want to perform an action on a specific component then we must supply our action with an index field (if the component is part of an array) or a unique key (this is better especially when building larger scale applications).

Here is an example of the format of an action:
```javascript
const ADD_TO_FRIENDS = 'ADD_TO_FRIENDS'
{
  type: ADD_TO_FRIENDS,
  [object Object] {
    name: 'Mohammad Sharif',
    age: '21',
    userID: '1234'
  }
}
```

#### Pure/Impure Functions

Pure and Impure are just two words to describe whether functions alter existing data or not. Pure functions do not modify any data where as Impure functions will modify and store the changed data. Pretty simple stuff!

```javascript
// Pure
function square(x) {
  return x * x;
}
//Impure
function square(x) {
  changeXInDataBase(x);
  return x * x;
}
```

#### The Reducer Function

All redux applications must implement the reducer which is a function that calculates the next state tree based off what the previous state tree was and the action that has been specified. The reducer function is a pure function, which means it does not alter the previous state but it returns a new state tree.

What makes redux fast is that when there is a state mutation, although a new state tree will be returned it will refer to the previous state to see which data was not altered. So for example:

```javascript
// Initial State
[object Object] {
  friends: [object: Object] {
    name: "Johnny Appleseed",
    age: "19"
  },
  text: "Hello!"
}

// Post mutation
[object Object] {
  friends: [object: Object] {
    name: "Johnny Appleseed",
    age: "19"
  }
}
```

In the above we see that after the reducer function was called the text field was removed from our state tree; however, the friends array stayed the same as it was before the function call. The reducer refers to the previous state and does not have to rewrite the new information, making redux fast.
