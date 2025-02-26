# JavaScript Interview Questions and Answers

## JavaScript Basics

### 1. How does JavaScript interact with the browser and server?
**Answer:** JavaScript interacts with browsers through the DOM (Document Object Model) API, allowing manipulation of HTML elements, handling events, and controlling browser behavior. For server interaction, JavaScript uses APIs like Fetch or XMLHttpRequest to make HTTP requests. With Node.js, JavaScript can also run on the server-side, handling HTTP requests, file operations, and database interactions directly.

### 2. What is a callback function? Why are callback functions used in asynchronous JavaScript?
**Answer:** A callback function is a function passed as an argument to another function, which is then invoked inside the outer function. Callbacks are essential for asynchronous operations because they allow you to specify what happens after an asynchronous task completes, without blocking execution.

```javascript
// Example: Callback with setTimeout
function fetchData(callback) {
  setTimeout(() => {
    const data = { name: "John", age: 30 };
    callback(data);
  }, 1000);
}

fetchData((data) => {
  console.log("Data received:", data);
});
console.log("This runs before the callback");
```

### 3. What is a Closure in JavaScript?
**Answer:** A closure is a function that has access to its own scope, the outer function's scope, and the global scope, even after the outer function has finished executing. Closures "remember" the environment in which they were created.

```javascript
function createCounter() {
  let count = 0;  // Private variable
  
  return function() {
    count++;      // Access to parent scope
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
```

Closures are useful for:
- Data encapsulation/private variables
- Function factories
- Event handlers and callbacks
- Implementing module patterns

### 4. What does `this` keyword indicate in JavaScript? How does it behave differently in arrow functions vs regular functions?
**Answer:** `this` refers to the execution context of a function:

- In regular functions, `this` is dynamically determined by how the function is called:
  - In a method, `this` refers to the owner object
  - In a standalone function, `this` refers to the global object (or undefined in strict mode)
  - In an event, `this` refers to the element that received the event
  - With `call()`, `apply()`, or `bind()`, `this` is explicitly set

- In arrow functions, `this` is lexically scoped - it retains the `this` value of the enclosing context and cannot be changed, even with `call()`, `apply()`, or `bind()`.

```javascript
// Regular function
const obj = {
  name: "Object",
  regularMethod: function() {
    console.log(this.name); // "Object"
    
    function innerFunction() {
      console.log(this.name); // undefined (or window.name in non-strict mode)
    }
    innerFunction();
  },
  
  arrowMethod: function() {
    console.log(this.name); // "Object"
    
    const innerArrow = () => {
      console.log(this.name); // "Object" - inherits this from parent scope
    }
    innerArrow();
  }
};
```

### 5. What is Event Bubbling in JavaScript?
**Answer:** Event bubbling is a mechanism where an event triggered on a nested element "bubbles up" through its ancestors in the DOM tree. When an event occurs on an element, it first runs the handlers on that element, then on its parent, then on other ancestors.

```javascript
// Example:
document.getElementById("child").addEventListener("click", function(e) {
  console.log("Child clicked");
  // e.stopPropagation(); // Prevents bubbling
});

document.getElementById("parent").addEventListener("click", function() {
  console.log("Parent clicked");
});

document.getElementById("grandparent").addEventListener("click", function() {
  console.log("Grandparent clicked");
});

// Clicking on the child element logs:
// "Child clicked"
// "Parent clicked"
// "Grandparent clicked"
```

This enables event delegation, where you can attach a single event listener to a parent element to handle events for multiple child elements.

### 6. What is an Event Loop? How does it handle asynchronous operations?
**Answer:** The Event Loop is a fundamental mechanism in JavaScript that handles asynchronous operations by continuously checking the message queue for new events/messages and executing associated callbacks.

