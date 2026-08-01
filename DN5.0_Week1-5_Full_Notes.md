# DN 5.0 Interview Notes — Week 1 to Week 5 (Full Compiled Notes)

---

# WEEK 1

## Topic 1: SOLID Principles

### 🎯 SOLID Kahan Se Aaya, Kyun Aaya

**Problem jo SOLID solve karta hai:**

1990s mein jab Object-Oriented Programming popular ho raha tha, developers classes toh bana rahe the, par **badly designed** classes — ek class 500 kaam kar rahi thi, code itna tightly coupled tha ki ek cheez change karo toh 10 jagah break ho jaaye. Isko bolte hain **"spaghetti code"** — sab kuch ek dusre mein uljha hua.

**Robert C. Martin ("Uncle Bob")** ne 2000s mein in 5 principles ko collect karke naam diya **SOLID** — taaki developers ek checklist follow kar sakein jisse unka code:
- **Maintainable** ho (change karna aasan ho)
- **Extensible** ho (naya feature add karna aasan ho bina purana tod ke)
- **Testable** ho (isolated tareeke se test ho sake)

**Yaad rakh — SOLID koi language-specific cheez nahi hai.** Ye sirf Java ke liye nahi — Python, JavaScript, kahin bhi OOP ho, ye principles apply hote hain. Isliye interviewer ye expect karta hai ki tujhe **concept** aaye, sirf definition nahi.

Ab ek-ek principle deep mein karte hain, **har ek ke baad uske saare possible questions.**

---

### 1️⃣ S — Single Responsibility Principle (SRP)

**Definition:** Ek class ke paas sirf **ek reason to change** hona chahiye.

**Deep samajh:** Log isko galat samajhte hain — "ek class ka ek method hona chahiye." **Galat.** SRP ka matlab hai ek class ka ek hi **"actor"** ya **"stakeholder"** ke liye responsible hona chahiye. Jaise agar `Employee` class salary calculate bhi kare aur report bhi print kare — agar Finance team salary logic change kare, ya IT team report format change kare, dono alag reasons se ek hi class change hogi. Ye SRP violation hai.

```java
// BAD — 2 responsibilities
class Employee {
    double calculateSalary() { ... }
    void printReport() { ... }
}

// GOOD — separated
class Employee {
    double calculateSalary() { ... }
}
class ReportPrinter {
    void printReport(Employee e) { ... }
}
```

**Possible Questions:**

**Q1: SRP kya hai?**
> Crisp: "Ek class ke paas change karne ka sirf ek hi reason hona chahiye."
> Elaboration: "Iska matlab hai ek class ek hi responsibility handle kare. Agar do alag business reasons se ek class change ho sakti hai, matlab woh do responsibilities handle kar rahi hai aur usse split karna chahiye."

**Q2: SRP violate hone se real problem kya hoti hai?**
> "Testing mushkil ho jaati hai kyunki ek test mein multiple concerns mix ho jaate hain, aur ek change ka side-effect dusri unrelated functionality pe pad sakta hai — jise 'ripple effect' bolte hain."

**Q3: Apne project se real example do jaha tune SRP apply kiya.**
> *(Isse tu personalize kar apne words mein — example: "Kashi Learning mein maine Authentication logic aur Course management logic alag services mein rakha — agar Clerk Auth ka integration change ho, mera course-related code affect nahi hota.")*

**Q4: "Ek class mein ek hi method hona chahiye" — sahi hai?**
> "Nahi, ye common misconception hai. SRP method count ke baare mein nahi, responsibility/reason-to-change ke baare mein hai. Ek class mein multiple related methods ho sakte hain jab tak woh sab ek hi responsibility ke andar aate hain."

---

### 2️⃣ O — Open/Closed Principle (OCP)

**Definition:** Classes **open honi chahiye extension ke liye, closed honi chahiye modification ke liye.**

**Deep samajh:** Matlab — jab naya feature add karna ho, tu **naya code add** kare, **purana code edit** na kare. Ye kaise possible hai? Abstraction (interface/abstract class) ke through.

```java
// BAD — naya payment method add karne ke liye ye class edit karni padegi
class PaymentProcessor {
    void pay(String type) {
        if (type.equals("card")) { ... }
        else if (type.equals("upi")) { ... }
        // naya method add karna ho toh yahan aur if-else badhega
    }
}

// GOOD — Strategy pattern se OCP follow
interface PaymentMethod {
    void pay();
}
class UpiPayment implements PaymentMethod { void pay() { ... } }
class CardPayment implements PaymentMethod { void pay() { ... } }
// Naya payment method add karna ho? Bas naya class banao, purana kuch touch nahi hota
```

**Possible Questions:**

**Q1: OCP kya hai?**
> Crisp: "Software entities extension ke liye open, modification ke liye closed honi chahiye."
> Elaboration: "Matlab naya behavior add karne ke liye maine existing tested code ko edit nahi karna — instead naya class/implementation add karke feature extend karta hoon."

**Q2: OCP achieve kaise karte hain practically?**
> "Abstraction ke through — interfaces ya abstract classes use karke, phir Strategy ya Factory jaise design patterns se naye implementations plug karte hain bina existing code chede."

**Q3: OCP violate karne se kya risk hai?**
> "Har naye feature ke liye purana, already-tested code touch hota hai — jisse naye bugs introduce hone ka risk badhta hai jo pehle se working functionality ko bhi todh sakte hain (regression)."

**Q4: Kya OCP ka matlab hai humein kabhi bhi purana code edit nahi karna chahiye?**
> "Nahi, bug fixes ke liye edit karna padta hai. OCP specifically **naye features/behavior add karne** ke context mein hai — waha hum modification avoid karke extension prefer karte hain."

---

### 3️⃣ L — Liskov Substitution Principle (LSP)

**Definition:** Agar `S` `T` ka subtype hai, toh `T` ke objects ko `S` ke objects se replace kiya ja sake **bina program ka correctness todhe.**

**Deep samajh:** Ye sabse confusing principle hai, isliye dhyan se samajh. Simple words mein — **child class, parent class ki jagah use ho sake bina koi surprise ke.**

**Classic example (interview mein bahut poocha jaata hai): Rectangle-Square problem**

```java
class Rectangle {
    void setWidth(int w) { this.width = w; }
    void setHeight(int h) { this.height = h; }
}
class Square extends Rectangle {
    void setWidth(int w) { this.width = w; this.height = w; } // dono set kar diya!
    void setHeight(int h) { this.width = h; this.height = h; }
}
```

Agar code kahin ye expect karta hai `setWidth(5)` ke baad sirf width change hogi, height wahi rahegi — Square isse tod dega, kyunki Square mein width set karne se height bhi change ho jaati hai. **Ye LSP violation hai** — Square, Rectangle ki jagah "safely" use nahi ho sakta.

**Possible Questions:**

**Q1: LSP kya hai, simple words mein?**
> Crisp: "Child class ko parent class ki jagah use kiya ja sake, bina program ka behavior galat kiye."
> Elaboration: "Agar mera code `Parent p = new Child()` likhta hai, toh Child object ko parent ki tarah hi behave karna chahiye — koi unexpected side-effect nahi hona chahiye."

**Q2: Rectangle-Square example se LSP samjhao.**
> *(Upar wala example bol — ye classic hai, must-know)*

**Q3: LSP violation ka real-world impact kya hota hai code mein?**
> "Agar main polymorphism use kar raha hoon (jaise ek list of `Shape` objects loop kar raha hoon), aur ek subclass expected behavior break kar de, toh mera code runtime pe crash ya wrong result de sakta hai — bina compile error ke, jo debug karna mushkil hota hai."

**Q4: LSP ko kaise avoid/fix karte hain?**
> "Agar child class parent ka behavior fundamentally badal rahi hai, toh inheritance use hi mat karo — composition use karo, ya better abstraction design karo jo dono cases ko sahi se represent kare."

---

### 4️⃣ I — Interface Segregation Principle (ISP)

**Definition:** Client ko un methods pe depend nahi karna chahiye jo woh use hi nahi karta. **Chhoti, specific interfaces bado, ek bada "fat" interface nahi.**

**Deep samajh:**

```java
// BAD — fat interface
interface Worker {
    void work();
    void eat();
}
class Robot implements Worker {
    void work() { ... }
    void eat() { throw new UnsupportedOperationException(); } // Robot khana nahi khata!
}

// GOOD — segregated
interface Workable { void work(); }
interface Eatable { void eat(); }
class Robot implements Workable { void work() { ... } }
class Human implements Workable, Eatable { void work() {...} void eat() {...} }
```

**Possible Questions:**

**Q1: ISP kya hai?**
> Crisp: "Ek client ko sirf woh methods dikhne chahiye jo woh actually use karta hai — unnecessary methods pe force nahi hona chahiye."
> Elaboration: "Bade, general-purpose interfaces ki jagah, hume chhote specific interfaces banane chahiye jinhe classes selectively implement kar sakein."

**Q2: ISP aur SRP mein confusion hoti hai, farak batao?**
> "SRP **classes** ke baare mein hai — ek class ka ek hi reason to change ho. ISP **interfaces** ke baare mein hai — ek interface client ko sirf relevant methods force kare, unrelated methods nahi. Dono 'separation of concerns' ka idea share karte hain, par apply alag jagah hote hain."

**Q3: Real example do jaha ISP na follow karne se problem hoti.**
> "Agar main ek `Printer` interface banata hoon jisme print, scan, fax teeno methods hain, aur mera `SimplePrinter` class sirf print kar sakta hai, toh usse scan/fax methods ko empty ya exception-throwing implement karna padega — jo bad design hai. Behtar hai `Printable`, `Scannable`, `Faxable` alag interfaces banau."

---

### 5️⃣ D — Dependency Inversion Principle (DIP)

**Definition:** High-level modules ko low-level modules pe depend nahi karna chahiye — dono ko **abstraction** pe depend karna chahiye.

**🔗 Yahi wo principle hai jo Week 2 mein Spring Framework ban jaata hai — isliye ye sabse important hai interview ke liye.**

```java
// BAD — high-level (OrderService) directly low-level (RazorpayGateway) pe depend
class OrderService {
    RazorpayGateway gateway = new RazorpayGateway(); // tightly coupled
}

// GOOD — dono abstraction (interface) pe depend
interface PaymentGateway { void pay(); }
class RazorpayGateway implements PaymentGateway { void pay() { ... } }
class OrderService {
    PaymentGateway gateway; // interface pe depend, concrete class pe nahi
    OrderService(PaymentGateway gateway) { this.gateway = gateway; }
}
```

**Possible Questions:**

**Q1: DIP kya hai?**
> Crisp: "High-level modules directly low-level modules pe depend na karein — dono abstraction (interfaces) pe depend karein."
> Elaboration: "Isse agar main payment gateway Razorpay se Stripe pe switch karta hoon, `OrderService` ka code touch nahi hota — sirf naya implementation inject hota hai."

**Q2: DIP aur DI (Dependency Injection) mein kya farak hai? (Bahut common question)**
> "DIP ek **principle** hai — design ka rule ki high-level aur low-level dono abstraction pe depend karein. DI ek **technique/pattern** hai jisse DIP ko implement karte hain — dependencies ko external source (jaise Spring) inject karta hai, class khud nahi banati. Spring Framework DI ko automate karta hai taaki hum DIP naturally follow karein."

**Q3: DIP real project mein kaise use kiya?**
> *(Personalize: "Samvaad mein maine ek `NotificationSender` interface banaya tha, jisse Email aur real-time Socket notification dono implementations plug ho sakein bina main logic change kiye.")*

---

### 🔥 Cross-Cutting Question (Interviewer favorite — SOLID ke saare principles ek saath test karta hai)

**Q: "SOLID ke 5 principles mein sabse zyada important kaunsa lagta hai tujhe, aur kyun?"**
> Is question ka **koi "correct" answer nahi hai** — interviewer ye dekhna chahta hai ki tu reasoning kar sakta hai. Suggestion: "Mujhe Dependency Inversion sabse impactful lagta hai kyunki modern frameworks jaise Spring isi principle pe bane hain — jo mujhe real projects mein directly Spring Boot ke through use karne ko mila."

**Q: "SOLID follow karne ka downside/tradeoff kya hai?"** *(Advanced interviewers ye poochte hain)*
> "Over-engineering ka risk hota hai — chhote, simple projects mein agar hum shuru se hi bahut zyada abstraction/interfaces bana dein, code unnecessarily complex ho jaata hai. SOLID ka use judgment ke saath karna chahiye, especially jab project scale/change hone wala ho."

---

## Topic 2: Design Patterns

### 🎯 Design Patterns Kahan Se Aaye, Kyun Aaye

1994 mein **4 authors** (Gang of Four — Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides) ne ek book likhi *"Design Patterns: Elements of Reusable Object-Oriented Software"*. Unka observation ye tha — developers duniya bhar mein **same problems** baar-baar solve kar rahe the (object banane ka best tareeka kya ho, do incompatible systems ko kaise jodo, ek object ka change dusre ko kaise pata chale) — par har bar **naye tareeke se, kabhi achhe kabhi bure**.

