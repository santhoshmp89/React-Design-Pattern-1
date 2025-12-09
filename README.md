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


### Sandbox link: https://codesandbox.io/p/devbox/agitated-tereshkova-m953kd?file=%2Fsrc%2Findex.js%3A9%2C11&workspaceId=ws_HNgpUAEXJVy67snonvHVmS

