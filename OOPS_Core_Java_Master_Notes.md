# 🔥 OOPS + CORE JAVA — MASTER INTERVIEW NOTES
### Kashi ke liye — Complete Flow, Hinglish mein, Interview Q&A format
### TCS NQT / Cognizant / Accenture LLD Round Ready

---

## 📖 KAISE PADHNA HAI YE NOTES?

Bhai ye pura document ek **flow** mein bana hai. Har topic previous topic se connect hota hai — jaise OOPS ki chain hai:

```
Class/Object → Constructor → this keyword → static keyword
     ↓
Polymorphism (Overloading + Overriding)
     ↓
Inheritance → Encapsulation → Abstraction
     ↓
Interfaces vs Abstract Class
     ↓
Access Modifiers → Class Relationships → Generics
     ↓
Wrapper Classes → Collections → Exception Handling → Iterator
```

Jo topics tune **high frequency** bola hai (Overloading/Overriding, Polymorphism, Abstraction real-life, static, Collections, Wrapper, Exception, Iterator) — unko maine **⭐⭐⭐ EXTRA DEEP** treatment di hai, zyada questions ke saath.

---

# 🧱 PART 1 — CLASS AUR OBJECT (Foundation)

## Q1. Class kya hoti hai? Object kya hota hai? Difference batao.

**Answer (keyword-wise):**
Class ek **blueprint/template** hai jo kisi bhi entity ke **attributes (data)** aur **behavior (methods)** define karta hai. Object us blueprint ka **actual instance** hota hai jiske paas real memory allocate hoti hai aur unique data hota hai.

**Real analogy:** Car ka design (blueprint) = Class. Actual Hyundai i20 red color wali showroom mein khadi = Object.

**Why important:** Interviewer ye isliye poochta hai kyunki agar tujhe basic hi clear nahi to Inheritance, Polymorphism sab confuse hoga — kyunki wo sab isi concept pe based hain.

```java
class Car {                    // BLUEPRINT
    String color;
    String brand;
    void drive() { }
}

Car myCar = new Car();         // OBJECT (instance)
```

**Connect:** Ye foundation hai — Constructor (next topic) isi Object ko initialize karta hai jab wo bane.

---

# 🏗️ PART 2 — CONSTRUCTOR

## Q2. Constructor kya hota hai? Iske key features?

**Answer:** Constructor ek special block hai jo **object create hote hi automatically** call hota hai, taaki object ek **valid state** mein start ho (null/garbage values na ho).

**Keywords to remember:**
- Class ke naam jaisa hi naam hota hai
- **No return type** (void bhi nahi)
- Automatically invoked
- **Overloading supported** hai (multiple constructors ek class mein)

## Q3. Constructor ke kitne types hote hain?

| Type | Kya karta hai |
|---|---|
| **Default Constructor** | Bina argument ke, Java khud banata hai agar tu na banaye. int=0, String=null deta hai |
| **Parameterized Constructor** | Actual values pass karke object banate ho |
| **Copy Constructor** | Dusre object ka data copy karke naya object banata hai |
| **Private Constructor** | Bahar se object banane se rokta hai — Singleton Pattern mein use hota hai |

## Q4. Copy Constructor mein `Movie copy = original;` galat kyun hai?

**Answer:** Ye **reference copy** hai, naya object nahi banta. `copy` aur `original` dono **same memory location** ko point karte hain. Agar copy mein change karoge to original bhi change ho jayega — kyunki Java mein objects **pass by reference (of the value)** hote hain.

**Sahi tareeka:**
```java
Movie(Movie other) {          // Copy Constructor
    this.title = other.title;
    this.duration = other.duration;
}
Movie copy = new Movie(original);   // ✅ Naya independent object
```

**Deep Copy vs Shallow Copy:**
- Shallow Copy → sirf reference copy hota hai, nested objects share hote hain (risky)
- Deep Copy → har attribute individually copy hota hai, poori tarah independent (safe)

## Q5. Constructor `final`, `static`, ya `abstract` ho sakta hai kya?

```
final    → NO  (Constructor inherit hi nahi hota, to final ka koi matlab nahi)
static   → NO  (Constructor object ka attribute hai, class ka nahi)
abstract → NO  (Constructor concrete hona zaroori hai, object initialize karna hai)
private  → YES (Singleton Pattern ke liye use hota hai)
```

## Q6. Constructor mein `return` statement use kar sakte hain?

**Answer:** Haan, lekin **value return nahi kar sakte** — sirf `return;` likh ke early exit kar sakte ho (jaise invalid value pe object initialize hone se rokna).

## Q7. Child class ka object banate waqt kaunsa constructor pehle call hota hai?

**Answer:** **Parent class ka constructor hamesha pehle call hota hai**, phir child ka. Ye JVM internally `super()` call karta hai agar tune explicitly nahi likha.

**Interview trap:** Agar `new Child()` bhi likho, phir bhi flow hai: Parent Constructor → Child Constructor.

## Q8. Constructor inherit ho sakta hai?

**Answer:** NO. Constructor sirf uski apni class ke liye hota hai. Child class `super()` use karke parent ka constructor **call** kar sakta hai, lekin **inherit** nahi karta.

---

