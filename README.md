# React Container–Presentation Pattern

## Overview

The **Container–Presentation Pattern** (also known as the Smart–Dumb Component pattern) is a design approach in React used to clearly separate **UI rendering** from **business logic and data handling**. This results in cleaner, more testable, and more maintainable code.

---

## Key Concepts

### **1. Presentational Components (UI/Dumb Components)**

* Concerned only with **how things look**.
* Receive all data and callback functions through **props**.
* Do **not** manage state (except local UI state).
* Do **not** fetch data.
* Usually written as functional components.

**Characteristics:**

* Pure and predictable
* Reusable across multiple containers
* Easy to unit test

---

### **2. Container Components (Smart Components)**

* Concerned with **how things work**.
* Handle **state, data fetching, and business logic**.
* Pass data and event handlers to presentational components.
* Usually use React hooks (useState, useEffect).

**Characteristics:**

* Manage all side effects
* Connect to APIs or data sources
* Orchestrate UI components

---

## Example Structure

### Presentational Component

```jsx
// UserList.js
const UserList = ({ users, onSelectUser }) => {
  return (
    <div>
      <h2>User List</h2>
      {users.map(user => (
        <p key={user.id} onClick={() => onSelectUser(user)}>
          {user.name}
        </p>
      ))}
    </div>
  );
};

export default UserList;
```

### Container Component

```jsx
// UserListContainer.js
import { useState, useEffect } from "react";
import UserList from "./UserList";

const UserListContainer = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetchUsers();
  }, []);

  const fetchUsers = async () => {
    const res = await fetch('/api/users');
    const data = await res.json();
    setUsers(data);
  };

  const handleSelectUser = (user) => {
    console.log('Selected:', user);
  };

  return (
    <UserList users={users} onSelectUser={handleSelectUser} />
  );
};

export default UserListContainer;
```

---

## Modern Approach: Using Custom Hooks

As React evolved, the logic extraction pattern shifted toward **Custom Hooks**, which replace traditional container components.

### Custom Hook (Logic)

```jsx
// useUsers.js
import { useState, useEffect } from "react";

export const useUsers = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch('/api/users')
      .then(res => res.json())
      .then(data => setUsers(data));
  }, []);

  return users;
};
```

### UI Component Using Hook

```jsx
// UserList.js
import { useUsers } from './useUsers';

const UserList = () => {
  const users = useUsers();

  return (
    <div>
      <h2>Users</h2>
      {users.map(u => (
        <div key={u.id}>{u.name}</div>
      ))}
    </div>
  );
};

export default UserList;
```

---

## When to Use This Pattern

Use Container–Presentation separation when:

* You need **clear separation of concerns**.
* UI components need to be **reusable**.
* You want **testable** logic and UI.
* Working in larger apps or teams.

Modern React still benefits from this pattern, though container logic is often moved into **custom hooks**.

---

## Suggested Folder Structure

```
/components
  /user
    UserList.js            # Presentational
    UserCard.js            # Presentational
    UserListContainer.js   # Container (optional)
/hooks
  useUsers.js              # Custom hook for data logic
```

---

## Summary

The Container–Presentation pattern helps:

* Improve maintainability
* Increase reusability
* Separate UI from logic
* Simplify testing

Although modern React encourages hooks and server components, this pattern remains valuable for structuring complex UI applications.

---

# Render Props Pattern

Sandbox link: https://codesandbox.io/p/devbox/agitated-tereshkova-m953kd?file=%2Fsrc%2Findex.js%3A9%2C11&workspaceId=ws_HNgpUAEXJVy67snonvHVmS

## Overview

The **Render Props** pattern is a React design technique where a component receives a **function as a prop**. This function is responsible for determining **what UI gets rendered**, giving the consumer complete control over presentation.

Render props are mainly used for sharing reusable logic between components while keeping the UI flexible.

---

## What Is a Render Prop?

A **render prop** is simply a prop whose value is a function. The component calls this function and passes it the data or state, and the function returns the UI elements.

**In short:** Logic lives in the component, UI comes from the function passed as a prop.

---

## Basic Example

### Without Render Props (tightly coupled)

```jsx
function MouseTracker() {
  const [pos, setPos] = useState({ x: 0, y: 0 });

  return (
    <div
      onMouseMove={(e) => setPos({ x: e.clientX, y: e.clientY })}
      style={{ height: "300px", border: "1px solid black" }}
    >
      X: {pos.x}, Y: {pos.y}
    </div>
  );
}
```

This works but does **not** allow UI customization.

---

## Render Props Version

```jsx
function Mouse({ children }) {
  const [pos, setPos] = useState({ x: 0, y: 0 });

  return (
    <div
      onMouseMove={(e) => setPos({ x: e.clientX, y: e.clientY })}
      style={{ height: "300px", border: "1px solid black" }}
    >
      {children(pos)}
    </div>
  );
}
```

### Usage

```jsx
<Mouse>
  {(pos) => (
    <h2>
      Mouse at {pos.x}, {pos.y}
    </h2>
  )}
</Mouse>
```

The parent component decides the UI based on shared mouse-tracking logic.

---

## Why Use Render Props?

* Share logic without duplicating code
* UI stays flexible and customizable
* No inheritance needed
* Good before hooks existed

---

## Downsides

* Can lead to nested function structures (wrapper hell)
* More verbose than modern alternatives
* Harder for beginners to read

---

## Modern Alternative: Custom Hooks

Render props are mostly replaced by **custom hooks** because they are cleaner and easier to use.

### Hook Example

```jsx
function useMouse() {
  const [pos, setPos] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handler = (e) => setPos({ x: e.clientX, y: e.clientY });
    window.addEventListener("mousemove", handler);
    return () => window.removeEventListener("mousemove", handler);
  }, []);

  return pos;
}
```

### Usage

```jsx
const pos = useMouse();
```

This approach is more idiomatic in modern React.

---

## Summary

* Render props allow you to pass logic but let the consumer decide how to render UI.
* Useful for reusable logic-heavy components.
* Custom hooks are now the preferred alternative.
