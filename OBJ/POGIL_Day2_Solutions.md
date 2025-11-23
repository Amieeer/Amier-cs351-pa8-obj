# PA8 POGIL Activity - Day 2 Solutions

## Part 5: Combined Inheritance Patterns

### Model 5A: Static and Object Inheritance Together

**1. What is `<Company>employeeCount` after creating all three employees?**

3 - all employees (both Company and Manager instances) increment the same static counter in the base Company class.

**2. What is `<Manager>employeeCount`? Why?**

Also 3. Manager inherits Company's static members, so `<Manager>employeeCount` refers to the same shared counter as `<Company>employeeCount`.

**3. Why does `.<mgr1>getCompanyName()` return `"TechCorp Management"` instead of `"TechCorp"`? What would happen if we used `<myclass>` instead of `<self>`?**

"TechCorp Management" - because `<self>` dynamically resolves to Manager at runtime, accessing Manager's overridden static `companyName`. If we used `<myclass>` instead, it would return "TechCorp" because `<myclass>` is statically bound to Company (where the method is defined).

### Model 5B: Advanced Shadowing

**4. Why does `.<obj>getStaticX()` return 100 instead of 300?**

`getStaticX()` returns 100 (Base's static x) because `<myclass>` is statically bound to the class where the method is defined (Base), NOT the runtime class of the object.

**5. Why does `.<obj>getInstanceX()` return 200 instead of 400?**

`getInstanceX()` returns 200 (Base's instance x) because the method is defined in Base and accesses Base's instance field `x`, not Derived's shadowed field.

**6. When you access `<obj>x` directly, you get 400. What's the difference between direct access and method access?**

Direct access `<obj>x` accesses the most derived (outermost) field, while method access uses the field visible in the class where the method is defined. This is a key distinction in OBJ's shadowing semantics.

**7. This shadowing behavior is unlike Java/C++. What advantage might allowing field shadowing provide? What danger?**

**Advantage**: Each class can maintain its own version of a field without naming conflicts, useful for class-specific state.
**Danger**: Can be confusing which field you're accessing; accidental shadowing can hide parent fields.

---

## Part 6: Polymorphism and Dynamic Dispatch

### Model 6A: Understanding `self` vs `this`

**8. What's the difference in output between using `.<self>getType()` and `.<this>getType()`?**

`.<self>getType()` uses dynamic dispatch (returns 2 for Circle, 3 for Square). `.<this>getType()` uses static dispatch (always returns 1, Shape's version).

**9. When Shape's `describeWithSelf` method calls `.<self>getType()`, which version of `getType` runs?**

The version defined in the actual object's class (Circle's or Square's `getType`), not Shape's version. `<self>` enables polymorphic behavior.

**10. Why might you want to use `<this>` instead of `<self>` sometimes?**

When you specifically want to call the current class's implementation, not allowing subclasses to override the behavior. Useful for internal implementation details or avoiding infinite recursion.

### Model 6B: External vs Internal Dispatch

**11. What prefix code is returned when `TimestampLogger` logs a message?**

2 - TimestampLogger's overridden `getPrefix` returns 2.

**12. When `ErrorLogger`'s `logError` calls `.<super>log(msg)`, which `getPrefix` gets called?**

ErrorLogger's `getPrefix` (returning 3). Even though we call the parent's `log` method, it uses `.<self>getPrefix()`, which dynamically dispatches to ErrorLogger's version.

**13. How would the behavior change if Logger's `log` used `.<this>getPrefix()` instead?**

It would always call Logger's `getPrefix` (returning 1), defeating polymorphism. Subclasses couldn't customize the prefix behavior.

**14. This demonstrates the Template Method pattern. Can you think of a real-world example where you'd want this behavior?**

Examples: HTTP request handlers (common request processing, customizable authentication), file parsers (common structure parsing, customizable data handling), game engines (common game loop, customizable render/update methods).

---

## Part 7: Environment Navigation Symbols

### Model 7A: The Power of `<!@>` (Lexical Environment)

**15. What value does `<!@>outerVar` access?**

999 - the value of `outerVar` from the lexically enclosing let-binding scope.

**16. After changing `outerVar` to 777, what does `accessLexical` print?**

777 - `<!@>` provides a live reference to the lexical environment, so changes are visible.

**17. Can you access `outerVar` from outside the let-binding?**

No. Variables in a let-binding are only accessible within the scope of that binding and any closures that capture them via `<!@>`.

### Model 7B: Current Environment with `@`

**18. What does `@` represent?**

The current environment object itself - a first-class value representing all accessible bindings at that point in execution.

**19. How is `@` different from `<this>`?**

`@` is the current environment (lexical scope), while `<this>` is the current object. `@` includes local variables, parameters, and let-bindings; `<this>` includes fields and methods.

### Model 7C: Practical Symbol Usage

**20. What's the initial size of `obj1`? Why does it use CustomConfig's `defaultSize` even though the `init` method is defined in ConfigurableClass?**

20 - CustomConfig's `defaultSize`. The `init` method inherited from ConfigurableClass uses `<self>defaultSize`, which dynamically resolves to CustomConfig's static member at runtime.

**21. When would you use `<superclass>` instead of naming the parent class directly?**

When you want code to work correctly even if the inheritance hierarchy changes, or when writing generic code that doesn't know the specific parent class name.

**22. OBJ has 6 special symbols. Create a one-sentence description for when to use each.**

- **`self`**: Use for polymorphic method calls and accessing static members dynamically (deep binding, always refers to base object)
- **`this`**: Use to access current object part's members without polymorphism (shallow binding to current class part)
- **`super`**: Use to call parent class's overridden methods or access parent object part
- **`myclass`**: Use to access static members of the class where the code is defined (static binding, NOT runtime class)
- **`superclass`**: Use to access parent class's static members
- **`<!@>`**: Use to access variables from the lexical environment where the class was defined (closure variables)

---

## Integration Challenge: Understanding Binding (Questions 23-26)

**23. Predict each output and explain your reasoning**

Based on the code structure:
```obj
define A = class
    static s = 100
    field f
    method init = proc() { set f = 200; this }
    method testMyclass = proc() <myclass>s
    method testSelf = proc() <self>s
    method testThis = proc() <this>f
end

define B = class extends A
    static s = 300
    field f
    method init = proc() { .<super>init(); set f = 400; this }
    method testSuper = proc() <super>f
end

define obj = .<new B>init()
```

Predictions:
- `.<obj>testMyclass()` → **100** (myclass is statically bound to class A where the method is defined, returns A's s=100)
- `.<obj>testSelf()` → **300** (self dynamically resolves to B at runtime, returns B's s=300)
- `.<obj>testThis()` → **200** (this refers to the A object part where the method is defined, returns A's f=200)
- `.<obj>testSuper()` → **200** (super refers to the parent A object part, returns A's f=200)
- `<obj>f` → **400** (direct field access gets B's f=400, as B's field shadows A's field)

**24. Run the code and verify your predictions**

After running the code, all predictions should match the actual output. This demonstrates:
- Static binding with `myclass` (compile-time resolution)
- Dynamic binding with `self` (runtime resolution)
- Shallow binding with `this` and `super` (object part navigation)
- Field shadowing in inheritance

**25. For any that you got wrong, explain why the actual output differs from your prediction**

Common misconceptions:
- Thinking `myclass` would resolve dynamically (it doesn't - it's static to where code is written)
- Confusing `this` with the entire object (it's just the current class part)
- Not understanding that `super` accesses the parent's object part, not the parent class

**26. Draw an environment diagram showing the object structure and how each symbol resolves**

```
obj (B instance):
├── B part:
│   ├── f = 400
│   ├── testSuper → method accessing <super>f
│   └── methods inherited from A
└── A part:
    ├── f = 200
    ├── testMyclass → method accessing A.s (static)
    ├── testSelf → method accessing runtime class's s
    └── testThis → method accessing current part's f

Class hierarchy:
B (s = 300)
  ↑ extends
A (s = 100)

Symbol Resolution:
- myclass in A methods → A (static)
- self from obj → B (dynamic)
- this in B part → B part
- super in B part → A part
- superclass from B → A
```

This diagram shows:
1. Object structure has separate parts for each class in the hierarchy
2. Fields can be shadowed (both A and B have field f)
3. Static members belong to classes, not object parts
4. Symbol resolution depends on binding type (static/dynamic/shallow)

---

_Course content developed by Declan Gray-Mullen for WNEU with Claude_