# 🔑 PART 3 — `this` KEYWORD

## Q9. `this` keyword ka use kya hai?

**Answer:** `this` current object ko refer karta hai. 4 main use cases:

1. **Ambiguity resolve karna** — jab parameter aur instance variable ka naam same ho:
```java
Person(String name) {
    this.name = name;   // this.name = object ka, name = parameter ka
}
```
2. **Constructor chaining** — ek constructor se dusra constructor call karna: `this(name, 0);`
3. **Method chaining** — current object return karke agla method turant call karna: `p.setName("Bob").setAge(25);`
4. **Current object pass karna** — kisi aur method ko current object bhejna

## Q10. `this` static method mein use kyun nahi ho sakta?

**Answer:** Static class ka belong karta hai, object ka nahi. `this` object ko refer karta hai. Isliye conflict hai — static context mein "current object" hota hi nahi.

---

# ⚡ PART 4 — STATIC KEYWORD ⭐⭐⭐ (HIGH FREQUENCY)

## Q11. `static` keyword ka use kya hai? Explain properly.

**Answer:** `static` keyword ka matlab hai — ye member (variable/method/block) **class ka hai, kisi specific object ka nahi**. Saare objects mile-julke isi ek copy ko share karte hain.

**Kaha use hota hai:**
```
static variable  → Class ki saari objects mein SHARE hoti hai
static method    → Class name se direct call hota hai, object banane ki zarurat nahi
static block     → Class load hote hi ek baar run hota hai (initialization ke liye)
static class     → Sirf nested class static ho sakti hai (top-level class nahi)
```

## Q12. Static variable vs Instance variable — difference?

| Static Variable | Instance Variable |
|---|---|
| Class ke saath ek hi copy | Har object ki apni alag copy |
| Class load hote hi memory milti hai | Object create hone par memory milti hai |
| `ClassName.variable` se access | `object.variable` se access |
| Example: Company name (sab employees ke liye same) | Example: Employee ID (har employee ka alag) |

**Real life example:** Bank ka interest rate — sab accounts ke liye **static** (same rate), lekin har account ka **balance** — instance variable (alag alag).

## Q13. Static method static variable ko access kar sakta hai but instance variable ko kyun nahi?

**Answer:** Static method class load hote hi ban jata hai — us waqt **koi object exist nahi karta**. Instance variable ka existence hi object ke saath bandha hai. Isliye static method directly instance variable access nahi kar sakta (object reference ke bina).

## Q14. Static method ke andar `this` ya `super` use kyun nahi hota?

**Answer:** `this`/`super` dono current object ko refer karte hain. Static method object ke bina bhi call ho sakta hai (`ClassName.method()`), isliye "current object" ka concept hi nahi banta.

## Q15. Static block kab use hota hai? Real example do.

**Answer:** Static block class **load hote hi ek baar** run hota hai — object banne se pehle bhi. Use hota hai jab koi **one-time initialization** karni ho (jaise database connection setup, config load karna).

```java
class Database {
    static Connection conn;
    static {
        conn = createConnection();   // Class load hote hi ek baar chalega
    }
}
```

## Q16. Static method ko override kar sakte hain kya?

**Answer:** **NO — static methods override nahi hote, sirf HIDE hote hain.** Ye interview ka bahut common trap question hai.

```java
class Parent {
    static void show() { System.out.println("Parent"); }
}
class Child extends Parent {
    static void show() { System.out.println("Child"); }  // Ye OVERRIDING nahi, METHOD HIDING hai
}
```

**Why?** Kyunki Overriding **runtime polymorphism** pe based hai (object type dekh ke decide hota hai), lekin static method **compile time** pe hi resolve ho jata hai reference type ke basis pe — isliye ye "method hiding" kehlata hai, overriding nahi.

**Connect:** Ye directly Polymorphism (agla topic) se juda hai — is question ka use interviewer Overriding ki depth check karne ke liye karta hai.

## Q17. Static import kya hota hai?

**Answer:** Static members (variables/methods) ko directly, bina class name likhe, use karne ki facility deta hai. Example: `import static java.lang.Math.*;` ke baad `sqrt(25)` likh sakte ho, `Math.sqrt(25)` likhne ki zarurat nahi.

---

# 🎭 PART 5 — POLYMORPHISM ⭐⭐⭐ (HIGHEST FREQUENCY TOPIC)

## Q18. Polymorphism kya hai? Word breakdown karo.

**Answer:** Poly = Many, Morphism = Forms. **"Same operation, alag-alag context mein alag behavior."**

**Real life example:** Ek hi insaan (Kashi) — office mein professional behavior, ghar mein casual behavior. Same person, different behavior based on context — yahi polymorphism hai code mein.

## Q19. Polymorphism ke kitne types hote hain?

```
1. Compile-Time Polymorphism  → Method Overloading
2. Runtime Polymorphism       → Method Overriding
```

---

## ⭐ METHOD OVERLOADING (Compile-Time)

## Q20. Method Overloading kya hai? Rules kya hain?

**Answer:** Same class mein **same method name**, lekin **parameters different** (number ya type mein).

**Memory trick jo instructor ne diya:** Truck ko "overload" karna — same cheez baar baar dalna, lekin har baar thoda different arrangement.

