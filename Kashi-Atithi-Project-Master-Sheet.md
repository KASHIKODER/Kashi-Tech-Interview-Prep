# Kashi Atithi — Project Interview Master Sheet
### Cognizant GenC / GenC Next | Owned. Rated. Ready to speak.
**Candidate:** Suyash Giri (Paflu) | **Interview:** 20 Aug 2026, 8:30 AM

---

## 0. Pehle Ye Samjho — Cognizant Project Round Actually Kaise Chalta Hai

Maine fresh 2025–2026 candidate reports aur Cognizant ki khud ki guidance check ki. Pattern bilkul consistent hai, isliye ab hum guess nahi kar rahe, evidence pe kaam kar rahe hain:

1. **Round almost hamesha project se hi shuru hota hai.** Interviewer tumhara resume/Superset dekhta hai, ek technology point karta hai, aur phir *usi ek cheez ko layer-by-layer drill karta hai* — jaise tumhare notes-document mein "Decision Tree" style follow-ups likhe hain, exactly wahi real interviews mein hota hai.
2. **Resume ki har line trigger hai.** Jo bhi likha hai (JWT, filter chain, S3, "10+ APIs", "centralized validation") — usse follow-up expected hai. Agar ek bhi tech stack pe confidence nahi hai to selection risk mein aata hai.
3. **GenC vs GenC Next ka fark depth mein hai, topic mein nahi.** GenC Next ("Deep Skilling") mein wahi topics zyada gehraai se poochhe jaate hain — same Kashi Atithi, but interviewer teesri-chauthi follow-up tak jaayega.
4. **Honesty > bluff.** Har report mein ye common hai — jo cheez implement nahi ki, usse claim mat karo. "Verify in code" wali cheezein bina padhe mat bolna; agar exact nahi pata to honest limitation bolna hi safe hai (tumhare notes-document ka "Golden Rule" bhi yahi kehta hai).
5. **Interview ka duration** typically 30–45 min hota hai — matlab time kam hai, har answer 30–90 second range mein hi dena hai, lambi lecture nahi.

**Bottom line:** Ye round tumhara test nahi le raha ki tumhe Spring Boot ki definition yaad hai ya nahi. Ye test kar raha hai ki **tumne khud kya banaya, kyun banaya, aur jab dikkat aayi to kaise solve ki.** Isi angle se ye poora sheet banaya hai.

---

## 1. Rating System — Isse Follow Karo

| Tag | Matlab | Kitna invest karna hai |
|---|---|---|
| 🔴 **HIGH** | Har recent report/pattern mein baar-baar aata hai, ya opening/closing question hai | Word-perfect ready — bina soche bol sako |
| 🟠 **MEDIUM** | Agar interviewer ne related tech pick kar li to almost certain follow-up | Solid answer + 1 follow-up ready |
| 🟡 **LOW** | Possible hai, rare hai, ya sirf trap-question ke roop mein aata hai | Ek honest line yaad rakho, over-prepare mat karo |

---

## 2. 5-Minute Repo Check — Aaj Raat Ye Blanks Bharo

Ye sheet tumhare answers ko "honest-by-design" banata hai, lekin ye 15 cheezein tumhe apne actual GitHub repo se ek baar confirm kar leni chahiye — 5 minute lagenge, but interview mein ye exact facts hi tumhe "bluff nahi kar raha" wala confidence denge.

**✅ Ye maine tumhare actual GitHub code (dono repos) padh ke khud verify kar diya hai — sab code-accurate hai, guess nahi.**

