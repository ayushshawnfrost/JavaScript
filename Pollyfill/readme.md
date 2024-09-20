# Polyfills  

| No. | Questions                                                                                                                                                                                                                        |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [Polifill for map?](#Polifill-for-map)                                                                                                                                                                                                 |
| 2   | [What is the history behind React evolution?](#What-is-the-history-behind-React-evolution)                                                                                                                                       |
| 3   | [What are the major features of React?](#what-are-the-major-features-of-react)                                                                                                                                                   |
| 4   | [What is JSX?](#what-is-jsx)                                                                                                                                                                                                     |
| 5   | [What is the difference between Element and Component?](#what-is-the-difference-between-element-and-component)                                                                                                                   |
| 6   | [How to create components in React?](#how-to-create-components-in-react)                                                                                                                                                         |
| 7   | [When to use a Class Component over a Function Component?](#when-to-use-a-class-component-over-a-function-component)                                                                                                             |
| 8   | [What are Pure Components?](#what-are-pure-components)                                                                                                                                                                           |
| 9   | [What is state in React?](#what-is-state-in-react)                                                                                                                                                                               |
| 10  | [What are props in React?](#what-are-props-in-react)                                                                                                                                                                             |
| 11  | [What is the difference between state and props?](#what-is-the-difference-between-state-and-props)                                                                                                                               |
| 12  | [What is the difference between HTML and React event handling?](#what-is-the-difference-between-html-and-react-event-handling)                                                                                                   |

### Polifill for map
```javascript
// [1,3,4,5].map((item, index, array)=>)

Array.prototype.myMap=function(callback){
  let array=[]
  for(let i=0;i<this.length;i++){
    array.push(callback(this[i], i, this))
  }
  return array
}
```

### Polifill for filter()
```javascript
// [1,3,4,5].filter((item, index, array)=>)

Array.prototype.myFilter=function(callback){
  let array=[]
  for(let i=0;i<this.length;i++){
    if(callback(this[i], i, this))array.push(this[i]))
  }
  return array
}
```


### Polifill for reduce()
```javascript
// [1,3,4,5].reduce((accumulator, currentValue, index, array)=>{}, initialValue)
// incase initialValue in undefined, accumulator -> array[0]

Array.prototype.myReduce=function(callback, initialValue){
  let accumulator=initialValue
  for(let i=0;i<this.length;i++){
    accumulator = accumulator ? callback(accumulator, this[i], i, this) : this[i]
  }
  return accumulator
}
```

### Polifill for call()
```javascript
// function.call(context={}, parameter1, ..., parametern )

Function.prototype.myCall=function(context, ...parameters){
  if(typeof this !== "function"){
    throw new Error(this+ "It's not a callable")
  }
  context.fn=this
  context.fn(...parameters)
}
```
```javascript
// Example code
let obj={
    name:"ayush"
}

myname.myCall(obj, "hello", "there")

```
```javascript
Function.prototype.myCall = function(thisArg, ...args) {
    // Ensure the `thisArg` is an object, or default to global object (window or globalThis)
    thisArg = thisArg || globalThis;

    // Create a unique property on `thisArg` to store the function (using Symbol to avoid name clashes)
    const fnSymbol = Symbol('fn');
    thisArg[fnSymbol] = this;

    // Call the function using the context and arguments
    const result = thisArg[fnSymbol](...args);

    // Clean up: remove the temporary property from `thisArg`
    delete thisArg[fnSymbol];

    // Return the result of the function call
    return result;
};

function greet(greeting, punctuation) {
    return `${greeting}, ${this.name}${punctuation}`;
}

const person = { name: 'Ayush' };

// Using the custom myCall method
console.log(greet.myCall(person, 'Hello', '!')); // Output: "Hello, Ayush!"

```

### Polifill for apply()
```javascript
// function.apply(context={}, [parameter1, ..., parametern] )

Function.prototype.myApply=function(context, parameterArray){
  if(typeof this !== "function"){
    throw new Error(this+ "It's not a callable")
  }
  if(!Array.isArray(parameterArray)){
    throw new Error(this+ "CreateListFromArrayLike called on non-Array")
  }
  context.fn=this
  context.fn(...parameters)
}
```
```javascript
// Example code
let obj={
    name:"ayush"
}

function myname(a,b){
    console.log(a+" "+b+" "+ this.name)
}

myname.myApply(obj, "hello", "there")
```

```javascript
// Better version
Function.prototype.myApply = function(thisArg, argsArray) {
    // Ensure `thisArg` is an object or default to the global object
    thisArg = thisArg || globalThis;

    // Create a unique property on `thisArg` to store the function (using Symbol to avoid name clashes)
    const fnSymbol = Symbol('fn');
    thisArg[fnSymbol] = this;

    // Call the function with the arguments from `argsArray`
    // Ensure `argsArray` is either an array or null/undefined
    const result = thisArg[fnSymbol](...(argsArray || []));

    // Clean up by deleting the temporary property
    delete thisArg[fnSymbol];

    // Return the result of the function call
    return result;
};

function sum(a, b) {
    return a + b;
}

const numbers = [3, 7];
console.log(sum.myApply(null, numbers));  // Output: 10

```
### Polifill for bind()
```javascript
// function.bind(context={}, parameter1, ..., parametern )

Function.prototype.myBind=function(context={}, ...parameter){
  if(typeof this !== "function"){
    throw new Error(this+ "cannot be bound as it's not callable")
  }
  context.fn=this
  return function(...newArgs){
    return context.fn(...parameter, ...newArgs) 
  }
}
```
```javascript
// Example code
let obj={
    name:"ayush"
}

function myname(a,b){
    console.log(a+" "+b+" "+ this.name)
}

myname.myBind(obj, "hello", "there")

```

```javascript
// Better code
Function.prototype.myBind = function(thisArg, ...boundArgs) {
    const originalFunction = this; // Store the original function

    // Return a new function
    return function(...args) {
        // If the function is called as a constructor, `this` should refer to the new instance.
        const isNew = this instanceof originalFunction;

        // Use the bound `thisArg` unless it's called with `new`, then use `this`.
        return originalFunction.apply(
            isNew ? this : thisArg, 
            [...boundArgs, ...args] // Combine boundArgs and new args
        );
    };
};

// Example
function multiply(a, b) {
    return a * b;
}

const double = multiply.myBind(null, 2); // Pre-fill `a` with 2
console.log(double(5));  // Output: 10

const person = {
    name: "Ayush",
    greet: function(greeting) {
        return `${greeting}, my name is ${this.name}`;
    }
};

const greetAyush = person.greet.myBind({ name: "John" });
console.log(greetAyush("Hello"));  // Output: "Hello, my name is John"


```

### Polifill for once

```javascript

function once(func, context){
  let ran
  return function(){
    if(func){
      ran = func.apply(context | this, arguments);
      func=null 
    }
    return ran
  }
}
let hello=once((a,b)=>{console.log("running"+a+b)})

hello(1,2)
hello(3,4)
hello(5,7)
```


### Polifill for memoize

```javascript
function fibo(num){
  if(num===0 || num===1)return num;
  return fibo(num-1)+fibo(num-2)
}
function memoize(func, context){
  let dictionary=new Map();
  return function(arg){
    if(dictionary.get(arg)){
      return dictionary.get(arg)
    }
    dictionary.set(arg, func.call(context || this, arg))
    return dictionary.get(arg)
  }
}
let hello=memoize(fibo)
console.time("1")
hello(23)
console.timeEnd("1")

console.time("2")
hello(23)
console.timeEnd("2")
```