```java
void start(String type) { }
void start(String type, int speed) { }     // Number of params different
void start(int vehicleId) { }              // Type different
```

**RULES (bahut important, interview mein exact ye poocha jata hai):**
```
✅ Method name SAME hona chahiye
✅ Parameters ka NUMBER different ho SKTA hai
✅ Parameters ka TYPE different ho SKTA hai
❌ Sirf RETURN TYPE alag hone se overloading NAHI hoti (compile error aata hai)
```

## Q21. Kya sirf return type change karke overloading kar sakte hain?

**Answer: NO.** Ye ek bahut common trap hai.

```java
int add(int a, int b) { return a+b; }
double add(int a, int b) { return a+b; }   // ❌ COMPILE ERROR — same signature
```

**Why error?** Java method ko uske **signature** (name + parameter list) se identify karta hai, return type se nahi. Compiler ko pata hi nahi chalega ki kaunsa method call karna hai agar sirf return type alag ho.

## Q22. Overloading kis time resolve hoti hai aur kaise?

**Answer:** **Compile time** pe. Compiler khud dekh leta hai ki kitne aur kis type ke arguments diye gaye hain, aur uske basis pe sahi method choose kar leta hai. Isko **Static Binding / Early Binding** bhi kehte hain.

---

## ⭐ METHOD OVERRIDING (Runtime)

## Q23. Method Overriding kya hai?

**Answer:** Jab **child class**, apne **parent class ke method ko dubara define** karta hai — same name, same parameters, but naya implementation.

```java
class Vehicle {
    void start() { System.out.println("Vehicle starting"); }
}
class Car extends Vehicle {
    @Override
    void start() { System.out.println("Car starting"); }   // OVERRIDE
}
```

## Q24. Overriding kis time resolve hoti hai?

**Answer:** **Runtime** pe. JVM decide karta hai ki actual **object kis type ka hai** (reference type nahi), aur uske hisaab se method call karta hai. Isko **Dynamic Binding / Late Binding** kehte hain.

```java
Vehicle v = new Car();   // Reference type Vehicle, Object type Car
v.start();                // "Car starting" prints — RUNTIME pe decide hua
```

## Q25. Overriding ke rules kya hain?

```
✅ Method name SAME
✅ Parameters SAME (number + type)
✅ Return type SAME ya covariant (subtype return kar sakte ho)
✅ Access modifier SAME ya usse zyada accessible (private se protected/public thik hai, ulta nahi)
❌ private, static, final methods override NAHI ho sakte
❌ Access modifier ko narrow (kam accessible) nahi bana sakte
```

## Q26. `@Override` annotation zaroori hai kya?

**Answer:** Technically zaroori nahi, lekin **best practice** hai. Ye compiler ko batata hai "main override kar raha hoon" — agar signature match nahi hua to **compile time pe hi error** de dega, warna galti runtime tak pata nahi chalegi.

## Q27. ⭐⭐⭐ OVERLOADING vs OVERRIDING — Full Comparison Table (MOST ASKED)

| Point | Overloading | Overriding |
|---|---|---|
| **Kaha hota hai** | Same class ke andar | Parent-Child class ke beech |
| **Kab resolve hota hai** | Compile time (Static Binding) | Runtime (Dynamic Binding) |
| **Parameters** | Different hone chahiye | Same hone chahiye |
| **Return type** | Change kar sakte ho (agar params bhi diff ho) | Same ya covariant hona chahiye |
| **Inheritance zaroori?** | NO | YES (parent-child relation chahiye) |
| **Access Modifier** | Koi restriction nahi | Kam restrictive nahi bana sakte |
| **Purpose** | Same operation, different input handle karna | Parent ke behavior ko customize karna |
| **Keyword** | `@Overload nahi hota` (no annotation) | `@Override` |

## Q28. Constructor Overloading aur Constructor ka this() call — kaise connect hai Method Overloading se?

**Answer:** Constructor bhi overload ho sakta hai (jaisa Part 2 mein dekha) — same rules apply hote hain: naam same (class name), parameters different. Ye Method Overloading ka hi ek special case hai jo specifically Constructors pe apply hota hai.

**Connect:** Yehi wajah hai ki Part 2 (Constructor) aur Part 5 (Polymorphism) related hain — Overloading ek common concept hai jo dono jagah use hota hai.

## Q29. Real life example do Overloading aur Overriding ka.

**Overloading real life:** Ek calculator app ka "add" button — number add kar sakta hai, string bhi concatenate kar sakta hai (context ke hisaab se same button, different input types).

**Overriding real life:** "Start Vehicle" ka button sab vehicles mein hai (Car, Bike, Truck) — lekin har vehicle ka apna "start" implementation hai. Button same hai (interface/parent), lekin actual kaam vehicle-specific hai.

---

# 🧬 PART 6 — INHERITANCE

## Q30. Inheritance kya hai?

**Answer:** Child class, parent class ki **properties aur methods ko reuse** karta hai bina dubara likhe. **"IS-A"** relationship represent karta hai.

```
Dog IS AN Animal → Dog extends Animal
```

## Q31. Inheritance ke types kya hain?

