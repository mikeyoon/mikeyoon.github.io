---
layout: post
title: "An opinionated guide to Javascript state management — Part 2: Essential vs Derived"
date: 2020-06-14 10:00:00 -07:00
published: true
---
# Summary

In the second article of this series (click [here]({% post_url 2020-06-06-An-opinionated-guide-part-1 %}) to read part 1), I'm going to introduce the two types of state that exist in all user interface (UI) applications: essential and derived. It's necessary to know the difference between the two otherwise you will not be able to implement them optimally. That will almost certainly result in a UI that is harder to maintain and re-renders unnecessarily, harming the user-experience.

Before we jump in, I'd like to briefly talk about a great paper on software complexity called ["Out of the tar pit"](http://curtclifton.net/papers/MoseleyMarks06a.pdf). It's an academic paper from 2006 that discusses in detail how software complexity is the root cause of most software development problems. I highly recommend reading through it if you're interested in deeply understanding what makes software engineering complicated. It's a bit of a long read, but definitely worth your time. The paper also discusses the impact of state, so it will also help you better understand how to solve state management issues as well.

<!--more-->

# Essential state

The first type of state is what can be called "essential". There are likely other names for it, but the definition is the same. This is state that is original and cannot be calculated from any other available information. It must be provided directly by some source, i.e. the user, or an API. A classic example of essential state would be someone's first and last name. From a code perspective, essential state is generally stored in its original form without transformation somewhere in the application. In our case we'll be using [flux](https://facebook.github.io/flux/) reducers to do this.

# Derived state

Derived state, as its name implies, is derived from either essential or other derived state. Since it can be calculated, it would ideally not be stored in any permanent form in code, but rather as some kind of function. The benefit of separating them from the reducers is that leaves reducers responsible for just updating references. All the complex logic can go where it is best suited, into a specialized function that can aggregate multiple pieces of state. We'll use the selector pattern in this article to demonstrate this.

# Simple example walkthrough

In this article as well as the rest of the series, I'll be providing code examples in mostly [Redux](https://redux.js.org/). This is because I believe the Redux/Flux is the most explicit (nothing is done for you automatically) and makes for great teaching examples. Most (or all?) state management frameworks end up solving similar problems, so if you understand Flux, you should be able to easily understand other frameworks as well.

Let's start with defining some essential state managed by a reducer. I've added some annotations for those who are unfamiliar with redux.

```ts
import { createSelector } from 'reselect';
import { createStore } from 'redux';

// Login action creator, this example won't dispatch any actions, but this helps show what the
// reducer will receive
function login(firstname, lastname) {
  return {
    type: 'LOGIN',
    firstName,
    lastName
  };
}

// Shortcut to make some initial essential state
const initialState = {
  user: {
    firstName: 'John',
    lastName: 'Smith',
    isLoggedIn: true
  }
}

// Just a monolithic reducer for everything to save space
// This will be called once with the INIT action below
function myReducer(state = initialState, action) {
  // Reducers work by switching on the action type and returning a new state object. It does not mutate state in place.
  // Mutating state in place will cause dirty-checking to fail and re-renders won't happen when needed.
  switch (action.type) {
    case 'LOGOUT':
      return {
        ...state,
        user: {
          ...state.user,
          isLoggedIn: false
        }
      };
    case 'LOGIN':
      return {
        ...state,
        user: {
          firstName: action.firstName,
          lastName: action.lastName
          isLoggedIn: true
        }
      };
    default:
      return state;
  }
}

// need the init action so the state gets populated with initialState constant
const store = createStore(myReducer, ['INIT']); 
```

The `initialState` is setup with the user already logged in for simplicity, but I've provided an action to show we could arrive at that state if starting from a logged-out position. The code is commented for those who are unfamiliar with the Redux pattern. Everything managed by `myReducer` is "essential" state, but let's see what happens if you decide to add a property called `fullName` to the state via this reducer.

```ts
// Shortcut to make some initial essential state
const initialState = {
  user: {
    // redacted
    fullName: 'John Smith'
  }
}

function myReducer(state = initialState, action) {
  switch (action.type) {
    // redacted
    case 'LOGIN':
      return {
        ...state,
        user: {
          firstName: action.firstName,
          lastName: action.lastName,
          fullName: action.firstName + ' ' + action.lastName,
          isLoggedIn: true
        }
      };
    case 'UPDATE_FIRSTNAME':
      return {
        ...state,
        user: {
          ...state.user,
          firstName: action.firstName,
          fullName: action.firstName + ' ' + state.user.lastName // More work here!
        }
      }
    default:
      return state;
  }
}
```

Notice in the `UPDATE_FIRSTNAME` and `LOGIN` actions, both have to do the same work to calculate the `fullName` property. This isn't too much of a problem in this simple case, but already you have the need to refactor the name into a function, and you have to reference the existing state to pull the `lastName`. Over time, as more complex state is added, the derived state will become more complex as well, and maintaining the reducers will become a challenge. Let's look at the alternative that uses [Reselect](https://github.com/reduxjs/reselect) instead.

```ts
// Shortcut to make some initial essential state
const initialState = {
  // redated
}

// An action that doesn't modify any user information.
// This is to help illustrate some reselect memoization behavior.
function someAction() {
  return {
    type: 'SOME_ACTION'
  };
}

function myReducer(state = initialState, action) {
  switch (action.type) {
    // redacted
    'SOME_ACTION': {
      return {
        ...state,
        some_flag: true
      }
    }
  }
}

const getFullNameAndTime = createSelector(
  state => state.user.firstName, // checked everytime an action is dispatched
  state => state.user.lastName, // checked everytime an action is dispatched
  (firstName, lastName) => // computed if the two inputs have changed
    firstName + ' ' + lastName + ', ' + new Date().toLocaleTimeString('en-us')
)

const FullNameDisplay = ({ fullName }) => {
  return (
    <div>
      {fullName}
    </div>
  );
};

// Not using react-redux library in order to better show how state is referenced in a component
let prev = '';
store.subscribe(() => {
  const next = getFullNameAndTime(store.getState());
  if (next !== prev) { // Render if the name has changed
    ReactDOM.render(<FullNameDisplay fullName=next />, document.getElementById('root'));
  }
});

store.dispatch(someAction());
```

Now in this case, we have the `getFullName` selector that will automatically recompute the `fullName` anytime either `firstName` or `lastName` is updated. Anytime an action that modifies either of the name properties, the selector will take care of the rest and we don't have to worry about updating that manually, which will be much safer.

## Reselect memoization

What really ties this pattern together is Reselect's memoization (most frameworks should have some form of memoization for derived state). It's very simple, only storing the previous values of the input functions and the result function. Each time an action is dispatched, like `someAction` in our example, the two input functions on `getFullNameAndTime` will be called. Also, this action doesn't result in firstName or lastName changing so the input functions will return values equivalent to the last. Since input values are equivalent, the selector will skip calling the result function and just return a reference to the previous value.

This memoization is what allows React to know when to re-render a relevant component, so even if our selector returned a complex object like `{ fullName }` instead, it'd still only re-render when necessary. This will be important if you end up creating a hierarchy of selectors, feeding complex objects from one into another. I'll show this in more detail in a later article. For now, let's just examine how the input functions work.

## Keep input functions simple

Reselect (and NgRx as well) selectors need to execute their input functions _every_ time they are called. In Redux and and similar frameworks, you can assume selectors are called every time an action is dispatched. This means they must be as simple as possible. There are likely very few cases (or maybe none at all) where it is necessary to do anything other than simply return a value out of the state. Additionally, selectors only do simple equality `===` checks, so do not make an input function that returns anything other than a primitive (string, number, etc). That means a new object (`state => { ... }`) or other complex type are out, as that will always fail an equality check and force a recompute.

# You will need to be flexible at times

In an ideal system, all essential state would be handled by simple reducers (or equivalent in non-flux frameworks) as shown above, while derived state is all managed in selectors. Unfortunately, like most software engineering patterns, there are always exceptions. Oftentimes, other criteria (such as performance) can take priority, and having derived state be automatically calculated may not provide the control needed to maintain the user-experience you require. Later on in the series, I'll provide more complex examples that show where it is easier, or required, to choose to manage derivable state in a reducer. For now, just know that you may encounter a situation where it will feel intuitive to stray from the pattern and that's fine as long as you understand the trade-offs.

# Conclusion

In this article we covered the differences between essential and derived state and how derived state should be modeled in a Redux environment. Derived state takes a bit of work to model well, so it's important to to be mindful of how exactly memoization is handled in your chosen framework. While all the provided examples were written using reselect, the concepts should apply to any framework, regardless of how much it handles for you automatically. In the next part of the series, we'll cover how to model side-effects, such as API calls, in your code.