**How it works:**
1. Synchronous code executes on the call stack
2. Asynchronous operations (setTimeout, promises, I/O, etc.) are delegated to the browser/Node.js APIs
3. When these operations complete, their callbacks are added to the callback/task queue
4. The event loop constantly checks if the call stack is empty
5. If the stack is empty, it takes the first callback from the queue and pushes it onto the stack for execution

The event loop handles different types of tasks in specific order:
- Microtasks (Promises, queueMicrotask) are processed after the current task and before the next task
- Macrotasks (setTimeout, setInterval, I/O) are processed one per event loop iteration

```javascript
console.log("Start");

setTimeout(() => {
  console.log("Timeout callback");
}, 0);

Promise.resolve().then(() => {
  console.log("Promise resolved");
});

console.log("End");

// Output:
// "Start"
// "End"
// "Promise resolved" (microtask runs before next macrotask)
// "Timeout callback"
```

### 7. Explain hoisting in JavaScript
**Answer:** Hoisting is JavaScript's default behavior of moving declarations to the top of their scope during the compilation phase, before code execution.

**For variables:**
- `var` declarations are hoisted and initialized with `undefined`
- `let` and `const` declarations are hoisted but not initialized (Temporal Dead Zone)

**For functions:**
- Function declarations are completely hoisted (both declaration and definition)
- Function expressions are not hoisted (if assigned to variables, follow variable hoisting rules)

```javascript
// Variable hoisting
console.log(x); // undefined (hoisted but not initialized)
var x = 5;

console.log(y); // ReferenceError: y is not defined (in TDZ)
let y = 10;

// Function hoisting
sayHello(); // Works: "Hello" (function declaration is fully hoisted)
function sayHello() {
  console.log("Hello");
}

sayBye(); // TypeError: sayBye is not a function
var sayBye = function() {
  console.log("Bye");
};
```

### 8. What is the difference between undefined and null?
**Answer:** 
- `undefined` represents a variable that has been declared but not assigned a value, or a function that doesn't return anything, or attempting to access an object property that doesn't exist
- `null` is an explicit assignment value representing "no value" or "empty" - it must be explicitly assigned

Key differences:
- `typeof undefined` is `"undefined"`
- `typeof null` is `"object"` (a historical bug in JavaScript)
- `undefined == null` is `true` (loose equality)
- `undefined === null` is `false` (strict equality)
- Functions return `undefined` by default, not `null`

```javascript
let a;
console.log(a); // undefined

let b = null;
console.log(b); // null

console.log(typeof undefined); // "undefined"
console.log(typeof null);      // "object"

console.log(undefined == null);  // true
console.log(undefined === null); // false
```

### 9. What are the different data types in JavaScript?
**Answer:** JavaScript data types can be divided into primitive and reference types:

**Primitive types:** (immutable, stored by value)
- `String`: Text data - `"Hello"`, `'world'`, `` `template` ``
- `Number`: Integer and floating-point numbers - `42`, `3.14`
- `Boolean`: `true` or `false`
- `undefined`: Represents a declared but uninitialized variable
- `null`: Represents the intentional absence of any value
- `Symbol`: Unique and immutable primitive introduced in ES6
- `BigInt`: For integers larger than Number can represent (ES2020)

**Reference types:** (mutable, stored by reference)
- `Object`: Key-value pairs - `{}`, `new Object()`
- `Array`: Ordered collections - `[]`, `new Array()`
- `Function`: Callable objects - `function() {}`, `() => {}`
- `Date`: Date and time representation - `new Date()`
- `RegExp`: Regular expressions - `/pattern/`, `new RegExp()`
- `Map`, `Set`, `WeakMap`, `WeakSet` (ES6 collections)

**Behavior differences:**
- Primitives are compared by value
- Reference types are compared by reference (memory address)

```javascript
// Primitives - compared by value
let a = 5;
let b = 5;
console.log(a === b); // true

// Reference types - compared by reference
let obj1 = { value: 5 };
let obj2 = { value: 5 };
console.log(obj1 === obj2); // false (different objects in memory)

let arr1 = [1, 2, 3];
let arr2 = arr1;
arr2.push(4);
console.log(arr1); // [1, 2, 3, 4] (both variables reference the same array)
```