```
1. Single           → Ek parent, ek child
2. Multi-level      → Chain: Animal → Mammal → Dog
3. Hierarchical     → Ek parent, multiple children: Animal → Dog, Cat
4. Multiple         → NOT SUPPORTED with classes (Diamond Problem)
5. Hybrid           → Classes + Interfaces ka mix
```

## Q32. Java Multiple Inheritance (class se) kyun support nahi karta? Diamond Problem kya hai?

**Answer:** Agar `Hybrid extends Dog, Cat` karo, aur dono mein same method `sound()` ho, to compiler confuse ho jayega ki **kaunsa sound() call kare** — Dog ka ya Cat ka. Ye ambiguity **Diamond Problem** kehlati hai.

**Solution:** **Interfaces se Multiple Inheritance achieve** karte hain — kyunki interface implement karne wale ko khud implementation likhni padti hai, isliye koi ambiguity nahi rehti.

```java
class Hybrid implements Dog, Cat {
    public void sound() {
        // Tum khud decide karte ho implementation, no ambiguity
    }
}
```

**Golden Rule:** Class ek hi extend kar sakte ho, Interface jitne chaho utne implement kar sakte ho.

## Q33. Inheritance ke advantages aur disadvantages?

```
ADVANTAGES:
✅ Code Reusability (eat() method baar baar likhna nahi padta)
✅ Ease of Maintenance (ek jagah change, sab jagah reflect)
✅ Supports Polymorphism (Overriding ke liye base hi Inheritance hai)

DISADVANTAGES:
❌ Tight Coupling (parent change karoge to child break ho sakta hai)
❌ Complexity increase hoti hai deep nested inheritance mein
```

**Connect:** Yahi wajah hai Overriding (Part 5) Inheritance ke bina possible hi nahi — Overriding ke liye parent-child relationship zaroori hai jo Inheritance deta hai.

---

# 🔒 PART 7 — ENCAPSULATION

## Q34. Encapsulation kya hai? Real life example do.

**Answer:** Data ko **hide karna (private)** aur usse **controlled access (getter/setter)** ke through expose karna.

**Real life example:** Medicine capsule — andar ka chemical formula tumhe pata nahi, tum bas capsule use karte ho jaisa banaya gaya hai. Same tarah, class ke andar ka data (private) directly touch nahi hota, sirf defined methods (getter/setter) se access hota hai.

```java
class BankAccount {
    private double balance;             // HIDDEN

    public void deposit(double amt) {   // CONTROLLED ACCESS
        if (amt > 0) balance += amt;
    }
    public double getBalance() {
        return balance;
    }
}
```

## Q35. Encapsulation ke fayde kya hain?

```
✅ Data Security        → Sensitive data bahar se directly access nahi
✅ Data Integrity       → Data sirf tumhare defined rules se change hota hai
✅ Flexibility          → Internal implementation change kar sakte ho, outside code affect nahi hoga
✅ Maintainability      → Bug fix ek jagah, poore system mein reflect
```

## Q36. Encapsulation aur Abstraction mein confuse ho jate hain log — difference?

**Answer:** Ye interview ka favourite trap question hai.

```
ENCAPSULATION → "HOW" ko hide karta hai data ke liye (data hiding via private + getter/setter)
ABSTRACTION   → "HOW" ko hide karta hai implementation ke liye (sirf essential dikhana, complexity chupana)
```

**Simple trick:** Encapsulation = **Data ko capsule mein band karna** (security). Abstraction = **Complexity ko chupana, sirf zaroori dikhana** (design).

---

# 🎭 PART 8 — ABSTRACTION ⭐⭐⭐ (HIGH FREQUENCY — REAL LIFE FOCUS)

## Q37. Abstraction kya hai? Real life examples do (3-4 examples).

**Answer:** Sirf **essential details dikhana**, **implementation ki complexity chupana**.

**Real Life Examples (interview mein bolne ke liye ready examples):**

1. **Car driving:** Tum accelerator, brake, steering use karte ho — engine ke andar combustion kaise ho raha hai wo tumhe pata nahi aur zarurat bhi nahi. (Steering = interface tumhare liye, engine internals = hidden implementation)

2. **ATM Machine:** Tum "Withdraw" button dabate ho — bank ke andar kya database queries chal rahi hain, kya validation ho raha hai, tumhe pata nahi. Tumhe sirf result (cash) chahiye.

3. **Mobile Phone Call:** Tum number dial karte ho aur call lagti hai — signal processing, network routing, sab kuch abstract hai tumse.

4. **TV Remote:** Volume button dabate ho — andar circuit kaise kaam karta hai wo abstract hai.

**Why interviewer poochta hai ye:** Kyunki candidates definition rat lete hain lekin real life example nahi de pate — ye unki **conceptual clarity** check karta hai.

## Q38. Abstraction kaise achieve karte hain Java mein?

**Answer:** 2 tareeke se:
```
1. Abstract Class
2. Interface
```

## Q39. Abstract Class kya hai? Rules kya hain?

**Answer:** Ek class jo `abstract` keyword se define hoti hai, jisme **abstract methods (no body) + concrete methods (with body)** dono ho sakte hain.

