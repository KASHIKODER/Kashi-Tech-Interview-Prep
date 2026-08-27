# 🎤 CORE JAVA + OOPS — FULL INTERVIEW SIMULATION NOTES
### Kashi ke liye — TCS NQT / Cognizant / Accenture Technical Round Ready

---

## 📖 IS FILE KO KAISE USE KARNA HAI

Bhai ab format change hai jaisa tune bola. Har topic mein:

```
🎤 Interview Question   → Interviewer exactly aise poochega (English mein, jaisa asli round mein hota hai)
💡 Interviewer ka Intent → Hinglish mein — wo ye kyun poochh raha hai, kya test kar raha hai
✅ Professional Answer  → Ye bologe (jaisa ka taisa bol sakte ho, polished aur confident)
🔁 Follow-up Questions  → Interviewer isi answer pe next kya poochega, chain mein
```

Answers **bologe jaise hain waise hi bol do** — ye already interview-ready hain. Bas practice karo bolne ki, ratna mat.

---

# PART 1 — CLASS & OBJECT

### 🎤 "Can you explain the difference between a class and an object?"

**💡 Intent:** Basic hai, lekin interviewer check karta hai ki tumhari foundation clear hai ya nahi — kyunki isi pe sab kuch tika hai.

**✅ Answer:**
"A class is a blueprint or template that defines the properties and behavior that its objects will have. An object is an actual instance of that class, created in memory, with its own specific values for those properties. For example, `Car` would be a class defining attributes like color and brand, and behavior like `drive()`. When I do `Car myCar = new Car()`, `myCar` becomes an object — a real instance with its own data, like a red Hyundai i20."

### 🔁 Follow-up: "Where exactly is memory allocated for an object — stack or heap?"
**Answer:** "The object itself, along with its instance variables, is allocated on the **heap**. Only the reference variable pointing to that object — like `myCar` — lives on the **stack**. So when I write `Car myCar = new Car()`, the reference `myCar` is on the stack, and it points to the actual object data sitting on the heap."

### 🔁 Follow-up: "Can two objects of the same class have different data at runtime?"
**Answer:** "Yes, absolutely. That's actually the core benefit of using classes — each object maintains its own independent set of instance variable values. So `Car car1` could be red with a petrol engine, and `Car car2` could be blue with a diesel engine, even though they share the same class definition and methods."

---

# PART 2 — CONSTRUCTORS

### 🎤 "What is a constructor, and why do we need it if we already have setters?"

**💡 Intent:** Ye poochke interviewer check karta hai ki tumhe pata hai constructor ka **real purpose** kya hai — sirf syntax nahi.

**✅ Answer:**
"A constructor is a special block that runs automatically the moment an object is created, and its job is to put the object into a valid initial state. The reason we prefer it over setters is that with setters, there's a window where the object exists but has incomplete or default data — which can lead to bugs if someone accesses it before setting values. A constructor guarantees the object is meaningfully initialized right from the moment it's born."

### 🔁 Follow-up: "What are the different types of constructors in Java?"
**Answer:** "There are four main types I'd highlight. First, the **default constructor** — a no-argument constructor Java provides automatically if we don't write one, which sets primitive fields to zero-equivalent values and object references to null. Second, the **parameterized constructor**, where we pass actual values at creation time. Third, the **copy constructor**, which takes another object of the same type and copies its data into the new object. And fourth, a **private constructor**, which restricts object creation from outside the class — this is the backbone of the Singleton design pattern."

### 🔁 Follow-up: "If I write `Movie copy = original;` instead of using a copy constructor, what happens?"
**Answer:** "That line doesn't create a new object at all — it just copies the reference. Both `copy` and `original` end up pointing to the exact same object in memory. So if I modify `copy.title`, `original.title` changes too, because there's really only one object being shared between two variable names. To actually get an independent object, I need to call `new` with a copy constructor that explicitly copies each field."

### 🔁 Follow-up: "Can a constructor be static, final, or abstract?"
**Answer:** "No to all three. It can't be `static` because a constructor is tied to object creation, not the class itself. It can't be `final` because final is meant to prevent overriding, and constructors aren't inherited in the first place, so that restriction is meaningless. And it can't be `abstract` because a constructor must always provide a concrete implementation — there's no 'future child will implement it' concept for constructors."

---

# PART 3 — `this` KEYWORD

### 🎤 "What is the use of the `this` keyword in Java?"

**✅ Answer:**
"`this` refers to the current object — the instance on which a method or constructor is currently operating. I mainly use it in four situations: first, to resolve naming conflicts between instance variables and parameters, like `this.name = name`. Second, for constructor chaining, calling one constructor from another within the same class using `this(...)`. Third, for method chaining, where a method returns `this` so I can call another method immediately after, like in a builder pattern. And fourth, to pass the current object as an argument to another method."

