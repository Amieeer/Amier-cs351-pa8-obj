# PA8 POGIL Activity - Day 1: Classes as First-Class Values

## CS351 Programming Languages - OBJ Language Exploration

**Instructions**: Work in groups of 2-4. Each person should run the code examples on their machine. Discuss your observations before answering questions. The recorder should document group answers, but everyone should understand the solutions.

---

Group Member 1:
Group Member 2:
Group Member 3:
Group Member 4:

---

### Running Code Examples

**Note:** To run the code examples in this activity, you have two options:

1. **Direct REPL input**: Type or paste the code directly into the REPL after running `rep`

2. **File + Interactive mode** (Recommended for longer examples):
   - Copy the code block to a file (e.g., `example.obj`)
   - Run: `(cat example.obj; cat) | rep`
   - This executes the file first, then keeps the REPL open for follow-up experiments
   - You can reference variables and objects defined in the file

Example workflow:

```bash
# Save Program A to a file
echo "define MyClass = class
    field x
end" > programA.obj

# Run it with interactive follow-up
(cat programA.obj; cat) | rep
# Now you can type: define obj = new MyClass
# And continue experimenting with MyClass
```

This technique is especially useful when you want to test variations without retyping the base code.

---

## Part 1: Classes Are Values (20 minutes)

_Based on Lessons 1-3_

### Model 1A: First-Class Classes

Run the following in your OBJ REPL:

```obj
% Program A
define MyClass = class
    field x
end

define AnotherRef = MyClass
define obj1 = new MyClass
define obj2 = new AnotherRef

```

**Critical Thinking Questions:**

1. What type of value is `MyClass`? (Hint: try `MyClass` by itself in the REPL)

2. Can you assign a class to a variable? What does this tell you about classes in OBJ?

3. Do `obj1` and `obj2` come from the same class definition? How can you verify this?

### Model 1B: Fields Are Instance-Specific

```obj
% Program B
define Counter = class
    field count
    method init = proc() {
        set count = 0;
        this
    }
    method increment = proc()
        set count = add1(count)
    method getCount = proc()
        count
end

define c1 = .<new Counter>init()
define c2 = .<new Counter>init()
.<c1>increment()
.<c1>increment()
.<c2>increment()

```

**Critical Thinking Questions:**

4. What values do `.<c1>getCount()` and `.<c2>getCount()` return?

5. Why are the counts different even though both objects come from the same class?

6. What does `count` refer to in the context of a method? (Without any prefix?)

**Synthesis Question:**

7. In Python, you write `self.count`. In OBJ, you can write just `count` or `<this>count`. What's the key difference in how these languages handle object member access?

---

## Part 2: Methods and Constructors (20 minutes)

_Based on Lessons 3-4_

### Model 2A: No Special Constructor Syntax

```obj
% Program C
define Point = class
    field x
    field y

    method init = proc(xVal, yVal) {
        set x = xVal;
        set y = yVal;
        this  % Why return this?
    }

    method moveTo = proc(newX, newY) {
        set x = newX;
        set y = newY;
        this  % Again, why?
    }

    method getX = proc() x
    method getY = proc() y
end

% Compare these two approaches:
define p1 = new Point
.<p1>init(3, 4)

define p2 = .<new Point>init(5, 6)

```

**Critical Thinking Questions:**

8. What's the difference between how `p1` and `p2` are created?

9. Why do `init` and `moveTo` return `this`? What does this enable?

10. Try: `.<.<.<new Point>init(1,2)>moveTo(3,4)>getX()`. What programming pattern does this demonstrate?

### Model 2B: Understanding `<expr>member` Syntax

```obj
% Program D
define Calculator = class
    field result

    method init = proc() {
        set result = 0;
        this
    }

    method add = proc(n) {
        set result = +(result, n);
        this
    }

    method multiply = proc(n) {
        set result = *(result, n);
        this
    }

    method getResult = proc()
        result
end

define calc = .<new Calculator>init()

% Try different ways to access result:
.<calc>add(5)
.<calc>multiply(3)
define answer = .<calc>getResult()

% What about this?
define directAccess = <calc>result  % Does this work?

```

**Critical Thinking Questions:**

11. What's the difference between `.<calc>getResult()` and `<calc>result`?

12. When would you use `.<obj>method()` vs `<obj>field`?

**Synthesis Question:**

13. Design Pattern Recognition: The pattern of returning `this` from methods is called "fluent interface" or "method chaining". Name a real-world library or API that uses this pattern extensively.

---

## Part 3: Static Members (20 minutes)

_Based on Lessons 5-7_

### Model 3A: Static vs Instance

```obj
% Program E
define BankAccount = class
    static bankName = "First National OBJ"
    static totalAccounts = 0

    field accountNumber
    field balance

    method init = proc(initialDeposit) {
        set balance = initialDeposit;
        set <BankAccount>totalAccounts = add1(<BankAccount>totalAccounts);
        set accountNumber = <BankAccount>totalAccounts;
        this
    }

    method getBalance = proc() balance
    method getAccountNumber = proc() accountNumber
    method getBankName = proc() <BankAccount>bankName
end

define acc1 = .<new BankAccount>init(100)
define acc2 = .<new BankAccount>init(200)
define acc3 = .<new BankAccount>init(300)

.<acc1>getAccountNumber()
.<acc2>getAccountNumber()
.<acc3>getAccountNumber()

% Check the static counter:
<BankAccount>totalAccounts

```

**Critical Thinking Questions:**

14. What value does `<BankAccount>totalAccounts` have after creating three accounts?

15. Each account has a unique `accountNumber`. How is this achieved using static members?

