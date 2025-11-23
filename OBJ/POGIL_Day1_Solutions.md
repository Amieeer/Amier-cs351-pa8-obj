# PA8 POGIL Activity - Day 1 Solutions

## Part 1: Classes Are Values

### Model 1A: First-Class Classes

**1. What type of value is `MyClass`?**

A class value. When evaluated in the REPL, `MyClass` returns a class object/reference.

**2. Can you assign a class to a variable? What does this tell you about classes in OBJ?**

Yes. This demonstrates that classes are first-class values in OBJ - they can be stored in variables, passed as arguments, and returned from functions, just like any other value.

**3. Do `obj1` and `obj2` come from the same class definition? How can you verify this?**

Yes, they come from the same class. `AnotherRef` is just another reference to `MyClass`, so both `obj1` and `obj2` are instances of the identical class definition.

### Model 1B: Fields Are Instance-Specific

**4. What values do `.<c1>getCount()` and `.<c2>getCount()` return?**

`.<c1>getCount()` returns 2, and `.<c2>getCount()` returns 1.

**5. Why are the counts different even though both objects come from the same class?**

Each object has its own independent copy of the `count` field. Fields are instance-specific, not shared between objects. The methods operate on each object's own field storage.

**6. What does `count` refer to in the context of a method? (Without any prefix?)**

It refers to the `count` field of the current object (`this`). Bare field names implicitly access the current object's fields.

**7. In Python, you write `self.count`. In OBJ, you can write just `count` or `<this>count`. What's the key difference?**

OBJ provides implicit access to the current object's members without requiring an explicit `self` or `this` prefix. The bare name automatically resolves to the current object's environment.

---

## Part 2: Methods and Constructors

### Model 2A: No Special Constructor Syntax

**8. What's the difference between how `p1` and `p2` are created?**

`p1` is created in two steps: first creating an uninitialized object, then calling `init`. `p2` uses method chaining to create and initialize in one expression: `.<new Point>init(5, 6)`.

**9. Why do `init` and `moveTo` return `this`? What does this enable?**

Returning `this` enables method chaining - you can call multiple methods in sequence on the same object in a single expression.

**10. Try: `.<.<new Point>init(1,2)>moveTo(3,4)>getX()`. What programming pattern does this demonstrate?**

Method chaining (fluent interface pattern). Each method returns the object itself, allowing successive method calls to be chained together.

### Model 2B: Understanding `<expr>member` Syntax

**11. What's the difference between `.<calc>getResult()` and `<calc>result`?**

`.<calc>getResult()` invokes a method (note the dot before the angle bracket and parentheses). `<calc>result` directly accesses a field (no dot, no parentheses).

**12. When would you use `.<obj>method()` vs `<obj>field`?**

Use `.<obj>method()` to invoke behavior/methods. Use `<obj>field` to access data/fields directly.

**13. Design Pattern Recognition: Name a real-world library or API that uses method chaining extensively.**

jQuery, Java's StringBuilder/Stream API, Python's Pandas DataFrame operations, or Builder pattern implementations in many languages.

---

## Part 3: Static Members

### Model 3A: Static vs Instance

**14. What value does `<BankAccount>totalAccounts` have after creating three accounts?**

3 - it's incremented each time an account is created.

**15. Each account has a unique `accountNumber`. How is this achieved using static members?**

Each `init` increments the static `totalAccounts` counter and assigns the new value to the instance's `accountNumber`, ensuring uniqueness.

**16. Can you access `bankName` through an object like `<acc1>bankName`? Try it. What happens?**

Yes, you can directly access static members through instance references. You can also use `<BankAccount>bankName` or access it within a method using `<myclass>bankName`.

### Model 3B: Static Inheritance

**17. What does `<self>` refer to in the `getWheels` method?**

`<self>` refers to the class of the actual object at runtime (the most derived class), not the class where the method is defined.

**18. Why does `myCar` return 4 wheels and `myBike` return 2 wheels even though both use the same `getWheels` method?**

Because `<self>` dynamically resolves to `Car` or `Motorcycle` respectively, accessing each subclass's overridden static `wheels` value.

**19. What manufacturer does the Car show? Why?**

"Generic Motors" - because `Car` doesn't override the `manufacturer` static member, so it inherits the parent's value.

**20. What advantage does `<self>` provide in inherited methods?**

It allows methods in a parent class to access static members that are overridden in subclasses, enabling polymorphic behavior for static members.

---

## Part 4: Basic Inheritance

### Model 4A: Object Structure in Inheritance

**21. Draw a diagram showing the structure of the `myDog` object. What fields does it contain?**

```
myDog object:
  Animal part:
    - name: "Buddy"
    - age: 3
  Dog part:
    - breed: "Golden Retriever"
```

The object contains three fields total: two from Animal (name, age) and one from Dog (breed).

**22. When Dog's `init` calls `.<super>init(n, a)`, what does `<super>` refer to?**

`<super>` refers to the parent class's (Animal's) object part within the current Dog object.

**23. Can Dog access Animal's fields through inheritance? How do you know?**

Yes. Both direct field access (`<myDog>name`) and method calls (`.<myDog>getName()`) work, demonstrating that inherited fields are accessible.

### Model 4B: Understanding Object Parts

**24. What happens when multiple classes in a hierarchy have the same field name?**

Each class maintains its own separate field with that name (field shadowing). The object contains multiple fields with the same name, one per class in the hierarchy.

**25. Can you access A's `x` from C's method?**

No, not directly. `<super>x` only accesses B's `x`. You'd need `<super>` twice or a different mechanism to access A's `x`.

**26. Draw the object structure showing all three `x` fields. How are they organized?**

```
obj (instance of C):
  A part: x = 1
  B part: x = 2
  C part: x = 3
```

Each class part maintains its own `x` field in a nested structure.

**27. Compare this to Java: What have you discovered about fields in OBJ?**

In OBJ, fields can be shadowed (multiple fields with the same name exist simultaneously), unlike Java where field access is determined by reference type. OBJ allows explicit navigation between shadowed fields using `<super>`.

---

## Integration Challenge

**28. What design decisions did you make about initialization?**

Example decisions: Always call `.<super>init()` first to initialize parent fields, increment `totalObjects` before assigning `id` to ensure uniqueness, return `this` for method chaining.

**29. How did you handle calling parent methods?**

Use `.<super>methodName()` to invoke parent class methods, particularly important in `init` to properly initialize inherited fields.

**30. What would you add to make this more realistic?**

Example additions: collision detection methods, rendering methods, velocity fields, type-specific behaviors (player input handling, enemy AI), health systems, etc.

---

## Reflection

**31. What surprised you most about how OBJ handles object-oriented programming?**

Answers vary: Common surprises include classes as first-class values, implicit field access without `this`, field shadowing, or the variety of environment navigation symbols.

**32. What's one thing OBJ does that you wish your favorite language did?**

Answers vary: Examples include method chaining by default, implicit `this` access, `<myclass>` for polymorphic static access, or explicit environment navigation.

**33. What's still confusing that you want to explore tomorrow?**

Answers vary: Common topics include the difference between `self` and `this`, understanding `<!@>`, complex inheritance scenarios, or dynamic dispatch.

**34. Share one "aha!" moment your group had during this activity.**

Answers vary: Examples include understanding that classes are values, realizing how field shadowing creates object parts, or discovering method chaining patterns.

---

_Course content developed by Declan Gray-Mullen for WNEU with Claude_