### 🔁 Follow-up: "Why can't `this` be used inside a static method?"
**Answer:** "Because `this` refers to a specific object instance, but a static method belongs to the class itself and can be called without ever creating an object — so there's no 'current object' for `this` to point to. That's a compile-time error if attempted."

---

# PART 4 — `static` KEYWORD

### 🎤 "Explain the `static` keyword and where you'd use it."

**💡 Intent:** Ye ek bahut common Java fresher question hai — interviewer check karta hai ki static vs instance ka mental model clear hai ya nahi.

**✅ Answer:**
"`static` means a member belongs to the class as a whole rather than to any individual object. A **static variable** is shared across all objects of that class — there's only one copy in memory, regardless of how many objects exist. A **static method** can be called directly on the class without creating an object, and it can only directly access other static members, not instance variables, because at the time it's invoked, no specific object may even exist yet. A **static block** runs once when the class is loaded, which is useful for one-time setup like initializing a configuration or a database connection."

### 🔁 Follow-up: "Can a static method access an instance variable directly? Why or why not?"
**Answer:** "No, it can't. A static method can be called even before any object of the class exists — for instance, `ClassName.method()` doesn't require `new`. Instance variables only exist once an object has been created. So the compiler has no guarantee an instance variable even exists at the point the static method runs, which is why direct access isn't allowed."

### 🔁 Follow-up: "Can a static method be overridden?"
**Answer:** "No — static methods can't be truly overridden, only *hidden*. If a subclass defines a static method with the same signature as one in the parent, it doesn't participate in runtime polymorphism. Which method gets called is decided at compile time based on the reference type, not the actual object type. This is called **method hiding**, and it's a common interview trap because it looks like overriding syntactically but behaves completely differently."

### 🔁 Follow-up: "So how is that different from regular method overriding?"
**Answer:** "Regular overriding is resolved at runtime using dynamic binding — the JVM checks the actual object type to decide which method to invoke. Static method hiding is resolved at compile time using static binding — it just looks at the reference variable's declared type. So `Parent p = new Child(); p.staticMethod();` would call `Parent`'s version, while `p.instanceMethod()` would call `Child`'s overridden version."

---

# PART 5 — `final` KEYWORD

### 🎤 "What does the `final` keyword mean in different contexts — variable, method, and class?"

**💡 Intent:** Interviewer check karta hai ki tumhe pata hai `final` ka meaning **context ke hisaab se change** hota hai — ye ek subtle but frequently asked distinction hai.

**✅ Answer:**
"`final` behaves differently depending on where it's applied. A **final variable** can be assigned only once — after that, its value or reference is locked and cannot be reassigned. A **final method** cannot be overridden by any subclass, which is useful when I want to guarantee that a piece of core logic can never be changed by inheritance. And a **final class** cannot be extended at all — `String` and `Integer` are classic examples, they're marked final so no one can create a subclass and alter their guaranteed immutable behavior."

### 🔁 Follow-up: "If I mark an object reference as `final`, can I still change the object's internal state?"
**Answer:** "Yes. `final` on a reference only prevents me from reassigning that reference to point to a different object — it doesn't make the object itself immutable. So if I have `final List<Integer> list = new ArrayList<>();`, I can still add or remove elements from that list; I just can't do `list = new ArrayList<>();` again."

---

# PART 6 — POLYMORPHISM: OVERLOADING vs OVERRIDING

### 🎤 "What is polymorphism, and how many types are there in Java?"

**✅ Answer:**
"Polymorphism means the same operation can behave differently depending on the context it's used in. Java supports two types: **compile-time polymorphism**, achieved through method overloading, where the compiler decides which method to call based on the arguments provided; and **runtime polymorphism**, achieved through method overriding, where the JVM decides which method to call based on the actual object type at runtime."

### 🔁 Follow-up: "What are the exact rules for method overloading?"
**Answer:** "For a method to be considered overloaded, it must have the same name but a different parameter list — either a different number of parameters or different parameter types, or both. Importantly, changing only the return type is **not** sufficient — if two methods have the same name and identical parameter types, differing only in return type, that's a compile-time error, because Java identifies methods by their signature, and return type isn't part of the signature."

### 🔁 Follow-up: "Give me a scenario where overloading would fail to compile."
**Answer:** "If I define `int add(int a, int b)` and then try to define `double add(int a, int b)` in the same class, that would fail to compile with a 'method already defined' error — because the parameter list is identical, and the compiler has no way to decide which version to call based on the call site alone."

### 🔁 Follow-up: "Now explain method overriding — what are the rules there?"
**Answer:** "For overriding, the method name and the parameter list must be exactly the same as in the parent class. The return type must be the same, or a covariant subtype of the original return type. The access modifier in the child class can be the same or more permissive than the parent's — but not more restrictive. And private, static, and final methods cannot be overridden at all."