```
RULES:
✅ Abstract aur concrete methods, dono ho sakte hain
✅ Constructor ho sakta hai
✅ Koi bhi type ka variable ho sakta hai (static, non-static, final, non-final)
❌ INSTANTIATE nahi kar sakte (new Animal() nahi chalega)
```

## Q40. Abstract class ko instantiate kyun nahi kar sakte?

**Answer:** Logically dekho — "Animal" naam ki koi cheez real world mein exist nahi karti akele mein. Tumhe Dog milega, Cat milega, koi specific animal milega — "generic Animal" nahi. Isliye code mein bhi ye enforce karte hain ki Animal ka direct object na bane.

## Q41. Interface kya hai? Rules?

```
RULES:
✅ Saare methods by default PUBLIC + ABSTRACT (Java 8 se pehle)
✅ Variables by default PUBLIC STATIC FINAL (constants)
✅ Java 8+ mein default aur static methods allowed
✅ Java 9+ mein private methods allowed
❌ Constructor NAHI ho sakta
❌ Instance variable NAHI ho sakta (sirf constants)
```

## Q42. ⭐ Abstract Class vs Interface — Full Comparison (MOST ASKED)

| Feature | Abstract Class | Interface |
|---|---|---|
| Methods | Abstract + Concrete dono | Abstract (+ default/static Java8+) |
| Variables | Koi bhi type | Sirf constants (static final) |
| Constructor | YES | NO |
| Multiple Inheritance | NO (ek hi extend) | YES (multiple implement) |
| Kab use karein | Shared code/state chahiye | Sirf contract define karna hai |
| Access Modifier | Koi bhi | Sirf public |

## Q43. Kab Abstract Class use karein, kab Interface?

```
ABSTRACT CLASS use karo jab:
→ Common functionality share karni hai (jaise sleep() sab animals ke liye same)
→ Instance variables/state chahiye
→ Constructor logic chahiye

INTERFACE use karo jab:
→ Sirf CONTRACT define karna hai (behavior enforce karna, implementation nahi)
→ Multiple inheritance chahiye
→ Unrelated classes mein same behavior chahiye (Bird aur Airplane dono Flyable ho sakte hain — totally different cheezein)
```

## Q44. Java 8 mein Default Methods kyun add kiye gaye?

**Answer:** **Backward Compatibility** ke liye. Pehle agar interface mein naya method add karte the, to us interface ko implement karne wali **saari classes break** ho jati thi (kyunki unhe wo method implement karna padta).

**Solution:** Default method — body ke saath, **optional to override**.

```java
interface Animal {
    default void breathe() {
        System.out.println("Breathing...");
    }
}
```

## Q45. Do interfaces mein same naam ka default method ho aur ek class dono implement kare to kya hoga?

**Answer:** **Diamond Problem again** — compiler **compile time error** dega. Class ko **explicitly override** karna hi padega us method ko, resolve karne ke liye. Koi automatic resolution nahi hota.

## Q46. Abstract method aur Default method mein difference?

```
Abstract Method  → NO body, MUST implement karna padta hai
Default Method   → Body hoti hai, OPTIONAL to override
```

---

# 🧩 PART 9 — ACCESS MODIFIERS

## Q47. 4 Access Modifiers kya hain aur unki scope?

| Modifier | Same Class | Same Package | Subclass (diff pkg) | Anywhere |
|---|---|---|---|---|
| **private** | ✅ | ❌ | ❌ | ❌ |
| **default** (none) | ✅ | ✅ | ❌ | ❌ |
| **protected** | ✅ | ✅ | ✅ | ❌ |
| **public** | ✅ | ✅ | ✅ | ✅ |

**Connect:** Encapsulation (Part 7) mein `private` isi access modifier ka use karta hai data hide karne ke liye.

---

# 🔗 PART 10 — CLASS RELATIONSHIPS

## Q48. LLD mein 4 important relationships kaunse hain?

```
1. Inheritance   → "IS-A"    → Dog IS AN Animal
2. Realization   → "IMPLEMENTS" → CreditCard IMPLEMENTS Payment interface
3. Association   → "HAS-A"   → Book HAS AN Author (independent existence)
4. Dependency    → "USES-A"  → Document USES Printer (temporary, method ke andar)
```

**Interview Tip:** Aggregation aur Composition ko interviewer normally directly nahi poochta LLD coding round mein — bas Association bol do, safe hai.

## Q49. Association vs Dependency mein difference?

```
Association → LONG TERM relationship, object CLASS ke ATTRIBUTE ke roop mein store hota hai
Dependency  → SHORT TERM relationship, object sirf METHOD PARAMETER ke roop mein use hota hai
```

---

# 🎯 PART 11 — GENERICS AND WILDCARDS

## Q50. Generics kya hai aur kyun use karte hain?

**Answer:** Generics se ek hi code **multiple data types** ke saath kaam kar sakta hai, bina duplicate likhe.

```java
public <T> void print(T item) { System.out.println(item); }
print(11);       // T = Integer
print("Hello");  // T = String
```

**Benefits:** Type Safety, Code Reusability, No manual Type Casting, Compile time error catching (runtime pe nahi).

## Q51. Wildcard `?` kya hai aur Generics `<T>` se kaise alag hai?