Toh unhone in common problems ke **proven, reusable solutions** ko document kiya — 23 patterns, teen categories mein baante:
- **Creational** (object kaise banaye) — Singleton, Factory, Builder
- **Structural** (objects ko kaise jodo) — Adapter, Decorator
- **Behavioral** (objects aapas mein kaise baat karein) — Observer, Strategy, Command

**🔗 Dot Connect (yaad rakh):** Design Patterns, SOLID principles ko **implement karne ke ready-made recipes** hain. Jaise DIP bolta hai "abstraction pe depend karo" — Strategy pattern **literally isi ko** code mein utaarta hai.

Ab ek-ek pattern deep mein karte hain.

---

### 1️⃣ Singleton Pattern

**Definition:** Ek class ka **sirf ek hi instance** poori application life mein bane, aur usko global access point mile.

**Kyun zaroori hua:** Socho tera application mein ek `Logger` class hai. Agar har class apna khud ka `new Logger()` banaye, toh multiple log files/streams create ho sakte hain — confusion. Ek hi shared instance chahiye.

```java
class Logger {
    private static Logger instance;
    private Logger() {}  // constructor private — bahar se 'new' nahi kar sakte
    
    public static Logger getInstance() {
        if (instance == null) {
            instance = new Logger();
        }
        return instance;
    }
}
```

**Analogy:** Ek ghar ka **main gate** — ek hi hota hai, sab uske through aate-jaate hain. Har room ka apna alag gate nahi hota.

**Possible Questions:**

**Q1: Singleton pattern kya hai aur kaise implement karte hain?**
> Crisp: "Ek class ka ek hi instance guarantee karta hai poori application mein."
> Elaboration: "Constructor ko private karke, aur ek static method (`getInstance()`) banake jo check kare instance already bana hai ya nahi — agar nahi bana toh banao, warna wahi existing return karo."

**Q2: Singleton multithreaded environment mein problem kyun create karta hai?**
> "Agar do threads simultaneously `getInstance()` call karein jab instance null ho, dono `if (instance == null)` check pass kar sakte hain aur **do alag instances** ban sakte hain — jo Singleton ka purpose hi tod deta hai. Isko fix karte hain `synchronized` keyword ya better, **Enum-based Singleton** se jo thread-safe hota hai by default."

**Q3: Real project mein Singleton kahan use kiya?**
> *(Personalize: "DB Connection pool aur Logger — dono ke multiple instances resource waste karte, ek hi shared instance chahiye tha.")*

---

### 2️⃣ Factory Method Pattern

**Definition:** Object create karne ka logic ek **centralized method** mein rakho, taaki client ko exact class ka naam pata na ho — bas type bolo, object mil jaaye.

**Kyun zaroori hua:** Jab `new` keyword directly code mein bikhra ho (jaise `new PDFDocument()`, `new WordDocument()` alag-alag jagah), aur kal ek naya document type add ho, tujhe **har jagah** dhoondh ke change karna padega. Factory ek hi jagah ye decision leta hai.

```java
interface Document { void open(); }
class PDFDocument implements Document { public void open() { ... } }
class WordDocument implements Document { public void open() { ... } }

class DocumentFactory {
    static Document createDocument(String type) {
        if (type.equals("pdf")) return new PDFDocument();
        else if (type.equals("word")) return new WordDocument();
        throw new IllegalArgumentException("Unknown type");
    }
}
```

**Analogy:** Restaurant ka **order counter** — tu "burger chahiye" bolta hai, counter decide karta hai kaunsa chef banayega, tujhe kitchen ke internal process se matlab nahi.

**Possible Questions:**

**Q1: Factory pattern kya hai?**
> Crisp: "Object creation logic ko ek centralized method mein encapsulate karta hai."
> Elaboration: "Client ko concrete class directly instantiate nahi karni padti — woh ek common interface/factory method use karta hai, aur actual decision (kaunsa object banana hai) factory ke andar hoti hai."

**Q2: Factory pattern ka fayda kya hai, directly `new` use karne ke against?**
> "Loose coupling milta hai — client sirf interface (`Document`) jaanta hai, concrete implementation (`PDFDocument`) nahi. Naya type add karna ho toh factory mein ek case add karo, client code touch nahi hota — yehi Open/Closed Principle hai."

**Q3: Factory aur Abstract Factory mein farak?** *(Thoda advanced, pooch sakte hain)*
> "Factory Method ek single product family banata hai (jaise sirf Documents). Abstract Factory **families of related products** banata hai — jaise ek UI theme factory jo Button, Checkbox, dono related objects same theme ke banaye."

---

### 3️⃣ Builder Pattern

**Definition:** Complex object ko **step-by-step** banane deta hai, especially jab object ke paas bahut saare optional parameters hon.

**Kyun zaroori hua:** Socho `Computer` object banana hai — CPU, RAM, Storage, GPU (optional), Cooling (optional)... Agar constructor mein sab parameters daaloge, tu "telescoping constructor problem" mein phas jaata hai — `new Computer(cpu, ram, storage, null, null, true, false...)` — bahut confusing.

```java
class Computer {
    private String cpu, ram, storage;
    private Computer(Builder b) { this.cpu = b.cpu; this.ram = b.ram; this.storage = b.storage; }
    
    static class Builder {
        String cpu, ram, storage;
        Builder setCpu(String c) { this.cpu = c; return this; }
        Builder setRam(String r) { this.ram = r; return this; }
        Computer build() { return new Computer(this); }
    }
}
// Usage:
Computer pc = new Computer.Builder().setCpu("i7").setRam("16GB").build();
```

**Analogy:** **Subway sandwich** banwana — bread choose karo, phir veggies, phir sauce, step by step, jitna chahiye utna add karo — sab kuch ek saath order nahi karna padta.

**Possible Questions:**

**Q1: Builder pattern kab use karte hain?**
> Crisp: "Jab object banane ke liye bahut saare parameters ho, especially optional wale."
> Elaboration: "Ye 'telescoping constructor' problem solve karta hai — multiple overloaded constructors ki jagah, ek fluent, readable step-by-step API deta hai object banane ke liye."

**Q2: Builder aur Factory mein farak kya hai?**
> "Factory ek **complete object turant** return karta hai based on type. Builder object ko **step-by-step**, piece-by-piece construct karta hai — jab construction process complex ho, multiple optional parts hon."

---

### 4️⃣ Adapter Pattern

**Definition:** Do **incompatible interfaces** ko saath kaam karne layak banata hai, bina unka existing code change kiye.

**Kyun zaroori hua:** Socho tera app ek `PaymentGateway` interface expect karta hai, par tu ek third-party library (Razorpay SDK) use kar raha hai jiska interface bilkul alag hai. Tu Razorpay ka code toh badal nahi sakta — beech mein ek Adapter class daalte hain jo translate kare.

```java
interface PaymentGateway { void pay(double amount); }

class RazorpaySDK {  // third-party, structure alag hai
    void makePayment(double amt, String currency) { ... }
}

class RazorpayAdapter implements PaymentGateway {
    RazorpaySDK sdk = new RazorpaySDK();
    public void pay(double amount) {
        sdk.makePayment(amount, "INR");  // translate kar diya
    }
}
```

**Analogy:** **Indian 3-pin plug ko US 2-pin socket** mein lagane wala adapter — dono ka design alag hai, adapter beech mein translate karta hai.

**Possible Questions:**

**Q1: Adapter pattern kya hai?**
> Crisp: "Ek incompatible interface ko dusre expected interface mein convert karta hai, taaki dono saath kaam kar sakein."
> Elaboration: "Jab main third-party library use karta hoon jiska interface mera application expect nahi karta, main ek Adapter class banata hoon jo beech mein translator ka kaam kare — main library ka code touch nahi karta."

**Q2: Real project mein Adapter kahan use hoga?**
> *(Personalize: "Agar Kashi Learning mein maine Razorpay se Stripe switch kiya hota, ek PaymentAdapter banata jo dono SDKs ko common `PaymentGateway` interface ke through expose karta — baaki application code touch nahi hota.")*

---

### 5️⃣ Decorator Pattern

**Definition:** Object mein **dynamically naya behavior/feature add** karta hai, bina uski original class change kiye ya naya subclass banaye.

**Kyun zaroori hua:** Agar inheritance se features add karo (jaise `EmailNotification`, `SMSNotification`, `EmailAndSMSNotification`, `EmailAndSMSAndPushNotification`...), combinations ke saath class explosion ho jaata hai. Decorator layer-by-layer wrap karta hai.

```java
interface Notifier { void send(String msg); }
class BasicNotifier implements Notifier {
    public void send(String msg) { System.out.println("Email: " + msg); }
}
class SMSDecorator implements Notifier {
    Notifier wrapped;
    SMSDecorator(Notifier n) { this.wrapped = n; }
    public void send(String msg) {
        wrapped.send(msg);
        System.out.println("SMS: " + msg);  // extra behavior added
    }
}
// Usage: Notifier n = new SMSDecorator(new BasicNotifier());
```

**Analogy:** **Pizza pe toppings** — base pizza wahi rehta hai, tu extra cheese, olives layer-by-layer add karta ja sakta hai bina naya pizza banaye.

**Possible Questions:**

**Q1: Decorator pattern kya hai?**
> Crisp: "Object ko runtime pe naye behaviors se 'wrap' karta hai, bina uski class edit kiye."
> Elaboration: "Ye inheritance ka alternative hai jab humein features ko dynamically, combination mein add karna ho — jaise Email + SMS dono notifications ek saath, bina alag class banaye har combination ke liye."

**Q2: Decorator aur Inheritance mein kab kisko choose karein?**
> "Inheritance compile-time pe fix hota hai aur class explosion create karta hai jab combinations badhein. Decorator runtime pe flexible hai — jitne chahiye utne decorators wrap kar sakte ho, dynamically."

---

### 6️⃣ Observer Pattern

**Definition:** Ek object (**Subject**) ka state change ho toh saare **dependent objects (Observers)** ko automatically notify kiya jaaye.

**Kyun zaroori hua:** Jab ek-se-many relationship honi ho — ek event, multiple listeners ko react karna ho, bina Subject ko har listener ka hardcoded knowledge ho.

```java
interface Observer { void update(double price); }
class Investor implements Observer {
    public void update(double price) { System.out.println("New price: " + price); }
}
class Stock {
    List<Observer> observers = new ArrayList<>();
    void subscribe(Observer o) { observers.add(o); }
    void setPrice(double price) {
        for (Observer o : observers) o.update(price);  // sabko notify
    }
}
```

**Analogy:** **YouTube subscribe** — channel video upload kare, sab subscribers ko notification jaata hai automatically, channel ko individually kisi ko call nahi karna padta.

**Possible Questions:**

**Q1: Observer pattern kya hai?**
> Crisp: "Jab ek object ka state change ho, uske saare dependents automatically notify ho jaate hain."
> Elaboration: "Subject apne observers ki list maintain karta hai. State change hone pe, subject loop karke sab observers ko notify karta hai — ye loose coupling deta hai kyunki Subject ko observer ke internal implementation ka pata nahi hota, bas interface pata hota hai."

**Q2: Observer pattern real-world mein kahan dikhega tujhe apne stack mein?**
> "Samvaad mein Socket.io ka real-time messaging bhi conceptually Observer pattern hai — jab ek user message bheje, saare connected clients (observers) ko event emit hoke notify kiya jaata hai."

---

### 7️⃣ Strategy Pattern

**Definition:** Ek family of algorithms define karo, har ek ko alag class mein encapsulate karo, aur **runtime pe interchange** kar sako.

**🔗 Dot Connect:** Ye **DIP** ka direct implementation hai — client, concrete algorithm pe nahi, ek common `Strategy` interface pe depend karta hai.

```java
interface PaymentStrategy { void pay(double amount); }
class UpiPayment implements PaymentStrategy { public void pay(double amt) { ... } }
class CardPayment implements PaymentStrategy { public void pay(double amt) { ... } }

class Checkout {
    PaymentStrategy strategy;
    Checkout(PaymentStrategy s) { this.strategy = s; }
    void checkout(double amt) { strategy.pay(amt); }
}
// Runtime pe: new Checkout(new UpiPayment()) ya new Checkout(new CardPayment())
```

**Analogy:** **Google Maps** — destination same rehta hai, par tu Walking, Driving, ya Transit strategy choose kar sakta hai runtime pe.

**Possible Questions:**

**Q1: Strategy pattern kya hai?**
> Crisp: "Alag-alag algorithms ko interchangeable banata hai runtime pe, bina client code change kiye."
> Elaboration: "Har algorithm apne class mein hota hai, ek common interface implement karte hue. Client sirf interface ke through interact karta hai, isliye naya strategy add karna ya switch karna aasan hai."

**Q2: Strategy aur Factory mein confusion hoti hai — farak clear kar.** *(Repeat hone wala classic question)*
> "Factory answers **'kaunsa object banao'** — creation ke baare mein hai. Strategy answers **'kaisa behave karo'** — runtime behavior choose karne ke baare mein hai. Dono interface use karte hain, par purpose alag hai."

---

### 8️⃣ Command Pattern

**Definition:** Ek request/action ko **object mein encapsulate** karta hai, taaki usse queue kiya ja sake, undo kiya ja sake, ya later execute kiya ja sake.