### 🔁 Follow-up: "This is the most important one — what's the actual difference between overloading and overriding, in terms of when they're resolved?"
**Answer:** "Overloading is resolved at **compile time** — this is called static or early binding, because the compiler has all the information it needs just from the method call's arguments. Overriding is resolved at **runtime** — this is called dynamic or late binding, because the JVM has to check the actual object the reference is pointing to before deciding which overridden version to execute. This is exactly why a parent-type reference pointing to a child object still calls the child's overridden method — the decision is deferred to runtime."

### 🔁 Follow-up: "Can constructors be overloaded? Can they be overridden?"
**Answer:** "Constructors can absolutely be overloaded — that's how we get default, parameterized, and copy constructors coexisting in one class. But constructors **cannot** be overridden, because they aren't inherited by subclasses in the first place — each class must define its own."

---

# PART 7 — INHERITANCE

### 🎤 "What is inheritance, and why doesn't Java support multiple inheritance through classes?"

**✅ Answer:**
"Inheritance allows a subclass to acquire the properties and methods of a superclass, representing an 'is-a' relationship — for example, a Dog is an Animal. Java doesn't allow a class to extend two classes because it would create the **diamond problem** — if two parent classes both define a method with the same signature, and a child inherits from both, the compiler has no unambiguous way to decide which version to use. To avoid this ambiguity entirely, Java restricts classes to single inheritance, but allows a class to implement multiple interfaces instead, since interfaces force the implementing class to explicitly provide its own version of any conflicting method."

### 🔁 Follow-up: "What are the types of inheritance Java supports?"
**Answer:** "Java supports single inheritance — one parent, one child; multilevel inheritance — a chain like Animal to Mammal to Dog; and hierarchical inheritance — one parent with multiple children, like Animal being extended by both Dog and Cat. True multiple inheritance through classes isn't supported, but a hybrid model combining class inheritance with multiple interface implementation is."

### 🔁 Follow-up: "What's a downside of overusing inheritance?"
**Answer:** "The biggest downside is tight coupling — if I change something in the parent class, it can unintentionally break behavior in child classes that depend on it. This is actually one of the reasons the design principle 'favor composition over inheritance' exists — composition tends to be more flexible because it avoids that rigid parent-child dependency."

---

# PART 8 — ENCAPSULATION

### 🎤 "What is encapsulation, and how does it differ from abstraction? People often confuse these two."

**💡 Intent:** Ye ek classic trap question hai — almost har interview mein poocha jata hai kyunki candidates definition confuse karte hain.

**✅ Answer:**
"Encapsulation is about bundling data with the methods that operate on it, and restricting direct access to that data — typically by making fields private and exposing controlled access through getters and setters. It's primarily about **data protection and security**. Abstraction, on the other hand, is about hiding implementation complexity and exposing only the essential functionality — it's about **simplifying what the user needs to know**, not about protecting data. So encapsulation controls 'who can touch this data,' while abstraction controls 'how much of the internal logic does the user even need to see.' Both often work together, but they solve different problems."

### 🔁 Follow-up: "Can you give an example where encapsulation prevents a bug?"
**Answer:** "Sure — in a `BankAccount` class, if `balance` were a public field, any code could directly do `account.balance = -5000`, creating an invalid state. By making `balance` private and only exposing a `deposit()` and `withdraw()` method, I can add validation logic — for instance, rejecting a withdrawal that exceeds the current balance — ensuring the balance can only ever be changed in valid, controlled ways."

---

# PART 9 — ABSTRACTION: ABSTRACT CLASS vs INTERFACE

### 🎤 "What is abstraction? Can you give a real-world example?"

**✅ Answer:**
"Abstraction means exposing only the essential behavior to the user while hiding the internal implementation details. A good real-world example is driving a car — I interact with the accelerator, brake, and steering wheel, but I don't need to know how the internal combustion engine or fuel injection system works. Similarly, in code, a class can expose a simple method like `startEngine()` while hiding all the underlying complexity of how that actually happens internally."

### 🔁 Follow-up: "How is abstraction actually implemented in Java?"
**Answer:** "Through two mechanisms — abstract classes and interfaces. An abstract class can mix abstract methods, which have no implementation and must be overridden, with concrete methods that provide shared, reusable behavior. It also supports constructors and any type of variable. An interface, on the other hand, purely defines a contract of behavior — traditionally all its methods were implicitly public and abstract, though since Java 8 it can also include default and static methods with actual implementations."

### 🔁 Follow-up: "So when would you choose an abstract class over an interface, and vice versa?"
**Answer:** "I'd reach for an **abstract class** when multiple related classes share common state or common implementation logic that I don't want to duplicate — say, a `sleep()` method that's identical for every Animal subclass. I'd reach for an **interface** when I just need to define a contract that unrelated classes must follow, or when I need a class to exhibit multiple independent behaviors, since a class can implement any number of interfaces but extend only one class."