### 10. What is the scope? And what are block scope, global scope, and function scope?
**Answer:** Scope determines the accessibility of variables, functions, and objects during runtime.

**Global Scope:**
- Variables declared outside any function or block
- Accessible throughout the entire program
- Variables declared without `var`, `let`, or `const` become global

**Function Scope:**
- Variables declared inside a function using `var`
- Accessible only within the function
- Each function creates its own scope

**Block Scope:**
- Variables declared inside a block `{}` using `let` or `const`
- Accessible only within the block
- Introduced in ES6

```javascript
// Global scope
var globalVar = "I'm global";
let globalLet = "I'm also global";

function exampleFunction() {
  // Function scope
  var functionVar = "I'm function-scoped";
  let functionLet = "I'm block-scoped";
  
  if (true) {
    // Block scope
    var blockVar = "I'm still function-scoped (var)";
    let blockLet = "I'm block-scoped";
    const blockConst = "I'm also block-scoped";
    
    console.log(globalVar);     // Accessible
    console.log(functionVar);   // Accessible
    console.log(blockLet);      // Accessible
  }
  
  console.log(blockVar);        // Accessible (var ignores blocks)
  console.log(blockLet);        // ReferenceError (let respects blocks)
}

console.log(functionVar);       // ReferenceError (function scope)
console.log(blockVar);          // ReferenceError (function scope)
```

### 11. Arrays in JavaScript
**Answer:** An array is an ordered, indexed collection of values in JavaScript. It can store elements of any type and dynamically resize.

**Key array methods and differences:**

**map vs forEach:**
- `map()` creates a new array by transforming each element and returns it
- `forEach()` just iterates over elements without returning anything

```javascript
const numbers = [1, 2, 3];

// map returns a new array
const doubled = numbers.map(num => num * 2);
console.log(doubled); // [2, 4, 6]

// forEach doesn't return anything
numbers.forEach(num => console.log(num)); // logs 1, 2, 3
```

**find vs filter:**
- `find()` returns the first element that satisfies the condition
- `filter()` returns all elements that satisfy the condition in a new array

```javascript
const people = [
  { name: "John", age: 25 },
  { name: "Jane", age: 30 },
  { name: "Jack", age: 30 }
];

// find returns the first match
const firstThirty = people.find(person => person.age === 30);
console.log(firstThirty); // { name: "Jane", age: 30 }

// filter returns all matches
const allThirty = people.filter(person => person.age === 30);
console.log(allThirty); // [{ name: "Jane", age: 30 }, { name: "Jack", age: 30 }]
```

**slice vs splice:**
- `slice()` returns a new array with selected elements without modifying the original
- `splice()` modifies the original array by adding/removing elements

```javascript
const array = [1, 2, 3, 4, 5];

// slice doesn't modify the original array
const sliced = array.slice(1, 3);
console.log(sliced);      // [2, 3]
console.log(array);       // [1, 2, 3, 4, 5] (unchanged)

// splice modifies the original array
const spliced = array.splice(1, 2, 'a', 'b');
console.log(spliced);     // [2, 3] (removed elements)
console.log(array);       // [1, 'a', 'b', 4, 5] (modified)
```

**shift vs unshift:**
- `shift()` removes the first element and returns it
- `unshift()` adds elements to the beginning and returns the new length

```javascript
const array = [2, 3, 4];

// shift removes from the beginning
const first = array.shift();
console.log(first);       // 2
console.log(array);       // [3, 4]

// unshift adds to the beginning
const newLength = array.unshift(1, 2);
console.log(newLength);   // 4
console.log(array);       // [1, 2, 3, 4]
```

## ES6 Features

### 1. Tell me about ES6
**Answer:** ES6 (ECMAScript 2015) was a major update to JavaScript that introduced significant improvements and new features:

**Arrow Functions:**
- Shorter syntax for function expressions
- Lexical `this` binding (inherits `this` from surrounding scope)
```javascript
// Traditional function
function add(a, b) {
  return a + b;
}

// Arrow function
const add = (a, b) => a + b;
```

**Classes:**
- Syntactic sugar over JavaScript's prototype-based inheritance
- Familiar OOP style with constructors, methods, inheritance
```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
  
  sayHello() {
    return `Hello, I'm ${this.name}`;
  }
}

class Employee extends Person {
  constructor(name, position) {
    super(name);
    this.position = position;
  }
}
```

**Modules:**
- Native support for modular code organization
- Import/export syntax
```javascript
// math.js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

// main.js
import { add, subtract } from './math.js';
import * as math from './math.js';
```

**Other key ES6 features:**
- `let` and `const` for block-scoped variables
- Template literals with string interpolation
- Default parameters
- Rest and spread operators
- Destructuring assignment
- Enhanced object literals
- Promises for async programming
- Map and Set collections
- Symbols
- Iterators and generators

### 2. What are the differences between var, let, and const?
**Answer:**

**Scope:**
- `var`: Function-scoped or globally-scoped
- `let` and `const`: Block-scoped

**Hoisting:**
- `var`: Hoisted and initialized with `undefined`
- `let` and `const`: Hoisted but not initialized (Temporal Dead Zone)

**Reassignment:**
- `var` and `let`: Can be reassigned
- `const`: Cannot be reassigned after initialization

**Redeclaration:**
- `var`: Can be redeclared in the same scope
- `let` and `const`: Cannot be redeclared in the same scope

**Initialization:**
- `var` and `let`: Can be declared without initialization
- `const`: Must be initialized during declaration

```javascript
// Scope
function example() {
  if (true) {
    var varVariable = 'var';     // Function-scoped
    let letVariable = 'let';     // Block-scoped
    const constVariable = 'const'; // Block-scoped
  }
  
  console.log(varVariable);      // 'var'
  console.log(letVariable);      // ReferenceError
  console.log(constVariable);    // ReferenceError
}

// Hoisting
console.log(varVariable);        // undefined
console.log(letVariable);        // ReferenceError (TDZ)
var varVariable = 'var';
let letVariable = 'let';

// Reassignment
var a = 1;
a = 2;                          // OK

let b = 1;
b = 2;                          // OK

const c = 1;
c = 2;                          // TypeError: Assignment to constant variable

// Note: Objects assigned to const can have their properties modified
const obj = { prop: 1 };
obj.prop = 2;                   // OK
obj = {};                       // TypeError
```

### 3. How does the Spread operator work?
**Answer:** The spread operator (`...`) expands an iterable (like an array or string) into individual elements, or an object into individual key-value pairs.

**Array spreading:**
```javascript
// Combine arrays
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]

// Clone an array
const original = [1, 2, 3];
const clone = [...original];

// Convert string to array of characters
const chars = [..."hello"]; // ['h', 'e', 'l', 'l', 'o']

// Use with function arguments
const numbers = [1, 2, 3];
const sum = Math.max(...numbers); // Same as Math.max(1, 2, 3)
```

**Object spreading:**
```javascript
// Merge objects
const obj1 = { a: 1, b: 2 };
const obj2 = { b: 3, c: 4 }; // b will be overwritten
const merged = { ...obj1, ...obj2 }; // { a: 1, b: 3, c: 4 }

// Clone an object
const original = { a: 1, b: 2 };
const clone = { ...original };

// Override specific properties
const user = { name: 'John', age: 30 };
const updatedUser = { ...user, age: 31 }; // { name: 'John', age: 31 }
```

### 4. What is a Prototype chain?
**Answer:** The prototype chain is JavaScript's inheritance mechanism. Every JavaScript object has a hidden property called `[[Prototype]]` (accessed via `__proto__` or `Object.getPrototypeOf()`) that points to its prototype object. When a property or method is accessed on an object, JavaScript first looks for it directly on the object. If not found, it looks on the object's prototype, then that prototype's prototype, and so on, forming a "chain" of prototypes.

```javascript
// Creating an object with a prototype
const animal = {
  eats: true,
  walk() {
    console.log("Animal walks");
  }
};