```
Generics <T>  → Type FIXED aur KNOWN, READ + WRITE dono
Wildcard <?>  → Type UNKNOWN, mostly READ ONLY operations ke liye
```

- `<? extends Number>` → Upper Bound — READ operations (Integer, Double sab accept)
- `<? super Integer>` → Lower Bound — WRITE operations

---

# 📦 PART 12 — WRAPPER CLASSES ⭐⭐⭐ (HIGH FREQUENCY)

## Q52. Wrapper Class kya hoti hai? Kyun zarurat padi?

**Answer:** Java ke primitive types (`int`, `char`, `boolean`, etc.) **objects nahi hote**. Lekin Collections framework (ArrayList, HashMap) sirf **Objects** store kar sakta hai, primitives nahi. Isliye har primitive ke liye ek corresponding **Wrapper Class** banayi gayi jo usse Object mein convert karti hai.

| Primitive | Wrapper Class |
|---|---|
| int | Integer |
| char | Character |
| boolean | Boolean |
| double | Double |
| float | Float |
| long | Long |
| byte | Byte |
| short | Short |

## Q53. Autoboxing aur Unboxing kya hai?

```
Autoboxing   → Primitive ko automatically Wrapper mein convert karna
                int a = 5; Integer obj = a;   // Automatic

Unboxing     → Wrapper ko automatically primitive mein convert karna
                Integer obj = 10; int a = obj;   // Automatic
```

**Kab introduce hua:** Java 5 mein autoboxing/unboxing introduce hui, taaki manually `Integer.valueOf()` aur `.intValue()` na likhna pade.

## Q54. Wrapper classes `immutable` kyun hoti hain?

**Answer:** Jaise `String`, Wrapper classes bhi **immutable** hain — ek baar object bann gaya to uski value change nahi hoti, naya object banta hai.

```java
Integer a = 10;
a = a + 5;   // Naya Integer object banega with value 15, purana discard
```

**Why:** Security, thread-safety, aur caching (jaise Integer cache -128 to 127 ke liye) ke liye.

## Q55. `Integer a = 127; Integer b = 127; a == b` → true ya false? Kyun?

**Answer:** **TRUE**, kyunki Java **Integer Cache** maintain karta hai -128 se 127 tak ke values ke liye. Ye range wale Integer objects reuse hote hain (same memory reference).

Lekin agar `Integer a = 200; Integer b = 200;` to `a == b` → **FALSE**, kyunki 127 se bahar cache nahi hota, naye objects banate hain.

**Interview Trap:** Isliye Wrapper classes compare karne ke liye hamesha `.equals()` use karo, `==` nahi (kyunki `==` reference compare karta hai, value nahi).

---

# 📚 PART 13 — COLLECTIONS FRAMEWORK ⭐⭐⭐ (HIGH FREQUENCY)

## Q56. Collections Framework kya hai?

**Answer:** Ek unified architecture jo **group of objects** ko store, manipulate aur transfer karne ke liye interfaces + classes provide karta hai (List, Set, Map, Queue).

## Q57. List, Set, Map mein basic difference?

```
List  → Ordered, DUPLICATES allowed, index se access (ArrayList, LinkedList)
Set   → Unordered (mostly), DUPLICATES NOT allowed (HashSet, TreeSet)
Map   → Key-Value pairs, keys UNIQUE hoti hain (HashMap, TreeMap)
```

## Q58. ArrayList vs LinkedList — difference?

| ArrayList | LinkedList |
|---|---|
| Internally **dynamic array** | Internally **doubly linked list** |
| **Get/Access** fast — O(1) | **Get/Access** slow — O(n) |
| **Insert/Delete (middle)** slow — O(n), shifting hoti hai | **Insert/Delete** fast — O(1) agar node reference pata ho |
| Memory less (koi extra pointer nahi) | Memory zyada (next/prev pointers) |

**Kab use karein:** Zyada read/access karna hai → ArrayList. Zyada insert/delete karna hai (especially beech mein) → LinkedList.

## Q59. HashMap kaise kaam karta hai internally?

**Answer:** HashMap **key ka hashCode()** calculate karta hai, phir usse ek **bucket index** mein map karta hai (array of buckets). Agar do keys ka same hashCode aaye (**collision**), to us bucket mein **LinkedList** (ya Java 8+ mein bade collision pe **Red-Black Tree**) bana ke store hota hai.

```
put(key, value):
1. key.hashCode() calculate hota hai
2. Us hash se bucket index decide hota hai
3. Us index pe value store hoti hai (collision hone pe linked list/tree mein)

get(key):
1. Same hashCode calculate karke bucket dhundta hai
2. equals() se exact key match confirm karta hai
```

## Q60. HashMap vs HashSet vs TreeMap vs LinkedHashMap?

```
HashMap        → No ordering guarantee, fastest, allows one null key
LinkedHashMap  → Insertion order maintain karta hai
TreeMap        → Sorted order (natural ya custom comparator) maintain karta hai — Red-Black Tree based
HashSet        → HashMap ka use karta hai internally (values ek dummy constant hoti hain)
```

## Q61. `equals()` aur `hashCode()` ka contract kya hai? Kyun important hai HashMap ke liye?