**Kyun zaroori hua:** Jab tujhe action ko **data ki tarah treat** karna ho — store karna ho, delay karna ho, ya undo/redo support chahiye ho.

```java
interface Command { void execute(); }
class LightOnCommand implements Command {
    Light light;
    public void execute() { light.turnOn(); }
}
class RemoteControl {
    Command command;
    void setCommand(Command c) { this.command = c; }
    void pressButton() { command.execute(); }
}
```

**Analogy:** **TV remote ka button** — har button ek command hai. Remote ko nahi pata TV internally kaise on hoti hai, woh bas command trigger karta hai.

**Possible Questions:**

**Q1: Command pattern kya hai?**
> Crisp: "Ek request ko object mein wrap karta hai, taaki execute, queue, ya undo kiya ja sake."
> Elaboration: "Ye action ko data mein convert kar deta hai — jisse hum commands ko store, log, queue, ya reverse (undo) kar sakte hain, jo direct method call se possible nahi hota."

**Q2: Command pattern kahan practically useful hai?**
> "Home automation mein — jaise tune DN5.0 mein banaya, ya undo/redo features (text editor), ya task queues jaha commands ko baad mein process karna ho."

---

### 9️⃣ MVC (Model-View-Controller)

**Definition:** Application ko teen layers mein todta hai — **Model** (data/business logic), **View** (UI), **Controller** (dono ke beech coordinator).

**Kyun zaroori hua:** Agar UI code aur business logic mix ho jaaye ek hi jagah, changes karna nightmare ban jaata hai — designer UI change kare toh logic tootne ka risk, developer logic change kare toh UI tootne ka risk. Separation zaroori hai.

**Analogy:** **Restaurant** — Kitchen (**Model** — data/recipes), Menu Card jo customer dekhta hai (**View**), Waiter jo order leke kitchen tak pahunchata hai aur food wapas laata hai (**Controller**).

**🔗 Dot Connect (bahut important):** Tera poora Spring Boot application isi pattern pe based hai — `@RestController` = Controller, `@Entity`/`@Service` = Model, aur JSON response = View (traditional MVC mein HTML view hoti thi, REST mein JSON hi "view" ban jaata hai).

**Possible Questions:**

**Q1: MVC kya hai?**
> Crisp: "Application ko Model (data), View (UI), aur Controller (coordinator) mein separate karta hai."
> Elaboration: "Model business logic/data handle karta hai, View user ko dikhne wala output hai, aur Controller user input leke Model ko update karta hai aur sahi View return karta hai. Ye separation of concerns deta hai."

**Q2: Tera Spring Boot project MVC kaise follow karta hai?**
> "`@RestController` Controller layer hai jo requests handle karta hai, `@Service` aur `@Entity`/Repository Model layer hain jo business logic aur data handle karte hain, aur chunki main REST API bana raha hoon, JSON response hi View ka role play karta hai instead of HTML template."

---

## Topic 3: PL/SQL (Stored Procedures)

### 🎯 Stored Procedures Kahan Se Aaye, Kyun Aaye

1970s-80s mein jab databases (Oracle, SQL Server) enterprise applications ka core banne lage, ek problem samne aayi: application aur database ke beech **har chhoti operation ke liye network round-trip** ho raha tha. Socho ek `TransferFunds` operation — check balance, deduct from account A, add to account B, log transaction — agar ye **4 alag SQL queries** application se bheji jaayein, network latency 4 guna, aur agar beech mein connection tootjaye, data **inconsistent** reh jaata hai (paisa deduct ho gaya, credit nahi hua).

Database vendors ne solution diya: **Stored Procedures** — pehle se compiled, database ke andar hi store hone waala code block, jisme multiple SQL statements ek unit ki tarah execute hote hain, **as a single transaction**. Application sirf ek call karta hai (`EXEC TransferFunds`), poora kaam database ke andar hi ho jaata hai — atomically.

**🔗 Dot Connect (yaad rakh iske liye):** Yehi "as a single transaction" wala idea Week 3 mein Spring ke `@Transactional` annotation mein wapas milega — concept same hai, sirf jagah alag (database-level vs application-level).

---

### Stored Procedure — Core Concept

**Definition:** Ek precompiled set of SQL statements jo database ke andar store hota hai aur naam se call kiya ja sakta hai, with support for logic (IF-ELSE, loops), parameters, aur transaction control.

```sql
CREATE OR REPLACE PROCEDURE TransferFunds(
    p_from_account IN NUMBER,
    p_to_account IN NUMBER,
    p_amount IN NUMBER
) AS
BEGIN
    UPDATE accounts SET balance = balance - p_amount WHERE acc_id = p_from_account;
    UPDATE accounts SET balance = balance + p_amount WHERE acc_id = p_to_account;
    COMMIT;
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
        RAISE;
END;
```

**Analogy:** Ek **saved recipe card** restaurant kitchen mein — chef baar baar poora recipe likhta nahi, bas naam bolta hai "Butter Chicken banao", aur poora predefined process trigger ho jaata hai — consistently, har baar same tareeke se.

---

### Teen Procedures Jo Tune Banaye — Concept Wise

| Procedure | Kya Karta Hai | Key Concept |
|---|---|---|
| `ProcessMonthlyInterest` | Har account pe interest calculate karke credit karta hai | Loop (Cursor) — har account row pe iterate karna |
| `UpdateEmployeeBonus` | Condition ke basis pe bonus update | IF-ELSE control structure |
| `TransferFunds` | Ek account se dusre mein paisa transfer | Transaction safety — COMMIT/ROLLBACK |

---

### 📋 Possible Questions — PL/SQL

**Q1: Stored Procedure kya hota hai aur application-level code se better kyun hai?**
> Crisp: "Ek precompiled SQL code block jo database ke andar store aur execute hota hai."
> Elaboration: "Application se multiple queries bhejne ki jagah, ek hi call se poora logic database ke andar execute ho jaata hai — isse network round-trips kam hote hain, aur critical operations (jaise fund transfer) ko transaction safety milti hai."

**Q2: `TransferFunds` mein agar beech mein failure ho (jaise dusra UPDATE fail ho jaaye), kya hoga?**
> Crisp: "ROLLBACK trigger hoga, koi bhi partial change save nahi hoga."
> Elaboration: "EXCEPTION block mein `ROLLBACK` likha hai — agar dusra UPDATE fail hota hai, poori transaction undo ho jaayegi. Isse ye guarantee milti hai ki paisa account A se deduct hoke 'gayab' nahi hoga agar account B mein credit fail ho jaaye — ye **atomicity** hai (ACID properties mein se ek)."

**Q3: COMMIT aur ROLLBACK mein farak?**
> Crisp: "COMMIT changes ko permanently save karta hai, ROLLBACK unhe undo karta hai."
> Elaboration: "Jab tak COMMIT nahi hota, changes sirf current transaction ke andar hain — kisi aur session ko dikhte nahi. Agar error aaye, ROLLBACK sab kuch pehle jaisa kar deta hai, jaise kuch hua hi nahi."

**Q4: Stored Procedure aur Function mein SQL mein kya farak hai?** *(Common trap question)*
> Crisp: "Function ek value **return** karta hai aur SELECT statement mein use ho sakta hai; Procedure return nahi karta (ya OUT parameters deta hai) aur independently call hota hai."
> Elaboration: "Function typically read-only calculations ke liye use hota hai (jaise `CalculateTax()`), Procedure business operations ke liye jo data modify karte hain (jaise `TransferFunds`)."

**Q5: `ProcessMonthlyInterest` mein multiple accounts pe kaise iterate karte ho?**
> Crisp: "Cursor use karke — row by row process karte hain."
> Elaboration: "Cursor ek pointer hai jo query result set ke through iterate karta hai, taaki har account row pe individually interest calculate karke update kiya ja sake, ek loop ke andar."

**Q6: Stored Procedures ka koi downside/tradeoff bhi hai?** *(Advanced follow-up)*
> Crisp: "Business logic database mein lock ho jaati hai, jo version control aur testing ko mushkil banata hai."
> Elaboration: "Application code (Java) ki tarah Stored Procedures ko unit test karna, Git mein track karna, ya CI/CD pipeline mein deploy karna utna smooth nahi hota. Isliye modern architectures mein heavy business logic application layer (Service class) mein rakhte hain, aur database sirf simple, performance-critical operations ke liye use karte hain."

---

## Topic 4: JUnit 5 + Mockito (Testing)

### 🎯 JUnit & Mockito Kahan Se Aaye, Kyun Aaye

Jab software applications bade hote gaye, ek problem samne aayi: developer code likhta tha, manually run karke check karta tha "chal raha hai ya nahi", aur agar kal koi aur developer ussi code ko touch kare, koi guarantee nahi thi ki purana functionality abhi bhi kaam kar raha hai ya nahi. Isse **regression bugs** kehte hain — naya change purane kaam ko todh de, bina kisi ko pata chale, jab tak production mein user complain na kare.

**JUnit** (1997, Kent Beck aur Erich Gamma ne banaya) ne is problem ko solve kiya — ek **framework jisse tests khud automatically likhe aur run kiye ja sakein**, taaki har code change ke baad tu ek button dabake verify kar sake sab kuch abhi bhi kaam kar raha hai ya nahi.

Par ek aur problem thi: agar tera `OrderService` ek real database ya external `PaymentGateway` API call karta hai, test run karne ke liye tujhe **real DB aur real payment gateway chahiye** — slow, unreliable, aur risky (galti se real payment ho sakta hai!). Isliye **Mockito** aaya — jisse tu fake ("mock") versions bana sakta hai in dependencies ke, sirf apni class ka logic isolate karke test karne ke liye.

**🔗 Dot Connect:** Yaad kar Week 1 ka SRP — "ek class ka ek kaam." Testing isi principle ka **proof** hai — agar teri class properly separated hai (single responsibility), usko isolate karke test karna aasan hota hai. Agar class mein 10 responsibilities mixed hain, testing bhi mushkil ho jaati hai.

---

### Core Concept — Test Lifecycle Annotations

```java
class OrderServiceTest {
    @BeforeAll
    static void setupOnce() { /* DB connection pool init — ek baar */ }
    
    @BeforeEach
    void setup() { /* fresh mock objects — har test se pehle */ }
    
    @Test
    void shouldCalculateTotalCorrectly() { ... }
    
    @AfterEach
    void cleanup() { /* har test ke baad reset */ }
    
    @AfterAll
    static void teardownOnce() { /* connection close — ek baar */ }
}
```

**Analogy:** Ek **science lab experiment** — `@BeforeEach` matlab har experiment se pehle table saaf karo aur fresh equipment lao (taaki ek test ka leftover data dusre test ko affect na kare), `@AfterEach` matlab experiment ke baad cleanup, `@BeforeAll`/`@AfterAll` matlab poori lab session shuru/khatam karne wale one-time setup (jaise lab khud kholna/band karna).

---

### AAA Pattern — Har Test Ka Structure

```java
@Test
void shouldReturnTotalPrice() {
    // Arrange
    Order order = new Order(100, 2);
    
    // Act
    double total = order.calculateTotal();
    
    // Assert
    assertEquals(200, total);
}
```

- **Arrange** — data/mocks/objects set up karo
- **Act** — jo method test karna hai, usse call karo
- **Assert** — result expected ke saath compare karo

**Analogy:** Ek **court case** — Arrange matlab evidence collect karna, Act matlab statement dena, Assert matlab judge ka verdict (sahi hai ya galat).

---

### Mockito — Fake Dependencies Banane Ka Tareeka

