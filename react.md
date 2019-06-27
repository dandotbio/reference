# React Notes

## State

When you call setState in a component, React automatically updates the child components inside of it too.



## Events

It is a convention to use on[Event] names for props which represent events and handle[Event] for the methods which handle the events.



## Components

n React, function components are a simpler way to write components that only contain a render method and don’t have their own state. Instead of defining a class which extends React.Component, we can write a function that takes props as input and returns what should be rendered. Function components are less tedious to write than classes, and many components can be expressed this way.


### Keys

React automatically uses key to decide which components to update. A component cannot inquire about its key

When a list is re-rendered, React takes each list item’s key and searches the previous list’s items for a matching key. If the current list has a key that does not exist in the previous list, React creates a component. If the current list is missing a key that exists in the previous list, React destroys the previous component. If the keys match, the component is moved. Keys tell React about the identity of each component which allows React to maintain state between re-renders. If a component’s key changes, the component will be destroyed and re-created with a new state.

If no key is specified, React will present a warning and use the array index as a key by default. Using the array index as a key is problematic when trying to re-order a list’s items or inserting/removing list items. Explicitly passing key={i} silences the warning but has the same problems as array indices and is not recommended in most cases.

Keys do not need to be globally unique. Keys only need to be unique between components and their siblings.



## Functions

Unlike the array push() method you might be more familiar with, the concat() method doesn’t mutate the original array, so we prefer it.


const?
let?
key?
ref?



https://reactjs.org/docs/state-and-lifecycle.html
