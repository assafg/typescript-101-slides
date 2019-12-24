## What exactly is typescript?

A typed language that compiles to Javascript

---

## Why use Typescript?

- Stict types
- Detect errors at build time
- Better intellisence
- Easier and safer refactoring
- Supports latest JS features

---

## The very basic
 
Create a `.ts` file

```typescript
// helloworld.ts
class Greeter {
  sayHello(name: string) {
    console.log(`Hello ${name}`);
  }  
}

const greeter = new Greeter();
greeter.sayHello('Jonh Snow');

```

---

```
npm install -g typescript
tsc helloworld.ts
```

---

## A better practice is to use a `npm script`

```
npm init
npm install --save-dev typescript
```

```json
// package.json

{
  ...
  "scripts": {
    "build": "tsc helloworld.ts"
  }
  ...
}
```

---

# try it

---

Should get

```javascript
// helloworld.js

var Greeter = /** @class */ (function () {
    function Greeter() {
    }
    Greeter.prototype.sayHello = function (name) {
        console.log("Hello " + name);
    };
    return Greeter;
}());
var greeter = new Greeter();
greeter.sayHello("Jonh Snow");

```

----

## A little tweek

create a `tsconfig.json`

```
npx tsc -init
```

change the `outDir` to  `"outDir": "./build"`

```json
// package.json

{
  ...
  "scripts": {
    "build": "tsc",
    "develop": "tsc --watch"
  }
  ...
}

```

---

# Basic types

[Full documentation](https://www.typescriptlang.org/docs/handbook/basic-types.html)

---

## Boolean

```ts
let isDone: boolean = false;
```

---

## Number

```ts
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
```

---

## String

```ts
let color: string = "blue";
color = 'red';

let fullName: string = `Bob Bobbington`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ fullName }.

I'll be ${ age + 1 } years old next month.`;
```

---

## Array

```ts
// Option 1
let list: number[] = [1, 2, 3];

// Option 2
let list: Array<number> = [1, 2, 3];
```

---

## Tuple

```ts
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ["hello", 10]; // OK
// Initialize it incorrectly
x = [10, "hello"]; // Error

console.log(x[0].substring(1)); // OK
console.log(x[1].substring(1)); // Error, 'number' does not have 'substring'

x[3] = "world"; // Error, Property '3' does not exist on type '[string, number]'.

console.log(x[5].toString()); // Error, Property '5' does not exist on type '[string, number]'.
```

---

## Enum

```ts
enum Color {
  Red, 
  Green, 
  Blue
}
let c: Color = Color.Green;

// Or set numbers:
enum Color {
  Red = 1, 
  Green = 2, 
  Blue = 4
}

let colorName: string = Color[2];
console.log(colorName); // Displays 'Green' as its value is 2 above

```

---

## Any

```ts
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean


let list: any[] = [1, true, "free"];
list[1] = 100;
```

---

## Void

```ts
function warnUser(): void {
    console.log("This is my warning message");
}
```

---

## Object

```ts
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error
```

---

# Interfaces

---

```ts
function printLabel(labeledObj: { label: string }) {
    console.log(labeledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

---

## Basic Interface declaration

```ts
interface LabeledValue {
    label: string;
}

function printLabel(labeledObj: LabeledValue) {
    console.log(labeledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

---

## Optional properties

```ts
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
    let newSquare = {color: "white", area: 100};
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

let mySquare = createSquare({color: "black"});
```

---

## Readonly properties

```ts
interface Point {
    readonly x: number;
    readonly y: number;
}

...

let p1: Point = { x: 10, y: 20 };
p1.x = 5; // error!
```

---

## Function Types

```ts
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    let result = source.search(subString);
    return result > -1;
}


// This is also okay
let mySearch: SearchFunc;
mySearch = function(src, sub) {
    let result = src.search(sub);
    return result > -1;
}
```

---

## Indexable Types

```ts
interface StringArray {
    [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];

```

---

## Dictionary Pattern

```ts
interface Person {
  name: string;
  age: number;
}

interface Family {
  [nickName: string]: Person;
}

const piki: Person = {
  name: "Ofri",
  age: 12
};
const luli: Person = {
  name: "Ella",
  age: 12
};
const myFamily = {
  piki,
  luli
};

const child = myFamily["piki"];

```

---

## Class Types

```ts
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date): void;
}

class Clock implements ClockInterface {
    currentTime: Date = new Date();
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

---

## Constructor checking

```ts
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
    tick(): void;
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```

---

## Extending Interfaces

```ts
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = {} as Square;
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

---

# Classes

Act very similar to JS classes. We will not go into them here.

[Further reading](https://www.typescriptlang.org/docs/handbook/classes.html)

---

# Functions

---

## Function Types

```ts
function add(x: number, y: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number { return x + y; };
```

---

## Optional and Default Parameters

```ts
function buildName(firstName: string, lastName: string) {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // error, too few parameters
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");         // ah, just right
```

---

## Optional

```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}

let result1 = buildName("Bob");                  // works correctly now
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");         // ah, just right
```

---

## Default

```ts
function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // works correctly now, returns "Bob Smith"
let result2 = buildName("Bob", undefined);       // still works, also returns "Bob Smith"
let result3 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result4 = buildName("Bob", "Adams");         // ah, just right
```

---

## Rest Parameters

```ts
function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + " " + restOfName.join(" ");
}

// employeeName will be "Joseph Samuel Lucas MacKinzie"
let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");

let buildNameFun: (fname: string, ...rest: string[]) => string = buildName;

```

---

## this

```ts
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        return function() {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

---

```ts
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        // NOTE: the line below is now an arrow function, allowing us to capture 'this' right here
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

---

## `this` parameters

```ts
interface Card {
    suit: string;
    card: number;
}
interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
}
let deck: Deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    // NOTE: The function now explicitly specifies that its callee must be of type Deck
    createCardPicker: function(this: Deck) {
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```