**Answer:** **Rule:** Agar do objects `equals()` se equal hain, to unka `hashCode()` bhi SAME hona chahiye.

**Why important:** HashMap pehle hashCode se bucket dhundhta hai, phir equals() se exact match confirm karta hai. Agar tumne `equals()` override kiya lekin `hashCode()` nahi — to logically equal objects alag buckets mein chale jayenge, aur HashMap sahi se kaam nahi karega (duplicate entries ban jayengi jo actually equal honi chahiye thi).

## Q62. Comparable vs Comparator — difference?

```
Comparable  → Class KHUD apni natural ordering define karti hai. compareTo() method implement karna padta hai
             (class ke andar hi implement hota hai — sirf EK sorting logic possible)

Comparator  → Alag se ek class/lambda banate hain custom sorting ke liye. compare() method implement hota hai
             (class ke bahar — MULTIPLE sorting logics bana sakte ho)
```

```java
// Comparable — class ke andar
class Student implements Comparable<Student> {
    public int compareTo(Student s) { return this.age - s.age; }
}

// Comparator — bahar se, flexible
Comparator<Student> byName = (s1, s2) -> s1.name.compareTo(s2.name);
```

---

# 🚨 PART 14 — EXCEPTION HANDLING ⭐⭐⭐ (HIGH FREQUENCY)

## Q63. Exception kya hai? Error aur Exception mein difference?

**Answer:** Exception ek **unwanted event** hai jo program ke normal flow ko disrupt karta hai (jaise divide by zero, file not found).

```
Exception → Recoverable hai, program handle karke continue kar sakta hai (checked/unchecked)
Error     → Non-recoverable, JVM level problem (OutOfMemoryError, StackOverflowError) — inhe handle karna practical nahi
```

## Q64. Checked vs Unchecked Exception — difference?

```
Checked Exception    → Compile time pe check hoti hai. Handle karna MANDATORY (try-catch ya throws)
                        Example: IOException, SQLException, FileNotFoundException

Unchecked Exception  → Runtime pe hoti hai. Handle karna OPTIONAL, compiler force nahi karta
                        Example: NullPointerException, ArrayIndexOutOfBoundsException, ArithmeticException
                        (Ye sab RuntimeException ki subclasses hain)
```

**Trick to remember:** Checked = Compile time = Compulsory. Unchecked = Runtime = Runtime pe hi pata chalta hai, forced nahi.

## Q65. try-catch-finally ka flow kaise chalta hai?

```java
try {
    // Risky code
} catch (SpecificException e) {
    // Handle karo
} finally {
    // HAMESHA chalega — chahe exception aaye ya na aaye
}
```

**Key rule:** `finally` block **hamesha execute hota hai**, chahe try mein `return` bhi ho jaye — sirf exception `System.exit()` call ho to finally skip hoga.

## Q66. `throw` vs `throws` mein difference?

```
throw   → Actually ek exception object CREATE aur RAISE karne ke liye use hota hai (method ke andar)
          Example: throw new ArithmeticException("Invalid");

throws  → Method signature mein LIKHTE hain ki ye method kaunsi exception FEK sakta hai (caller ko warn karna)
          Example: void readFile() throws IOException { }
```

## Q67. Custom Exception kaise banate hain aur kyun?

**Answer:** Apni khud ki exception class banate hain jab **business-specific error** represent karna ho jo built-in exceptions se clear nahi hota.

```java
class InsufficientBalanceException extends Exception {
    InsufficientBalanceException(String msg) { super(msg); }
}
```

**Why:** Better readability aur specific error handling — jaise "InsufficientBalanceException" bolna zyada clear hai generic "Exception" bolne se.

## Q68. Multi-catch aur Exception hierarchy ka order kyun important hai?

**Answer:** Catch blocks **specific se general** order mein likhne padte hain, warna compile error aayega (unreachable code).

```java
try {
    // code
} catch (ArithmeticException e) {   // SPECIFIC pehle
    // ...
} catch (Exception e) {             // GENERAL baad mein
    // ...
}
```

Agar Exception (general) pehle likh do, to ArithmeticException wala catch **kabhi reach hi nahi hoga** — compiler error dega "already caught."

## Q69. finally vs finalize() — confuse mat hona!

```
finally     → Block hai, try-catch ke saath use hota hai, cleanup code ke liye (always executes)
finalize()  → Method hai, Garbage Collector object destroy karne se pehle call karta hai (deprecated in newer Java, unreliable)
```

---

# 🔄 PART 15 — ITERATOR vs ENUMERATOR ⭐⭐⭐ (HIGH FREQUENCY)

## Q70. Iterator kya hai?

**Answer:** Iterator ek interface hai jo Collection ke elements ko **ek-ek karke traverse** karne ka tareeka deta hai, bina underlying structure (array/list) expose kiye.

```java
Iterator<Integer> it = list.iterator();
while (it.hasNext()) {
    int val = it.next();
}
```

## Q71. Iterator vs Enumeration — Full Comparison (MOST ASKED)

