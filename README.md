- [React Container–Presentation Pattern] (https://github.com/santhoshmp89/React-Design-Pattern-1/blob/main/README.md#react-containerpresentation-pattern)
- [Render Props Pattern] (https://github.com/santhoshmp89/React-Design-Pattern-1/blob/main/README.md#render-props-pattern)


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

---

# React Higher-Order Components (HOC) – Wiki

## 1. What is a Higher-Order Component (HOC)?

A **Higher-Order Component (HOC)** is an advanced React pattern for reusing component logic.

> **Definition**: An HOC is a function that takes a component and returns a new enhanced component.

```ts
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```

HOCs do **not modify** the original component. They **compose** behavior.

---

## 2. Why use HOCs?

HOCs are used to handle **cross-cutting concerns** that apply to many components:

* Authentication & authorization
* Feature flags
* Theming
* Error boundaries
* Analytics / tracking
* Data fetching (legacy pattern)
* Performance optimizations

---

## 3. Basic HOC Example

```tsx
const withLogger = (Component) => {
  return function WithLogger(props) {
    console.log('Component rendered');
    return <Component {...props} />;
  };
};
```

Usage:

```tsx
export default withLogger(MyComponent);
```

---

## 4. HOC with Configuration (Factory Pattern)

Most real-world HOCs accept configuration.

```tsx
const withPermission = (permission) => (Component) => {
  return function WithPermission(props) {
    if (!hasPermission(permission)) return null;
    return <Component {...props} />;
  };
};
```

Usage:

```tsx
export default withPermission('admin')(Dashboard);
```

---

## 5. Injecting Props via HOC

```tsx
const withTheme = (Component) => (props) => {
  const theme = useContext(ThemeContext);
  return <Component {...props} theme={theme} />;
};
```

The wrapped component automatically receives the injected props.

---

## 6. HOC with TypeScript (Safe Typing)

```ts
type InjectedProps = {
  theme: string;
};

const withTheme = <P extends InjectedProps>(
  Component: React.ComponentType<P>
) => {
  return (props: Omit<P, keyof InjectedProps>) => {
    const theme = useContext(ThemeContext);
    return <Component {...(props as P)} theme={theme} />;
  };
};
```

Benefits:

* Prevents prop collision
* Strong type inference

---

## 7. Ref Forwarding in HOCs

HOCs block refs unless explicitly forwarded.

```tsx
const withFocus = (Component) =>
  React.forwardRef((props, ref) => {
    return <Component {...props} ref={ref} />;
  });
```

---

## 8. Render Hijacking

HOCs can control rendering completely.

```tsx
const withErrorBoundary = (Component) =>
  class ErrorBoundary extends React.Component {
    state = { hasError: false };

    static getDerivedStateFromError() {
      return { hasError: true };
    }

    render() {
      if (this.state.hasError) return <Fallback />;
      return <Component {...this.props} />;
    }
  };
```

---

## 9. Composing Multiple HOCs

Avoid nesting by composing:

```ts
const enhance = compose(
  withRouter,
  withAuth,
  withTheme
);

export default enhance(MyComponent);
```

---

## 10. Performance Optimization with HOC

```tsx
const withMemo = (Component) => React.memo(Component);
```

Or custom comparison:

```tsx
React.memo(Component, (prev, next) => prev.id === next.id);
```

---

## 11. Common Mistakes ❌

### ❌ Creating HOC inside render

```tsx
function App() {
  const Enhanced = withTheme(MyComponent); // ❌
  return <Enhanced />;
}
```

### ✅ Correct usage

```tsx
const Enhanced = withTheme(MyComponent);
```

---

## 12. HOC vs Hooks

| Use Case         | Prefer |
| ---------------- | ------ |
| Logic reuse      | Hooks  |
| Render control   | HOC    |
| Class components | HOC    |
| Prop injection   | HOC    |

---

## 13. When to Use HOCs in Modern React

HOCs are still relevant for:

* Auth / permission layers
* Error boundaries
* Feature flags
* Analytics wrappers
* Library-level abstractions

Hooks are preferred for most local logic reuse.

---

## 14. Best Practices ✅

* Use curried HOCs
* Forward refs when needed
* Avoid prop collisions
* Compose HOCs
* Prefer hooks for simple logic

---

## 15. Summary

* HOCs enhance components
* They enable reuse of cross-cutting logic
* Powerful but should be used judiciously
* Still essential in many production use cases

---

