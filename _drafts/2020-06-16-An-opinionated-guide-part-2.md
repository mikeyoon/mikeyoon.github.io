---
layout: post
title: "An opinionated guide to Javascript state management — Part 2: A tale of two states"
date: 2020-06-16 10:00:00 -07:00
---
# Summary

In the second article of this series, I'm going to introduce the two types of state that exist in all UI applications: essential and derived. It's important to know the difference between the two because it's important to model them differently in code. Failure to do so will almost certainly result in a UI that is harder to maintain and redraws unnecessarily.

Before we jump in, I'd like to briefly talk about a great paper on software complexity called ["Out of the tar pit"](http://curtclifton.net/papers/MoseleyMarks06a.pdf). It's an academic paper from 2006 that discusses in detail how software complexity is the root cause of most software development problems. I highly recommend reading through it if you're interested in better understanding software engineering from a fundamentals standpoint. It's a bit of a long read, but definitely worth your time. The paper also discusses the impact of state, so it will also help you better understand how to solve state management issues as well.

<!--more-->

# Essential State

The first type of state is what can be called "essential". There are likely other names for it, but the definition is the same. This is state that is original and cannot be calculated from any other available information. It must be provided directly by some source, i.e. the user, or from an API. A classic example of essential state would be someone's first and last name. From a code perspective, essential state is generally stored in its original form without transformation somewhere in the application.

# Derived State

Derived state, as its name implies, is derived from either essential or other derived state. Since it can be calculated, it would ideally not be stored in any permanent form in code, but rather as some kind of automatic calculation. The benefit to modeling it as a calculation is  essential state changes, the developers do not need to be concerned about updating any logic  

# Examples

In this article I'll be providing code examples that may be based on Redux or NgRx. This is because I believe the Redux/Flux is the most explicit (nothing is done for you automatically) so makes for the best teaching examples. I believe all state management frameworks end up doing similar work, so if you understand Flux, you should be able to quickly understand other frameworks as well.

## Basic

Below is a basic example in Redux + Reselect. If you're not familiar with these frameworks, I'll try to explain everything as much as possible. I'll also exclude using any helper functions provided by the frameworks so you can see more of what is happening. Please note that this code is just for demonstration purposes, and doesn't include any best practices (so don't use this in your project!).

```ts
import { createSelector } from 'reselect';
import { createStore } from 'redux';

// Shortcut to make some initial essential state
const initialState = {
  user: {
    firstName: 'John',
    lastName: 'Smith',
  },
  charts: [ // Note that storing a colection in an simple array like this may be slower
    {
      id: 1,
      name: 'chart 1',
      config: { /* Some JSON */ }, // configuration on how to draw given the dataset
      format: { /* Some JSON */ } // maybe some formatting options
    },
    {
      id: 2,
      name: 'chart 2',
      config: { /* Some JSON */ },
      format: { /* Some JSON */ }
    }
  ],
  dataset: { // Represent some data from an API
    columns: [/* Some JSON */],
    content: [/* Some JSON */]
  }
}

// Just a generic reducer for everything to save space
// This will be called once with the init action below
function myReducer(state = initialState, action) {
  switch (action.type) {
    case 'ADD_CHART':
      return state.charts.concat(/* new chart */);
    default:
      return state;
  }
}

// need the init action so the state gets populated with initialState constant
const store = createStore(myReducer, ['INIT']); 

function isValidChart(chart) {
  const isValid = lib.isValid(chart); // some expensive validation logic
  return isValid;
}

// Derived state selector
// Takes a chart id and returns a selector so we can have a memoized 
export const createIsValidSelector(id) {
  return createSelector(
    state => state.charts.find(c => c.id === id), // Performance penalty
    chart => isValidChart(chart)
  );
}

// Inside a react-like component
class ChartRenderer {
  selector = null;
  unsubscribe = null;

  constructor() { // include redux store
    this.unsubscribe = store.subscribe(() => {
      if (this.selector != null) {
        // Get a snapshot of the current state, and pass it to our selector
        this.isValid = this.selector(store.getState());
        this.setState({
          isValid
        });
      }
    });
  }

  componentWillReceiveProps(props) {
    // Need to materialize our selector for this chart instance
    this.selector = createIsValidSelector(props.id);
  }
}
```

This is a very contrived example, but I just want to highlight the "essential" state defined in the `initialState` constant, and the derived state generated by the selector created in `createIsValidSelector`. In a redux implementation, the essential state should be managed by the reducers, and derived state goes into selectors, which are memoized.

## 

# You still need to be flexible

I mentioned earlier that the ideal system would have all derived state be modeled as calculations/computations. Unfortunately, real-world software engineering is usually far from ideal. Oftentimes, performance can be a top priority, and having derived state be automatically calculated doesn't provide the control needed to maintain the user-experience you require.