| Feature | Enumeration | Iterator |
|---|---|---|
| **Kab aaya** | Java 1.0 se (legacy) | Java 1.2 se (Collections Framework ke saath) |
| **Kaha use hota** | Legacy classes (Vector, Hashtable) | Saare modern Collections (ArrayList, HashSet, etc.) |
| **Methods** | `hasMoreElements()`, `nextElement()` | `hasNext()`, `next()`, `remove()` |
| **Remove karne ki capability** | ❌ NAHI — traversal ke waqt element remove nahi kar sakte | ✅ HAAN — `remove()` method se traversal ke waqt hi element remove kar sakte ho |
| **Fail-Fast** | NO — modification detect nahi karta | YES — agar Collection modify hui traversal ke beech mein (remove() ke alawa), `ConcurrentModificationException` throw karta hai |
| **Speed** | Thoda fast (kam overhead) | Thoda slower (fail-fast check ki wajah se) |

**Interview mein exact ye poocha jata hai:** *"Iterator ka sabse bada advantage Enumeration ke upar kya hai?"*
**Answer:** **`remove()` method** — Iterator traversal ke dauraan hi safely element remove kar sakta hai. Enumeration mein ye possible nahi.

## Q72. ConcurrentModificationException kab aata hai?

**Answer:** Jab tum Iterator se traverse kar rahe ho aur **beech mein directly Collection ko modify** karte ho (jaise `list.remove()` direct call karna, `iterator.remove()` nahi) — to Iterator ka internal `modCount` mismatch ho jata hai aur exception throw hota hai.

```java
for (Integer i : list) {
    if (i == 3) list.remove(i);   // ❌ ConcurrentModificationException
}

// SAHI TAREEKA:
Iterator<Integer> it = list.iterator();
while (it.hasNext()) {
    if (it.next() == 3) it.remove();   // ✅ Safe removal
}
```

## Q73. ListIterator kya hai aur Iterator se kaise alag hai?

**Answer:** ListIterator, Iterator ka **extended version** hai — sirf **List** implementations ke liye available hai (Set/Map ke liye nahi).

```
Iterator      → Sirf FORWARD direction traverse kar sakta hai
ListIterator  → FORWARD aur BACKWARD dono direction traverse kar sakta hai
                 + add(), set() methods bhi hain (Iterator mein nahi)
```

---

# 🧠 FINAL — COMPLETE INTERVIEW SIMULATION (Bolke Practice Karo)

Bina notes dekhe, ye sab bolke answer do — jaise real interview mein interviewer poochta hai:

**Round 1 — Foundation:**
1. Class aur Object mein difference? Real example do.
2. Constructor ke 4 types kya hain?
3. `this` keyword ke 4 use cases?
4. `static` keyword ka use kya hai? Static method static variable hi kyun access kar sakta hai?

**Round 2 — Polymorphism (Deep):**
5. Overloading vs Overriding — pura comparison table bolo.
6. Sirf return type change karke overloading kyun nahi hoti?
7. Static method override ho sakta hai kya? Explain "method hiding."
8. Compile time vs Runtime polymorphism — kaunsa kab resolve hota hai?

**Round 3 — Pillars of OOPS:**
9. Diamond Problem kya hai aur Java kaise solve karta hai?
10. Encapsulation aur Abstraction mein exact difference?
11. Abstraction ke 3 real life examples do.
12. Abstract Class vs Interface — kab kya use karein?
13. Java 8 default methods kyun add hue?

**Round 4 — Core Java:**
14. Wrapper classes kyun zarurat padi? Autoboxing kya hai?
15. `Integer a=127, b=127; a==b` — true ya false, kyun?
16. ArrayList vs LinkedList — kab kya use karein?
17. HashMap internally kaise kaam karta hai?
18. equals() aur hashCode() ka contract kya hai?
19. Checked vs Unchecked Exception — difference aur examples?
20. throw vs throws?
21. Iterator vs Enumeration — sabse bada advantage kya hai Iterator ka?
22. ConcurrentModificationException kab aata hai aur kaise avoid karein?

---

## ✅ QUICK REVISION KEYWORDS (Interview se pehle last look ke liye)

```
Class/Object       → Blueprint / Instance
Constructor         → Auto-invoked, no return type, same name as class
this               → Ambiguity resolve, chaining, current object
static             → Class level, shared, no "this"
Overloading        → Compile time, same class, diff params
Overriding         → Runtime, parent-child, same signature
Diamond Problem    → Multiple inheritance ambiguity → solved via interfaces
Encapsulation      → Data hiding (private + getter/setter)
Abstraction        → Implementation hiding (abstract class/interface)
Default Method     → Java 8, backward compatibility
Wrapper Class      → Primitive → Object, immutable, Integer cache -128 to 127
HashMap            → hashCode() → bucket → equals() confirm
Comparable         → compareTo(), class ke andar, single logic
Comparator         → compare(), class ke bahar, multiple logics
Checked Exception  → Compile time, mandatory handling
Unchecked Exception→ Runtime, RuntimeException subclass
Iterator           → hasNext(), next(), remove() — fail-fast
Enumeration        → Legacy, no remove(), no fail-fast
```

---

**All the best bhai! Ye notes baar baar revise karo — especially Overloading/Overriding, Abstraction real-life examples, aur Iterator vs Enumeration, kyunki inpe interviewer sabse zyada follow-up questions poochta hai.** 💪