### 🔁 Follow-up: "Why were default methods introduced in interfaces in Java 8?"
**Answer:** "Purely for backward compatibility. Before Java 8, if you added a new method to an interface, every class that already implemented that interface would break, because they'd now be missing a mandatory method implementation. Default methods solve this — they provide a default body, so existing implementing classes continue to compile and work without modification, while still having the option to override it if they need custom behavior."

### 🔁 Follow-up: "What happens if a class implements two interfaces that both have a default method with the same signature?"
**Answer:** "That reintroduces the diamond problem, and Java doesn't resolve it automatically — it's a compile-time error unless the implementing class explicitly overrides that method itself to resolve the conflict. This forces the developer to make a deliberate choice rather than leaving any ambiguity."

---

# PART 10 — ACCESS MODIFIERS

### 🎤 "Explain the four access modifiers and their visibility scope."

**✅ Answer:**
"`private` restricts access to within the same class only. Default, or package-private, allows access within the same package. `protected` extends that to subclasses even in different packages, in addition to the same package. And `public` allows access from anywhere in the application, with no restriction at all. As a general rule, I'd default to the most restrictive modifier that still meets the requirement — this keeps encapsulation strong."

---

# PART 11 — CLASS RELATIONSHIPS (UML / LLD)

### 🎤 "In LLD, how would you differentiate Association, Aggregation, Composition, and Dependency?"

**✅ Answer:**
"Association is a general 'has-a' relationship where two objects are aware of each other but exist completely independently — like a Person having a Car. Aggregation is a special case of association where one object contains a collection of others, but those others can still exist independently — like a Team having Players who could join a different team. Composition is a stronger form where the contained object cannot exist without the container — like a House having Rooms; a Room has no meaning outside a House. Dependency is the loosest and most temporary relationship — one class simply uses another inside a method, typically as a parameter, without storing a long-term reference to it — like a Document using a Printer only within a `print()` method."

### 🔁 Follow-up: "In an actual coding interview, do you draw all of these diagrams?"
**Answer:** "Not usually — in a live LLD coding round, I focus on writing the actual classes and their relationships directly in code rather than drawing formal UML diagrams, since that's what's being evaluated. But understanding these relationships helps me reason about whether to store a reference as a field (association/composition) or just pass it as a method parameter (dependency), which directly shapes how I design the classes."

---

# PART 12 — GENERICS & WILDCARDS

### 🎤 "Why do we use generics, and what's the difference between generics and wildcards?"

**✅ Answer:**
"Generics let me write a single class or method that works with multiple data types while still maintaining compile-time type safety, avoiding the need for manual type casting and preventing runtime `ClassCastException`s. The key difference with wildcards is that generics use a known, fixed type parameter — like `T` — consistently throughout the method or class, whereas a wildcard, denoted `?`, represents an unknown type and is primarily useful for read-only operations where the exact type doesn't matter to the logic. An upper-bounded wildcard, `? extends Number`, is good for reading values safely, while a lower-bounded wildcard, `? super Integer`, is good for writing values safely."

---

# PART 13 — STRING HANDLING

### 🎤 "Why is the String class immutable in Java?"

**💡 Intent:** Ye almost guaranteed poocha jata hai — interviewer thread-safety aur security ka reasoning sunna chahta hai, sirf "haan immutable hai" nahi.

**✅ Answer:**
"String is made immutable for a few deliberate reasons. First, **security** — Strings are widely used for things like file paths, network connections, and database URLs, so if a String could be modified after being passed around, it could be a serious vulnerability. Second, the **String pool** — Java maintains a pool of String literals to save memory, and this only works safely if Strings can't change, since multiple references might be pointing to the same pooled object. Third, **thread safety** — an immutable object can be shared across threads without any synchronization concerns, since its state can never change. And fourth, it enables **safe caching of the hashCode**, which makes Strings efficient as HashMap keys."

### 🔁 Follow-up: "What is the String pool, and how does `new String("abc")` differ from `"abc"` directly?"
**Answer:** "The String pool is a special memory area where String literals are stored and reused. When I write `String a = "abc";`, Java checks the pool first — if `"abc"` already exists there, it reuses that same reference rather than creating a new object. But `new String("abc")` explicitly forces the creation of a brand-new object on the heap, outside the pool, even if `"abc"` already exists in the pool. So `a == b` would be false if `b` was created with `new String("abc")`, even though `a.equals(b)` would be true, because `==` compares references while `.equals()` compares actual content."

### 🔁 Follow-up: "When would you use StringBuilder or StringBuffer instead of String?"
**Answer:** "When I need to perform a lot of string concatenation or modification, especially in a loop. Since String is immutable, every concatenation like `s = s + "x"` actually creates a brand-new String object, which is inefficient and creates unnecessary garbage for the collector to clean up. `StringBuilder` is mutable and modifies its internal buffer directly, making it much more efficient for repeated modifications. The only difference between `StringBuilder` and `StringBuffer` is that `StringBuffer` is synchronized and thread-safe, while `StringBuilder` is not — so I'd use `StringBuilder` in a single-threaded context for better performance, and `StringBuffer` only if multiple threads are modifying the same buffer concurrently."

