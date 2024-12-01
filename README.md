<!-- @format -->

# Redux Intro

This repository showcases advanced React concepts, with a primary focus on Redux, as I learn and explore them under the guidance of my favorite teacher, Jones Schmedtamm.

- [Redux Intro](#redux-intro)
  - [Key Concepts](#key-concepts)
    - [1. Redux Store](#1-redux-store)
    - [2. Reducer](#2-reducer)
    - [3. Actions](#3-actions)
  - [Using Redux in React Components](#using-redux-in-react-components)
    - [Connecting Redux to React](#connecting-redux-to-react)
      - [Example: Account Operations Component](#example-account-operations-component)
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