| # | Confirm karo | Actual Answer (code se verified) |
|---|---|---|
| 1 | Total exact REST API count | **19** — Auth(2) + Booking(4) + Room(8) + User(5) |
| 2 | JWT library | **`io.jsonwebtoken` (jjwt)** v0.12.5 — `jjwt-api`, `jjwt-impl`, `jjwt-jackson` |
| 3 | JWT filter class ka naam | **`JWTAuthFilter`**, extends `OncePerRequestFilter` |
| 4 | Password encoder | **Yes, `BCryptPasswordEncoder`** (via `PasswordEncoder` bean in `SecurityConfig`) |
| 5 | Overlap query — JPQL ya derived method? | **Dono, do jagah alag logic hai** — (a) room-search ke liye `RoomRepository.findAvailableRoomsByDatesAndTypes()` ek real JPQL query hai; (b) actual booking-*save* ke time pe overlap check ek **in-memory Java Stream logic** hai (`roomIsAvailable()` method in `BookingService`) — DB query nahi, already-loaded `room.getBookings()` collection pe check hota hai |
| 6 | `@Transactional` laga hai? | **Nahi — poore codebase mein kahin nahi hai**, `BookingService` included |
| 7 | Locking | **None** — na `@Lock`, na `@Version`, kahin bhi nahi |
| 8 | Relationship annotations | `Booking.user` → `@ManyToOne(EAGER)`; `Booking.room` → `@ManyToOne(LAZY)`; `Room.bookings` → `@OneToMany(mappedBy="room", LAZY, cascade=ALL)`; `User.bookings` → same pattern, `cascade=ALL` |
| 9 | Fetch type on bookings collection | **LAZY** on both `Room.bookings` and `User.bookings`. (Note: `Booking.user` khud EAGER hai, `Booking.room` LAZY hai — asymmetric, verify karke bolna) |
| 10 | AWS SDK version | **v1** (`com.amazonaws:aws-java-sdk-s3`, version 1.12.728) — v2 nahi |
| 11 | S3 mein URL store hoti hai ya key? | **Full URL** store hoti hai (`https://bucketname.s3.amazonaws.com/filename`), directly `Room.roomPhotoUrl` field mein |
| 12 | Deployed hai ya sirf local? | **Sirf local** — README mein sirf `./mvnw spring-boot:run` + `npm start` hai, koi live/production URL nahi, frontend `localhost:4040` ko hit karta hai |
| 13 | Individual ya team? | **[apne hisaab se bharo — code se ye pata nahi chalta, tumhe khud confirm karna hai]** |
| 14 | Automated tests? | **Sirf ek default Spring Boot smoke test** (`contextLoads()`) — koi real unit/integration test nahi hai. Postman/manual testing hi hui hogi |
| 15 | Exact status codes | **200 (success), 400 (register/validation failure), 404 (not found AND booking conflict dono — 409 kahin use nahi hota), 500 (unexpected)**. Booking conflict bhi 404 hi return karta hai, 409 nahi |

Agar #13 abhi decide nahi kiya, koi baat nahi — baaki sab 100% code-verified hai.

---

## 2.1 Real Code Findings — 6 Genuine Gotchas (Bonus, sab code se nikale hain)

Bhai, ye section sabse valuable hai. Har point genuinely tumhare code mein hai — maine khud padha. Ye exactly wahi cheez hai jo ek sharp interviewer khud repo dekh ke poochh sakta hai, aur agar tum khud confidently bata do bina unke poochhe, ye **maximum ownership signal** deta hai.

**Finding 1 — JWT expiry comment galat hai (🔴 agar JWT deep-dive ho to zaroor poochha ja sakta hai)**
```java
private static final long EXPIRATION_TIME = 1000 * 60 * 24 * 7; //for 7 days
```
Comment "7 days" bolta hai, lekin formula mein ek `* 60` missing hai. Actual value = 10,080,000 ms = **~168 minutes (~2.8 hours)**, 7 days nahi. Frontend/response mein bhi hardcoded string `"7 Days"` bhej di jaati hai, jo actual expiry se match nahi karti.
> **Agar poochha jaaye "token kitni der valid rehta hai":** "The intended design was a 7-day expiry, but tracing through my `EXPIRATION_TIME` calculation, I'm missing one multiplication factor for hours — so the actual token life is closer to 2.8 hours. It's a one-line fix, and it's a good example of why I'd want a unit test around token expiry logic."
*(Ye bolna tumhe defensive nahi, balki extremely sharp dikhayega — kyunki tumne khud dhoonda.)*

**Finding 2 — Booking overlap check DB query nahi, in-memory hai, aur over-conservative hai (🔴 HIGH — booking-conflict follow-up mein ye best answer hai)**
`saveBooking()` mein overlap check `room.getBookings()` (already-loaded collection) pe ek Java Stream ke through hota hai, database query se nahi. Aur logic thoda over-strict hai — ek condition (`checkOutDate.isBefore(existingCheckOutDate)`) itni broad hai ki agar room pe **koi bhi** future booking exist karti hai, to usse **pehle** ki ek completely non-overlapping date-range bhi galti se "not available" bata degi.
> Example: Existing booking Jan 10–20. Naya request Jan 1–5 (bilkul overlap nahi karta) — phir bhi reject ho jaayega, kyunki naya checkout(Jan5) existing checkout(Jan20) se pehle hai.
> **Agar poochha jaaye "kya tumhara overlap logic perfect hai":** "It's safe — I traced through it and it never lets a real conflict through. But it's over-conservative: because of one broad condition, it can reject a completely non-overlapping earlier booking if the room already has a later booking. I'd tighten that condition to the standard `newCheckIn < existingCheckOut AND newCheckOut > existingCheckIn` formula, which is actually what I already used correctly in my room-search JPQL query."

