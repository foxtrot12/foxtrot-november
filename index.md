# Frontend Notes

## JavaScript

### Event loop
- **Concept**: The event loop is a mechanism in the JS runtime that coordinates synchronous code (call stack) with async callbacks (queues) so async work can complete without blocking the main thread. 
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
  (Microtasks from Promises run before macrotasks like setTimeout callbacks.) 

### Promises
- Promises provide a cleaner alternative to callbacks and help avoid “callback hell”. 
- They make it easier to write sequential async flows and parallel async flows. 
- **Tiny examples**
  ```js
  // Sequential (do this, then that)
  fetch("/user")
    .then(r => r.json())
    .then(user => fetch(`/orders?userId=${user.id}`))
    .then(r => r.json())
    .then(orders => console.log(orders));
  ``` 

  ```js
  // Parallel (do both together)
  const [a, b] = await Promise.all([fetch("/a"), fetch("/b")]);
  ``` 

### Hoisting
- **Hoisting**: variable & function declarations are “hoisted” to the top of their scope. 
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
- `const`: block scope; redeclare not allowed; reassign not allowed (but object contents can still change). 
- **Tiny example**
  ```js
  const user = { name: "A" };
  user.name = "B";     // OK (mutating object)
  // user = {}         // Not allowed (reassign)
  ``` 

### Higher-order functions (HOF)
- A **higher-order function** takes one or more functions as arguments (and/or returns a function). 
- **Tiny example**
  ```js
  const nums =  [1,2,3];
  const doubled = nums.map(n => n * 2); // map takes a function
  ``` 

### Anonymous functions
- **Anonymous function**: a function without a name; commonly used as an argument to another function or assigned to a variable. 
- They can access variables from the outer scope (closure). 
- **Tiny example**
  ```js
  function makeCounter() {
    let count = 0;
    return function () { // anonymous
      count++;
      return count;
    };
  }
  const c = makeCounter();
  c(); // 1
  c(); // 2
  ``` 

### Event propagation: capturing vs bubbling
- **Bubbling**: event propagates from the element up to the root/document. 
- **Capturing**: event propagates from the root/document down to the element. 
- Event phases: capturing → target → bubbling. 
- Capturing is disabled by default; enable it via the `addEventListener` option. 
- **Tiny example**
  ```js
  parent.addEventListener("click", () => console.log("bubble"), false); // default
  parent.addEventListener("click", () => console.log("capture"), true); // capturing
  ``` 

### Creating objects (common ways)
- Object literal: `{}`. 
- `new Object()` (constructor). 
- `Object.create(proto)` (create from an existing object as prototype). 
- Constructor function (blueprint; use `new`).
- ES2015 classes (syntactic structure; uses `constructor`).
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

