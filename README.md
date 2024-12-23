<!-- @format -->

# 🏦 The React-Redux Bank ⚛️

This repository showcases advanced React concepts, with a primary focus on Redux, as I learn and explore them under the guidance of my favorite teacher, Jones Schmedtamm.

- [🏦 The React-Redux Bank ⚛️](#-the-react-redux-bank-️)
  - [Key Concepts](#key-concepts)
    - [1. Redux Store](#1-redux-store)
    - [2. Reducer](#2-reducer)
    - [3. Actions](#3-actions)
  - [Using Redux in React Components](#using-redux-in-react-components)
    - [Connecting Redux to React](#connecting-redux-to-react)
      - [Example: Account Operations Component](#example-account-operations-component)
  - [Redux Middleware and Thunks](#redux-middleware-and-thunks)
  - [Redux Toolkit](#redux-toolkit)
    - [Creating The Store With RTK](#creating-the-store-with-rtk)
    - [Creating Account Slice](#creating-account-slice)
  - [Legacy Usage: Connecting Redux with React Using `conect`](#legacy-usage-connecting-redux-with-react-using-conect)
      - [Example: Legacy Balance Display Component](#example-legacy-balance-display-component)

## Key Concepts

### 1. Redux Store

The store is the centralized state container.

```jsx
import { combineReducers, createStore } from "redux";
import accountReducer from "./features/account/accountSlice";

const rootReducer = combineReducers({
  account: accountReducer,
});

const store = createStore(rootReducer);

export default store;
```

### 2. Reducer

Reducers handle state changes in response to actions.

```jsx
const initialStateAccount = {
  balance: 0,
};

export default function accountReducer(state = initialStateAccount, action) {
  switch (action.type) {
    case "account/deposit":
      return { ...state, balance: state.balance + action.payload };
    case "account/withdraw":
      return { ...state, balance: state.balance - action.payload };
    default:
      return state;
  }
}
```

### 3. Actions

Actions describe state changes and are dispatched to the store.

```jsx
export function deposit(amount) {
  return { type: "account/deposit", payload: amount };
}

export function withdraw(amount) {
  return { type: "account/withdraw", payload: amount };
}
```

## Using Redux in React Components

### Connecting Redux to React

Use the `useSelector` and `useDispatch` hooks from react-redux to interact with the Redux store in React components.

#### Example: Account Operations Component

```jsx
import React, { useState } from "react";
import { useDispatch, useSelector } from "react-redux";
import { deposit, withdraw } from "./accountSlice";

function AccountOperations() {
  const [amount, setAmount] = useState("");
  const dispatch = useDispatch();
  const balance = useSelector((state) => state.account.balance);

  function handleDeposit() {
    if (amount) dispatch(deposit(Number(amount)));
    setAmount("");
  }

  function handleWithdraw() {
    if (amount) dispatch(withdraw(Number(amount)));
    setAmount("");
  }

  return (
    <div>
      <h2>Balance: ${balance}</h2>
      <input
        type="number"
        value={amount}
        onChange={(e) => setAmount(e.target.value)}
        placeholder="Enter amount"
      />
      <button onClick={handleDeposit}>Deposit</button>
      <button onClick={handleWithdraw}>Withdraw</button>
    </div>
  );
}

export default AccountOperations;
```

## Redux Middleware and Thunks

**Middleware** is a function that sits between dispatching an action and reaching the reducer in the store. It allows us to run code _after_ an action is dispatched, but _before_ it reaches the reducer.

Middleware is perfect for handling:

- Asynchronous code (like API calls)
- Side effects (timers, logging, etc.)
- Tasks that need to be run outside of the reducer

**Thunk** is the most popular middleware library for Redux. It allows Redux to handle asynchronous actions, such as waiting for an API response, before dispatching the data to the store. This enables us to defer dispatching actions to the future, often after receiving data from an external source.

- insted of returning an action object from the action creator function we return a new function

```jsx
export function deposit(amount, currency) {
  if (currency === "USD") return { type: "account/deposit", payload: amount };

  return async function (dispatch, getState) {
    dispatch({ type: "account/convertingCurrency" });

    const res = await fetch(
      `https://api.frankfurter.app/latest?amount=${amount}&from=${currency}&to=USD`
    );
    const data = await res.json();
    const converted = data.rates.USD;

    dispatch({ type: "account/deposit", payload: converted });
  };
}
```

## Redux Toolkit

- The modern and prefered way of writing Redux code
- An opinionated approach, forcing us to use Redux best practices
- Built on top of clasic Redux
- Less `boilerplate` - allow us to write a lot less code to achive the same result

1. We can write code that "mutates" state inside reducers
2. Action creators are authomatically created
3. Automatic setup of thunk middleware and DevTools

### Creating The Store With RTK

```jsx
import { configureStore } from "@reduxjs/toolkit";

import accountReducer from "./features/account/accountSlice";
import customerReducer from "./features/customers/customerSlice";

const store = configureStore({
  reducer: {
    account: accountReducer,
    customer: customerReducer,
  },
});

export default store;
```

### Creating Account Slice

```jsx
import { createSlice } from "@reduxjs/toolkit";

const initialState = {
  balance: 0,
  loan: 0,
};

const accountSlice = createSlice({
  name: "account",
  initialState,
  reducers: {
    deposit(state, action) {
      state.balance += action.payload;
    },
    withdraw(state, action) {
      state.balance -= action.payload;
    },
  },
});

export const { deposit, withdraw } = accountSlice.actions;

export default accountSlice.reducer;
```

## Legacy Usage: Connecting Redux with React Using `conect`

Before the introduction of hooks like useSelector and useDispatch, Redux used the connect function to integrate with React components.

#### Example: Legacy Balance Display Component

```jsx
import { connect } from "react-redux";

function BalanceDisplay({ balance }) {
  return <div className="balance">{balance}</div>;
}

function mapStateToProps(state) {
  return {
    balance: state.account.balance,
  };
}

export default connect(mapStateToProps)(BalanceDisplay);
```