const rabbit = Object.create(animal);
rabbit.jumps = true;

console.log(rabbit.jumps); // true (own property)
console.log(rabbit.eats);  // true (inherited from animal)
rabbit.walk();            // "Animal walks" (method from animal)

// Checking the prototype chain
console.log(rabbit.__proto__ === animal);           // true
console.log(animal.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__);            // null (end of chain)
```

The prototype chain works with constructor functions and classes too:

```javascript
// Constructor function
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(`${this.name} is eating`);
};

function Rabbit(name) {
  Animal.call(this, name);
  this.jumps = true;
}

// Setting up inheritance
Rabbit.prototype = Object.create(Animal.prototype);
Rabbit.prototype.constructor = Rabbit;

Rabbit.prototype.jump = function() {
  console.log(`${this.name} is jumping`);
};

const rabbit = new Rabbit("Bugs");
rabbit.eat();  // "Bugs is eating" (inherited)
rabbit.jump(); // "Bugs is jumping" (own)
```

### 5. Difference between class and object
**Answer:** In JavaScript, classes and objects are related but distinct concepts:

**Class:**
- A blueprint or template for creating objects
- Defines properties and methods that objects will have
- Introduced in ES6 as syntactic sugar over prototype-based inheritance
- Cannot be used directly (must be instantiated)
- Uses the `class` keyword, has a `constructor`, and can extend other classes

**Object:**
- An instance of a class or a standalone entity
- Has actual data (property values) and behavior (methods)
- Can be created using object literals, constructors, `Object.create()`, or class instantiation
- Can be used and manipulated directly

```javascript
// Class definition (blueprint)
class Car {
  constructor(make, model) {
    this.make = make;
    this.model = model;
    this.speed = 0;
  }
  
  accelerate(amount) {
    this.speed += amount;
    return this.speed;
  }
  
  brake(amount) {
    this.speed = Math.max(0, this.speed - amount);
    return this.speed;
  }
}

// Objects (instances of Car class)
const car1 = new Car('Toyota', 'Corolla');
const car2 = new Car('Honda', 'Civic');

console.log(car1.make);        // 'Toyota'
console.log(car2.make);        // 'Honda'
console.log(car1.accelerate(20)); // 20
console.log(car2.accelerate(15)); // 15

// Standalone object (not class-based)
const car3 = {
  make: 'Ford',
  model: 'Mustang',
  speed: 0,
  accelerate(amount) {
    this.speed += amount;
    return this.speed;
  }
};
```

### 6. Explain Call by Value vs Call by Reference
**Answer:** JavaScript uses different parameter passing mechanisms depending on data types:

**Call by Value (Primitive Types):**
- When passing primitive types (strings, numbers, booleans, etc.)
- A copy of the value is passed to the function
- Changes to the parameter inside the function don't affect the original value

```javascript
function incrementValue(x) {
  x = x + 1;
  console.log("Inside function:", x);
}

let num = 5;
incrementValue(num);      // Inside function: 6
console.log("Original:", num);  // Original: 5 (unchanged)
```

**Call by Reference (Objects and Arrays):**
- When passing objects or arrays
- A reference to the memory location is passed
- Changes to the object's properties or array's elements inside the function affect the original

```javascript
function addProperty(obj) {
  obj.newProp = "Added";
}

function reassignObject(obj) {
  obj = { reassigned: true }; // This creates a new object
  console.log("Inside function:", obj);
}

let myObj = { original: true };

addProperty(myObj);
console.log(myObj); // { original: true, newProp: "Added" } (modified)