---

# PART 14 — OBJECT CLASS METHODS: equals(), hashCode(), toString()

### 🎤 "Why should you override both `equals()` and `hashCode()` together, and not just one of them?"

**💡 Intent:** Ye bahut deep aur bahut commonly asked hai — especially Collections ke context mein.

**✅ Answer:**
"There's a strict contract in Java: if two objects are considered equal by `equals()`, they **must** produce the same `hashCode()`. This matters because hash-based collections like `HashMap` and `HashSet` use `hashCode()` first to locate the correct bucket, and only then use `equals()` to confirm an exact match within that bucket. If I override `equals()` to say two objects are logically equal, but leave the default `hashCode()` — which is typically based on memory address — those two 'equal' objects could end up in completely different buckets. That means a `HashSet` could end up storing duplicate entries that should have been considered the same, silently breaking collection behavior."

### 🔁 Follow-up: "What does overriding `toString()` actually give you?"
**Answer:** "By default, `toString()` returns something like `ClassName@hashcode`, which isn't very readable. Overriding it lets me return a meaningful, human-readable representation of the object's state — which is especially useful for debugging, logging, and printing objects directly, since something like `System.out.println(obj)` implicitly calls `toString()`."

---

# PART 15 — WRAPPER CLASSES

### 🎤 "Why does Java need wrapper classes when it already has primitive types?"

**✅ Answer:**
"Primitive types like `int` or `boolean` aren't objects — they can't be stored in Collections, which only work with objects, and they can't have methods called on them. Wrapper classes like `Integer` and `Boolean` provide an object representation of each primitive, allowing them to be used wherever an object is required — for example, storing integers in an `ArrayList<Integer>`. Java also handles the conversion automatically through autoboxing and unboxing, so I rarely need to convert manually."

### 🔁 Follow-up: "If I write `Integer a = 127; Integer b = 127; System.out.println(a == b);` — what's the output, and why?"
**Answer:** "That prints `true`, which surprises a lot of people. Java maintains an **Integer cache** for values between -128 and 127 — since these are extremely commonly used values, autoboxed Integers in this range are cached and reused rather than creating a new object each time. So `a` and `b` actually point to the same cached object. But if I used 200 instead of 127, `a == b` would print `false`, because outside that cached range, each autoboxing creates a distinct new object. This is exactly why I always use `.equals()` to compare wrapper object values, and never rely on `==`."

---

# PART 16 — COLLECTIONS FRAMEWORK

### 🎤 "Compare ArrayList and LinkedList — when would you choose one over the other?"

**✅ Answer:**
"`ArrayList` is backed internally by a dynamic array, so random access by index is very fast — constant time. But inserting or deleting an element in the middle requires shifting subsequent elements, which is linear time. `LinkedList` is backed by a doubly linked list, so insertion and deletion — especially at the beginning or if I already have a reference to the node — are constant time, but accessing an arbitrary index requires traversing from the head or tail, making it linear time. In practice, I'd choose `ArrayList` when I'm doing a lot of read or index-based access, and `LinkedList` when I'm doing frequent insertions and deletions, particularly at the ends."

### 🔁 Follow-up: "Walk me through how HashMap actually works internally."
**Answer:** "When I call `put(key, value)`, HashMap first computes `key.hashCode()`, and uses that to determine which bucket — essentially an index in an internal array — the entry should go into. If another key already hashes to the same bucket, that's called a **collision**, and the entries are stored together, traditionally as a linked list within that bucket, though since Java 8, if a bucket gets too many entries, it converts to a balanced red-black tree for better worst-case performance. When I call `get(key)`, HashMap recomputes the hash to jump straight to the correct bucket, and then uses `equals()` to confirm the exact key match within that bucket, since multiple keys can share a bucket."

### 🔁 Follow-up: "What's the difference between Comparable and Comparator?"
**Answer:** "`Comparable` is implemented directly inside the class whose objects need to be sorted, through a single `compareTo()` method — this defines the class's one natural ordering. `Comparator` is a separate class or lambda expression that defines a custom ordering externally, through a `compare()` method, without modifying the original class at all. The main practical difference is flexibility: with `Comparable` I get exactly one sorting logic, but with `Comparator` I can define as many different sorting strategies as I need, and apply whichever one is relevant at the call site."