**🔗 Dot Connect:** Week 2 mein tu Spring ka `@Autowired` seekhega — real dependencies inject karne ke liye. Mockito **testing ke context mein wahi kaam karta hai**, bas real dependency ki jagah fake (mock) inject karta hai.

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {
    @Mock
    PaymentGateway paymentGateway;  // fake — real API call nahi hoga
    
    @InjectMocks
    OrderService orderService;  // OrderService ke andar paymentGateway inject ho jaayega
    
    @Test
    void shouldProcessPaymentSuccessfully() {
        when(paymentGateway.pay(100)).thenReturn(true);  // stub — "jab ye call ho, ye return karo"
        
        boolean result = orderService.checkout(100);
        
        assertTrue(result);
        verify(paymentGateway).pay(100);  // confirm — actually call hua ya nahi
    }
}
```

| Term | Kaam |
|---|---|
| `@Mock` | Fake object banata hai (koi real logic nahi, sab methods by default "do nothing") |
| `@InjectMocks` | Fake objects ko real class ke andar automatically inject karta hai |
| `when(x).thenReturn(y)` | Stubbing — "jab ye method call ho with ye input, ye output do" |
| `verify(mock)` | Confirm karo method actually call hua |
| `verify(mock, never())` | Confirm karo method call **nahi** hua |
| `assertThrows(Exception.class, () -> ...)` | Confirm karo exception throw hua expected type ka |

**Analogy — poora Mockito concept:** Movie shooting mein **stunt double** — asli actor (real PaymentGateway/Database) ko dangerous scene (real money transaction/slow network call) mein nahi daalte. Double (mock) use karte hain jo bilkul waisa hi dikhta/behave karta hai jitna hume chahiye, but bina real risk ke.

---

### 📋 Possible Questions — JUnit + Mockito

**Q1: Unit testing kya hai aur kyun zaroori hai?**
> Crisp: "Code ke ek chhote isolated part (unit) ko automatically test karna, taaki wo expected tarike se behave kare."
> Elaboration: "Ye manual testing ki jagah automated, repeatable verification deta hai — har code change ke baad turant pata chal jaata hai kuch tootha toh nahi (regression detection)."

**Q2: @Mock aur @InjectMocks mein farak?** *(Sabse zyada poocha jaane wala question)*
> Crisp: "@Mock fake dependency banata hai, @InjectMocks usse real class mein inject karta hai."
> Elaboration: "@Mock se main `PaymentGateway` ka fake version banata hoon jiska real behavior nahi hota. @InjectMocks Mockito ko batata hai ki is fake ko `OrderService` (jo main actually test kar raha hoon) ke constructor/fields mein daal do — taaki main OrderService ka logic isolate karke test kar sakoon, bina real PaymentGateway ke."

**Q3: when().thenReturn() kya karta hai, aur agar main isko na likhun toh kya hoga?**
> Crisp: "Ye mock ko batata hai specific input pe kya output dena hai — stubbing."
> Elaboration: "Bina stubbing ke, mock method call hone pe default value return karta hai — object ke liye `null`, boolean ke liye `false`, number ke liye `0`. Isse test unexpected fail ho sakta hai agar main manually expected return value define nahi karta."

**Q4: verify() kis liye use hota hai, assert se alag kyun hai?**
> Crisp: "verify() confirm karta hai ki ek method call **hua** ya nahi; assert result **value** check karta hai."
> Elaboration: "assertEquals() ye check karta hai ki output sahi hai. verify() ye check karta hai ki **behavior** sahi hua — jaise `verify(paymentGateway).pay(100)` confirm karta hai ki pay() method actually 100 ke saath call hua, chahe uska return value kuch bhi tha."

**Q5: AAA pattern kya hai aur isse follow karna kyun important hai?**
> Crisp: "Arrange-Act-Assert — test likhne ka structured tareeka."
> Elaboration: "Ye tests ko readable aur consistent banata hai — koi bhi teammate test padh ke turant samajh sakta hai setup kya hai, kya action ho raha hai, aur expected result kya hai — bina poora logic trace kiye."

**Q6: @BeforeEach har test se pehle kyun run hota hai — kya sirf ek baar setup nahi kar sakte?**
> Crisp: "Taaki har test independent aur isolated rahe — ek test ka data dusre ko affect na kare."
> Elaboration: "Agar setup sirf ek baar ho (@BeforeAll jaisa), aur ek test us shared state ko modify kar de, agla test unexpectedly fail ho sakta hai jo actual bug nahi, balki test pollution hai. @BeforeEach fresh state guarantee karta hai har test ke liye."

**Q7: assertThrows() kaise use karte hain aur kab zaroori hota hai?**
> Crisp: "Confirm karta hai ki code expected exception throw kare — jab hum negative/error scenarios test karte hain."
> Elaboration: "Jaise agar `checkout()` ko negative amount diya jaaye, expected hai IllegalArgumentException throw ho. `assertThrows(IllegalArgumentException.class, () -> orderService.checkout(-100))` confirm karta hai exactly yehi hua, warna test fail ho jaayega."

**Q8: Real project mein Mockito kaise use kiya tune?** *(Personalize karna — apna answer)*
> *(Isse apne words mein customize kar — example structure: "Maine Service layer test kiya jisme Repository ko mock kiya, taaki real MongoDB/database ke bina bhi business logic verify ho sake — fast aur reliable tests mile.")*

---

## Topic 5: SLF4J + Logback (Logging)

### 🎯 Logging Frameworks Kahan Se Aaye, Kyun Aaye

Har application mein sabse basic debugging tool tha `System.out.println()` — jab kuch galat ho, print statement daal do, dekho kya chal raha hai. Par production applications mein ye approach **bilkul fail** ho jaati hai:

1. **Koi control nahi** — agar 1000 print statements hain, sabko ek saath dekhna hai ya sirf errors, koi choice nahi
2. **Console pe hi jaata hai** — production server pe koi console dekhne nahi baitha, output kahin save nahi hota
3. **Performance cost** — string concatenation har baar hoti hai, chahe zaroorat ho ya na ho
4. **No context** — sirf message hai, kaunse module se aaya, kab aaya, kitna severe hai — kuch pata nahi

Isliye **structured logging frameworks** bane. **SLF4J (Simple Logging Facade for Java)** ek **facade/interface** hai — matlab ye khud logging nahi karta, ye sirf ek **common API** deta hai. **Logback** actual implementation hai jo real logging karta hai (file mein likhna, console pe print karna, rotate karna, etc).

**🔗 Dot Connect (ye samajhna important hai):** SLF4J aur Logback ka relationship, **JPA aur Hibernate** ke relationship jaisa hai (jo Week 2 mein aayega) — SLF4J ek specification/interface hai, Logback uska implementation hai. Isse agar kal tu Logback se Log4j2 pe switch kare, tera application code (jo SLF4J API use karta hai) **bilkul nahi badalta** — sirf underlying implementation swap hoti hai. Yehi DIP (abstraction pe depend karo, concrete implementation pe nahi) ka real-world example hai.

---

### Log Levels — Severity Hierarchy

```
TRACE → DEBUG → INFO → WARN → ERROR
(sabse zyada detail)              (sabse critical)
```

**Yaad rakhne ka trick:** *"Truly Devoted Interns Work Enthusiastically"* — ya jo bhi easy lage tujhe.

**Analogy — Doctor ka checkup record:**
- **TRACE** — har chhota detail (heart rate ka fluctuation second-by-second) — sirf deep debugging ke liye
- **DEBUG** — detailed notes jo sirf doctor padhta hai treatment plan ke liye
- **INFO** — normal update ("patient stable, checkup complete")
- **WARN** — "BP thoda high hai, dhyan rakhna" — abhi emergency nahi, par monitor karo
- **ERROR** — "emergency, turant action lo"

**Production mein important concept:** Environment ke hisaab se log level set karte hain — **production mein typically `INFO` ya `WARN`** rakhte hain (taaki noise kam ho, disk space bache), **development mein `DEBUG`** rakhte hain (taaki har detail dikhe debugging ke liye).

---

### Parameterized Logging — Kyun Zaroori Hai

```java
// BAD — string concatenation hamesha hoti hai
log.info("User " + userId + " logged in at " + timestamp);

