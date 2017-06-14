![Redux Image](https://d2eip9sf3oo6c2.cloudfront.net/series/square_covers/000/000/025/full/EGH_Redux-New.png?1496436379){:height="50%" width="50%"}

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

#### Store Methods

There are a few different store methods that will be of use when building a Redux application. The first one is the **getState()** method, which when called will return the state of the redux store. The second method is the **dispatch(action)** method. When this method is called with a action type, it will perform the specified action to change the state of the application. The final method is **subscribe(listener)** which will allow registering a callback that the redux store will call whenever a state is changed so that the UI can be updated to reflect the changes. It basically enables a listener for our store.

The following are examples of the three different methods:

```javascript
const scorekeeper = (state = 0, action) => {
  switch(action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
    default:
      return state;
  }
}
// ES6 syntax similar effect as var createStore = Redux.createStore;
const { createStore } = Redux;
// Calling createStore with counter as the reducer to manage state updates
const store = createStore(counter);

store.getState(); // will return 0, the initial state of application

store.dispatch({type: 'INCREMENT'}); // This will dispatch the increment action
store.getState(); // will now return 1 due to the increment action

// Creates a listener so that each time the state changes the UI is updated
store.subscribe(() => {
  document.body.innerText = store.getState();
});

document.addEventListener('click', () => {
  store.dispatch({type: 'INCREMENT'});
});
```

#### Avoiding Mutations

Making sure we don't mutate data allows for more consistency around our overall program. If we modify a piece of data in our application that something else is dependent on then it will alter the overall application. When looking at large scale applications or even making an application that can be scalable in the future taking a pure approach will benefit in the long run.

##### Avoiding Array Mutations

There are three methods to look at when wanting to work with arrays but not modify the data in the original structure. The first method is the **concat(arrays or values)** method which will add a value or values to to an array but returns the new array instead of modifying the original. The second method is **slice(beginIndex, endIndex)** which takes two parameters and will return a portion of the array within that range, this does not mutate the original array.

```javascript
const listBeforeConcat = [];
const listAfterConcat = listBeforeConcat.concat([1]);
console.log(listAfterConcat);
// Returns [1]

const listBeforeSlice = [1, 2, 3, 4];
const listAfterSlice = listBeforeSlice.slice(2,4);
console.log(listAfterSlice);
// Returns [3, 4]
```

##### Avoiding Object Mutations

When working with objects we may want to alter a piece of data present in that object and return it but with a pure approach. This would mean we cannot alter the original object. One way of doing so would be to return a new object with the specific field modified as follows:
```javascript
const changeFriendship = (person) => {
  return {
    name: "Mohammad",
    age: "21",
    friend: !person.friend
  }
}
```

However there is a key problem by doing this, later on in our application we might add fields to our object, possibly a birth date or some other property. If we do not remember this piece of code then we will forget to update it, resulting in a future bug that will be tough to catch.

This is why we must use the **Object.assign()** method to assign properties to a target object and can return a new object.

```javascript
const changeFriendship = (person) => {
  return Object.assign({}, person, {
    friend: !person.friend
  });
};
```

#### Reducer Composition

When using reducers we want to make sure we don't have a lot going on inside of the reducer, this means we should extract functions that do other things within our reducer function. So if we think about any time that a function is doing too many things at once, we can pull out the excess functions and call them so that each function does one specific thing.

##### Reducer Composition for Objects

Considering the concept behind reducer composition, we want each function to handle a specific task. Now when it comes to specific objects we want to let different reducers handle different parts of the state tree and then combine the results. This is the main way to approach reducers in Redux applications and is such a common practice that Redux provides a **combineReducers()** function.

The function basically takes away the dirty work of writing the code for combining the end result and just generates a top level reducer. Combine reducers just takes on argument that is an object, and that object specifies the mapping between the state fields and the reducers that manage them. The function returns an overall reducer function.

*NOTE: By always naming reducers after the state keys that they manage, ES6 allows for object literal shorthand notation, meaning less writing*

```javascript
const todoApp = combineReducers({
  todos, // "todos" is both the state field and reducer function name
  visibilityFilter // "visibilityFilter" is both the state field and reducer function name
});
```