### 🔁 Follow-up: "How would HashSet's `add()` behave differently if I hadn't properly overridden `equals()` and `hashCode()` on my custom class?"
**Answer:** "If those methods aren't overridden, `HashSet` falls back to `Object`'s default implementations, which compare by memory reference rather than logical content. So even if I add two objects that I consider 'the same' based on their field values, `HashSet` would treat them as distinct and allow both in — because their default hash codes and reference-based equality checks would differ. This is exactly why the equals-hashCode contract matters so much for custom objects used in hash-based collections."

---

# PART 17 — EXCEPTION HANDLING

### 🎤 "What's the difference between checked and unchecked exceptions?"

**✅ Answer:**
"Checked exceptions are checked by the compiler at compile time — if a method can throw one, I'm forced to either handle it with a try-catch or declare it with `throws`. Examples include `IOException` and `SQLException`, typically representing external, recoverable conditions like file access or database issues. Unchecked exceptions, which extend `RuntimeException`, aren't checked at compile time — handling them is optional, and they usually represent programming errors, like `NullPointerException` or `ArrayIndexOutOfBoundsException`, that ideally should be prevented through good code rather than caught everywhere."

### 🔁 Follow-up: "What's the difference between `throw` and `throws`?"
**Answer:** "`throw` is used inside a method body to actually raise a specific exception instance at that point in the code — for example, `throw new IllegalArgumentException(\"invalid input\")`. `throws` is used in a method's signature to declare that this method might propagate a certain exception up to its caller, without necessarily handling it itself — it's a warning to whoever calls this method that they need to handle or further propagate that exception."

### 🔁 Follow-up: "Does the `finally` block always execute, even if there's a `return` in the try block?"
**Answer:** "Yes, `finally` executes in almost every case, even if there's a `return` statement inside the `try` or `catch` block — the JVM will execute the `finally` block before actually returning control to the caller. The only situations where `finally` won't run are if the JVM itself terminates abruptly, for example through `System.exit()`, or if the thread is forcibly killed."

### 🔁 Follow-up: "Why would you create a custom exception class instead of just throwing a generic `Exception`?"
**Answer:** "A custom exception makes the code far more expressive and easier to handle precisely. If I throw `new InsufficientBalanceException(\"...\")` instead of a generic `Exception`, calling code can catch that specific type and respond appropriately, without accidentally swallowing unrelated exceptions in the same catch block. It also makes stack traces and logs much clearer about what actually went wrong from a business logic perspective."

---

# PART 18 — ITERATOR vs ENUMERATION

### 🎤 "What's the difference between Iterator and Enumeration, and why would you prefer one over the other?"

**✅ Answer:**
"Enumeration is a legacy interface from Java 1.0, used mainly with older classes like `Vector` and `Hashtable`. Iterator was introduced with the Collections Framework and is the standard way to traverse modern collections like `ArrayList` and `HashSet`. The key practical advantage of Iterator is its `remove()` method — it lets me safely remove an element from the underlying collection while I'm actively iterating over it, which Enumeration simply doesn't support at all. Iterator is also **fail-fast** — if the underlying collection is structurally modified by any means other than the iterator's own `remove()`, it immediately throws a `ConcurrentModificationException`, which helps catch bugs early rather than allowing unpredictable behavior."

### 🔁 Follow-up: "When exactly would ConcurrentModificationException be thrown, and how do you avoid it?"
**Answer:** "It gets thrown when I modify a collection directly — say, calling `list.remove(item)` — while iterating over it with a for-each loop or an Iterator, because the internal modification counter the Iterator tracks no longer matches what it expects. To avoid it, I should call `iterator.remove()` directly on the Iterator object instead of modifying the collection through its own reference during traversal — that keeps the internal state consistent."

---

# PART 19 — MULTITHREADING BASICS

### 🎤 "What's the difference between extending Thread and implementing Runnable?"

**✅ Answer:**
"Both let me define a task to run on a separate thread, but there's an important design difference. If I extend `Thread`, my class can't extend any other class, since Java doesn't support multiple inheritance of classes — that's a real limitation. If I implement `Runnable` instead, my class is free to extend another class if needed, and I just pass the `Runnable` instance into a `Thread` object to run it. In general, implementing `Runnable` is considered better practice because it separates the task itself from the thread execution mechanism, and it keeps the design more flexible."

### 🔁 Follow-up: "What does the `synchronized` keyword actually do?"
**Answer:** "`synchronized` ensures that only one thread at a time can execute a particular block of code or method on a given object, by acquiring a lock — often called a monitor — associated with that object. This prevents race conditions when multiple threads try to read and modify shared data concurrently. Without it, two threads could interleave their operations on shared state and produce an inconsistent or corrupted result."

### 🔁 Follow-up: "What are the main states in a thread's lifecycle?"
**Answer:** "A thread moves through New, when it's created but not yet started; Runnable, once `start()` is called and it's eligible to run; Running, when the CPU is actively executing it; Blocked or Waiting, if it's paused waiting for a resource or another thread's signal; and finally Terminated, once its `run()` method completes or it's stopped."