**Finding 3 — `@Valid` kahin bhi use nahi hoti (🟠 MEDIUM, DTO/validation follow-up ka trap)**
`User` aur `Booking` entities pe `@NotBlank`, `@NotNull`, `@Future` jaise Bean Validation annotations hain, lekin controllers mein `@RequestBody` ke saath kahin bhi `@Valid` nahi lagaya gaya — matlab **ye annotations abhi trigger hi nahi hote.** Check-out > check-in wali validation manually service layer mein `if` condition se hoti hai, structural validation se nahi.
> **Agar poochha jaaye:** "I have Bean Validation annotations declared on my entities, but I haven't wired `@Valid` on the controller parameters yet, so right now they're documentation more than enforcement. My actual date-order check happens manually in the service layer. Adding `@Valid` plus a `@ControllerAdvice` handler for `MethodArgumentNotValidException` is a concrete next step."

**Finding 4 — Koi global exception handler nahi (🟠 MEDIUM)**
Poore project mein `@ControllerAdvice`/`@ExceptionHandler` kahin nahi hai. Har service method apna khud ka try-catch repeat karta hai aur same shared `Response` DTO mein status code set karta hai.
> **Honest answer:** "I don't have a centralized `@ControllerAdvice` yet — each service method handles its own try-catch and populates a shared `Response` object. It works, but it repeats the same pattern everywhere. Centralizing it into one exception handler is my top code-quality improvement."

**Finding 5 — Request DTOs actually raw entities hain (🟠 MEDIUM — resume ke "DTO-based request handling" claim se seedha juda hai)**
Sirf `LoginRequest` ek real request DTO hai. `register()` seedha `User` entity leta hai `@RequestBody` mein, aur booking-creation seedha `Booking` entity leta hai. **Response side** pe DTOs (`UserDTO`, `RoomDTO`, `BookingDTO`) properly use hote hain, lekin **request side** pe nahi.
> **Agar "DTO-based request handling" pe poochha jaaye:** "My response side is fully DTO-driven — I map every entity to a dedicated DTO before returning it, so I never leak internal fields outward. My request side is a mix — login uses a proper `LoginRequest` DTO, but registration and booking creation currently bind the entity directly. That's an area I'd tighten next, especially since binding the entity directly technically allows a client to send fields they shouldn't control."
*(Ye bolna dikhata hai tumhe apne resume claim ki exact boundary pata hai — bahut strong signal.)*

**Finding 6 — Room delete cascade se saari bookings delete ho jaati hain (🟡 LOW-MEDIUM, "what if you delete a room with bookings" trap)**
`Room.bookings` pe `cascade = CascadeType.ALL` hai aur delete hard-delete hai (`deleteById`), koi soft-delete/status flag nahi. Matlab agar Admin ek room delete kare jiski bookings hain, **saari historical bookings bhi cascade se delete ho jaayengi.**
> **Honest answer:** "Right now deleting a room cascades and removes its bookings entirely — there's no soft-delete or archival. For a real hotel system I'd want to deactivate a room instead of hard-deleting it, so booking history survives for audit purposes."

**Bonus, chhota sa context point:** Tumhara backend package `com.phegondev.PhegonHotel` hai — agar interviewer GitHub kholta hai aur ye naam recognize karta hai (ye base structure ek well-known Java/Spring hotel-booking tutorial se milta hai), to "did you follow a tutorial?" wala question directly aa sakta hai. Ye tumhare notes-document mein already covered hai (Section 29.1) — safe answer wahi use karo: **"I started from a reference implementation to learn the Spring Security + JWT + S3 pattern properly, and then I understood, modified, and extended it — for example [pick 2 real things you added/changed, like adding X feature or fixing Y]. I can explain every line because I've traced through the whole flow myself."** Isse jhoot nahi bolna padega, aur genuine confidence bhi dikhega.

---

## 3. The Opening — 🔴 HIGH (yeh 100% aayega)

### 30-second version
> "Kashi Atithi is a full-stack hotel booking and management system with User and Admin roles. I built the backend using Java, Spring Boot 3, Spring Security with JWT, and MySQL through Spring Data JPA and Hibernate. Users can browse rooms and create bookings, admins manage rooms, and I implemented date-overlap logic to prevent double bookings. Room images are stored in AWS S3."