16. Can you access `bankName` through an object like `<acc1>bankName`? Try it. What happens?

### Model 3B: Static Inheritance

```obj
% Program F
define Vehicle = class
    static wheels = 0  % Default, to be overridden
    static manufacturer = "Generic Motors"

    field vin

    method init = proc(vinNumber) {
        set vin = vinNumber;
        this
    }

    method getWheels = proc() <self>wheels
    method getManufacturer = proc() <self>manufacturer
    method getVIN = proc() vin
end

define Car = class extends Vehicle
    static wheels = 4
end

define Motorcycle = class extends Vehicle
    static wheels = 2
    static manufacturer = "Speed Bikes Inc"
end

define myCar = .<new Car>init("CAR123")
define myBike = .<new Motorcycle>init("BIKE456")

.<myCar>getWheels()      % 4
.<myBike>getWheels()     % 2
.<myCar>getManufacturer()  % "Generic Motors"
.<myBike>getManufacturer() % "Speed Bikes Inc"

```

**Critical Thinking Questions:**

17. What does `<self>` refer to in the `getWheels` method?

18. Why does `myCar` return 4 wheels and `myBike` return 2 wheels even though both use the same `getWheels` method?

19. What manufacturer does the Car show? Why?

**Synthesis Question:**

20. This is different from Java/C++ where static members are accessed via the class name. What advantage does `<self>` provide in inherited methods?

---

## Part 4: Basic Inheritance (20 minutes)

_Based on Lessons 6-8_

### Model 4A: Object Structure in Inheritance

```obj
% Program G
define Animal = class
    field name
    field age

    method init = proc(n, a) {
        set name = n;
        set age = a;
        this
    }

    method getName = proc() name
    method getAge = proc() age
end

define Dog = class extends Animal
    field breed

    method init = proc(n, a, b) {
        .<super>init(n, a);  % Call parent's init
        set breed = b;
        this
    }

    method getBreed = proc() breed
end

define myDog = .<new Dog>init("Buddy", 3, "Golden Retriever")

% Can we access parent fields through methods?
.<myDog>getName()    % "Buddy"
.<myDog>getAge()     % 3
.<myDog>getBreed()   % "Golden Retriever"

% Can we access fields directly?
<myDog>name    % "Buddy"
<myDog>age     % 3
<myDog>breed   % "Golden Retriever"

```

**Critical Thinking Questions:**

21. Draw a diagram showing the structure of the `myDog` object. What fields does it contain?

22. When Dog's `init` calls `.<super>init(n, a)`, what does `<super>` refer to?

23. Can Dog access Animal's fields through inheritance? How do you know?

### Model 4B: Understanding Object Parts

```obj
% Program H
define A = class
    field x
    method init = proc() {
        set x = 1;
        this
    }
    method getX = proc() x
end

define B = class extends A
    field x  % Shadow parent's x!
    method init = proc() {
        .<super>init();
        set x = 2;
        this
    }
end

define C = class extends B
    field x  % Shadow again!
    method init = proc() {
        .<super>init();
        set x = 3;
        this
    }
    method getMyX = proc() x
    method getParentX = proc() <super>x
end

define obj = .<new C>init()
.<obj>getMyX()      % 3
.<obj>getParentX()  % 2
% Can we get A's x? Not directly from C...

```

**Critical Thinking Questions:**

24. What happens when multiple classes in a hierarchy have the same field name?

25. Can you access A's `x` from C's method? (This is a limitation we'll address next Class!)

26. Draw the object structure showing all three `x` fields. How are they organized?

**Synthesis Question:**

27. Compare this to Java: In Java, fields are not virtual/polymorphic but methods are. What have you discovered about fields in OBJ?

---

## Integration Challenge (10 minutes)

### Design Your Own Class Hierarchy

Working as a group, design a small class hierarchy for a simple game:

```obj
% Your task: Complete this hierarchy
define GameObject = class
    static totalObjects = 0
    field x
    field y
    field id

    method init = proc(xPos, yPos) {
        % TODO: Initialize fields, increment totalObjects, assign unique id
        % set x = ?
        % set y = ?
        % set <GameObject>totalObjects = ?
        % set id = ?
        % this
    }

    method move = proc(dx, dy) {
        % TODO: Update position
        % set x = ?
        % set y = ?
        % this
    }

end

define Player = class extends GameObject
    field health
    field score

    % TODO: Add init method that calls super and sets health=100, score=0
    % method init = proc(xPos, yPos) {
    %     .<super>init(xPos, yPos);
    %     set health = 100;
    %     set score = 0;
    %     this
    % }

    % TODO: Add takeDamage method that reduces health

    % TODO: Add addPoints method that increases score
end

define Enemy = class extends GameObject
    field damage

    % TODO: Add appropriate methods
end

% Test your hierarchy:
% - Create 2 players and 3 enemies
% - Move them around
% - Have players take damage
% - Check that totalObjects is correct
```

**Group Discussion:**

28. What design decisions did you make about initialization?

29. How did you handle calling parent methods?

30. What would you add to make this more realistic?

---

---

## Day 1 Summary

Today you discovered:

- Classes are first-class values (can be assigned to variables)
- Objects have independent field instances
- Methods can return `this` for chaining
- Static members belong to classes, not instances
- Inheritance creates object parts with potentially shadowed fields
- The `<expr>member` syntax for navigating different environments

Tomorrow we'll explore:

- Polymorphism and dynamic dispatch
- Advanced scope symbols (`self`, `myclass`, `superclass`, `<!@>`)
- How to navigate complex inheritance hierarchies
- The difference between shallow and deep binding

---

_Course content developed by Declan Gray-Mullen for WNEU with Claude_