---

# PART 20 — JAVA 8 FEATURES: LAMBDA & STREAMS

### 🎤 "What is a lambda expression, and why was it introduced?"

**✅ Answer:**
"A lambda expression is essentially a concise way to represent an instance of a functional interface — an interface with exactly one abstract method — without writing out a full anonymous class. It was introduced in Java 8 to reduce boilerplate and support a more functional style of programming, especially useful for passing behavior as an argument, like in the Streams API or event handlers. For example, instead of writing an entire anonymous `Runnable` implementation, I can just write `() -> System.out.println(\"Running\")`."

### 🔁 Follow-up: "What is the Stream API used for?"
**Answer:** "The Stream API lets me process collections in a declarative, functional style — operations like filtering, mapping, and reducing a collection can be chained together fluently, rather than writing explicit loops. For example, `list.stream().filter(x -> x > 10).map(x -> x * 2).collect(Collectors.toList())` filters, transforms, and collects results in a single readable pipeline. It also supports parallel processing easily through `parallelStream()`, which can improve performance on large datasets."

### 🔁 Follow-up: "What is a functional interface?"
**Answer:** "A functional interface is an interface that has exactly one abstract method, though it can still have default and static methods. It's the target type for a lambda expression — the lambda essentially provides the implementation for that single abstract method. `Runnable`, `Comparator`, and `Function` are common built-in examples, and I can also mark my own interfaces with `@FunctionalInterface` to get compile-time enforcement of this single-method rule."

---

# PART 21 — JVM, JRE, JDK & MEMORY MANAGEMENT

### 🎤 "Can you explain the difference between JVM, JRE, and JDK?"

**✅ Answer:**
"JVM, the Java Virtual Machine, is the runtime engine that actually executes compiled Java bytecode — it's what makes Java platform-independent, since each OS has its own JVM implementation that understands the same bytecode. JRE, the Java Runtime Environment, includes the JVM plus the core libraries needed to actually run Java applications, but it doesn't include development tools. JDK, the Java Development Kit, includes everything in the JRE plus development tools like the compiler `javac`, debugger, and other utilities needed to actually write and build Java programs. So the relationship is: JDK contains JRE, and JRE contains JVM."

### 🔁 Follow-up: "How does memory management work in Java — Stack vs Heap?"
**Answer:** "The **Stack** stores method call frames, including local variables and object references — it follows a last-in-first-out structure and is automatically cleaned up as methods return. The **Heap** is where all objects actually live, including their instance variables — it's shared across the entire application and managed by the garbage collector rather than being tied to a specific method call. So when I write `Car c = new Car();` inside a method, the reference `c` sits on the Stack, but the actual `Car` object sits on the Heap."

### 🔁 Follow-up: "What is garbage collection, and how does the JVM decide what to collect?"
**Answer:** "Garbage collection is the JVM's automatic process of reclaiming memory occupied by objects that are no longer reachable from any active reference — meaning no part of the running program can access them anymore. The JVM periodically traces from root references, like local variables and static fields, and any object it can't reach through that trace is considered garbage and eligible for collection. This removes the need for manual memory deallocation, though it does introduce some unpredictability in exactly when collection happens, which is why we generally can't rely on `finalize()` for critical cleanup timing."

---

# PART 22 — DESIGN PATTERNS (BASICS)

### 🎤 "Can you explain the Singleton design pattern and how you'd implement it?"

**✅ Answer:**
"Singleton ensures that a class has exactly one instance throughout the application's lifetime, and provides a single global access point to it. I'd implement it by making the constructor private, so no external code can call `new` directly, holding a private static reference to the single instance, and exposing a public static `getInstance()` method that creates the instance on first call and returns that same instance on every subsequent call. In a multithreaded context, I'd also make `getInstance()` synchronized, or use double-checked locking, to prevent two threads from accidentally creating two separate instances simultaneously."

### 🔁 Follow-up: "Can you briefly explain the Factory pattern and when you'd use it?"
**Answer:** "Factory pattern centralizes object creation logic in one place, rather than scattering `new` calls with conditional logic throughout the codebase. Instead of the client deciding exactly which concrete class to instantiate, it calls a factory method and passes in some identifying information, and the factory decides which concrete implementation to return. I'd use this when I have multiple related classes implementing a common interface, and the exact class to instantiate depends on runtime conditions — it keeps the client code decoupled from concrete implementations."

### 🔁 Follow-up: "What about the Builder pattern?"
**Answer:** "Builder pattern is useful when an object has many optional fields, and a huge constructor with many parameters would be confusing and error-prone. Instead, I chain method calls, each setting one field and returning the builder itself — this is exactly where `this` returning the current object, that I mentioned earlier, becomes really important — and finally call a `build()` method to construct the actual object. It makes object construction readable and avoids constructor telescoping."

---

# PART 23 — SOLID PRINCIPLES