reassignObject(myObj);
console.log(myObj); // { original: true, newProp: "Added" } (unchanged)
```

**Important Note:**
- JavaScript is technically always "pass by value", but when the value is a reference (as with objects), it behaves like "pass by reference" for the object's contents
- Reassigning the parameter variable inside a function doesn't affect the original reference

### 7. What is a Higher-order Function?
**Answer:** A higher-order function is a function that either:
1. Takes one or more functions as arguments, or
2. Returns a function as its result

Higher-order functions enable powerful functional programming patterns like composition, abstraction, and reusability.

**Examples:**

1. Functions that accept functions as arguments:
```javascript
// Array methods like map, filter, reduce
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(num => num * 2);

// Custom higher-order function
function repeat(n, action) {
  for (let i = 0; i < n; i++) {
    action(i);
  }
}

repeat(3, console.log); // Logs: 0, 1, 2
```

2. Functions that return functions:
```javascript
// Function factory
function multiply(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = multiply(2);
const triple = multiply(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15

// Currying
const add = a => b => a + b;
const add5 = add(5);
console.log(add5(3)); // 8
```

Benefits of higher-order functions:
- Code reusability
- Abstraction (hide implementation details)
- Composition (building complex functions from simpler ones)
- Creating domain-specific language constructs
- Enabling functional programming patterns

### 8. What is Object-Oriented Programming (OOP)?
**Answer:** Object-Oriented Programming is a programming paradigm based on the concept of "objects" that contain data (properties) and code (methods). JavaScript supports OOP through prototypes, classes, and objects.

**Main principles of OOP:**

1. **Encapsulation:**
   - Bundling data and methods that operate on that data within a single unit (object)
   - Information hiding (private/public members)

```javascript
class BankAccount {
  #balance = 0; // Private field (ES2020+)
  
  constructor(owner) {
    this.owner = owner;
  }
  
  deposit(amount) {
    if (amount > 0) {
      this.#balance += amount;
      return true;
    }
    return false;
  }
  
  getBalance() {
    return this.#balance;
  }
}

const account = new BankAccount("John");
account.deposit(100);
console.log(account.getBalance()); // 100
// console.log(account.#balance); // SyntaxError (private field)
```

2. **Inheritance:**
   - Creating new classes (child classes) based on existing ones (parent classes)
   - Child classes inherit properties and methods from parent classes

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(`${this.name} makes a noise`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Call parent constructor
    this.breed = breed;
  }
  
  speak() {
    console.log(`${this.name} barks`); // Override parent method
  }
}

const dog = new Dog("Rex", "German Shepherd");
dog.speak(); // "Rex barks"
```

3. **Polymorphism:**
   - Objects of different classes can be treated as objects of a common superclass
   - Method overriding (redefining methods in child classes)

```javascript
class Shape {
  area() {
    return 0;
  }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }
  
  area() {
    return Math.PI * this.radius * this.radius;
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }
  
  area() {
    return this.width * this.height;
  }
}

// Polymorphism in action
function calculateArea(shape) {
  return shape.area();
}

const shapes = [new Circle(5), new Rectangle(4, 5)];
shapes.forEach(shape => {
  console.log(`Area: ${calculateArea(shape)}`);
});
```

4. **Abstraction:**
   - Hiding complex implementation details and showing only the necessary features
   - Using interfaces, abstract classes, or simply omitting internal details

```javascript
// Abstraction through interface consistency
class Vehicle {
  constructor(make, model) {
    this.make = make;
    this.model = model;
  }
  
  start() {
    throw new Error("Method 'start' must be implemented");
  }
  
  stop() {
    throw new Error("Method 'stop' must be implemented");
  }
}

class Car extends Vehicle {
  start() {
    return "Car engine started";
  }
  
  stop() {
    return "Car engine stopped";
  }
}

// User doesn't need to know how the car starts internally
const myCar = new Car("Toyota", "Corolla");
console.log(myCar.start()); // "Car engine started"
```
