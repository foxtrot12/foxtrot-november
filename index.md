# Frontend Notes


## JavaScript


### Event loop
- **Concept**: The event loop coordinates synchronous code (call stack) with async callbacks (queues) so async work can complete without blocking the main thread.
- **Visual**:
  ![Event Loop Diagram](https://media.geeksforgeeks.org/wp-content/uploads/20250208123836185275/Event-Loop-in-JavaScript.jpg)
- **Tiny example**
  ```js
  console.log("A");
  setTimeout(() => console.log("B"), 0);
  Promise.resolve().then(() => console.log("C"));
  console.log("D");
  // Typical output: A, D, C, B
  ```
  Microtasks (Promise `.then`) run before the next macrotask (like `setTimeout`).


### Promises
- Promises are a cleaner alternative to callbacks, help avoid callback hell, and make sequential/parallel async flows easier.
- **Tiny examples**
  ```js
  // Sequential
  fetch("/user")
    .then(r => r.json())
    .then(user => fetch(`/orders?userId=${user.id}`))
    .then(r => r.json())
    .then(console.log);
  ```


  ```js
  // Parallel
  const [a, b] = await Promise.all([fetch("/a"), fetch("/b")]);
  ```


### AJAX (Asynchronous JavaScript and XML)
- AJAX enables asynchronous communication between client and server.
- It’s commonly done via `XMLHttpRequest` or (more commonly in modern apps) `fetch`.
- **Tiny example (`fetch`)**
  ```js
  const res = await fetch("/api/todos");
  const data = await res.json();
  console.log(data);
  ```


### XMLHttpRequest vs fetch (quick compare)
- `XMLHttpRequest`: often callback-style; has `abort`; tends to be more verbose/complex.
- `fetch()`: Promise-based; usually cleaner request setup and composition.
- **Tiny example (XHR)**
  ```js
  const xhr = new XMLHttpRequest();
  xhr.open("GET", "/api/todos");
  xhr.onload = () => console.log(JSON.parse(xhr.responseText));
  xhr.send();
  ```


### AbortController (cancel fetch)
- `AbortController` can be used to cancel ongoing async operations like `fetch` requests.
- **Tiny example**
  ```js
  const controller = new AbortController();


  fetch("/api/search?q=react", { signal: controller.signal })
    .then(r => r.json())
    .then(console.log)
    .catch(err => {
      if (err.name === "AbortError") return; // request cancelled
      throw err;
    });


  // cancel it
  controller.abort();
  ```


### Polyfills
- Polyfills provide modern functionality to older browsers (by implementing missing features in JS).


### Don’t extend built-in JS objects (generally)
- Extending/overwriting built-in objects (like `Array.prototype`) can cause conflicts between libraries and unexpected behavior.


### Hoisting
- Declarations are “hoisted” to the top of their scope.
- **Tiny example**
  ```js
  console.log(x); // undefined
  var x = 10;


  hello(); // works
  function hello() { console.log("hi"); }
  ```


### var vs let vs const (quick rules)
- `var`: function/global scope; redeclare allowed; reassign allowed.
- `let`: block scope; redeclare not allowed; reassign allowed.
- `const`: block scope; redeclare not allowed; reassign not allowed.
- **Tiny example**
  ```js
  const user = { name: "A" };
  user.name = "B";  // OK (mutating object)
  // user = {}      // Not allowed (reassign)
  ```


### Higher-order functions (HOF)
- A HOF takes one or more functions as arguments (and/or returns a function).
- **Tiny example**
  ```js
  const nums = ; [developer.mozilla](https://developer.mozilla.org/en-US/docs/Web/API/AbortController/abort)
  const doubled = nums.map(n => n * 2);
  ```


### Anonymous functions + closure
- Anonymous functions are often used as arguments or assigned to variables; they can capture outer variables (closure).
- **Tiny example**
  ```js
  function makeCounter() {
    let count = 0;
    return function () { return ++count; };
  }
  const c = makeCounter();
  c(); // 1
  c(); // 2
  ```


### Event propagation: capturing vs bubbling
- Bubbling: element → root. Capturing: root → element.
- Phases: capturing → target → bubbling; capturing is off by default (enable via option).
- **Tiny example**
  ```js
  parent.addEventListener("click", () => console.log("bubble"));        // default
  parent.addEventListener("click", () => console.log("capture"), true); // capture
  ```


### Creating objects (common ways)
- `{}` (literal), `new Object()`, `Object.create(proto)`, constructor functions with `new`, ES2015 `class`.
- **Tiny examples**
  ```js
  const a = { x: 1 };


  const b = Object.create(a);
  b.y = 2;


  function Person(name) { this.name = name; }
  const p = new Person("Sam");


  class Car { constructor(model) { this.model = model; } }
  const c = new Car("Swift");
  ```


### bind() and this (incl. arrow functions)
- `bind()` returns a new function bound to a specific `this` (and can pre-fill args).
- Arrow functions don’t have their own `this`; they use lexical `this`.
- **Tiny examples**
  ```js
  const obj = { x: 10 };
  function f() { return this.x; }
  const bound = f.bind(obj);
  bound(); // 10
  ```


  ```js
  const obj2 = {
    x: 10,
    regular() { return this.x; },
    arrow: () => this.x
  };
  obj2.regular(); // 10
  obj2.arrow();   // typically undefined
  ```


### Prototypal inheritance
- Objects link to other objects via the prototype chain.
- **Tiny example**
  ```js
  const base = { greet() { return "hi"; } };
  const child = Object.create(base);
  child.greet(); // "hi"
  ```


### CommonJS vs ES Modules (CJS vs ESM)
- **CommonJS**: uses `require()` and `module.exports` and loads modules synchronously.
- **ES Modules**: uses `import`/`export` and is supported natively in modern browsers.
- **Tiny examples**
  ```js
  // CommonJS
  const fs = require("fs");
  module.exports = { x: 1 };
  ```


  ```js
  // ESM
  import thing from "./thing.js";
  export const x = 1;
  ```


### Map vs plain Object
- `Map`: any key type; insertion order maintained; has `.size`; iterates via `keys/values/entries/forEach`.
- `Object`: keys are string/symbol; order is not a good “guaranteed map” to rely on; no `.size` (use `Object.keys().length`).
- **Tiny example**
  ```js
  const m = new Map();
  m.set({ id: 1 }, "objKey");
  m.set(2, "numKey");
  console.log(m.size); // 2


  const obj = { a: 1, b: 2 };
  console.log(Object.keys(obj).length); // 2
  ```


### Map vs WeakMap / Set vs WeakSet
- Add detailed notes here (your page only lists the topics).


### Iterators
- An iterator implements `next()` which returns `{ value, done }`.
- **Tiny example**
  ```js
  const arr = ["x", "y"];
  const it = arr[Symbol.iterator]();


  console.log(it.next()); // { value: "x", done: false }
  console.log(it.next()); // { value: "y", done: false }
  console.log(it.next()); // { value: undefined, done: true }
  ```


### Generators
- Generators pause/resume and return an iterator; `yield` produces values.
- **Tiny example**
  ```js
  function* gen() {
    yield 1;
    yield 2;
    yield 3;
  }
  for (const n of gen()) console.log(n);
  ```


### Property descriptors (property flags)
- Properties have flags like `writable`, `enumerable`, and `configurable`.
- Read a descriptor with `Object.getOwnPropertyDescriptor()` and define/update with `Object.defineProperty()`.
- **Tiny example**
  ```js
  const obj = {};
  Object.defineProperty(obj, "id", {
    value: 123,
    writable: false,
    enumerable: true,
    configurable: false
  });


  console.log(Object.getOwnPropertyDescriptor(obj, "id"));
  // { value: 123, writable: false, enumerable: true, configurable: false }
  ```


### Garbage collection (high level)
- Mark-and-sweep: start from roots, mark reachable objects as “in use”, then sweep (remove) unmarked objects.
- Generational GC: modern engines group objects by age/usage and focus collection on younger generations (many objects die young).
- **Tiny example idea**
  ```js
  let a = { big: new Array(1e6).fill(0) };
  a = null; // becomes eligible for GC (no longer reachable)
  ```


### Cookies vs localStorage vs sessionStorage
- Cookies: sent to server with every request; small (~4KB).
- localStorage: persists until deleted; larger (~5MB); not sent to server.
- sessionStorage: cleared when tab closes; not sent to server.
- JS can’t access HttpOnly cookies.


---


## React


### Key prop (lists)
- `key` helps React identify list items to update only what changed.
- Avoid array index as `key` when list can reorder (bugs/unnecessary re-renders).
- **Tiny example**
  ```jsx
  {items.map(item => <Row key={item.id} item={item} />)}
  ```


### Reconciliation
- React updates the DOM to match the Virtual DOM (diffing and applying minimal changes).
- **Tiny example**
  ```jsx
  function Counter() {
    const [count, setCount] = useState(0);
    return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
  }
  ```


### Optimizing React Context performance
- Memoise context values; split contexts to reduce re-renders.
- **Tiny examples**
  ```jsx
  const value = useMemo(() => ({ user, setUser }), [user]);
  return <UserContext.Provider value={value}>{children}</UserContext.Provider>;
  ```


  ```jsx
  // Split contexts: UserStateContext + UserDispatchContext
  ```


### Hooks (rules)
- Don’t call hooks inside loops/conditions/nested functions.
- Call hooks only from React components or other hooks.
- **Tiny example**
  ```jsx
  useEffect(() => {
    if (!ready) return;
    // do work
  }, [ready]);
  ```


### useRef
- Mutable object that persists across renders; can point to DOM nodes for direct access.
- **Tiny example**
  ```jsx
  function FocusInput() {
    const inputRef = useRef(null);
    return (
      <>
        <input ref={inputRef} />
        <button onClick={() => inputRef.current?.focus()}>Focus</button>
      </>
    );
  }
  ```


### useCallback
- Memoises a function reference; useful when passing callbacks as props.
- **Tiny example**
  ```jsx
  const onClick = useCallback(() => setCount(c => c + 1), []);
  return <Child onClick={onClick} />;
  ```


### useMemo
- Memoises an expensive computed value.
- **Tiny example**
  ```jsx
  const filtered = useMemo(() => items.filter(x => x.active), [items]);
  ```


### useReducer
- Alternative to `useState` for complex state logic; next state depends on previous.
- Parts: state, action, reducer (pure function → next state).
- **Tiny example**
  ```jsx
  function reducer(state, action) {
    switch (action.type) {
      case "inc": return { count: state.count + 1 };
      case "dec": return { count: state.count - 1 };
      default: return state;
    }
  }


  function Counter() {
    const [state, dispatch] = useReducer(reducer, { count: 0 });
    return (
      <>
        <button onClick={() => dispatch({ type: "dec" })}>-</button>
        <span>{state.count}</span>
        <button onClick={() => dispatch({ type: "inc" })}>+</button>
      </>
    );
  }
  ```


### React Portal
- Renders children into a DOM node outside the parent component hierarchy.
- **Tiny example**
  ```jsx
  import { createPortal } from "react-dom";


  function Modal({ open, children }) {
    if (!open) return null;
    return createPortal(
      <div className="backdrop">{children}</div>,
      document.getElementById("modal-root")
    );
  }
  ```


### Higher-order component (HOC)
- A HOC takes a component and returns a new component with additional props/behavior.
- **Tiny example**
  ```jsx
  const withLogger = (Component) => (props) => {
    console.log("render", Component.name);
    return <Component {...props} />;
  };


  const LoggedButton = withLogger(Button);
  ```


### Flux pattern
- Flux enforces one-directional data flow (Dispatcher → Stores → View).  
- Benefits: predictable state, easier debugging/testing, separation of concerns.


### Presentational vs Container components
- Presentational components: focus on how things look (UI rendering).
- Container components: focus on how things work (logic/state/data).
- This separation helps keep code clean and organized.


### One-way data flow
- In React, data typically flows parent → child, which makes behavior predictable and improves maintainability.


### Fiber
- React Fiber is a rewrite of the reconciliation algorithm introduced in React 16.
- It breaks rendering work into smaller units and lets React pause/resume work.
- It enabled features like time slicing and made patterns like Suspense more feasible.


### Time slicing
- Prevents “jank” by breaking large rendering tasks into smaller chunks and yielding control back to the browser between chunks.
- React can prioritize urgent work (like user input / animations) over background work.


### Suspense
- A declarative way to handle async operations like code splitting by pausing rendering until something is ready (then showing fallback UI).
- **Tiny example**
  ```jsx
  const Page = React.lazy(() => import("./Page"));


  function App() {
    return (
      <React.Suspense fallback={<div>Loading…</div>}>
        <Page />
      </React.Suspense>
    );
  }
  ```


---


## HTML


### Loading JavaScript with script
- `<script>` blocks HTML parsing until it executes.
- `<script async>` downloads in parallel; executes ASAP (can interrupt parsing).
- `<script defer>` downloads in parallel; executes after HTML parsing.
- **Tiny example**
  ```html
  <script src="/legacy.js"></script>
  <script defer src="/app.js"></script>
  <script async src="/analytics.js"></script>
  ```


### Server-Sent Events (SSE) basics
- SSE sends automatic updates from server to client over a long-running HTTP connection using `EventSource`.
- It’s one-way (server → client) on the same connection; browsers can automatically reconnect.
- WebSockets are a persistent socket (upgraded from HTTP) and can support text/binary frames for two-way messaging.
- **Tiny example**
  ```js
  const es = new EventSource("/stream");
  es.onmessage = (e) => console.log("update:", e.data);
  ```


---


## CSS


### Selector specificity
- Specificity decides which rule wins when multiple match.
- **Tiny example**
  ```css
  #btn { color: red; }
  .toolbar button { color: blue; } /* loses to #btn */
  ```


### Positioning
- `static`, `relative`, `absolute`, `fixed`, `sticky`.
- **Tiny example**
  ```css
  .badge { position: absolute; top: 0; right: 0; }
  ```


### Box model
- Content/padding/border/margin; `box-sizing` changes width meaning.
- **Tiny example**
  ```css
  .a { box-sizing: content-box; width: 100px; padding: 10px; border: 5px solid; }
  .b { box-sizing: border-box;  width: 100px; padding: 10px; border: 5px solid; }
  ```


### Flexbox vs Grid
- Flexbox: 1D layouts; Grid: 2D layouts.
- **Tiny example**
  ```css
  .row { display: flex; gap: 12px; }
  .grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; }
  ```


### transform: translate
- Moves the element visually while keeping its original layout space; often avoids reflow and is efficient.
- **Tiny example**
  ```css
  .card:hover { transform: translateY(-4px); }
  ```