// GOOD — parameterized
log.info("User {} logged in at {}", userId, timestamp);
```

**🔗 Yahi wo concept hai jo interviewer specifically test karta hai — deeply samajh:**

Agar log level `INFO` pe set hai aur tune `log.debug("User " + userId + "...")` likha hai — chunki `debug` enabled nahi hai, log print toh nahi hoga, **par string concatenation phir bhi ho jaayegi**, kyunki Java pehle expression evaluate karta hai, phir method ko call karta hai. Ye **wasted computation** hai — especially jab loop ke andar hazaron baar log call ho.

Parameterized version (`{}`  placeholders) mein, Logback **pehle check karta hai** level enabled hai ya nahi, **tabhi** string banata hai. Isse unnecessary work bachta hai.

**Analogy:** Ye waise hi hai jaise tu kisi ko letter likhne se pehle check kare "kya ye insaan mujhe reply karega?" — agar nahi, letter likhna hi mat shuru karo (parameterized), vs letter poora likh ke phir decide karna bhejna hai ya nahi (concatenation — waste effort).

---

### 📋 Possible Questions — SLF4J + Logback

**Q1: SLF4J aur Logback mein farak kya hai?** *(Sabse common question, must-nail)*
> Crisp: "SLF4J ek logging facade/interface hai, Logback uska actual implementation hai."
> Elaboration: "SLF4J khud koi logging nahi karta — ye ek common API provide karta hai jise application code use karta hai. Logback (ya Log4j2) actual kaam karta hai — file mein likhna, rotate karna, format karna. Isse hum implementation switch kar sakte hain bina application code change kiye — jaise JPA/Hibernate ka relationship."

**Q2: Log levels ka order bata aur inka use case?**
> Crisp: "TRACE → DEBUG → INFO → WARN → ERROR, detail se criticality tak."
> Elaboration: "TRACE sabse granular detail hai, DEBUG development-time troubleshooting ke liye, INFO normal application flow track karne ke liye, WARN potential problem jo abhi critical nahi, aur ERROR actual failure jise immediate attention chahiye. Production mein typically INFO ya WARN use karte hain, DEBUG/TRACE sirf local development mein."

**Q3: Parameterized logging performance ke liye better kyun hai?** *(Deep-dive question, tayaar rakh)*
> Crisp: "String concatenation avoid karta hai jab log level disabled ho, isse unnecessary computation nahi hoti."
> Elaboration: "`log.info(\"User \" + userId)` mein Java string ko hamesha build karta hai, chahe INFO level enabled ho ya na ho. `log.info(\"User {}\", userId)` mein Logback pehle level check karta hai, tabhi placeholder ko actual value se replace karta hai — agar level disabled hai, koi string building hoti hi nahi."

**Q4: Production mein log level kya rakhna chahiye, aur kyun?**
> Crisp: "Typically INFO ya WARN — taaki noise kam ho aur disk/performance overhead na aaye."
> Elaboration: "DEBUG/TRACE production mein rakhne se logs bahut zyada verbose ho jaate hain — disk space fill hoti hai fast, aur important messages (WARN/ERROR) bhi dhoondhna mushkil ho jaata hai bheed mein. Development/staging mein DEBUG useful hota hai deep troubleshooting ke liye."

**Q5: Agar tujhe production mein ek intermittent bug debug karna ho jo reproduce nahi ho raha, kya approach lega logging ke through?**
> *(Scenario-based question — reasoning dikhana)*
> "Main relevant classes ka log level temporarily DEBUG pe increase karunga (bina poori application ko, sirf specific package/logger ke liye — Logback isko allow karta hai per-package configuration se), taaki next occurrence pe detailed context mile bina overall system ko overload kiye."

**Q6: Logback configuration file (`logback.xml`) mein kya define hota hai?**
> Crisp: "Log format, output destination (console/file), aur rotation policy."
> Elaboration: "Isme hum define karte hain log kaha jaaye (console, file, ya dono), kaisa format ho (timestamp, level, message pattern), aur file rotation rules (jaise daily naya file banao, ya size limit cross hone pe naya file) — taaki log files infinitely bade na hote rahein."

---

# WEEK 2

## Spring Core — IoC, DI, Maven, JPA

### 🎯 Spring Framework Kahan Se Aaya, Kyun Aaya

2002-2003 mein Java enterprise development ka standard tha **EJB (Enterprise JavaBeans)** — heavyweight, complex, XML configuration ka bojh, aur testing **bahut** mushkil (kyunki components tightly coupled the container ke saath, unit testing ke liye poora application server chahiye hota tha).

**Rod Johnson** ne 2003 mein apni book *"Expert One-on-One J2EE Design and Development"* mein dikhaya ki EJB ke bina, **simpler, testable, POJO-based** (Plain Old Java Objects) architecture se bhi enterprise applications banayi ja sakti hain. Usi se **Spring Framework** born hua — core idea: **objects apne dependencies khud na banayein, koi external cheez unhe provide kare.**

**🔗 Yahi wo moment hai jaha Week 1 ka DIP (Dependency Inversion Principle) literally ek framework ban gaya.** Yaad kar — DIP kehta hai "high-level module directly low-level pe depend na kare, dono abstraction pe depend karein." Spring ne isko **automate** kar diya — ek container banaya jo khud objects create karta hai aur unke dependencies inject karta hai.

---

### IoC (Inversion of Control) — Control Kiska Hai

**Normal approach (Control application ke paas):**
```java
class OrderService {
    PaymentGateway gateway = new RazorpayGateway();  // main khud object bana raha hoon
}
```
Yahan `OrderService` khud decide kar raha hai ki `RazorpayGateway` chahiye — control uske paas hai. Agar kal `StripeGateway` chahiye, is class ko edit karna padega.

**Spring approach (Control Spring ke paas — "Inverted"):**
```java
@Service
class OrderService {
    private final PaymentGateway gateway;
    
    @Autowired
    OrderService(PaymentGateway gateway) {  // Spring provide karega
        this.gateway = gateway;
    }
}
```
Ab `OrderService` sirf bolta hai "mujhe koi `PaymentGateway` chahiye" — kaunsa concrete implementation milega, ye decision **Spring Container** leta hai. Control application code se container ko chala gaya — isliye naam **"Inversion" of Control**.

**Analogy:** Normal restaurant mein tu khud grocery shopping karta hai (control tere paas). Spring ek **catering service** hai — tu bolta hai "mujhe biryani chahiye", woh khud ingredients arrange karke bana ke deta hai. Tera control invert ho gaya — tu consumer hai, provider nahi.

**DI (Dependency Injection)** IoC ka **mechanism** hai — jis tareeke se Spring dependencies provide karta hai:
- **Constructor Injection** (preferred) — constructor ke through
- **Setter Injection** — setter method ke through
- **Field Injection** — directly field pe `@Autowired` (avoid karna chahiye)

---

### Spring Stereotype Annotations

| Annotation | Kaam | Layer |
|---|---|---|
| `@Component` | Generic Spring-managed bean | Koi bhi |
| `@Service` | Business logic layer | Service layer (semantic clarity ke liye) |
| `@Repository` | Data access layer | DAO layer (bonus: exception translation) |
| `@Controller` / `@RestController` | Web layer | Controller (Week 3 mein detail) |
| `@Autowired` | "Yahan dependency inject karo" | — |
| `@ComponentScan` | "In packages mein components dhoondho" | — |

**🔗 Dot Connect — @Repository special kyun hai:** Ye sirf naming convention nahi hai. `@Repository` Spring ko batata hai ki agar database-related exception aaye (jaise SQL exception), usse Spring ke apne unchecked `DataAccessException` mein translate kar do — taaki tera code specific database vendor (Oracle vs MySQL) ke exceptions pe directly depend na kare.

---

### 📋 Possible Questions — IoC & DI

**Q1: IoC kya hai?**
> Crisp: "Object creation aur dependency management ka control application code se ek external container (Spring) ko chala jaata hai."
> Elaboration: "Normally class apne dependencies khud `new` keyword se banati hai. IoC mein, ek container ye responsibility leta hai — class sirf bolti hai use kya chahiye, container decide karta hai kya dega."

**Q2: DI aur IoC mein farak?** *(Bahut common, must-nail)*
> Crisp: "IoC ek broad principle hai (control ka inversion), DI uska specific implementation mechanism hai (dependencies inject karna)."
> Elaboration: "IoC container ke through implement hone ke multiple tareeke ho sakte hain, DI unmein se sabse common hai — jisme dependencies constructor, setter, ya field ke through inject hoti hain."

**Q3: Constructor Injection kyun preferred hai Field Injection ke against?** *(Deep-dive, tayaar rakh)*
> Crisp: "Constructor injection immutability, testability, aur fail-fast behavior deta hai."
> Elaboration: "Constructor injection se fields `final` ban sakti hain — object create hote hi fully initialized guaranteed hota hai. Testing mein bina Spring container ke bhi main manually `new OrderService(mockGateway)` bana sakta hoon. Field injection (`@Autowired` directly field pe) reflection use karta hai, immutable nahi ban sakta, aur circular dependency jaisi problems runtime tak chhup sakti hain jo constructor injection compile/startup time pe hi pakad leta hai."

**Q4: @Component, @Service, @Repository mein functional farak kya hai — sab toh beans hi bana rahe hain?**
> Crisp: "Functionally similar hain, par @Repository exception translation deta hai aur baaki semantic clarity ke liye layer specify karte hain."
> Elaboration: "@Service aur @Component technically same kaam karte hain — sirf code readability/intent clear karne ke liye alag naam hain. @Repository extra behavior deta hai — database exceptions ko Spring ke common `DataAccessException` hierarchy mein translate karta hai."

**Q5: Circular dependency kya hoti hai aur Spring ise kaise handle karta hai?**
> Crisp: "Jab do beans ek dusre pe depend karein (A needs B, B needs A) — Spring startup pe fail ho sakta hai."
> Elaboration: "Constructor injection mein circular dependency turant startup pe error deti hai (fail-fast) — jo achha hai kyunki problem jaldi pata chalti hai. Field/Setter injection mein Spring kabhi-kabhi resolve kar leta hai lazy initialization se, par ye actually bad design ka sign hai — better hai classes ko redesign karna taaki circular dependency ho hi na."

---

### 🎯 Maven Kahan Se Aaya, Kyun Aaya

2000s ke shuru mein Java projects **Ant** use karte the build ke liye — par Ant mein har project ka build script alag likhna padta tha, aur dependencies (JAR files) **manually download karke** project mein daalni padti thi. Agar teri library ko khud kisi aur library ki zaroorat ho (transitive dependency), tujhe woh bhi manually dhoondhni padti thi. Chaos.

**Maven (2004)** ne solve kiya: ek **standard, convention-based** build tool jo dependencies ko ek central repository (Maven Central) se **automatically download** karta hai, transitive dependencies khud resolve karta hai, aur build process ko standard lifecycle mein daal deta hai.

**Analogy:** Maven ek **grocery delivery app** hai — `pom.xml` tera shopping list hai (kaunsi dependencies chahiye, kaunsa version), Maven khud jaake (Maven Central repository se) download karke sahi jagah rakh deta hai, aur agar ek item ke saath dusra zaroori hai (jaise pasta ke saath sauce), woh bhi automatically le aata hai (transitive dependency).

### Build Lifecycle
```
validate → compile → test → package → verify → install → deploy
```
- **validate** — project structure sahi hai check
- **compile** — source code compile
- **test** — unit tests run (yaad Week 1 JUnit!)
- **package** — JAR/WAR banao
- **install** — local repository mein daalo
- **deploy** — remote repository mein bhejo (team ke liye)

---

### 📋 Possible Questions — Maven

**Q1: pom.xml kya hai?**
> Crisp: "Project Object Model — dependencies, plugins, aur build configuration define karta hai XML format mein."
> Elaboration: "Isme project ki details (groupId, artifactId, version), dependencies (kaunsi libraries chahiye), aur build plugins hote hain jo compile/package/test process control karte hain."

**Q2: Transitive dependency kya hoti hai?**
> Crisp: "Ek dependency jo tune directly add nahi ki, par tune jo library add ki, usko chahiye thi."
> Elaboration: "Jaise agar main Spring Boot Starter Web add karta hoon, woh khud Tomcat, Jackson, aur kayi aur libraries pull karta hai automatically — mujhe unko manually add nahi karna padta. Maven ye dependency tree khud resolve karta hai."

**Q3: mvn install aur mvn deploy mein farak?**
> Crisp: "install local repository mein daalta hai (sirf tere machine ke liye), deploy remote/shared repository mein (team ke liye)."
> Elaboration: "install se woh JAR sirf tere local `.m2` folder mein jaata hai, jo tere doosre local projects use kar sakte hain. deploy se woh company ke shared artifact repository (jaise Nexus) mein jaata hai jaha poori team access kar sake."

---

### 🎯 Spring Data JPA + Hibernate Kahan Se Aaya, Kyun Aaya

**🔗 Dot Connect — Ye samajhna zaroori hai:** Week 1 mein tune PL/SQL mein raw SQL likha — Stored Procedures directly database ke saath baat karte hue. Par Java jaisi **Object-Oriented** language mein, developer ko baar-baar SQL likhna aur uske result (rows/columns) ko manually Java objects mein convert karna — ye **repetitive aur error-prone** kaam tha. Isko bolte hain **"Object-Relational Impedance Mismatch"** — objects aur relational tables ka structure fundamentally alag hota hai (objects mein inheritance/relationships hote hain, tables mein sirf rows-columns).

**Hibernate (2001, Gavin King)** is problem ko solve karne aaya — **ORM (Object-Relational Mapping)**: Java objects ko directly database tables se map kar do, aur SQL khud-ba-khud generate ho jaaye object operations se.

Baad mein Java community ne isko standardize kiya — **JPA (Java Persistence API)** ek **specification** ban gayi (rules ka set), aur **Hibernate uska ek implementation** ban gaya. Phir Spring ne **Spring Data JPA** banaya jo Hibernate ke upar ek aur layer hai — jisse repository methods likhne ki bhi zaroorat nahi, sirf method **naam** se query generate ho jaati hai.

**🔗 Isliye ye pattern dobara dikha:** JPA/Hibernate ka relationship, SLF4J/Logback jaisa hi hai — **specification vs implementation.**

---

### Core Annotations

```java
@Entity
class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    private String email;
}

interface EmployeeRepository extends JpaRepository<Employee, Long> {
    Employee findByEmail(String email);  // Hibernate khud query bana leta hai method naam se
    List<Employee> findByNameContaining(String keyword);
}
```

| Annotation/Concept | Kaam |
|---|---|
| `@Entity` | Batata hai ye class ek database table hai |
| `@Id` | Primary key field |
| `@GeneratedValue` | ID auto-generate ho (auto-increment) |
| `JpaRepository<Entity, ID>` | CRUD methods free mein milte hain — save(), findById(), delete(), findAll() |
| Derived Query Methods | Method naam se hi query ban jaati hai — `findByEmail`, `findByNameContaining` |
| H2 | In-memory database — testing/dev, restart pe data gone |
| `ddl-auto` | `create`, `update`, `validate`, `none` — schema kaise manage ho |

**Analogy:** JpaRepository ek **smart form** hai — tu bas method ka naam bolta hai ("findByEmail"), backend office (Hibernate) khud samajh jaata hai kya query chahiye aur bana deta hai. Tujhe SQL likhne ki zaroorat nahi.

---

### 📋 Possible Questions — Spring Data JPA & Hibernate

**Q1: JPA aur Hibernate mein farak kya hai?** *(Sabse zyada poocha jaane wala question is topic mein)*
> Crisp: "JPA ek specification hai (interface/rules), Hibernate uska implementation hai (actual working code)."
> Elaboration: "JPA batata hai *kya* hona chahiye (annotations jaise @Entity, @Id, methods ka contract) — ye khud koi code execute nahi karta. Hibernate actually implement karta hai *kaise* — SQL generate karna, connections manage karna. Agar kal main Hibernate se EclipseLink (dusra JPA implementation) switch karu, mera application code (jo JPA annotations use karta hai) touch nahi hoga."

**Q2: JpaRepository extend karne se hume kya milta hai bina kuch likhe?**
> Crisp: "Basic CRUD operations free mein — save, findById, findAll, delete, etc."
> Elaboration: "JpaRepository, Spring Data JPA ka ek interface hai jo already generic CRUD methods define karta hai. Main sirf `interface EmployeeRepository extends JpaRepository<Employee, Long>` likhta hoon, aur mujhe implementation likhne ki zaroorat nahi — Spring runtime pe khud proxy class generate karta hai."

**Q3: Derived Query Methods kaise kaam karte hain — `findByEmail` likhne se query kaise ban jaati hai?**
> Crisp: "Spring Data JPA method ke naam ko parse karke query generate karta hai — koi implementation likhni nahi padti."
> Elaboration: "`findByEmail(String email)` — Spring 'findBy' ko dekh ke samajh jaata hai ye ek SELECT query hai, aur 'Email' field name se WHERE clause bana deta hai: `SELECT * FROM employee WHERE email = ?`. Complex queries ke liye keywords chain kar sakte hain jaise `findByNameContainingAndAgeGreaterThan`."

**Q4: ddl-auto=update production mein risky kyun hai?** *(Interviewer ye trap-style question poochta hai)*
> Crisp: "Ye automatically schema change kar sakta hai bina warning ke — columns drop/modify ho sakte hain unintentionally."
> Elaboration: "`update` mode mein Hibernate entity classes dekh ke khud schema alter karta hai. Production mein ye dangerous hai — agar galti se ek field remove ki entity se, corresponding column drop ho sakta hai with data loss. Production mein `validate` (sirf check kare, change na kare) ya proper migration tools jaise Flyway/Liquibase use karte hain jo version-controlled, reviewable schema changes dete hain."

**Q5: N+1 query problem kya hai?** *(Advanced question, agar poochein)*
> Crisp: "Jab ek list fetch karne ke liye 1 query lagti hai, par har item ke related data ke liye alag-alag N extra queries chal jaati hain."
> Elaboration: "Jaise agar main 100 Employees fetch karu aur har Employee ka Department lazily load ho, toh 1 query Employees ke liye + 100 alag queries Department ke liye — total 101 queries, jo bahut inefficient hai. Fix: `JOIN FETCH` ya `@EntityGraph` use karke ek hi query mein related data bhi le aana."

**Q6: H2 database kya hai aur kab use karte hain?**
> Crisp: "Ek in-memory database, mainly testing/development ke liye — application restart pe data gayab ho jaata hai."
> Elaboration: "Real production database (MySQL/Postgres/Oracle) ke bina bhi hum quickly application test kar sakte hain, kyunki H2 memory mein hi chalta hai — koi separate DB server setup nahi karna padta. Isliye JUnit tests mein bhi commonly use hota hai."

---

### 🔗 Week 2 Ka Full Connection (Recap Mode)

> "Maine IoC/DI se shuru kiya — jo Week 1 ke Dependency Inversion Principle ko real framework mein convert karta hai. Maven se dependencies manage kiya, aur Spring Data JPA se — jo Hibernate ka layer hai — database operations ko bina raw SQL likhe handle kiya, jo Week 1 ke PL/SQL approach se bilkul alag hai, par same goal — data ko efficiently manage karna."

---

# WEEK 3

## Spring Boot REST APIs + Exception Handling + JWT

### 🎯 REST APIs Kahan Se Aaye, Kyun Aaye

2000 mein **Roy Fielding** ne apni PhD dissertation mein REST (**Re**presentational **S**tate **T**ransfer) define kiya. Us waqt web applications banane ke liye SOAP jaisa protocol use hota tha — bahut heavyweight, XML-based, strict contracts, aur complex.

Fielding ka observation tha — web already ek **simple, scalable pattern** follow karta hai: har cheez ek **resource** hai (URL se identify hoti hai), aur **standard HTTP methods** (GET, POST, PUT, DELETE) se us resource pe operations hoti hain. Usne socha, **isi pattern ko APIs banane ke liye kyun na use karein** — simple, stateless, aur HTTP ke existing infrastructure (caching, status codes) ka fayda uthate hue.

**🔗 Dot Connect:** Week 2 mein tune JPA se database ko Java objects mein represent kiya. Ab REST APIs woh **wahi objects (data) ko HTTP ke through duniya tak expose karte hain** — taaki Angular frontend (Week 5) ya koi bhi client unhe access kar sake.

---

### REST Controller — Core Building Blocks

```java
@RestController
@RequestMapping("/api/employees")
class EmployeeController {
    
    @Autowired
    EmployeeService service;  // Week 2 ka DI wapas yahan!
    
    @GetMapping("/{id}")
    ResponseEntity<Employee> getEmployee(@PathVariable Long id) {
        return ResponseEntity.ok(service.findById(id));
    }
    
    @GetMapping
    List<Employee> getAll(@RequestParam(required = false) String department) {
        return service.findByDepartment(department);
    }
    
    @PostMapping
    ResponseEntity<Employee> create(@RequestBody @Valid Employee emp) {
        return ResponseEntity.status(201).body(service.save(emp));
    }
}
```

| Annotation | Kaam | HTTP Method |
|---|---|---|
| `@RestController` | Batata hai ye class JSON return karegi, HTML view nahi | — |
| `@RequestMapping` | Base path define (jaise `/api/employees`) | — |
| `@GetMapping` | Data fetch | GET |
| `@PostMapping` | Naya resource banao | POST |
| `@PutMapping` | Poora resource replace karo | PUT |
| `@DeleteMapping` | Resource delete karo | DELETE |
| `@PathVariable` | URL ke andar se value nikalo — `/employees/{id}` mein `id` | — |
| `@RequestParam` | Query string se value nikalo — `?department=IT` | — |
| `@RequestBody` | JSON body ko Java object mein convert (deserialize) karo | — |

**Analogy — HTTP methods:** Ek **library** ka system — GET matlab "kitab dikhao" (dekhna, badalna nahi), POST matlab "nayi kitab add karo", PUT matlab "poori kitab ka content replace karo", DELETE matlab "kitab hata do".

---

### HTTP Status Codes

| Code | Matlab | Kab Use |
|---|---|---|
| 200 | OK | Successful GET/PUT |
| 201 | Created | Successful POST (naya resource bana) |
| 400 | Bad Request | Client ne galat/invalid data bheja |
| 401 | Unauthorized | Login/token missing ya invalid |
| 404 | Not Found | Resource exist hi nahi karta |
| 500 | Internal Server Error | Backend mein kuch crash hua |

**Analogy:** Waiter ka status update — "ready hai" (200), "naya order confirm ho gaya" (201), "menu mein ye item hai hi nahi tumne kya order kiya" (400), "tum is table pe order karne authorized nahi ho" (401), "ye dish exist nahi karti menu mein" (404), "kitchen mein aag lag gayi, kuch nahi ban sakta abhi" (500).

---

### 📋 Possible Questions — REST APIs

**Q1: REST kya hai aur RESTful API kise kehte hain?**
> Crisp: "REST ek architectural style hai jisme resources ko URLs se identify karte hain aur standard HTTP methods se operate karte hain."
> Elaboration: "RESTful API woh hai jo REST ke principles follow kare — stateless ho (har request independent, server session store na kare), resources ko proper nouns se represent kare (`/employees`, `/employees/1`, verbs nahi jaise `/getEmployee`), aur HTTP methods (GET/POST/PUT/DELETE) se operations define kare."

**Q2: @PathVariable aur @RequestParam mein farak?** *(Bahut common question)*
> Crisp: "@PathVariable URL path ka part hoti hai, @RequestParam query string ka part."
> Elaboration: "`/employees/5` mein `5` PathVariable hai — URL structure ka hissa, resource identify karta hai. `/employees?department=IT` mein `department=IT` RequestParam hai — optional filtering/searching ke liye use hota hai, URL ke `?` ke baad."

**Q3: PUT aur PATCH mein farak?** *(Advanced follow-up, tayaar rakh)*
> Crisp: "PUT poora resource replace karta hai, PATCH sirf specific fields update karta hai."
> Elaboration: "Agar main PUT se sirf email bhejta hoon, baaki fields (jaise name) `null` ho sakti hain kyunki PUT poore object ko replace karta hai. PATCH partial update deta hai — sirf jo fields bheji hain, wahi update hoti hain."

**Q4: REST APIs stateless kyun hone chahiye?**
> Crisp: "Taaki server ko client ka koi session state store na karna pade — scalability aasan ho."
> Elaboration: "Agar server session store kare, load-balanced multiple servers ke beech session sync karna problem ban jaata hai. Stateless approach mein har request apne aap mein complete hoti hai (jaise JWT token ke saath), koi bhi server usse independently handle kar sakta hai."

**Q5: 200 aur 201 ka farak, aur kab kaunsa use karte ho?**
> Crisp: "200 successful operation ke liye general hai, 201 specifically naya resource create hone pe."
> Elaboration: "GET ya PUT successful hone pe 200 return karte hain. POST se jab naya resource banta hai (jaise naya employee add hua), 201 return karna correct practice hai — ye client ko explicitly batata hai 'kuch naya bana hai'."

---

### 🎯 Global Exception Handling Kahan Se Aaya, Kyun Aaya

Jaise-jaise applications mein controllers badhte gaye, ek problem ubhri — **har controller apna khud ka try-catch likh raha tha** exceptions handle karne ke liye. Agar `EmployeeController`, `OrderController`, `PaymentController` — sab mein alag-alag jagah similar error-handling logic duplicate ho raha ho (jaise "resource not found toh 404 return karo, validation fail toh 400 return karo"), ye Week 1 ke **SRP violation** jaisa hai — error handling ka concern controller ki actual responsibility (request handle karna) ke saath mix ho raha hai.

Spring ne solution diya: **`@ControllerAdvice`** — ek **centralized exception handler** jo poore application ke liye kaam kare, taaki individual controllers clean rahein, sirf apna business logic handle karein.

**🔗 Dot Connect:** Yehi exact principle hai jo Week 1 mein tune SRP mein padha — "ek concern, ek jagah." Exception handling ab ek dedicated class ki responsibility hai, har controller ki nahi.

```java
@ControllerAdvice
class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)
    ResponseEntity<String> handleNotFound(ResourceNotFoundException e) {
        return ResponseEntity.status(404).body(e.getMessage());
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    ResponseEntity<Map<String, String>> handleValidation(MethodArgumentNotValidException e) {
        // @Valid fail hone pe ye trigger hota hai
        return ResponseEntity.status(400).body(extractErrors(e));
    }
    
    @ExceptionHandler(Exception.class)  // catch-all fallback
    ResponseEntity<String> handleGeneric(Exception e) {
        return ResponseEntity.status(500).body("Something went wrong");
    }
}
```

**Analogy:** Ek company ka **centralized customer-complaint department** — har department (Sales, Support, Billing) apna khud ka complaint desk nahi rakhta. Sab complaints ek jagah forward hoti hain, jaha specialized team handle karti hai based on complaint type.

**`@Valid`** — Input validation ka trigger. Jab `@RequestBody @Valid Employee emp` likha ho, Spring automatically entity ke andar defined validations (`@NotNull`, `@Email`, `@Size`) check karta hai, invalid data controller method ke andar pahunchne se pehle hi reject ho jaata hai.

---

### 📋 Possible Questions — Exception Handling

**Q1: @ControllerAdvice kya hai aur kyun use karte hain?**
> Crisp: "Ek centralized jagah jaha poore application ke exceptions handle hote hain, individual controllers ke andar try-catch ki jagah."
> Elaboration: "@ExceptionHandler methods define karke, main specific exceptions (jaise ResourceNotFoundException) ko specific HTTP response mein convert karta hoon — ek jagah se, poore application ke liye. Isse controllers clean rehte hain aur error-handling logic duplicate nahi hoti."

**Q2: @Valid kaise kaam karta hai aur agar validation fail ho toh kya hota hai?**
> Crisp: "@Valid entity ke andar defined constraints check karta hai request body ko controller tak pahunchne se pehle."
> Elaboration: "Agar Employee entity mein `@NotNull` email field hai aur client empty email bhejta hai, Spring automatically `MethodArgumentNotValidException` throw kar deta hai — controller method ka code chalta hi nahi. Main @ControllerAdvice mein is exception ko catch karke proper 400 response with error message bhej sakta hoon."

**Q3: Custom exception class kaise aur kyun banate ho?**
> Crisp: "Business-specific error scenarios ko meaningful naam dene ke liye, generic exceptions ki jagah."
> Elaboration: "`ResourceNotFoundException extends RuntimeException` banake, jab employee na mile, main `throw new ResourceNotFoundException(\"Employee not found\")` likhta hoon — ye code ko readable banata hai, aur @ControllerAdvice specifically is exception type ke liye alag handling de sakta hai (404), generic Exception se alag (500)."

**Q4: Generic `Exception.class` handler kyun rakhte hain fallback ke liye?**
> Crisp: "Unexpected/unhandled exceptions ke liye safety net, taaki client ko raw stack trace na dikhe."
> Elaboration: "Agar koi unexpected error aaye jiske liye specific handler nahi likha, ye catch-all handler use ko generic 500 error message dega, actual internal exception details expose kiye bina — jo security ke liye bhi important hai."

---

### 🎯 JWT Kahan Se Aaya, Kyun Aaya

**🔗 Dot Connect (samajhna zaroori):** Upar humne REST APIs banayi — par har API **public** nahi honi chahiye. Traditional web applications mein authentication **session-based** hota tha — user login kare, server ek session create karke memory mein store kare, aur browser ko ek session ID cookie mile. Har request pe server us session ID se memory mein check karta ki user kaun hai.

Problem: jaise-jaise applications **scale** hone lage (multiple servers, load balancers), session-based approach mushkil ho gaya — agar user Server A pe login kare par uski agli request Server B pe jaaye, Server B ko us session ka pata nahi hoga (jab tak session sync na ho, jo complex hai).

**JWT (JSON Web Token, 2015 standardized — RFC 7519)** ne solve kiya: **server kuch bhi store na kare** — saari user info khud token ke andar ho, cryptographically signed, taaki koi tamper na kar sake. Server sirf token verify karta hai, remember kuch nahi karta — **stateless authentication.**

---

### JWT Structure — `Header.Payload.Signature`

```
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VySWQiOjEyMywicm9sZSI6IkFETUlOIn0.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
     ↑ Header                    ↑ Payload                              ↑ Signature
```

| Part | Content | Important Detail |
|---|---|---|
| **Header** | Algorithm info (jaise HMAC256) | — |
| **Payload** | Actual data — userId, role, expiry time | **Encoded (Base64), NOT encrypted** — koi bhi decode karke padh sakta hai |
| **Signature** | Header+Payload ko secret key se sign kiya | Ye verify karta hai token tamper toh nahi hua |

**⚠️ Sabse important trap concept — bhool mat:** JWT payload **encrypted nahi hota**, sirf **Base64 encoded** hota hai. Iska matlab koi bhi (bina secret key ke bhi) token ko decode karke uska content padh sakta hai. Signature sirf ye guarantee karta hai ki content **tamper** nahi hua — confidentiality nahi deta. Isliye **password ya sensitive data kabhi payload mein mat daalo.**

**Flow:**
```java
// Token generate karte waqt (login ke baad)
String token = Jwts.builder()
    .setSubject(user.getUsername())
    .claim("role", user.getRole())
    .setExpiration(new Date(System.currentTimeMillis() + 86400000))
    .signWith(SignatureAlgorithm.HS256, secretKey)
    .compact();

// Security Config mein
http.sessionManagement()
    .sessionCreationPolicy(SessionCreationPolicy.STATELESS);  // koi session store nahi
```

**Analogy — poora JWT concept:** Ek **movie ticket with QR code**. Tu ek baar counter pe payment karke ticket leta hai (login — ek baar credentials verify hote hain). Uske baad theatre ke andar jahan bhi jaana ho (kisi bhi protected API endpoint), tu bas ticket dikhata hai — dobara paisa nahi dena padta (stateless, dobara login nahi karna). QR code scan karke verify hota hai ticket genuine hai ya nahi (signature verification) — par ticket pe likhi info (seat number, movie name) koi bhi padh sakta hai bina scan kiye bhi, agar ticket haath mein aa jaaye (unencrypted payload).

---

### 📋 Possible Questions — JWT

**Q1: JWT kya hai aur ye traditional session-based authentication se kyun better hai?** *(Almost guaranteed question)*
> Crisp: "JWT ek self-contained token hai jisme user info hoti hai, jisse server ko session store karne ki zaroorat nahi padti — stateless authentication."
> Elaboration: "Session-based auth mein server ko har user ka session memory mein rakhna padta hai — multiple servers hone pe (load balanced) sync ek problem hai. JWT mein saari zaroori info token ke andar hoti hai, server kuch store nahi karta — koi bhi server independently token verify kar sakta hai. Isse horizontal scaling aasan hoti hai."

**Q2: JWT encrypted hota hai?** *(Classic trap question — bahut poochte hain)*
> Crisp: "Nahi — JWT payload sirf Base64 **encoded** hota hai, **encrypted nahi**."
> Elaboration: "Koi bhi token le ke decode kar sakta hai aur payload padh sakta hai — jwt.io jaisi sites pe paste karke instantly dekh sakte ho. Signature sirf integrity verify karta hai — ki content tamper nahi hua. Isliye sensitive info (password, credit card) kabhi payload mein nahi daalni chahiye."

**Q3: JWT Header, Payload, Signature — teeno ka kaam bata.**
> Crisp: "Header algorithm batata hai, Payload actual claims/data hota hai, Signature verify karta hai tamper hua ya nahi."
> Elaboration: "Header mein signing algorithm (HS256) ka info hota hai. Payload mein claims hote hain — userId, role, expiry (`exp`). Signature, Header+Payload ko secret key se hash karke banta hai — jab server token receive karta hai, wahi hash dobara calculate karta hai aur compare karta hai; agar match nahi hua, matlab token tamper hua hai."

**Q4: SessionCreationPolicy.STATELESS kyun set karte hain Spring Security mein?**
> Crisp: "Spring ko batata hai koi HTTP session create/use na kare — pura authentication JWT token pe based ho."
> Elaboration: "Agar STATELESS set na karein, Spring Security by default session banata hai, jo JWT ke stateless philosophy ke against hai. STATELESS set karne se Spring har request ko independently, sirf token ke basis pe authenticate karta hai."

**Q5: JWT token expire ho jaaye toh kya hota hai, aur refresh token kya hota hai?** *(Advanced follow-up)*
> Crisp: "Expired token invalid ho jaata hai, user ko dobara login karna padta hai — jab tak refresh token mechanism na ho."
> Elaboration: "JWT mein `exp` claim expiry time set karta hai. Security ke liye access tokens ka expiry short rakhte hain (jaise 15 min). Refresh Token ek longer-lived, separate token hota hai jo naya access token generate karne ke liye use hota hai bina user ko baar-baar password dalwaye."

**Q6: JWT stateless hai, toh agar main kisi user ko logout/ban karna chahoon turant, kaise karunga?** *(Tricky conceptual question, dikhata hai deep samajh)*
> Crisp: "Ye JWT ka genuine limitation hai — stateless hone ki wajah se server ke paas 'active tokens' ki list nahi hoti jo instantly invalidate ki ja sake."
> Elaboration: "Common solutions: ek server-side blacklist maintain karo (jo partially statelessness todta hai), ya short expiry rakho taaki impact limited ho, ya token version/flag DB mein rakho jo check ho har request pe. Ye ek tradeoff hai — full stateless purity vs instant revocation control."

---

### 🔗 Week 3 Ka Full Connection (Recap Mode)

> "Week 2 mein maine data ko JPA se manage karna seekha, Week 3 mein maine usko REST APIs ke through expose kiya — proper HTTP methods aur status codes ke saath. Errors ko centralized `@ControllerAdvice` se handle kiya, taaki controllers clean rahein — same SRP principle jo Week 1 mein seekha tha. Aur in APIs ko secure karne ke liye JWT use kiya, jo stateless hai, isliye scalable systems ke liye perfect fit hai."

---

# WEEK 4

## SonarQube + Microservices

### 🎯 SonarQube Kahan Se Aaya, Kyun Aaya

Jaise-jaise teams bade hote gaye aur multiple developers ek hi codebase pe kaam karne lage, ek problem samne aayi: **code review sirf human eyes pe depend karta tha.** Ek reviewer thak sakta hai, kuch miss kar sakta hai, ya alag-alag reviewers alag-alag standards follow karte hain — inconsistency. Isse security vulnerabilities (jaise SQL injection possible hona) aur bad design patterns (jaise SRP violations) production tak pahunch jaate the, kyunki koi automated check nahi tha.

**SonarQube (2008, originally "Sonar")** ne solve kiya — ek **static code analysis tool** jo code ko **bina run kiye** (static) scan karta hai aur automatically batata hai: kahan bugs hain, kahan security risk hai, kahan design achha nahi hai — before code even merge ho.

**🔗 Dot Connect (important):** Yaad kar Week 1 ka SOLID aur Design Patterns — SonarQube literally check karta hai **kya tune wo principles follow kiye ya nahi.** Jaise agar ek class SRP violate kar rahi hai (bahut zyada responsibilities), SonarQube isse "code smell" ki tarah flag karega.

---

### Core Concepts

| Term | Matlab |
|---|---|
| **Code Smell** | Kaam toh kar raha hai, par design achha nahi — future mein maintenance problem banega |
| **Bug** | Actual defect — wrong behavior dega runtime pe |
| **Vulnerability** | Security risk — jaise SQL injection possible hona, hardcoded password |
| **Technical Debt** | "Baad mein theek karenge" wala pending cleanup — jitna zyada debt, utna future development slow |
| **Quality Gate** | Pass/Fail threshold — decide karta hai code merge/deploy karna safe hai ya nahi |
| **Code Coverage** | Kitna % code, tests se cover ho raha hai (Week 1 ka JUnit yahan wapas connect hota hai!) |

**Analogy — poora concept:** SonarQube ek **health checkup report** hai:
- **Bug** = abhi ki bimari (turant treat karo)
- **Code Smell** = risky lifestyle habit (abhi problem nahi, par future risk)
- **Vulnerability** = koi attack kar sakta hai (security threat)
- **Quality Gate** = doctor ka "fit to travel" certificate — bina isके clear hue, tu deploy (travel) nahi kar sakta

**🔗 Dot Connect — Code Coverage:** Week 1 mein tune JUnit/Mockito seekha tests likhne ke liye. SonarQube exactly check karta hai — **tere likhe hue tests, kitna % actual code cover kar rahe hain.** Agar coverage kam hai, matlab bahut sa code untested hai — risky.

---

### 📋 Possible Questions — SonarQube

**Q1: SonarQube kya hai aur ye code review process mein kaise fit hota hai?**
> Crisp: "Ek static code analysis tool jo bina code run kiye bugs, vulnerabilities, aur design issues detect karta hai."
> Elaboration: "Ye typically CI/CD pipeline mein integrate hota hai — jab bhi code push/PR banta hai, SonarQube automatically scan karke report deta hai. Isse human reviewers ko repetitive, mechanical checks (naming conventions, obvious bugs) khud nahi dhundhne padte — woh design/logic pe focus kar sakte hain."

**Q2: Bug aur Code Smell mein farak kya hai?** *(Common question)*
> Crisp: "Bug actual runtime defect hai; Code Smell design problem hai jo abhi kaam kar raha hai par maintainability risk hai."
> Elaboration: "Bug matlab code galat behavior dega — jaise null pointer exception ka risk. Code Smell matlab code functionally sahi hai, par badly structured hai — jaise ek method bahut lambi hai, ya duplicate code hai — jo future changes ko mushkil banayega."

**Q3: Quality Gate kya hai aur kyun important hai?**
> Crisp: "Ek threshold jo decide karta hai ki code deploy/merge karne layak hai ya nahi."
> Elaboration: "Quality Gate rules define karti hai — jaise 'code coverage kam se kam 80% ho', 'zero critical bugs ho', 'zero security vulnerabilities ho'. Agar in conditions mein se koi fail ho, build pipeline ruk jaata hai — isse bad code production mein jaane se pehle hi rok diya jaata hai."

**Q4: Technical Debt kya hota hai aur kyun accumulate hota hai?**
> Crisp: "Woh pending cleanup/refactoring jo hum 'baad mein karenge' bolke deadline pressure mein skip kar dete hain."
> Elaboration: "Jaise agar deadline ke pressure mein quick-fix code likha jaaye instead of proper solution, ye technical debt create karta hai — jaise financial loan, jitna time badhta hai, 'interest' (future maintenance cost) badhta jaata hai. SonarQube isko quantify karke track karta hai."

---

### 🎯 Microservices Kahan Se Aaye, Kyun Aaye

**🔗 Ye sabse important dot-connect hai poore DN 5.0 mein — dhyan se samajh.**

Week 2-3 mein tune jo bhi banaya — Controller, Service, JPA, sab **ek hi codebase, ek hi application** mein tha. Isko **Monolith** kehte hain. Chhote applications ke liye ye perfectly fine hai — simple deploy, simple debug.

Par jab company bahut badi ho jaati hai (jaise Amazon, Netflix), monolith mein problems aati hain:

1. **Scaling problem** — agar sirf "Payment" feature pe load zyada hai, poori application scale karni padegi, chahe "Login" feature ko zaroorat na ho — wasteful.
2. **Deployment risk** — ek chhota bug fix karne ke liye bhi **poori** application redeploy karni padti hai — risky, slow.
3. **Team bottleneck** — 50 developers ek hi codebase pe kaam karein, merge conflicts aur coordination nightmare ban jaata hai.

**Amazon aur Netflix (2010s) ne pioneer kiya Microservices architecture** — application ko **chhote, independent services** mein todo, har service apna kaam khud sambhale, apni database rakhe, aur independently deploy/scale ho sake.

---

### Core Microservices Concepts

| Concept | Kya Hai | Analogy |
|---|---|---|
| **Monolith vs Microservices** | Ek bada app vs multiple chhote independent apps | Joint family (sab ek ghar) vs Alag flats (apna space, shared building) |
| **DB per Service** | Har service ki apni database, dusri service directly access nahi karti | Har flat ka apna ration — koi doosre ka access nahi karta |
| **API Gateway** | Single entry point jo requests ko sahi service tak route kare | Building ka main security gate — sab visitors pehle yahan aate hain |
| **Eureka (Service Discovery)** | Services ek dusre ko dhoondhne ke liye registry use karte hain | Building ka directory board — "Flat 302 mein kaun rehta hai" |
| **Circuit Breaker (Resilience4j)** | Agar ek service down ho, poore system ko crash hone se bachao | Fuse/MCB — ek appliance fail ho, poora ghar bijli nahi khoye |
| **Feign Client** | Ek service ka dusri service ko call karne ka easy tareeka (REST call jaise normal method call) | Intercom — direct call jaise lagta hai, actually network call hai |
| **Sync (REST) vs Async (Kafka/RabbitMQ)** | REST turant response chahta hai; Kafka/RabbitMQ "message daal do, jab free ho process karo" | REST = phone call (turant baat), Kafka = WhatsApp message (jab time mile reply) |

**🔗 Dot Connect — Circuit Breaker aur SRP:** Interesting cheez — Microservices khud **SRP ka application-level version** hai. Jaise Week 1 mein ek class ka ek hi responsibility honi chahiye thi, waise hi ek microservice ka ek hi business capability honi chahiye (jaise "Account Service" sirf accounts handle kare, "Loan Service" sirf loans).

---

### Circuit Breaker — Deep Dive (Bahut Common Interview Topic)

**Problem jo ye solve karta hai:** Socho `Order Service`, `Payment Service` ko call karta hai. Agar `Payment Service` slow ho gaya ya crash ho gaya, `Order Service` ke saare threads uske response ka wait karte reh jaayenge — eventually `Order Service` bhi crash ho jaayega. Isse **"Cascading Failure"** kehte hain — ek service ki problem poore system ko down kar deti hai.

**Circuit Breaker teen states mein kaam karta hai:**

```
CLOSED (normal — requests jaa rahi hain) 
    ↓ (failures threshold cross)
OPEN (requests turant fail ho jaati hain, bina wait kiye — fallback response)
    ↓ (kuch time baad)
HALF-OPEN (thodi requests test ke liye bhejo, dekho service theek hui ya nahi)
    ↓ (agar success)                    ↓ (agar fail)
CLOSED (wapas normal)              OPEN (phir se band)
```

**Analogy:** Ghar ka **electrical MCB** — agar current overload ho, MCB turant trip ho jaata hai (OPEN state), poore ghar ki bijli nahi jalti sirf us circuit ki jaati hai. Kuch der baad tu MCB reset karke test karta hai (HALF-OPEN) — agar sab theek hai, wapas ON (CLOSED), warna phir trip.

---

### 📋 Possible Questions — Microservices

**Q1: Microservices architecture kya hai aur Monolith se kaise alag hai?**
> Crisp: "Application ko chhote, independently deployable services mein todna, jaha har service apni khud ki responsibility aur database handle kare."
> Elaboration: "Monolith mein poora application ek hi codebase, ek hi deployment unit hai. Microservices mein har business capability (jaise Account, Loan) apna alag service hai — independently develop, deploy, aur scale ho sakta hai, bina baaki services ko affect kiye."

**Q2: Circuit Breaker pattern kyun zaroori hai?** *(Almost guaranteed question)*
> Crisp: "Ek service fail hone pe poore system ko cascading failure se bachata hai."
> Elaboration: "Agar Service A, Service B ko call kar raha hai aur B down ho jaaye, bina Circuit Breaker ke A bhi wait karte-karte resources exhaust kar sakta hai aur crash ho sakta hai. Circuit Breaker threshold cross hone ke baad calls ko turant fail kar deta hai (fallback response deta hai) bina wait kiye — jaise MCB overload pe turant trip ho jaata hai."

**Q3: Circuit Breaker ki teen states explain karo.**
> Crisp: "CLOSED (normal operation), OPEN (calls turant fail, fallback), HALF-OPEN (test karke dekho service recover hui ya nahi)."
> Elaboration: "Normal mein CLOSED state hoti hai, requests normally jaati hain. Agar failures ek threshold cross karein, circuit OPEN ho jaata hai — ab requests actual service tak jaati hi nahi, turant fallback response milta hai. Kuch time baad HALF-OPEN state mein limited requests bhej ke test karte hain — agar successful hon, CLOSED pe wapas, warna phir OPEN."

**Q4: Eureka (Service Discovery) ki zaroorat kyun hai?**
> Crisp: "Services ko ek dusre ki location dynamically pata karne ke liye, hardcoded IP addresses ke bina."
> Elaboration: "Microservices mein instances scale up/down hote rehte hain, IPs change ho sakte hain (especially containers/cloud mein). Eureka ek registry hai jaha har service register hoti hai apni location ke saath — dusri services usse query karke current location dhoondh leti hain, bina hardcoded config ke."

**Q5: Sync (REST) aur Async (Kafka/RabbitMQ) communication mein kab kaunsa choose karoge?**
> Crisp: "Turant response chahiye toh REST (sync), response ka wait na karna ho toh messaging queue (async)."
> Elaboration: "Jaise agar user login kar raha hai aur turant confirmation chahiye, REST (sync) use karenge. Par agar 'order placed' hone pe email bhejni hai — usko turant hona zaroori nahi, toh message queue mein daal denge, jo background mein process ho jaayegi — isse main flow slow nahi hota, aur agar email service down bhi ho, order placement fail nahi hoga."

**Q6: DB per Service kyun follow karte hain, shared database kyun nahi?**
> Crisp: "Taaki services independently evolve kar sakein bina ek dusre ko break kiye."
> Elaboration: "Agar sab services ek shared database use karein, ek service ka schema change dusri services ko silently break kar sakta hai — tight coupling create hoti hai jo microservices ka poora purpose hi khatam kar deti hai. Har service apni DB owns karti hai, aur agar doosri service ko data chahiye, API call ke through maangti hai."

**Q7: Tune apna 2-service setup (Account Service aur Loan Service) kaise banaya tha? Explain the architecture.** *(Personal project question — tayaar rakh)*
> *(Apne words mein customize karna: "Maine Account Service port 8083 pe aur Loan Service port 8084 pe run kiya, dono independent Spring Boot applications the with apni khud ki responsibility. REST ke through communicate karte the, aur main circuit breaker/service discovery concepts bhi explore kiya same setup mein.")*

**Q8: Feign Client kyun use karte hain, RestTemplate directly kyun nahi?** *(Advanced follow-up)*
> Crisp: "Feign Client boilerplate code kam karta hai — service call normal Java method jaisi dikhti hai."
> Elaboration: "RestTemplate mein manually URL banani padti hai, headers set karne padte hain. Feign mein sirf ek interface define karo with annotations, aur Spring khud implementation generate kar deta hai — call karna bilkul aisa lagta hai jaise local method call kar rahe ho."

---

### 🔗 Week 4 Ka Full Connection (Recap Mode)

> "Week 4 mein maine seekha ki jo code maine likha hai uski quality kaise verify karte hain — SonarQube se, jo actually check karta hai ki maine Week 1 ke SOLID principles aur clean design follow kiya ya nahi. Aur phir Microservices seekhi — jab application bahut badi ho jaaye, usko chhote independent services mein todna, with resilience patterns jaise Circuit Breaker taaki ek service ki failure poore system ko na le doobe."

---

# WEEK 5

## Angular

### 🎯 Angular Kahan Se Aaya, Kyun Aaya

**🔗 Dot Connect pehle:** Week 3 tak tune sirf **backend** banaya — REST APIs jo JSON data return karti hain. Par ek normal user ko JSON nahi dikhna chahiye, use ek **visual interface** chahiye — buttons, forms, pages. Yahi kaam **frontend framework** karta hai.

2010 mein Google ne **AngularJS** banaya — us waqt websites mostly **static HTML** thi, ya jQuery se manually DOM manipulate karte the (bahut messy, unstructured code, "spaghetti" jaisa — same problem jo Week 1 mein OOP se pehle thi). AngularJS ne **Single Page Application (SPA)** concept popularize kiya — poori website ek baar load ho, aur uske baad sirf zaroori data change ho (poora page reload na ho), jaisa Gmail ya Facebook kaam karta hai.

2016 mein Google ne poora framework **rewrite** kiya — TypeScript-based, better performance, better structure — naam rakha sirf **"Angular"** (AngularJS se alag, version 2+).

**🔗 Sabse bada dot yahi hai (interview mein bol ye):** Angular apne core mein **wahi Dependency Injection concept use karta hai jo tune Week 2 mein Spring mein seekha.** Components apni dependencies (Services) khud nahi banate — Angular ka DI container provide karta hai. **Same philosophy, do alag ecosystems (Java backend aur TypeScript frontend) mein.**

---

### Core Building Blocks

```typescript
// Component — UI ka ek block
@Component({
  selector: 'app-employee-list',
  template: `<div *ngFor="let emp of employees">{{ emp.name }}</div>`
})
class EmployeeListComponent {
  employees: Employee[] = [];
  
  constructor(private employeeService: EmployeeService) {}  // DI — Spring jaisa!
  
  ngOnInit() {
    this.employeeService.getAll().subscribe(data => this.employees = data);
  }
}

// Service — REST API se baat karne ka logic (Week 3 ke backend ko call karta hai)
@Injectable({ providedIn: 'root' })
class EmployeeService {
  constructor(private http: HttpClient) {}
  
  getAll() {
    return this.http.get<Employee[]>('/api/employees');  // Week 3 ka REST API!
  }
}
```

| Concept | Kya Hai | Analogy |
|---|---|---|
| **Component** | UI ka reusable block (HTML+CSS+Logic milke) | LEGO block — combine karke poori page banti hai |
| **Service** | Reusable business logic / API calls | Restaurant ka kitchen — components (waiters) isse data mangte hain |
| **Dependency Injection** | Angular khud dependencies provide karta hai constructor mein | Spring ka **exact same concept**, bas TypeScript mein |
| **RouterLink / RouterOutlet** | Navigation between pages bina full page reload ke | TV remote — channel badalta hai, TV device wahi rehta hai |
| **AuthGuard (CanActivate)** | Route access control — login na ho toh page block | Bouncer jo entry se pehle ID check kare |
| **ngModel** | Two-way data binding — input aur variable automatically sync | Mirror — jo type karo, turant reflect ho variable mein |
| **\*ngIf** | Conditionally element dikhana/hide karna | "Agar logged in hai toh Logout button dikhao" |
| **\*ngFor** | List ko loop karke render karna | Ek list of courses ko cards mein render karna |
| **Reactive Forms + FormBuilder + Validators** | Form ko TypeScript code se control karna (vs simple template-driven) | Zyada control, complex validation ke liye better |

---

### AuthGuard — Sabse Important Dot Connect (JWT ke saath)

**🔗 Ye connection interview mein bahut impress karta hai — deeply samajh:**

Week 3 mein tune JWT banaya backend security ke liye. Par frontend pe bhi ek layer chahiye — agar user login nahi hai, use `/dashboard` route pe jaane hi mat do (UX ke liye, security backend pe already enforced hai).

```typescript
@Injectable({ providedIn: 'root' })
class AuthGuard implements CanActivate {
  constructor(private router: Router) {}
  
  canActivate(): boolean {
    const token = localStorage.getItem('jwt_token');  // Week 3 ka JWT!
    if (!token) {
      this.router.navigate(['/login']);
      return false;  // route access deny
    }
    return true;  // route access allow
  }
}

// Route config mein use hota hai:
{ path: 'dashboard', component: DashboardComponent, canActivate: [AuthGuard] }
```

**Analogy:** AuthGuard ek **security checkpoint** hai building ke andar — poori building mein ghusne ka pass (login) alag hai, par kuch specific floors (protected routes) pe jaane ke liye extra ID verification chahiye.

**⚠️ Important nuance jo interviewer test kar sakta hai:** AuthGuard sirf **UX-level** protection hai — ye frontend route ko hide karta hai. **Real security backend pe honi chahiye** (JWT verification Spring Security mein) — kyunki frontend code browser mein hai, koi bhi technically bypass kar sakta hai agar backend properly secure na ho.

---

### 📋 Possible Questions — Angular

**Q1: Component aur Service mein farak kya hai?** *(Sabse common basic question)*
> Crisp: "Component UI aur user interaction handle karta hai; Service reusable business logic aur data-fetching handle karta hai."
> Elaboration: "Component ka kaam hai screen pe dikhana aur user actions (click, input) capture karna. Service mein API calls, shared logic hota hai jo multiple components use kar sakte hain — isse code duplicate nahi hota aur separation of concerns maintain hota hai (SRP wapas yahan!)."

**Q2: Angular mein Dependency Injection kaise kaam karta hai, aur Spring se kaise compare karta hai?** *(Guaranteed high-value question)*
> Crisp: "Angular ka DI system bhi Spring jaisa hai — components apni dependencies khud nahi banate, Angular ka injector provide karta hai."
> Elaboration: "Jaise Spring mein `@Autowired` constructor mein dependency inject karta hai, Angular mein bhi main constructor mein service declare karta hoon (`constructor(private employeeService: EmployeeService)`), aur Angular ka DI container automatically instance provide kar deta hai. `@Injectable({ providedIn: 'root' })` batata hai service ek singleton ki tarah application-wide available hai — bilkul Spring ke `@Service` bean jaisa."

**Q3: \*ngIf aur \*ngFor mein farak?**
> Crisp: "*ngIf conditionally element dikhata/chhupata hai; *ngFor list ko loop karke multiple times render karta hai."
> Elaboration: "*ngIf ek boolean condition ke basis pe DOM se element ko add/remove karta hai (jaise login hone pe hi Logout button dikhana). *ngFor ek array ko iterate karke har item ke liye ek template repeat karta hai — jaise employees ki list ko cards mein render karna."

**Q4: AuthGuard kaise kaam karta hai aur ye JWT ke saath kaise integrate hota hai?** *(Almost guaranteed — cross-topic question)*
> Crisp: "AuthGuard ek route ko activate hone se pehle check karta hai valid JWT token exist karta hai ya nahi."
> Elaboration: "`CanActivate` interface implement karke, main check karta hoon localStorage/cookie mein token hai ya nahi. Agar nahi, `router.navigate()` se login page pe redirect kar deta hoon aur `false` return karta hoon jisse route block ho jaata hai. Ye same pattern maine RBAC ke liye bhi use kiya — instructor aur student ko alag routes dikhana based on role jo JWT payload mein hota hai."

**Q5: ngModel (two-way binding) kaise kaam karta hai?**
> Crisp: "Input field aur TypeScript variable ko automatically sync karta hai dono directions mein."
> Elaboration: "Agar user input field mein type kare, variable automatically update hota hai. Aur agar main code se variable change karu, input field bhi automatically update ho jaata hai — 'two-way' isliye kehte hain, ek-directional binding (`[value]` ya `{{ }}`) ke against."

**Q6: Reactive Forms aur Template-driven Forms mein farak?** *(Advanced, agar poochein)*
> Crisp: "Reactive Forms TypeScript code se control hoti hain (zyada control, complex validation), Template-driven forms HTML template mein directives se banti hain (simple forms ke liye)."
> Elaboration: "Reactive Forms mein `FormBuilder` aur `Validators` use karke form structure programmatically define karte hain — testing aur complex conditional validation ke liye better hai. Template-driven simple forms ke liye jaldi likhi ja sakti hai, par bade/dynamic forms ke liye maintain karna mushkil hota hai."

**Q7: RouterLink aur normal `<a href>` mein farak kyun hai?**
> Crisp: "RouterLink poora page reload kiye bina navigate karta hai (SPA behavior); `<a href>` poora page reload karta hai."
> Elaboration: "Angular ek Single Page Application hai — `<a href>` use karne se poori application dobara load ho jaayegi (jaise traditional website), jo SPA ke performance benefit ko khatam kar deta hai. RouterLink Angular ke router ko use karta hai jo sirf zaroori component ko swap karta hai, baaki application state preserved rehti hai."

**Q8: Apne project mein RBAC kaise implement kiya Angular mein?** *(Personal project question — Kashi Learning se connect)*
> *(Apne words mein customize karna: "Maine JWT payload mein role store kiya (instructor/student), AuthGuard mein us role ko check kiya specific routes ke liye, aur *ngIf use kiya UI elements conditionally dikhane ke liye — jaise sirf instructor ko course-creation button dikhna chahiye.")*

---

### 🔗 Week 5 Ka Full Connection (Recap Mode)

> "Angular mein maine woh backend seekha hua sab kuch frontend mein consume karna seekha — Services se REST APIs (Week 3) ko call kiya, AuthGuard se JWT (Week 3) verify karke routes protect kiye, aur interestingly, Angular ka Dependency Injection bilkul Spring (Week 2) jaisa hai — same design philosophy, do alag stacks mein."

---

*End of compiled notes — Week 1 to Week 5*