### 🎤 "Can you briefly explain the SOLID principles?"

**💡 Intent:** LLD round mein directly poocha jata hai — one-liner clarity expect karta hai interviewer, ratta nahi.

**✅ Answer:**
"**Single Responsibility** — a class should have only one reason to change, meaning it should do one job well. **Open/Closed** — classes should be open for extension but closed for modification, so I add new behavior through new code rather than editing existing tested code. **Liskov Substitution** — a subclass should be usable wherever its parent class is expected, without breaking correctness. **Interface Segregation** — it's better to have several small, specific interfaces rather than one large interface that forces implementing classes to define methods they don't actually need. And **Dependency Inversion** — high-level modules should depend on abstractions, not on concrete implementations, so components remain loosely coupled and easier to swap or test."

### 🔁 Follow-up: "Give me a quick real example of violating the Single Responsibility Principle."
**Answer:** "If I had one `Employee` class that both calculates salary and also handles saving employee data to a database, that violates SRP — it now has two reasons to change: a change in the tax calculation logic, or a change in how data is persisted. I'd split this into an `Employee` class holding data and logic specific to an employee, and a separate `EmployeeRepository` class responsible solely for persistence."

---

# PART 24 — QUICK-FIRE ROUND (Common Comparison Questions)

### 🎤 "Array vs ArrayList — what's the difference?"
**✅ Answer:** "An array has a fixed size decided at creation time and can hold primitives directly. An `ArrayList` is part of the Collections Framework, can grow or shrink dynamically, but can only hold objects, not primitives directly — which is where wrapper classes and autoboxing come in."

### 🎤 "What is a marker interface? Give an example."
**✅ Answer:** "A marker interface has no methods at all — it simply 'marks' a class as having a certain capability, which other code can check for using `instanceof`. `Serializable` is the classic example — implementing it tells the JVM that objects of this class are allowed to be converted into a byte stream, even though the interface itself declares no methods to implement."

### 🎤 "What's the difference between `==` and `.equals()`?"
**✅ Answer:** "`==` compares references for objects — whether two variables point to the exact same memory location. `.equals()`, when properly overridden, compares logical or content-based equality. For primitives, `==` compares actual values directly, since primitives aren't objects at all."

---

# 🧩 FULL MOCK INTERVIEW — CONTINUOUS FLOW SIMULATION

*Ye ek asli 20-25 minute ka Core Java + OOPS round jaisa lagta hai. Interviewer ek topic se dusre topic pe naturally jump karta hai — bilkul aise hi practice karo, bina rukе.*

**Interviewer:** "Let's start simple — what is the difference between a class and an object?"
**You:** *(Part 1 answer)*

**Interviewer:** "Good. Now, where is an object actually stored in memory?"
**You:** *(Heap/Stack follow-up)*

**Interviewer:** "Okay, moving on — what's a constructor, and why not just use setters?"
**You:** *(Part 2 answer)*

**Interviewer:** "If I write `Movie copy = original` instead of a proper copy constructor, what breaks?"
**You:** *(reference copy follow-up)*

**Interviewer:** "Let's talk about polymorphism. What are the two types, and what's the real difference between them?"
**You:** *(Part 6 — compile-time vs runtime binding)*

**Interviewer:** "Can a static method be overridden?"
**You:** *(method hiding trap answer)*

**Interviewer:** "Explain encapsulation versus abstraction — people usually mix these up."
**You:** *(Part 8/9 combined answer)*

**Interviewer:** "Why is String immutable in Java?"
**You:** *(Part 13 answer)*

**Interviewer:** "If I write `Integer a = 127, b = 127; a == b` — true or false, and why?"
**You:** *(Integer cache answer)*

**Interviewer:** "Walk me through how a HashMap works internally."
**You:** *(Part 16 HashMap answer)*

**Interviewer:** "Why do we need to override both `equals()` and `hashCode()` together?"
**You:** *(Part 14 answer)*

**Interviewer:** "What's the difference between checked and unchecked exceptions?"
**You:** *(Part 17 answer)*

**Interviewer:** "Last one — Iterator versus Enumeration, what's the real advantage?"
**You:** *(Part 18 — remove() and fail-fast answer)*

**Interviewer:** "That's it from my side, thank you!"

---

## ✅ HOW TO PRACTICE THIS FILE

1. **Pehle read karo** har section normally, samajhte hue.
2. **Phir bolke practice karo** — professional answer ko zor se bolo, jaise interviewer saamne baitha hai.
3. **Mock flow section** ko bina ruke end-to-end bolo — ye tumhara asli simulation hai.
4. **Follow-up questions khud se poocho** apne aap ko — agar answer atak raha hai, wapas us Part pe jao.

**All the best bhai — ye poora file ab tumhara core Java + OOPS interview arsenal hai. Practice karte raho, confidence apne aap aayega.** 💪
