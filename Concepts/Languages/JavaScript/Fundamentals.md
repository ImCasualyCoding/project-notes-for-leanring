# JavaScript: Variables, Scopes, & Data Structures 💻

JavaScript (JS) is a high-level, interpreted scripting language that makes web pages interactive. It runs on the client browser, manipulating HTML elements and styles in response to user actions.

This note documents JS variable bindings, scoping rules, references vs values, and fundamental data structures.

---

## 1. Variable Declarations & Scope

In modern JavaScript, we declare variables using three keywords, each with different scoping rules:

### A. Scopes Explained
*   **Global Scope**: Variables declared outside any function or block. Accessible everywhere.
*   **Function Scope**: Variables declared inside a `function` block. Invisible outside that function.
*   **Block Scope**: Variables declared inside curly braces `{ ... }` (like `if` statements or `for` loops). Invisible outside those braces.

### B. Declaration Comparison:

| Keyword | Scope Type | Reassignable? | Hoisted? |
| :--- | :--- | :--- | :--- |
| **`const`** | Block | No | No (Temporal Dead Zone) |
| **`let`** | Block | Yes | No (Temporal Dead Zone) |
| **`var`** | Function | Yes | Yes (Hoisted to top of scope) |

#### Scope Example:
```javascript
function scopeDemo() {
    var functionScoped = "I am function-scoped";
    
    if (true) {
        let blockScoped = "I am block-scoped";
        const alsoBlockScoped = "I am also block-scoped";
        console.log(blockScoped); // Works
    }
    
    console.log(functionScoped); // Works
    // console.log(blockScoped); // ReferenceError: blockScoped is not defined
}
```

> [!TIP]
> Always default to using `const`. If you know the variable value needs to change later (like a counter or tracking index), use `let`. Avoid `var` to prevent scope leaks and hoisting bugs.

---

## 2. Primitives vs. Objects (Reference Types)

JavaScript divides data types into two main categories:

### A. Primitives (Passed by Value)
Types: `String`, `Number`, `Boolean`, `null`, `undefined`, `Symbol`.
*   When you copy a primitive, you create a completely new copy of the data.
*   Modifying the copy does not affect the original.

```javascript
let scoreA = 100;
let scoreB = scoreA; // Copies the value 100
scoreB = 200;        // Changes scoreB

console.log(scoreA); // Output: 100 (unaffected)
```

### B. Objects (Passed by Reference)
Types: `Object`, `Array`, `Function`.
*   Objects are stored in memory. Variables do not hold the object itself; they hold a **reference pointer** to the object's location in memory.
*   When you copy an object variable, you copy the **pointer**, not the data. Both variables point to the same object.

```javascript
const clientA = { name: "John C", age: 65 };
const clientB = clientA; // Copies the memory pointer

clientB.name = "John Modified";

console.log(clientA.name); // Output: "John Modified" (original was altered!)
```

---

## 3. Arrow Functions vs. Standard Declarations

Functions are blocks of code designed to perform specific operations.

```javascript
// 1. Standard Function Declaration
function calculateSway(progress) {
    return Math.sin(progress * Math.PI / 2);
}

// 2. Modern Arrow Function Expression
const calculateSwayArrow = (progress) => {
    return Math.sin(progress * Math.PI / 2);
};

// Implicit Return (Shorthand)
const calculateSwayShort = progress => Math.sin(progress * Math.PI / 2);
```

### Lexical `this` Binding
The main functional difference between standard and arrow functions is the behavior of the `this` keyword:
*   **Standard functions** dynamically bind `this` based on *how* the function was called.
*   **Arrow functions** inherit `this` lexically from their surrounding code context. This is highly useful when writing event handlers or asynchronous loops.

---

## 4. Array Collections & Operations

Arrays are ordered lists of data. In modern JS, we use built-in higher-order functions to iterate over arrays instead of writing traditional `for` loops.

```javascript
const numbers = [1, 2, 3, 4, 5];

// A. forEach() - Runs a function on each element. Returns undefined.
numbers.forEach(num => console.log(num * 2));

// B. map() - Transforms each item and returns a NEW array.
const doubled = numbers.map(num => num * 2); 
console.log(doubled); // [2, 4, 6, 8, 10]

// C. filter() - Returns a new array containing items that pass a true/false test.
const evens = numbers.filter(num => num % 2 === 0);
console.log(evens); // [2, 4]
```

*   **Learn how JavaScript executes these functions sequentially**: **[[Concepts/Languages/JavaScript/Concurrency & Renders|JavaScript Concurrency & Renders]]**
*   **Learn how JavaScript updates layouts based on array data**: **[[Concepts/Languages/JavaScript/DOM API|JavaScript DOM API]]**