### `bind()` and `this` (incl. arrow functions)
- `Function.prototype.bind` returns a new function bound to a particular `this` (and can also pre-fill arguments).
- Arrow functions don’t have their own `this`; they use the `this` from where they are defined. 
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
  obj2.arrow();   // typically undefined (lexical this)
  ``` 

### Prototypal inheritance
- Every JS object has a hidden prototype reference that links it to another object (prototype chain). 
- **Tiny example**
  ```js
  const base = { greet() { return "hi"; } };
  const child = Object.create(base);

  child.greet(); // "hi" (found via prototype chain)
  ``` 

### Cookies vs localStorage vs sessionStorage (web storage)
- Cookies: client/server; as specified; sent to server with every request; ~4KB. 
- localStorage: client; until deleted; not sent to server; ~5MB; accessible across window/tab. 
- sessionStorage: client; until tab/window close; not sent to server; ~5MB; same tab. 
- Security note: JS cannot access HttpOnly cookies. 


## React

### Key prop (lists)
- `key` helps React uniquely identify elements to re-render only what changed. 
- Using array index as `key` can cause performance issues/bugs when item order changes (unnecessary re-renders, incorrect updates).
- **Tiny example**
  ```jsx
  // Prefer stable IDs
  {items.map(item => <Row key={item.id} item={item} />)}

  // Avoid index if list can reorder/insert/delete
  {items.map((item, i) => <Row key={i} item={item} />)}
  ``` 

### Reconciliation
- **Reconciliation** is the process where React updates the DOM to match the Virtual DOM.
- **Tiny example**
  ```jsx
  // When `count` changes, React reconciles and updates only the changed text node.
  function Counter() {
    const [count, setCount] = useState(0);
    return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
  }
  ``` 

### Optimizing React Context performance
- Goal: reduce unnecessary re-renders when using context. 
- Techniques noted: memoise the context value; split context. 
- **Tiny examples**
  ```jsx
  // Memoise context value
  const value = useMemo(() => ({ user, setUser }), [user]);
  return <UserContext.Provider value={value}>{children}</UserContext.Provider>;
  ``` 

  ```jsx
  // Split context (separate read-heavy vs write-heavy pieces)
  // e.g., UserContext + UserDispatchContext
  ```

### React Hooks (rules)
- Hooks cannot be called inside loops, conditions, or nested functions. 
- Hooks should be called only from React components or other hooks.
- These rules help keep state/lifecycle behavior correct. 
- **Tiny example**
  ```jsx
  // Bad: hook inside condition
  if (ready) {
    useEffect(() => {}, []);
  }

  // Good: hook at top level
  useEffect(() => {
    if (!ready) return;
    // ...
  }, [ready]);
  ``` 


## HTML

### Loading JavaScript with `<script>`
- Default `<script>`: HTML parsing is blocked until the script executes. 
- `<script async>`: downloads async; executes as soon as available. 
- `<script defer>`: downloads async; executes after HTML parsing completes. 
- **Tiny example**
  ```html
  <script src="/legacy.js"></script>
  <script defer src="/app.js"></script>
  <script async src="/analytics.js"></script>
  ``` 


## CSS

### Selector specificity
- Specificity decides which rule applies when multiple rules match. 
- Computed as a 3-part weight: ID, class/attribute/pseudo-class, type/pseudo-element. 
- Compare left-to-right; any non-zero on the left outweighs later columns. 
- **Tiny example**
  ```css
  /* (1,0,0) beats (0,1,1) */
  #btn { color: red; }
  .toolbar button { color: blue; }
  ``` 

### Positioning
- `static`: normal flow; offsets and `z-index` don’t apply. 
- `relative`: stays in flow; positioned relative to itself. 
- `absolute`: out of flow; positioned relative to closest positioned ancestor. 
- `fixed`: out of flow; relative to viewport; doesn’t move on scroll. 
- `sticky`: relative until threshold then fixed. 
- **Tiny example**
  ```css
  .badge {
    position: absolute;
    top: 0;
    right: 0;
  }
  ``` 

### Box model
- Space is calculated using content, padding, border, margin. 
- `content-box` vs `border-box` changes how width/height are interpreted. 
- **Tiny example**
  ```css
  .a { box-sizing: content-box; width: 100px; padding: 10px; border: 5px solid; }
  .b { box-sizing: border-box;  width: 100px; padding: 10px; border: 5px solid; }
  ``` 

### Flexbox vs Grid
- Flexbox is mainly for **1D** layout (row OR column). 
- Grid is mainly for **2D** layout (rows AND columns). 
- **Tiny example**
  ```css
  .row { display: flex; gap: 12px; }
  .grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; }
  ``` 

### `transform: translate(...)`
- `translate` changes the element’s visual position while it still occupies its original space in layout. 
- Changing translate doesn’t affect nearby elements’ layout, doesn’t trigger reflow, and triggers compositing (often via a GPU layer), so it’s usually more efficient than changing `top/left` (which can trigger layout/reflow). 
- **Tiny example**
  ```css
  .card:hover {
    transform: translateY(-4px);
  }
  ``` 
