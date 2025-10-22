# Object-Oriented Programming (OOP) in JavaScript — Complete Guide
### English + Roman Urdu (Inner Working ke saath)

Yeh guide bilkul step-by-step hai — **inner working** aur **process** ke saath samjhenge ke OOP JavaScript mein **kaise kaam karta hai**.

---

## Table of Contents

1. [OOP Fundamentals](#1-oop-fundamentals)
2. [Objects Kaise Bantay Hain](#2-objects-kaise-bantay-hain)
3. [Prototype System - Inner Working](#3-prototype-system---inner-working)
4. [Constructor Function - Step by Step](#4-constructor-function---step-by-step)
5. [ES6 Classes - Andar Kya Hota Hai](#5-es6-classes---andar-kya-hota-hai)
6. [Inheritance - Process Explained](#6-inheritance---process-explained)
7. [Encapsulation - Data Hiding](#7-encapsulation---data-hiding)
8. [Polymorphism - Same Name, Different Behavior](#8-polymorphism)
9. [this Keyword - Inner Working](#9-this-keyword---inner-working)
10. [Memory Management](#10-memory-management)
11. [Common Mistakes](#11-common-mistakes)

---

## 1) OOP Fundamentals

### ❓ OOP Kya Hai?

**English**: Object-Oriented Programming means organizing code using objects that contain data (properties) and actions (methods).

**Roman Urdu**: OOP ka matlab hai ke hum apne code ko **objects** ki form mein organize karte hain. Objects mein **data** (properties) aur **functions** (methods) hotay hain.

### 🎯 Real World Example

```javascript
// Real world car ko code mein represent karna
let car = {
  // Properties (data)
  brand: "Toyota",
  color: "Red",
  speed: 0,
  
  // Methods (actions)
  start() {
    console.log(this.brand + " start ho gayi");
  },
  
  accelerate() {
    this.speed += 10;
    console.log("Speed: " + this.speed + " km/h");
  }
};

car.start();       // "Toyota start ho gayi"
car.accelerate();  // "Speed: 10 km/h"
```

**Process samjhein**:
1. `car` ek object hai
2. `brand`, `color`, `speed` properties hain (data store karte hain)
3. `start()`, `accelerate()` methods hain (actions perform karte hain)
4. `this` keyword current object ko point karta hai

---

### 📋 OOP Ke 4 Principles

| Principle | English | Roman Urdu | Example |
|-----------|---------|------------|---------|
| **Abstraction** | Hide complexity, show essentials | Complexity chhupakar sirf zaruri cheezein dikhana | ATM machine: button press karo, andar ka mechanism nahi dekhna |
| **Encapsulation** | Bundle data with methods that use it | Data aur methods ko ek saath rakhna aur control karna | Private variables jo bahar se access nahi ho sakte |
| **Inheritance** | Child class gets parent's features | Ek class dusri class ki properties inherit kare | `Dog` class `Animal` se inherit kare |
| **Polymorphism** | Same method, different implementation | Ek naam, alag-alag kaam | `speak()` Dog mein bark, Cat mein meow |

---

## 2) Objects Kaise Bantay Hain

JavaScript mein objects banane ke **5 tareeqay** hain. Har ek ka inner working samjhte hain.

### Method 1: Object Literal

```javascript
// Sabse simple tareeqa
let student = {
  name: "Zohaib",
  age: 21,
  study: function() {
    console.log(this.name + " parh raha hai");
  }
};

student.study(); // "Zohaib parh raha hai"
```

**Inner Working**:
- JavaScript engine memory mein ek object create karta hai
- `name`, `age` properties us object mein store hoti hain
- `study` method bhi us object ka part ban jata hai
- Har property aur method direct object mein stored hai

**Limitation**: Agar 100 students banana ho to 100 baar likhna parega!

---

### Method 2: Constructor Function (Old Way)

```javascript
function Student(name, age) {
  this.name = name;  // property set kar rahe hain
  this.age = age;
}

// Prototype pe method add kar rahe hain (shared method)
Student.prototype.study = function() {
  console.log(this.name + " parh raha hai");
};

let s1 = new Student("Ali", 20);
let s2 = new Student("Sara", 19);

s1.study(); // "Ali parh raha hai"
s2.study(); // "Sara parh raha hai"
```

**Inner Working jab `new Student("Ali", 20)` chalata hai**:

**Step 1**: Empty object create hota hai
```javascript
let obj = {}; // internally
```

**Step 2**: Object ki `[[Prototype]]` set hoti hai
```javascript
obj.__proto__ = Student.prototype;
```

**Step 3**: Constructor function call hota hai `this = obj` ke saath
```javascript
// this = obj
this.name = "Ali";  // obj.name = "Ali"
this.age = 20;      // obj.age = 20
```

**Step 4**: Object return hota hai
```javascript
return obj; // s1 = obj
```

**Memory Diagram**:
```
s1 ──┐
     ├─→ {name: "Ali", age: 20}
     │   __proto__ ──→ Student.prototype ──→ {study: function}
s2 ──┘
     └─→ {name: "Sara", age: 19}
         __proto__ ──→ Student.prototype (SAME!)
```

---

### Method 3: ES6 Class (Modern Way)

```javascript
class Student {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  
  study() {
    console.log(this.name + " parh raha hai");
  }
}

let s1 = new Student("Hamza", 22);
s1.study(); // "Hamza parh raha hai"
```

**Inner Working**: 
- `class` keyword sirf **syntactic sugar** hai
- Internally wahi constructor function + prototype system use hota hai
- `study()` method `Student.prototype` pe jata hai

**Proof**:
```javascript
console.log(typeof Student);           // "function"
console.log(s1.__proto__ === Student.prototype); // true
```

**Class vs Constructor - Dono Same**:
```javascript
// Ye dono bilkul same kaam karte hain internally:

// Constructor function
function Student(name) { this.name = name; }
Student.prototype.study = function() { /*...*/ };

// ES6 Class
class Student {
  constructor(name) { this.name = name; }
  study() { /*...*/ }
}
```

---

### Method 4: Object.create()

```javascript
const studentMethods = {
  study() {
    console.log(this.name + " parh raha hai");
  }
};

let s1 = Object.create(studentMethods);
s1.name = "Ahmed";
s1.study(); // "Ahmed parh raha hai"
```

**Inner Working**:
- `Object.create(proto)` ek naya object banata hai
- Us object ki `[[Prototype]]` `proto` set kar deta hai
- Direct prototype link set hota hai

---

### Method 5: Factory Function

```javascript
function createStudent(name, age) {
  return {
    name: name,
    age: age,
    study() {
      console.log(this.name + " parh raha hai");
    }
  };
}

let s1 = createStudent("Usman", 21);
s1.study(); // "Usman parh raha hai"
```

**Inner Working**:
- Simple function hai jo object return karta hai
- `new` keyword ki zarurat nahi
- Har call pe naya object banta hai

---

## 3) Prototype System - Inner Working

JavaScript mein **classical inheritance nahi hai** — yeh **prototype-based** hai.

### 🔍 Prototype Chain Kya Hai?

```javascript
const animal = {
  eats: true,
  walk() {
    console.log("Animal chal raha hai");
  }
};

const dog = Object.create(animal);
dog.barks = true;

console.log(dog.barks);  // true (dog mein hai)
console.log(dog.eats);   // true (animal se mila)
dog.walk();              // "Animal chal raha hai" (animal se)
```

**Step-by-Step Process jab `dog.walk()` call hota hai**:

**Step 1**: JavaScript `dog` object mein `walk` dhoondhta hai
```javascript
dog.walk → undefined (nahi mila)
```

**Step 2**: `dog` ki `[[Prototype]]` mein dhoondhta hai (yaani `animal`)
```javascript
dog.__proto__.walk → function found! ✓
```

**Step 3**: Method ko call karta hai `this = dog` ke saath
```javascript
walk.call(dog) // "Animal chal raha hai"
```

### 📊 Prototype Chain Visualization

```
dog object
│
├─ barks: true
├─ [[Prototype]] ──→ animal object
                     │
                     ├─ eats: true
                     ├─ walk: function
                     ├─ [[Prototype]] ──→ Object.prototype
                                          │
                                          ├─ toString: function
                                          ├─ hasOwnProperty: function
                                          ├─ [[Prototype]] ──→ null
```

**Lookup Process**:
1. Property pehle object mein dhoondhte hain
2. Phir `__proto__` (prototype) mein
3. Phir `__proto__` ke `__proto__` mein
4. Chain ke end tak (null)
5. Agar mil gayi to return, warna `undefined`

---

### 🧪 Practical Example: String Methods

```javascript
let str = "hello";
console.log(str.toUpperCase()); // "HELLO"
```

**Inner Working**:

```
"hello" (primitive)
    ↓
JavaScript temporarily converts to String object
    ↓
String {"hello"}
├─ [[Prototype]] → String.prototype
                   ├─ toUpperCase: function ✓
                   ├─ toLowerCase: function
                   ├─ split: function
                   └─ [[Prototype]] → Object.prototype
```

**Process**:
1. `"hello"` primitive hai
2. JavaScript temporarily `new String("hello")` banata hai
3. `String.prototype.toUpperCase` method call hota hai
4. Result milne ke baad temporary object delete ho jata hai

---

## 4) Constructor Function - Step by Step

Chalo ab **poora process** detail mein samjhte hain:

### 📝 Example Setup

```javascript
function Car(brand, color) {
  this.brand = brand;
  this.color = color;
  console.log("Constructor chal raha hai, this =", this);
}

Car.prototype.start = function() {
  console.log(this.brand + " start ho gayi");
};

Car.prototype.info = function() {
  console.log(`${this.brand} car ${this.color} rang ki hai`);
};
```

### 🔄 Execution Process: `let myCar = new Car("Honda", "Blue")`

**JavaScript Engine ke andar kya hota hai**:

#### Step 1: Empty Object Creation
```javascript
// Internally
let newObj = {};
```

#### Step 2: Prototype Linking
```javascript
newObj.__proto__ = Car.prototype;

// Ab newObj prototype chain mein hai:
// newObj → Car.prototype → Object.prototype → null
```

#### Step 3: Constructor Execution
```javascript
// Function call hota hai with this = newObj
Car.call(newObj, "Honda", "Blue");

// Andar:
this.brand = "Honda";  // newObj.brand = "Honda"
this.color = "Blue";   // newObj.color = "Blue"
console.log("Constructor chal raha hai, this =", newObj);
```

#### Step 4: Return Object
```javascript
// Agar constructor explicitly object return nahi karta:
return newObj;

// myCar ab newObj ko point karta hai
let myCar = newObj;
```

### 📸 Memory Snapshot

```
myCar
  │
  └──→ Object
       ├── brand: "Honda"
       ├── color: "Blue"
       └── [[Prototype]]: Car.prototype
                          ├── start: function
                          ├── info: function
                          └── [[Prototype]]: Object.prototype
```

### 🎯 Method Call Process: `myCar.start()`

**Step 1**: `myCar` mein `start` dhoondhte hain
```javascript
myCar.start → undefined (not found)
```

**Step 2**: `myCar.__proto__` (yaani `Car.prototype`) mein dhoondhte hain
```javascript
myCar.__proto__.start → function found! ✓
```

**Step 3**: Function execute hota hai with `this = myCar`
```javascript
start.call(myCar);
// Andar: console.log(this.brand + " start ho gayi")
// Output: "Honda start ho gayi"
```

---

### ⚡ Multiple Instances

```javascript
let car1 = new Car("Toyota", "Red");
let car2 = new Car("Suzuki", "White");

car1.start(); // "Toyota start ho gayi"
car2.start(); // "Suzuki start ho gayi"
```

**Memory mein kya hota hai**:

```
car1 ──→ {brand: "Toyota", color: "Red"}
         │
         └──→ [[Prototype]]: Car.prototype ←──┐
                            ├── start         │
                            └── info          │ (SHARED)
                                              │
car2 ──→ {brand: "Suzuki", color: "White"}   │
         │                                    │
         └──→ [[Prototype]]: Car.prototype ───┘
```

**Key Point**: 
- Har car ki apni `brand` aur `color` hai (unique data)
- Lekin `start` aur `info` methods **shared** hain (ek hi copy)
- Yeh memory efficient hai!

---

## 5) ES6 Classes - Andar Kya Hota Hai

```javascript
class Animal {
  constructor(name) {
    this.name = name;
    console.log("Animal constructor called");
  }
  
  speak() {
    console.log(this.name + " awaz kar raha hai");
  }
  
  sleep() {
    console.log(this.name + " so raha hai");
  }
}

let cat = new Animal("Billi");
cat.speak(); // "Billi awaz kar raha hai"
```

### 🔍 JavaScript Engine Translation

Class ko internally ye convert hota hai:

```javascript
// Internally
function Animal(name) {
  this.name = name;
  console.log("Animal constructor called");
}

Animal.prototype.speak = function() {
  console.log(this.name + " awaz kar raha hai");
};

Animal.prototype.sleep = function() {
  console.log(this.name + " so raha hai");
};
```

**Proof karo**:
```javascript
console.log(typeof Animal);                    // "function"
console.log(cat.__proto__ === Animal.prototype); // true
console.log(Animal.prototype.speak);            // function
```

### 📋 Class vs Constructor - Side by Side

| Feature | Constructor Function | ES6 Class |
|---------|---------------------|-----------|
| Syntax | `function Person() {}` | `class Person {}` |
| Methods | `Person.prototype.method = ...` | `method() {}` inside class |
| Constructor | Function itself | `constructor() {}` method |
| `new` required | Optional (but should use) | Mandatory (error without it) |
| Hoisting | Function hoisting | No hoisting |
| Strict mode | Optional | Automatic |

---

## 6) Inheritance - Process Explained

### 🧬 Basic Inheritance Example

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  eat() {
    console.log(this.name + " kha raha hai");
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Parent constructor call
    this.breed = breed;
  }
  
  bark() {
    console.log(this.name + " bhonk raha hai: Woof!");
  }
}

let myDog = new Dog("Tommy", "German Shepherd");
myDog.eat();  // "Tommy kha raha hai" (Animal se)
myDog.bark(); // "Tommy bhonk raha hai: Woof!" (Dog ka)
```

### 🔄 Step-by-Step Process: `new Dog("Tommy", "German Shepherd")`

#### Step 1: Empty Object Creation
```javascript
let newObj = {}; // internally
```

#### Step 2: Prototype Chain Setup
```javascript
// Dog.prototype ki [[Prototype]] Animal.prototype ko point karti hai
Dog.prototype.__proto__ = Animal.prototype;

// newObj ki [[Prototype]] Dog.prototype ko point karti hai
newObj.__proto__ = Dog.prototype;
```

**Prototype Chain ban gayi**:
```
newObj → Dog.prototype → Animal.prototype → Object.prototype → null
```

#### Step 3: Constructor Execution

**3a. Dog constructor start**:
```javascript
// this = newObj (but abhi incomplete hai)
constructor(name, breed) {
  super(name); // ← Yeh line parent ko call karegi
  this.breed = breed;
}
```

**3b. `super(name)` calls Animal constructor**:
```javascript
// Animal.call(newObj, "Tommy")
Animal.constructor.call(newObj, "Tommy");

// Andar:
this.name = "Tommy"; // newObj.name = "Tommy"
```

**3c. Dog constructor continue**:
```javascript
this.breed = "German Shepherd"; // newObj.breed = "German Shepherd"
```

#### Step 4: Return Object
```javascript
return newObj; // myDog = newObj
```

### 📸 Final Memory Structure

```
myDog
  │
  └──→ Object
       ├── name: "Tommy"
       ├── breed: "German Shepherd"
       └── [[Prototype]]: Dog.prototype
                          ├── bark: function
                          └── [[Prototype]]: Animal.prototype
                                             ├── eat: function
                                             └── [[Prototype]]: Object.prototype
```

### 🎯 Method Lookup: `myDog.eat()`

**Process**:
1. `myDog` mein `eat` dhoondhte hain → ❌ Nahi mila
2. `myDog.__proto__` (Dog.prototype) mein → ❌ Nahi mila
3. `Dog.prototype.__proto__` (Animal.prototype) mein → ✅ Mil gaya!
4. `eat.call(myDog)` execute hota hai
5. Output: "Tommy kha raha hai"

---

### 🔧 Method Overriding

```javascript
class Animal {
  speak() {
    console.log("Animal awaz kar raha hai");
  }
}

class Cat extends Animal {
  speak() {
    console.log("Meow meow!");
  }
}

class Dog extends Animal {
  speak() {
    console.log("Woof woof!");
  }
}

let cat = new Cat();
let dog = new Dog();

cat.speak(); // "Meow meow!"
dog.speak(); // "Woof woof!"
```

**Inner Working**:
- Jab `cat.speak()` call hota hai
- Pehle `cat` mein dhoondhte hain → Nahi
- Phir `Cat.prototype` mein → **Mil gaya!** ✓
- Animal.prototype tak jaane ki zarurat nahi
- **Nearest method use hota hai** (overriding)

---

### 🔗 `super` Keyword Explained

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    return this.name + " awaz kar raha hai";
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Parent constructor call
    this.breed = breed;
  }
  
  speak() {
    let parentMsg = super.speak(); // Parent method call
    return parentMsg + " - Woof!";
  }
}

let dog = new Dog("Bruno", "Bulldog");
console.log(dog.speak()); 
// "Bruno awaz kar raha hai - Woof!"
```

**`super` ke 2 uses**:

1. **Constructor mein**: `super(args)` → Parent constructor call karta hai
2. **Method mein**: `super.methodName()` → Parent ka method call karta hai

**Process**:
```javascript
super.speak() 
  ↓
Animal.prototype.speak.call(this)
  ↓
"Bruno awaz kar raha hai"
```

---

## 7) Encapsulation - Data Hiding

Encapsulation ka matlab: **Data ko hide karna** taake wo directly access na ho sake.

### Method 1: Private Fields (Modern - #)

```javascript
class BankAccount {
  #balance = 0;        // Private field
  #accountNumber;      // Private field
  
  constructor(accNum, initialBalance) {
    this.#accountNumber = accNum;
    this.#balance = initialBalance;
  }
  
  // Public method to access private data
  deposit(amount) {
    if (amount > 0) {
      this.#balance += amount;
      console.log(`${amount} rupees jama hogaye`);
    }
  }
  
  withdraw(amount) {
    if (amount > 0 && amount <= this.#balance) {
      this.#balance -= amount;
      console.log(`${amount} rupees nikale`);
    } else {
      console.log("Insufficient balance");
    }
  }
  
  getBalance() {
    return this.#balance;
  }
}

let account = new BankAccount("ACC001", 5000);
account.deposit(2000);           // "2000 rupees jama hogaye"
console.log(account.getBalance()); // 7000
account.withdraw(3000);           // "3000 rupees nikale"

// console.log(account.#balance); // ❌ SyntaxError - private hai!
```

**Inner Working**:
- `#balance` aur `#accountNumber` **truly private** hain
- JavaScript engine compile time pe check karta hai
- Bahar se access karne pe **syntax error** aata hai
- Sirf class ke andar access ho sakti hain

---

### Method 2: Closures (Old Way)

```javascript
function createBankAccount(initialBalance) {
  // Private variable (closure mein hai)
  let balance = initialBalance;
  
  // Public methods return kar rahe hain
  return {
    deposit(amount) {
      if (amount > 0) {
        balance += amount;
        console.log(`${amount} jama hogaye. Balance: ${balance}`);
      }
    },
    
    withdraw(amount) {
      if (amount > 0 && amount <= balance) {
        balance -= amount;
        console.log(`${amount} nikale. Balance: ${balance}`);
      } else {
        console.log("Insufficient balance");
      }
    },
    
    getBalance() {
      return balance;
    }
  };
}

let acc = createBankAccount(1000);
acc.deposit(500);              // "500 jama hogaye. Balance: 1500"
console.log(acc.getBalance()); // 1500
// console.log(acc.balance);   // undefined - access nahi hai!
```

**Inner Working (Closure Magic)**:

```
createBankAccount() call hota hai
    ↓
Execution context banta hai with local variable `balance`
    ↓
Object return hota hai with 3 methods
    ↓
Methods closure banate hain - wo `balance` ko access kar sakte hain
    ↓
Function khatam hone ke baad bhi `balance` memory mein rahta hai
(kyunki methods us ko reference kar rahe hain)
    ↓
Bahar se `balance` directly access nahi ho sakta
```

**Diagram**:
```
acc ──→ {
         deposit: function (closure → balance),
         withdraw: function (closure → balance),
         getBalance: function (closure → balance)
        }
        
Scope Chain:
Methods → Closure (balance: 1000) → Global Scope
```

---

### Method 3: WeakMap (Advanced)

```javascript
const privateData = new WeakMap();

class User {
  constructor(name, password) {
    this.name = name; // Public
    
    // Private data WeakMap mein store
    privateData.set(this, {
      password: password
    });
  }
  
  checkPassword(input) {
    const data = privateData.get(this);
    return data.password === input;
  }
}

let user = new User("Zohaib", "secret123");
console.log(user.name);                    // "Zohaib" - public
console.log(user.checkPassword("wrong"));  // false
console.log(user.checkPassword("secret123")); // true
// console.log(user.password);             // undefined - private!
```

---

### 🔒 Encapsulation Benefits

```javascript
class Counter {
  #count = 0;
  #maxCount = 100;
  
  increment() {
    if (this.#count < this.#maxCount) {
      this.#count++;
    } else {
      console.log("Maximum limit reached");
    }
  }
  
  decrement() {
    if (this.#count > 0) {
      this.#count--;
    }
  }
  
  getValue() {
    return this.#count;
  }
}

let counter = new Counter();
counter.increment();
counter.increment();
console.log(counter.getValue()); // 2

// Agar encapsulation na hota:
// counter.#count = -50; // Invalid state!
// counter.#count = 1000; // Limit cross!
```

**Faida**:
- **Data protection**: Invalid values set nahi ho sakte
- **Controlled access**: Sirf methods ke through access
- **Easy to change**: Internal implementation change kar sakte ho without breaking code
- **Validation**: Har change pe validation add kar sakte ho

---

## 8) Polymorphism

**Polymorphism** ka matlab: **Ek naam, alag-alag kaam**

Same method name, different implementations in different classes.

### 🎭 Example 1: Method Overriding

```javascript
class Shape {
  area() {
    return 0; // Default
  }
  
  describe() {
    console.log("Main ek shape hoon");
  }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }
  
  area() {
    return Math.PI * this.radius ** 2;
  }
  
  describe() {
    console.log(`Main ${this.radius} radius ka circle hoon`);
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
  
  describe() {
    console.log(`Main ${this.width}x${this.height} ka rectangle hoon`);
  }
}

// Polymorphism in action
let shapes = [
  new Circle(5),
  new Rectangle(4, 6),
  new Circle(3)
];

shapes.forEach(shape => {
  shape.describe();
  console.log("Area:", shape.area());
  console.log("---");
});
```

**Output**:
```
Main 5 radius ka circle hoon
Area: 78.53981633974483
---
Main 4x6 ka rectangle hoon
Area: 24
---
Main 3 radius ka circle hoon
Area: 28.274333882308138
---
```

**Inner Working**:
1. Loop mein har `shape` alag object hai
2. Jab `shape.area()` call hota hai, JavaScript check karta hai actual object kya hai
3. Agar `Circle` hai to `Circle.prototype.area` call hota hai
4. Agar `Rectangle` hai to `Rectangle.prototype.area` call hota hai
5. **Runtime pe decide hota hai** kon sa method call hoga

---

### 🎨 Example 2: Real-World Payment System

```javascript
class Payment {
  constructor(amount) {
    this.amount = amount;
  }
  
  processPayment() {
    console.log("Processing payment...");
  }
}

class CreditCardPayment extends Payment {
  constructor(amount, cardNumber) {
    super(amount);
    this.cardNumber = cardNumber;
  }
  
  processPayment() {
    console.log(`Processing credit card payment of Rs.${this.amount}`);
    console.log(`Card: ****${this.cardNumber.slice(-4)}`);
  }
}

class EasypaisaPayment extends Payment {
  constructor(amount, phoneNumber) {
    super(amount);
    this.phoneNumber = phoneNumber;
  }
  
  processPayment() {
    console.log(`Processing Easypaisa payment of Rs.${this.amount}`);
    console.log(`Phone: ${this.phoneNumber}`);
  }
}

class BankTransfer extends Payment {
  constructor(amount, accountNumber) {
    super(amount);
    this.accountNumber = accountNumber;
  }
  
  processPayment() {
    console.log(`Processing bank transfer of Rs.${this.amount}`);
    console.log(`Account: ${this.accountNumber}`);
  }
}

// Polymorphism ka faida
function checkout(payment) {
  payment.processPayment(); // Kon sa method call hoga? Runtime pe decide hoga!
}

let payment1 = new CreditCardPayment(5000, "1234567890123456");
let payment2 = new EasypaisaPayment(3000, "03001234567");
let payment3 = new BankTransfer(10000, "PK12BANK0123456789");

checkout(payment1);
console.log("---");
checkout(payment2);
console.log("---");
checkout(payment3);
```

**Faida**:
- `checkout()` function ko pata nahi kon sa payment type hai
- Har payment type apna khud ka `processPayment()` implementation use karta hai
- Naye payment methods add karna easy hai
- Code flexible aur maintainable ban jata hai

---

## 9) this Keyword - Inner Working

`this` JavaScript mein **sabse tricky concept** hai. Isko samajhna bohot zaroori hai!

### 🎯 Rule 1: Regular Function Call

```javascript
function showThis() {
  console.log(this);
}

showThis(); // undefined (strict mode) ya Window (non-strict)
```

**Inner Working**:
- Simple function call mein `this` undefined hota hai (strict mode)
- Non-strict mode mein global object (`window` browser mein)

---

### 🎯 Rule 2: Method Call

```javascript
const person = {
  name: "Zohaib",
  greet() {
    console.log("Hello, I'm " + this.name);
  }
};

person.greet(); // "Hello, I'm Zohaib"
```

**Inner Working**:
```javascript
person.greet()
    ↓
greet.call(person) // this = person
    ↓
console.log("Hello, I'm " + person.name)
```

**Rule**: Jis object ne method call kiya, `this` usi ko point karega.

---

### 🎯 Rule 3: Lost `this` Context (Common Problem!)

```javascript
const person = {
  name: "Ali",
  greet() {
    console.log("Hello, I'm " + this.name);
  }
};

person.greet(); // ✅ "Hello, I'm Ali"

const greetFunction = person.greet;
greetFunction(); // ❌ "Hello, I'm undefined"
```

**Kya hua?**

**Step 1**: Method assign karte waqt
```javascript
const greetFunction = person.greet;
// Ab greetFunction ek simple function hai, object se link khatam
```

**Step 2**: Simple function call
```javascript
greetFunction(); // this = undefined (strict) ya window
```

**Solutions**:

#### Solution 1: Arrow Function
```javascript
const person = {
  name: "Sara",
  greet: () => {
    console.log("Hello, I'm " + this.name);
  }
};
// ⚠️ Arrow function apne parent scope ki `this` use karta hai
// Object method ke liye use mat karo!
```

#### Solution 2: bind()
```javascript
const person = {
  name: "Hamza",
  greet() {
    console.log("Hello, I'm " + this.name);
  }
};

const boundGreet = person.greet.bind(person);
boundGreet(); // ✅ "Hello, I'm Hamza"
```

**Inner Working of bind()**:
```javascript
person.greet.bind(person)
    ↓
// Naya function return karta hai
function boundGreet() {
  return person.greet.call(person);
}
```

#### Solution 3: Arrow Function in Class
```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
  
  // Regular method
  greet() {
    console.log("Regular: " + this.name);
  }
  
  // Arrow function property
  greetArrow = () => {
    console.log("Arrow: " + this.name);
  }
}

const p = new Person("Ahmed");

// Regular method
const regular = p.greet;
// regular(); // ❌ Error - undefined

// Arrow function
const arrow = p.greetArrow;
arrow(); // ✅ "Arrow: Ahmed" - works!
```

---

### 🎯 Rule 4: Constructor Call (`new`)

```javascript
function Car(brand) {
  this.brand = brand;
  console.log("this =", this);
}

const myCar = new Car("Toyota");
// this = Car {brand: "Toyota"}
```

**Inner Working**:
```javascript
new Car("Toyota")
    ↓
1. {} empty object
2. {}.__proto__ = Car.prototype
3. Car.call({}, "Toyota") // this = {}
4. return {} // this
```

---

### 🎯 Rule 5: Event Listeners

```javascript
const button = document.querySelector('button');

const obj = {
  count: 0,
  
  // Wrong way
  handleClickWrong() {
    console.log(this); // this = button element ❌
    this.count++; // Error!
  },
  
  // Right way - arrow function
  handleClickRight: function() {
    setTimeout(() => {
      console.log(this); // this = obj ✅
      this.count++;
    }, 1000);
  }
};

// button.addEventListener('click', obj.handleClickWrong); // ❌
// button.addEventListener('click', obj.handleClickRight.bind(obj)); // ✅
```

---

### 🎯 Rule 6: Arrow Functions

Arrow functions apni khud ki `this` nahi banate — parent scope ki `this` use karte hain.

```javascript
const obj = {
  name: "Object",
  
  regularFunc() {
    console.log("Regular:", this.name); // "Object"
    
    function inner() {
      console.log("Inner regular:", this.name); // undefined
    }
    inner();
    
    const innerArrow = () => {
      console.log("Inner arrow:", this.name); // "Object"
    };
    innerArrow();
  }
};

obj.regularFunc();
```

**Output**:
```
Regular: Object
Inner regular: undefined
Inner arrow: Object
```

**Kyu?**
- Regular function apni `this` banata hai
- Arrow function parent ki `this` inherit karta hai

---

### 📋 `this` Rules Summary Table

| Situation | `this` Value | Example |
|-----------|-------------|---------|
| Regular function call | undefined (strict) / global | `func()` |
| Method call | Object before dot | `obj.method()` |
| Constructor call (`new`) | New object created | `new Class()` |
| `call/apply/bind` | First argument | `func.call(obj)` |
| Arrow function | Parent scope's `this` | `() => {}` |
| Event listener | Element that triggered event | `el.addEventListener(...)` |

---

### 🔧 Practical Example: `this` in Classes

```javascript
class Counter {
  constructor() {
    this.count = 0;
  }
  
  // Method 1: Regular method (this can be lost)
  incrementRegular() {
    this.count++;
    console.log("Count:", this.count);
  }
  
  // Method 2: Arrow function property (this always bound)
  incrementArrow = () => {
    this.count++;
    console.log("Count:", this.count);
  }
}

const counter = new Counter();

// Direct call - dono work karenge
counter.incrementRegular(); // ✅ Count: 1
counter.incrementArrow();   // ✅ Count: 2

// Extracted reference
const regFunc = counter.incrementRegular;
const arrFunc = counter.incrementArrow;

// regFunc(); // ❌ Error - this is undefined
arrFunc();    // ✅ Count: 3 - works!
```

---

## 10) Memory Management

### 📊 Instance Properties vs Prototype Methods

```javascript
// ❌ Bad Practice - Memory Waste
class Person {
  constructor(name) {
    this.name = name;
    
    // Har instance ke liye naya function!
    this.greet = function() {
      console.log("Hi, " + this.name);
    };
  }
}

const p1 = new Person("Ali");
const p2 = new Person("Sara");

console.log(p1.greet === p2.greet); // false - alag-alag functions!
```

**Memory Diagram (Bad)**:
```
p1 ──→ { name: "Ali", greet: function }    } 2 separate
p2 ──→ { name: "Sara", greet: function }   } functions!
```

---

```javascript
// ✅ Good Practice - Shared Method
class Person {
  constructor(name) {
    this.name = name;
  }
  
  // Prototype pe method - shared!
  greet() {
    console.log("Hi, " + this.name);
  }
}

const p1 = new Person("Ali");
const p2 = new Person("Sara");

console.log(p1.greet === p2.greet); // true - same function!
```

**Memory Diagram (Good)**:
```
p1 ──→ { name: "Ali" }     }
                           } ──→ Person.prototype ──→ { greet: function }
p2 ──→ { name: "Sara" }    }                            (shared!)
```

**Memory Saving**:
- 1000 instances = 1000 separate functions (bad) vs 1 shared function (good)
- Shared method saves memory
- Faster object creation

---

### 🧹 Garbage Collection

```javascript
function createObject() {
  const obj = {
    data: new Array(1000000) // Large array
  };
  return obj;
}

let myObj = createObject();
// myObj memory mein hai

myObj = null; // Reference khatam
// Ab garbage collector memory free kar dega
```

**Process**:
1. Object create hota hai → Memory allocate
2. Reference lose hota hai → Mark for deletion
3. Garbage collector run hota hai → Memory free

---

### 💾 Closures and Memory

```javascript
function heavyOperation() {
  const bigData = new Array(1000000).fill('data');
  
  return {
    getData: () => bigData, // Closure - bigData memory mein rahega
    size: () => bigData.length
  };
}

let result = heavyOperation();
// bigData memory mein hai (closure ki wajah se)

result = null; // Ab memory free hogi
```

**Memory Leak Warning**:
```javascript
// ❌ Memory Leak Example
const cache = {};

function addToCache(key, value) {
  cache[key] = value; // Kabhi remove nahi hota!
}

// Solution: WeakMap use karo
const cache = new WeakMap();
```

---

## 11) Common Mistakes

### ❌ Mistake 1: Forgetting `new`

```javascript
function Person(name) {
  this.name = name;
}

// Wrong
const p1 = Person("Ali"); // Forgot `new`
console.log(p1);          // undefined
console.log(window.name); // "Ali" - global pollution!

// Right
const p2 = new Person("Sara");
console.log(p2.name); // "Sara"
```

**Fix with Safety Check**:
```javascript
function Person(name) {
  if (!(this instanceof Person)) {
    return new Person(name);
  }
  this.name = name;
}

const p = Person("Ahmed"); // Works without `new`!
```

---

### ❌ Mistake 2: Modifying Prototype of Built-in Objects

```javascript
// ❌ Never do this!
Array.prototype.myMethod = function() {
  return "custom";
};

const arr = [1, 2, 3];
console.log(arr.myMethod()); // Works but dangerous!
```

**Kyu galat hai?**
- Other libraries ke saath conflict
- Future JavaScript versions mein same name ka method aa sakta hai
- Debugging mushkil ho jati hai

---

### ❌ Mistake 3: Using Arrow Functions as Methods

```javascript
const obj = {
  name: "Test",
  
  // ❌ Wrong - arrow function
  greet: () => {
    console.log(this.name); // `this` is not `obj`!
  }
};

obj.greet(); // undefined
```

**Sahi tareeqa**:
```javascript
const obj = {
  name: "Test",
  
  // ✅ Right - regular method
  greet() {
    console.log(this.name);
  }
};

obj.greet(); // "Test"
```

---

### ❌ Mistake 4: Deep Inheritance Chains

```javascript
// ❌ Too deep - fragile and confusing
class A {}
class B extends A {}
class C extends B {}
class D extends C {}
class E extends D {} // Too deep!
```

**Better approach - Composition**:
```javascript
// ✅ Composition - flexible
const canWalk = {
  walk() { console.log("Walking"); }
};

const canSwim = {
  swim() { console.log("Swimming"); }
};

const canFly = {
  fly() { console.log("Flying"); }
};

// Duck can walk, swim, and fly
const duck = Object.assign({}, canWalk, canSwim, canFly);
duck.walk(); // "Walking"
duck.swim(); // "Swimming"
duck.fly();  // "Flying"
```

---

### ❌ Mistake 5: Not Understanding Prototype Chain

```javascript
function Parent() {}
Parent.prototype.value = 1;

const child1 = new Parent();
const child2 = new Parent();

// Mistake: Thinking this modifies only child1
child1.value = 2; // Creates new property on child1

console.log(child1.value); // 2 - own property
console.log(child2.value); // 1 - prototype property

// But if we modify prototype:
Parent.prototype.value = 10;
console.log(child1.value); // 2 - own property shadows prototype
console.log(child2.value); // 10 - uses prototype

// Confusing!
```

**Samajh**:
- Property assign karne se **new property** ban jati hai (shadowing)
- Prototype change karne se existing objects affected hote hain (jo apni property nahi rakhte)

---

## 12) Advanced Concepts

### 🔗 Mixins (Composition Pattern)

```javascript
// Mixin objects with behaviors
const canEat = {
  eat(food) {
    console.log(`${this.name} is eating ${food}`);
  }
};

const canWalk = {
  walk() {
    console.log(`${this.name} is walking`);
  }
};

const canSwim = {
  swim() {
    console.log(`${this.name} is swimming`);
  }
};

// Create objects by mixing behaviors
class Animal {
  constructor(name) {
    this.name = name;
  }
}

class Duck extends Animal {}
Object.assign(Duck.prototype, canEat, canWalk, canSwim);

const duck = new Duck("Donald");
duck.eat("bread");  // "Donald is eating bread"
duck.walk();        // "Donald is walking"
duck.swim();        // "Donald is swimming"
```

**Faida**:
- Reusable behaviors
- No deep inheritance
- Mix and match as needed
- More flexible than inheritance

---

### 🏭 Factory Pattern

```javascript
// Factory function - decides which object to create
function createAnimal(type, name) {
  const animals = {
    dog: class Dog {
      constructor(name) {
        this.name = name;
      }
      speak() {
        return `${this.name} says Woof!`;
      }
    },
    
    cat: class Cat {
      constructor(name) {
        this.name = name;
      }
      speak() {
        return `${this.name} says Meow!`;
      }
    }
  };
  
  const AnimalClass = animals[type];
  return new AnimalClass(name);
}

const dog = createAnimal('dog', 'Buddy');
const cat = createAnimal('cat', 'Whiskers');

console.log(dog.speak()); // "Buddy says Woof!"
console.log(cat.speak()); // "Whiskers says Meow!"
```

---

### 🎨 Singleton Pattern

```javascript
class Database {
  constructor() {
    if (Database.instance) {
      return Database.instance;
    }
    
    this.connection = "Connected";
    Database.instance = this;
  }
  
  query(sql) {
    console.log(`Executing: ${sql}`);
  }
}

const db1 = new Database();
const db2 = new Database();

console.log(db1 === db2); // true - same instance!
```

**Use case**: Jab sirf ek instance chahiye (database connection, config, etc.)

---

## 13) Real-World Complete Example

Chalo ab sab concepts ko ek saath use karke ek **complete application** banate hain:

```javascript
// Base Class with encapsulation
class Vehicle {
  #registrationNumber;
  #fuelLevel = 100;
  
  constructor(brand, model, regNumber) {
    this.brand = brand;
    this.model = model;
    this.#registrationNumber = regNumber;
  }
  
  // Getter for private field
  getRegistration() {
    return this.#registrationNumber;
  }
  
  // Protected method (convention: _method)
  _consumeFuel(amount) {
    this.#fuelLevel -= amount;
    if (this.#fuelLevel < 0) this.#fuelLevel = 0;
  }
  
  refuel() {
    this.#fuelLevel = 100;
    console.log(`${this.brand} ${this.model} refueled`);
  }
  
  getFuelLevel() {
    return this.#fuelLevel;
  }
  
  // Abstract method (to be overridden)
  start() {
    throw new Error("start() must be implemented");
  }
  
  getInfo() {
    return `${this.brand} ${this.model} (${this.getRegistration()})`;
  }
}

// Inheritance - Car
class Car extends Vehicle {
  constructor(brand, model, regNumber, doors) {
    super(brand, model, regNumber);
    this.doors = doors;
  }
  
  start() {
    this._consumeFuel(2);
    console.log(`🚗 ${this.getInfo()} started - Fuel: ${this.getFuelLevel()}%`);
  }
  
  drive(distance) {
    const fuelNeeded = distance * 0.5;
    this._consumeFuel(fuelNeeded);
    console.log(`Drove ${distance}km - Fuel: ${this.getFuelLevel()}%`);
  }
}

// Inheritance - Bike
class Bike extends Vehicle {
  constructor(brand, model, regNumber, type) {
    super(brand, model, regNumber);
    this.type = type; // sports, cruiser, etc
  }
  
  start() {
    this._consumeFuel(1);
    console.log(`🏍️  ${this.getInfo()} started - Fuel: ${this.getFuelLevel()}%`);
  }
  
  wheelie() {
    console.log(`${this.brand} doing a wheelie! 🤸`);
  }
}

// Mixin for electric vehicles
const electricMixin = {
  #batteryLevel: 100,
  
  charge() {
    this.#batteryLevel = 100;
    console.log("🔋 Battery fully charged");
  },
  
  getBattery() {
    return this.#batteryLevel;
  }
};

// Electric Car with mixin
class ElectricCar extends Car {
  constructor(brand, model, regNumber, doors) {
    super(brand, model, regNumber, doors);
    Object.assign(this, electricMixin);
  }
  
  start() {
    console.log(`⚡ ${this.getInfo()} started silently`);
  }
}

// Fleet Management System
class Fleet {
  #vehicles = [];
  
  addVehicle(vehicle) {
    this.#vehicles.push(vehicle);
    console.log(`✅ Added: ${vehicle.getInfo()}`);
  }
  
  startAll() {
    console.log("\n🚦 Starting all vehicles:");
    this.#vehicles.forEach(v => v.start());
  }
  
  refuelAll() {
    console.log("\n⛽ Refueling all vehicles:");
    this.#vehicles.forEach(v => {
      if (v.getFuelLevel() < 50) {
        v.refuel();
      }
    });
  }
  
  getReport() {
    console.log("\n📊 Fleet Report:");
    this.#vehicles.forEach(v => {
      console.log(`- ${v.getInfo()} | Fuel: ${v.getFuelLevel()}%`);
    });
  }
}

// Usage - Polymorphism in action
const fleet = new Fleet();

const car1 = new Car("Honda", "Civic", "ABC-123", 4);
const car2 = new Car("Toyota", "Corolla", "XYZ-789", 4);
const bike1 = new Bike("Yamaha", "R1", "BK-001", "sports");
const eCar = new ElectricCar("Tesla", "Model 3", "EV-100", 4);

fleet.addVehicle(car1);
fleet.addVehicle(car2);
fleet.addVehicle(bike1);
fleet.addVehicle(eCar);

fleet.startAll();

// Polymorphism - same method, different behavior
console.log("\n🛣️  Testing individual vehicles:");
car1.drive(50);
bike1.wheelie();
// eCar.charge(); // Electric car specific method

fleet.getReport();
fleet.refuelAll();
fleet.getReport();
```

**Output**:
```
✅ Added: Honda Civic (ABC-123)
✅ Added: Toyota Corolla (XYZ-789)
✅ Added: Yamaha R1 (BK-001)
✅ Added: Tesla Model 3 (EV-100)

🚦 Starting all vehicles:
🚗 Honda Civic (ABC-123) started - Fuel: 98%
🚗 Toyota Corolla (XYZ-789) started - Fuel: 98%
🏍️  Yamaha R1 (BK-001) started - Fuel: 99%
⚡ Tesla Model 3 (EV-100) started silently

🛣️  Testing individual vehicles:
Drove 50km - Fuel: 73%
Yamaha doing a wheelie! 🤸

📊 Fleet Report:
- Honda Civic (ABC-123) | Fuel: 73%
- Toyota Corolla (XYZ-789) | Fuel: 98%
- Yamaha R1 (BK-001) | Fuel: 99%
- Tesla Model 3 (EV-100) | Fuel: 100%

⛽ Refueling all vehicles:
Honda Civic refueled

📊 Fleet Report:
- Honda Civic (ABC-123) | Fuel: 100%
- Toyota Corolla (XYZ-789) | Fuel: 98%
- Yamaha R1 (BK-001) | Fuel: 99%
- Tesla Model 3 (EV-100) | Fuel: 100%
```

**Is example mein sab concepts:**
- ✅ Encapsulation (private fields)
- ✅ Inheritance (Car, Bike extend Vehicle)
- ✅ Polymorphism (different start() implementations)
- ✅ Abstraction (Fleet manages complexity)
- ✅ Composition (electricMixin)

---

## 14) Quick Cheat Sheet

### Object Creation
```javascript
// 1. Literal
const obj = { prop: "value" };

// 2. Constructor
function Obj() { this.prop = "value"; }
const o = new Obj();

// 3. Class
class Obj {
  constructor() { this.prop = "value"; }
}
const o = new Obj();

// 4. Object.create
const proto = { method() {} };
const obj = Object.create(proto);

// 5. Factory
function create() {
  return { prop: "value" };
}
```

### Inheritance
```javascript
class Parent {
  constructor(x) { this.x = x; }
  method() { return this.x; }
}

class Child extends Parent {
  constructor(x, y) {
    super(x);    // Parent constructor
    this.y = y;
  }
  
  method() {
    return super.method() + this.y; // Parent method
  }
}
```

### Private Fields
```javascript
class Example {
  #private = 0;
  
  increment() {
    this.#private++;
  }
  
  get value() {
    return this.#private;
  }
}
```

### this Binding
```javascript
// Method call
obj.method(); // this = obj

// Standalone
const fn = obj.method;
fn(); // this = undefined

// Fix with bind
const bound = obj.method.bind(obj);
bound(); // this = obj

// Fix with arrow
const arrow = () => obj.method();
arrow(); // this = obj (lexical)
```

---

## 15) Summary (آخری بات)

### Key Points (Yaad Rakhna)

1. **Objects**: Data + Methods ka bundle
2. **Prototype**: JavaScript ka inheritance system (classical nahi)
3. **Classes**: Clean syntax, internally prototypes use hote hain
4. **new keyword**: Object banata hai, prototype link karta hai, constructor call karta hai
5. **this**: Context-dependent - kis ne call kiya us pe depend karta hai
6. **Encapsulation**: Private fields (#) ya closures se data hide karo
7. **Inheritance**: extends use karke parent class se inherit karo
8. **Polymorphism**: Same method name, different implementations
9. **Composition > Inheritance**: Deep chains se bacho, mix behaviors

### Best Practices

✅ **Do**:
- Use classes for clarity
- Put methods on prototype (shared)
- Use private fields (#) for sensitive data
- Prefer composition over deep inheritance
- Use arrow functions for callbacks
- Use `super()` in child constructors
- Check `instanceof` for type checking

❌ **Don't**:
- Don't define methods in constructor
- Don't modify built-in prototypes
- Don't forget `new` with constructors
- Don't use arrow functions as object methods
- Don't create deep inheritance chains (>3 levels)
- Don't access private fields outside class

---

## 16) Practice Exercise

Apne liye ek **Library Management System** banao with:
- Book class (title, author, ISBN, available)
- Member class (name, memberID, borrowedBooks)
- Library class (manages books and members)
- Methods: borrowBook(), returnBook(), searchBook()
- Private fields for ISBN and memberID
- Inheritance for different book types (Fiction, NonFiction)

Try karo! 💪

---

**Agar koi confusion ho to poochna - main aur detail mein samjha dunga!** 🚀