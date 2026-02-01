# Frontend Notes

## JavaScript

### Event Loop
- **Concept**: The event loop is a mechanism in the JS runtime that handles asynchronous operations. It constantly checks if the **Call Stack** is empty; if so, it pushes tasks from the **Callback Queue** (or Microtask Queue) to the Stack for execution. 
- **Visual**:
  ![Event Loop Diagram](https://media.geeksforgeeks.org/wp-content/uploads/20250208123836185275/Event-Loop-in-JavaScript.jpg)

### Hoisting
- **Definition**: Variable and function declarations are moved ("hoisted") to the top of their scope during the compilation phase.
- **Example**:
  ```javascript
  console.log(x); // undefined (var is hoisted but not initialized)
  var x = 5;

  sayHello(); // "Hello" (function declarations are fully hoisted)
  function sayHello() { console.log("Hello"); }
  ```

### var vs let vs const
| Feature | var | let | const |
| :--- | :--- | :--- | :--- |
| **Scope** | Function / Global | Block `{}` | Block `{}` |
| **Reassign?** | Yes | Yes | No |
| **Redeclare?**| Yes | No | No |
| **Hoisting** | Initialized as `undefined` | Hoisted but "TDZ" (ReferenceError) | Hoisted but "TDZ" (ReferenceError) |

- **Example**:
  ```javascript
  if (true) {
    var a = 1; // accessible outside block
    let b = 2; // ReferenceError outside block
  }
  ```

### Web Storage (Cookies vs Local vs Session)
- **Cookies**: 4KB, sent with every HTTP request. Used for auth tokens.
- **LocalStorage**: ~5MB, persists until explicitly deleted.
- **SessionStorage**: ~5MB, persists only until the tab is closed.

---

## React

### Key Prop
- **Use**: Helps React identify which items have changed, added, or removed.
- **Benefit**: Rendering efficiency (recycles DOM nodes instead of destroying/recreating).
- **Example**:
  ```jsx
  // Good
  {items.map(item => <li key={item.id}>{item.name}</li>)}
  
  // Bad (using index can cause bugs on reorder)
  {items.map((item, index) => <li key={index}>{item.name}</li>)}
  ```

### Virtual DOM
- **Process**:
  1. **Render**: Create a new Virtual DOM tree.
  2. **Diff**: Compare new VDOM with the previous one.
  3. **Commit**: Update only the changed nodes in the real DOM.

### React Entities
- **Node**: Anything renderable (`<div />`, `"text"`, `null`).
- **Element**: Object description of a node (`{ type: 'div', props: ... }`).
- **Component**: Function that returns Elements (`const App = () => <div />`).

---

## HTML

### Script Loading
- **`<script src="...">`**: Pauses HTML parsing, fetches script, executes it, then resumes parsing.
- **`<script async src="...">`**: Fetches in parallel; executes **immediately** when ready (pauses HTML parsing during execution).
- **`<script defer src="...">`**: Fetches in parallel; executes **only after** HTML parsing is complete.

---

## CSS

### Specificity
- **Formula**: `(Inline Styles, ID, Class/Attribute, Tag)`
- **Comparison**: Compare left-to-right.
- **Example**:
  - `#nav` = `(1, 0, 0)` -> **Winner**
  - `.nav-item` = `(0, 1, 0)`
  - `div p` = `(0, 0, 2)`

### Positioning
- **`static`**: Default.
- **`relative`**: Offset relative to its normal position. `top: 10px` moves it down 10px from where it *would* have been.
- **`absolute`**: Removed from flow. Positioned relative to nearest *non-static* ancestor.
- **`fixed`**: Relative to viewport (stays on screen during scroll).
- **`sticky`**: Toggles between relative and fixed based on scroll position.

### Box Model
- **`content-box`**: `width` = content only. Border/padding add to element size.
  - *Ex*: `width: 100px` + `padding: 20px` = **140px** visible width.
- **`border-box`**: `width` = content + padding + border.
  - *Ex*: `width: 100px` + `padding: 20px` = **100px** visible width (content shrinks to 60px).