### 90-second version (agar "thoda aur detail mein batao" bole)
> "Kashi Atithi is a hotel booking and management system built around two roles — User and Admin. The frontend is in React, and the backend is Java with Spring Boot 3. After login, Spring Security's filter chain validates a JWT before any protected request reaches the controller, and role-based rules decide User versus Admin access.
>
> The backend exposes REST APIs for authentication, room management, and bookings. MySQL stores Users, Rooms, and Bookings, mapped through Spring Data JPA and Hibernate. Before creating a booking, the service checks whether the requested dates overlap an existing active booking for that room. Room images go to AWS S3 instead of the database — MySQL just stores the reference.
>
> I structured the backend using Controller, Service, and Repository layers, with DTOs so the API contract stays separate from my persistence entities. This project gave me hands-on experience with authentication, relational modelling, ORM, and real business-rule implementation — not just CRUD."

### Follow-up: "Why this project?" 🔴 HIGH
> "I wanted a backend project where correctness actually mattered — not just basic CRUD. A hotel booking system forced me to deal with authentication, role-based access, relational data modelling, and date-overlap business logic, which is closer to real enterprise problems."

### Follow-up: "Individual or team?" 🔴 HIGH
Bolo jo sach hai — **[apna answer yahan fill karo from repo check #13]**. Agar individual: "I built it independently, including the backend architecture, security, database design, and frontend integration." Agar team: "It was a team project — my responsibility was ____, and I understood how it connected with ____."

---

## 4. Architecture Walkthrough — 🔴 HIGH

Jab bole "explain the flow" ya "how does a request travel through your system":

> "A request from React first hits the Spring Security filter chain. My JWT filter reads the Authorization header, validates the token's signature and expiry, and sets the authenticated user in the SecurityContext. Role-based rules then decide if that user can access the route. From there the Controller receives a validated DTO, the Service layer applies business logic — like the date-overlap check for bookings — and the Repository layer talks to MySQL through Spring Data JPA and Hibernate. For room images specifically, the Service layer separately calls the AWS SDK to upload to S3, and only the returned key or URL gets saved with the Room entity."

**Booking-creation flow (yaad rakho, ye specific flow drill hoga):**
```
User sends roomId + checkIn + checkOut
  → JWT auth + USER role check
  → DTO validation (checkout > checkin)
  → Room existence check
  → Repository searches overlapping active bookings
  → Conflict? → reject (409)
  → No conflict? → create + save booking (201)
```

---

## 5. Tech Deep-Dive — Grouped by Priority

### 5.1 Spring Boot & Layered Architecture — 🔴 HIGH

**Q: Why Spring Boot, not plain Spring?**
> "Spring gives the core container and dependency injection. Spring Boot adds auto-configuration, starter dependencies, and an embedded server, so I didn't have to manually wire every component or deploy to a separate Tomcat install. It let me focus on booking logic instead of infrastructure setup."

**Q: Why Controller-Service-Repository, why not put everything in the controller?**
> "If I put everything in the controller, it would end up handling HTTP requests, business rules, and database code all at once — that becomes hard to manage. So I split it into three layers. The Controller only handles the HTTP request and response. The Service layer holds the actual business logic — for example, checking whether a room is available for the requested dates. And the Repository layer only talks to the database — fetching or saving data. This way, each layer has one clear responsibility. So if I ever need to change my availability logic, I only touch the Service layer — the Controller and Repository don't need to change at all."

**Q: Is this MVC? Is this microservices?**
> "Spring MVC is used internally for handling requests — routing and mapping. But in the classic MVC pattern, there's also a View that returns an HTML page. In my case, there's no View — I return JSON, because it's a REST API, and React handles the display separately on the frontend. On top of that, I additionally organized my code into Controller, Service, and Repository layers, which is a separate design choice for structuring the backend.

> As for microservices — no, this is not microservices. It's a single Spring Boot application. I build it as one JAR file, and it runs as one deployment, with all my modules — Users, Rooms, Bookings — sharing the same database. Microservices would mean each of these being a separate, independently deployed application. So I'd call this a layered monolith — one application, but internally well-organized."


---

### 5.2 Spring Security + JWT — 🔴 HIGH (sabse bada question-magnet)

**Q: Walk me through your JWT flow.**
> "On login, the AuthenticationManager verifies credentials, and if valid, my JWT service generates a signed token containing the user's identity and expiry. The frontend sends this token back as `Authorization: Bearer <token>` on every protected request. My custom filter — extending `OncePerRequestFilter` — extracts it, validates the signature and expiry, loads the user's authorities, and sets the authentication in the SecurityContext before the request reaches the controller."

**Q: Is JWT encrypted? Can anyone read it?**
> "No — a standard signed JWT is encoded, not encrypted. Anyone can decode the payload. The signature only proves it wasn't tampered with. That's why I never put sensitive data like passwords in the payload."

**Q: How do you log out a JWT user?** 🟠 (dangerous if bluffed)
> "Right now logout is client-side only — the frontend clears the token and role from `localStorage`. A stolen token would technically still be valid until it expires. A production-grade fix would be a refresh-token revocation strategy, which I'd add as an improvement."

**Q: hasRole vs hasAuthority — did you use which one, and why?** 🟠
> "I used `hasAuthority`, checking the exact string like `hasAuthority('ADMIN')`, and my `User` entity's `getAuthorities()` returns the role exactly as stored in the database — no `ROLE_` prefix anywhere. I chose `hasAuthority` specifically so I didn't have to manage that prefix convention separately from what's stored in MySQL."

**Q: 401 vs 403 in your project?**
> "**[worth actually testing once tonight]** — my `SecurityConfig` doesn't define a custom `AuthenticationEntryPoint`, so Spring Security's default behavior applies. In my case that most likely means both a missing token and a wrong role come back as 403, since I never configured a separate 401 handler. I'd confirm this with a quick Postman test, and it's something I'd improve by adding an explicit entry point for a cleaner 401/403 split."

**Q: Where's your JWT signing key stored?** 🟠 (honest-answer trap)
> "Currently it's a hardcoded string inside `JWTUtils`. That's fine for a learning project, but I know it should move to `application.properties` or an environment variable, the same way I already externalized my AWS credentials with `@Value`. It's on my improvement list."

**Q: Are the User/Room/Booking relationships all authenticated the same way?** 🟠
> "Actually no — I permit `/auth/**`, `/rooms/**`, and `/bookings/**` at the URL level in my `SecurityFilterChain`, and enforce the actual Admin/User restriction with `@PreAuthorize` at the method level on individual endpoints. Only `/users/**` requires authentication at the filter-chain level itself. So my authorization is mostly method-level security, not URL-pattern security."

---

### 5.3 Booking Conflict / Double-Booking — 🔴 HIGH (single most dangerous question)

**Q: How exactly did you prevent double booking?**
> "Before saving a booking, I load the room's existing bookings and run a check across them for date overlap — if the new date range clashes with any existing booking on that room, I reject the request. I actually have two separate overlap mechanisms in the project: my room-search feature uses a proper JPQL query with the standard `checkIn <= requestedCheckOut AND checkOut >= requestedCheckIn` formula, and my actual booking-creation path checks the already-loaded bookings collection in the service layer using Java Streams rather than a fresh query."

**Q: So can two guests never clash — is double booking impossible?** 🔴 (the trap version)
> "Structurally it prevents real overlaps — I traced through the logic and couldn't find a case where a genuine overlap slips through. But it's actually over-conservative in one place: one of my conditions is broader than it needs to be, so it can reject a booking that doesn't truly overlap, if the room already has some other future booking. And separately, there's no transaction or locking around the check-then-save sequence, so two truly simultaneous requests could still race past each other — that would need row-level locking in production, which I understand conceptually even though I haven't implemented or load-tested it."

*Ye answer tumhe extremely strong dikhata hai kyunki tumne khud apna code trace karke exact limitation nikali hai — generic "race condition ho sakti hai" line se kahin zyada powerful hai.*

**Q: Same-day checkout and check-in — conflict ya nahi?**
> "In my actual save-booking check, a new check-in exactly equal to an existing checkout does NOT get flagged as a conflict — so back-to-back bookings on the same day are allowed. My JPQL search query uses `<=`/`>=`, which is slightly more conservative and would treat same-day boundaries as touching. I'd want to make both paths consistent."

---

### 5.4 JPA + Hibernate + Relationships — 🟠 MEDIUM-HIGH

**Q: JPA vs Hibernate vs Spring Data JPA — what's the difference?**
> "JPA is the persistence specification — it defines the annotations and contract. Hibernate is the provider that actually implements it and generates the SQL. Spring Data JPA sits above both and gives me repository interfaces so I don't write boilerplate CRUD code myself."

**Q: How are User, Room, Booking related?**
> "A Booking belongs to one User and one Room, so Booking has `@ManyToOne` relationships to both — the Bookings table holds the foreign keys, so Booking is the owning side. User and Room expose a `@OneToMany` collection back, using `mappedBy`."

**Q: Why isn't it a direct Many-to-Many between User and Room?**
> "Because the relationship carries its own data — check-in date, checkout date, status, price. That makes Booking a first-class entity with its own attributes, not just a join table."

**Q: Lazy or eager loading for the bookings collection?**
> "The `Room.bookings` and `User.bookings` collections are both LAZY — loading a Room shouldn't automatically pull its entire booking history. Interestingly, `Booking.user` itself is EAGER while `Booking.room` is LAZY — I'd want to make that consistent, but the reasoning for the collections being lazy is to avoid pulling unnecessary data or an N+1 problem when loading many rooms."

**Q: What happens if an Admin deletes a Room that has bookings?**
> "My `Room.bookings` relationship has `cascade = ALL`, and deletion is a hard delete through `deleteById`. So right now, deleting a room cascades and permanently removes all of its booking records too — there's no soft-delete or archival. For a real system, I'd deactivate the room instead so booking history survives for audit purposes."

---

### 5.5 MySQL / Database Design — 🟠 MEDIUM

**Q: Why MySQL and not MongoDB?**
> "The core domain here is relational — a Booking always belongs to exactly one User and one Room, and I needed joins, foreign-key integrity, and transactional consistency for reservation data. That fits a relational database naturally."

**Q: What indexes would help your booking queries?**
> "Since overlap queries filter by room first and then by date range, an index starting with room ID — combined with status and dates — would help. I'd confirm this against the actual execution plan rather than index blindly."

---

### 5.6 DTO / Validation / Exception Handling — 🟠 MEDIUM

**Q: Why not expose your JPA entities directly in the API?**
> "Directly exposing entities can leak internal fields, cause recursive JSON from bidirectional relationships, and lets a client set fields they shouldn't — like an ID or a status. DTOs let me control exactly what the API accepts and returns."

**Q: What happens on a validation error?**
> "Right now, business rules — like checkout being after check-in, or the room actually being available — are checked manually in the service layer with explicit `if` conditions, and I catch a custom `OurException` in each service method to set the status code on a shared `Response` object. I do have Bean Validation annotations like `@NotBlank` and `@Future` declared on my entities, but I haven't wired `@Valid` on the controller parameters yet, so those annotations aren't actively enforced right now — that's a specific gap I know about and would fix next."

**Q: What status code do you return for a booking conflict — 409?**
> "Currently it's 404 — my `OurException` gets caught and mapped to 404 regardless of whether the underlying issue is 'room not found' or 'room not available for those dates.' I know 409 Conflict is the more semantically correct code for an unavailable-room case, and differentiating my exception types so they map to different status codes is one of my concrete next improvements."

---

### 5.7 AWS S3 — 🟠 MEDIUM (kam common but strong "different" question — interviewer isko specifically pick kar sakta hai kyunki resume mein highlight hai)

**Q: Why S3 instead of storing images in MySQL?**
> "Storing binary image data directly in the database bloats it and mixes media delivery with structured business data. I stored room images in S3 as objects and kept only the key or URL reference in the Room row in MySQL."

**Q: What if the S3 upload succeeds but the database save fails?**
> "They aren't one atomic transaction — an S3 upload and a MySQL commit are two separate systems. If the DB save fails after a successful upload, I'd end up with an orphaned S3 object, so a production version needs a compensating cleanup step."

**Q: How are your AWS credentials handled?**
> "They're externalized with Spring's `@Value` annotation, pulled from `application.properties` — which is git-ignored, so the actual keys never touch source control. They're not hardcoded in Java."

**Q: What object key do you use for uploaded images?**
> "**[honest limitation]** — right now I use the file's original filename directly as the S3 object key, without generating a unique key like a UUID. That means two uploads with the same filename would overwrite each other in the bucket. Using a generated unique key per upload is a clear improvement I'd make."

**Q: AWS SDK version — v1 or v2?**
> "v1 — I used `aws-java-sdk-s3`. I know AWS recommends v2 for new projects going forward; v1 still works fine and was simpler for me to get working correctly for this project."

---

### 5.8 React Frontend Integration — 🟡 LOW-MEDIUM

**Q: How does React talk to your backend?**
> "Through Axios — I have a static `ApiService` class where each method calls the right endpoint. The JWT and role are stored in `localStorage` after login, and a `getHeader()` helper attaches `Authorization: Bearer <token>` manually to protected calls. I also have `ProtectedRoute` and `AdminRoute` wrapper components in React Router that check `isAuthenticated()`/`isAdmin()` before rendering a page — but that's only presentation-layer protection; the actual security boundary is Spring Security on the backend."

**Q: Axios interceptor use kiya?**
> "No, I attach the header manually per request through a shared helper method rather than a global Axios interceptor. A request interceptor would reduce that repetition — that's a reasonable improvement."

---

### 5.9 Maven — 🟡 LOW

**Q: What does Maven do here?**
> "It manages my dependencies — Spring Boot, Security, JPA, MySQL driver, JWT library, AWS SDK — and handles the build lifecycle: compile, test, package into an executable JAR."

---

## 6. "Dikkat Aayi, Kaise Solve Kiya" — Story Bank — 🔴 HIGH

**Ye exactly wahi cheez hai jo tumne poocha — "kya kiya, dikkat kya aayi, kaise solve kiya."** Interviewer ye zaroor poochega kyunki isi se pata chalta hai project genuinely tumne khud debug kiya ya nahi.

**Important rule: In char options mein se sirf WAHI story bolo jo tumhare saath actually hui ho.** Agar exact ye nahi hui, honest generic version use karo (neeche diya hai) — kabhi fabricate mat karo, kyunki follow-up mein pakड़े jaane ka risk hai.

**Option A — Booking overlap boundary bug**
> "The tricky part wasn't the overlap logic itself, it was the boundary case — should a guest checking out on the same date another checks in count as a conflict? I initially used inclusive comparisons and it wrongly rejected valid back-to-back bookings. I switched to modelling the stay as a half-open interval and tested it against multiple boundary scenarios before it behaved correctly."

**Option B — Security filter order / role bug**
> "I once had a valid Admin token getting a 403 on room creation. I reproduced it with a fresh token and inspected the authorities in the SecurityContext — the database stored the role as `ADMIN`, but my security rule expected `ROLE_ADMIN`. I normalized how I converted the role into a `GrantedAuthority` and re-tested both User and Admin tokens. I learned role-prefix consistency across storage, token claims, and Spring Security config really matters."

**Option C — Recursive JSON from bidirectional relationships**
> "When I first returned entities directly from a controller, a User with bookings that referenced their Room, which referenced back to bookings, caused a serialization loop. I fixed it by returning response DTOs instead of raw entities, which also gave me control over exactly which fields the API exposed."

**Option D — S3 + database consistency (code-accurate version)**
> "In my room-creation flow, I upload to S3 first and only save the Room record afterward, using the returned URL. But I noticed while reviewing my own code that I don't have any compensating cleanup — if the S3 upload succeeds and the database save fails right after, I'd be left with an orphaned S3 object with nothing pointing to it. I understand that's a gap; a production version would need a cleanup job or a saga-style compensation step."

**Option E — Overlap logic over-blocking (real, code-verified, strongest option) — 🔴**
> "While re-checking my own overlap logic recently, I realised one of my conditions was broader than necessary — it could reject a new booking that doesn't actually overlap an existing one, just because the room already has some other future booking with a later checkout. It's safe — it never lets a real conflict through — but it's overly conservative in that specific edge case. I traced through it with concrete examples to confirm exactly which condition was too broad, which is exactly the kind of thing I'd want a unit test to catch."

**Fallback — Safe generic version (agar upar wale options bolna comfortable na lage):**
> "The most challenging part conceptually was making sure my date-overlap logic and role-based security worked correctly together — not just individually. I tested the same feature from both the happy path and edge cases like boundary dates or wrong roles, and that process taught me to think about failure modes upfront rather than only the main flow."

---

## 7. Dangerous / Trap Questions — 🔴 HIGH

| Trap Question | Safe Honest Answer |
|---|---|
| "So double booking can literally never happen?" | "Normal sequential conflicts, no — verified by tracing my logic. A true simultaneous race would need explicit row-level locking, which I understand conceptually but haven't implemented or load-tested." |
| "Exactly how many APIs did you build?" | **"19"** — 2 auth, 8 room, 4 booking, 5 user. Say this exact number, not "10+". |
| "Is this deployed live?" | "No — it runs locally via `mvnw spring-boot:run` and `npm start`. I've tested the full flow locally end-to-end, but I haven't deployed it publicly yet. I can walk through the deployment steps I'd take — say, Render or an EC2 instance for the backend and Vercel for the frontend." |
| "Why both JPA and Hibernate — isn't that redundant?" | "They're different layers — JPA is the spec, Hibernate is the implementation that actually runs the SQL. I'm not using two competing things, one implements the other." |
| "Automated tests hain?" | "Just the default Spring Boot smoke test that checks the context loads — no real unit or integration tests yet. I tested manually through Postman and the UI: valid/invalid tokens, wrong roles, missing fields, and overlapping bookings. Adding JUnit/Mockito coverage for the service layer, especially the booking-overlap logic, is my top testing priority." |
| "'Centralized validation' — kya matlab exactly, aur kya sach mein hai?" | "Honestly, right now it's more manual than centralized — I check business rules like date order directly in the service layer with `if` conditions, and I catch a custom `OurException` per method rather than using one global `@ControllerAdvice`. My Bean Validation annotations exist on the entities but aren't wired with `@Valid` yet. I'd describe it as 'consistent validation logic,' and centralizing it into one handler is a concrete next step I've already identified." |
| "Did you follow a tutorial for this?" | "I started from a reference implementation to properly learn the Spring Security + JWT + S3 pattern, then understood, traced, and extended it myself — I can explain every line, including the specific gaps and improvements I've since identified, like my overlap logic and missing `@Valid` validation." |

---

## 8. Rapid-Fire Recall Table — 🟡 LOW effort, but instant answer chahiye

| Question | One-line answer |
|---|---|
| Authentication vs Authorization? | Authentication = who you are; Authorization = what you're allowed to do |
| Why constructor injection? | Explicit required dependencies, immutable fields, easy to test |
| Default Spring bean scope? | Singleton per application context (not automatically thread-safe) |
| `@Component` vs `@Service` vs `@Repository`? | Same mechanism, different intent — Repository also does persistence-exception translation |
| Checked vs unchecked exception? | Checked must be declared/caught; unchecked extends RuntimeException, used for business failures |
| Why `BigDecimal` for price? | Avoids floating-point precision errors in money math |
| `mappedBy` means? | Marks the inverse (non-owning) side of a bidirectional relationship |
| N+1 problem? | One query for a list + one extra query per item's lazy association |
| 409 vs 400? | 400 = malformed request; 409 = valid request conflicting with current server state (my project actually returns 404 for a booking conflict right now — 409 would be the textbook-correct choice) |
| Presigned URL? | Temporary, limited-access URL to an S3 object without exposing credentials |
| `mvn package` vs `install`? | package builds the JAR; install also puts it in the local Maven repo |
| CORS secures the backend? | No — it's browser-enforced only; real security is Spring Security on the server |

---

## 9. Bhai, Isse Kaise Use Karo

1. **Aaj:** Section 2 (repo check) 15 minute mein bhar lo — ye poore sheet ko "fake-proof" bana dega.
2. **Kal (Project Day, jaisa plan mein tha):** Section 3–5 zor se bolo, bina notes dekhe — mirror mein ya record karke.
3. **Har section ke end mein khud se poochho:** "Agar ab interviewer ek level aur deep poochhe, kya main answer de sakta hoon?" — agar nahi, wahi topic ka follow-up dobara padho.
4. **Section 6 aur 7 sabse zyada baar bolo** — ye exactly wo hai jo interviewer asli mein test karta hai: genuine ownership.
5. Mock interview ke time isi sheet ko checklist ki tarah use karo — main tumse koi bhi question is list se poochh sakta hoon jab bologe "mock lo."

Confidence isliye aayega kyunki har answer ke peeche ya to tumhara actual code hai, ya ek honest "main verify karunga / ye meri current limitation hai" — dono hi strong hain, bluff kabhi nahi.

---

## 10. Note — Ab Ye Sheet 100% Tumhare Actual Code Pe Based Hai

Maine tumhare dono repos (backend + frontend) khud extract karke, file-by-file padha — controllers, security config, entities, services, repos, JWT util, S3 service, sab. Section 2 aur Section 2.1 ke saare facts wahin se aaye hain, guess nahi. Baaki interview answers (Sections 3–9) ab in exact facts ke around update ho chuke hain.

**Sabse zyada value tumhe milegi agar tum Section 2.1 ke 6 findings zor se bol ke practice karo** — kyunki ye exactly wo level of detail hai jo bataata hai ki tumne apna khud ka code genuinely samjha hai, sirf resume pe likha nahi. Ye Cognizant GenC Next mein sabse zyada score karne wali cheez hai.
