# SQL + PL/SQL — Complete Interview Guide (Basic → Pro)
### Bhai ke liye, Hinglish mein, line-by-line samjhaya hua

> **Kaise use karo ye guide:** Har concept ke saath code hai, uske neeche **"Line-by-line (interviewer ko bologe)"** section hai jisme har line ka *kyun* likha gaya hai wo explain kiya gaya hai — assumption nahi, reasoning. Jab interview mein poochein "ye kyun likha?", tum seedha wahi bol sakte ho jo yaha likha hai.
>
> Database examples ke liye hum ek common schema use karenge poori guide mein (taaki tumhe alag-alag example yaad na rakhne pade):

```sql
-- COMMON SCHEMA (isko hi baar baar reference karenge)
CREATE TABLE employees (
    emp_id      NUMBER PRIMARY KEY,
    emp_name    VARCHAR2(50) NOT NULL,
    dept_id     NUMBER,
    salary      NUMBER(10,2),
    manager_id  NUMBER,
    hire_date   DATE,
    CONSTRAINT fk_dept FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);

CREATE TABLE departments (
    dept_id     NUMBER PRIMARY KEY,
    dept_name   VARCHAR2(50)
);
```

**Line-by-line (interviewer ko bologe):**
- `emp_id NUMBER PRIMARY KEY` — Primary key isliye kyunki har employee ko uniquely identify karna hai, aur PK automatically NOT NULL + UNIQUE constraint enforce karta hai, plus ek index bhi bana deta hai internally, jisse lookup fast ho.
- `emp_name VARCHAR2(50) NOT NULL` — NOT NULL isliye kyunki business rule hai ki employee ka naam kabhi khali nahi ho sakta — data integrity ke liye.
- `dept_id NUMBER` — Ye foreign key column hai, isliye NOT NULL nahi rakha (kyunki koi employee bina department ke bhi ho sakta hai, jaise notice period pe).
- `FOREIGN KEY (dept_id) REFERENCES departments(dept_id)` — Referential integrity enforce karne ke liye, taaki koi aisa dept_id insert na ho jo departments table mein exist hi nahi karta.

---

## PART 1: SQL FUNDAMENTALS

### 1.1 — SQL ke categories: DDL, DML, DCL, TCL

**Interviewer ye pehle hi poochta hai — "SQL commands ko categorize karo."**

| Category | Full form | Commands | Kaam kya karta hai |
|---|---|---|---|
| DDL | Data Definition Language | CREATE, ALTER, DROP, TRUNCATE, RENAME | Table/DB ka **structure** define/change karta hai |
| DML | Data Manipulation Language | SELECT, INSERT, UPDATE, DELETE | Table ke **data** ko manipulate karta hai |
| DCL | Data Control Language | GRANT, REVOKE | Permissions/access control |
| TCL | Transaction Control Language | COMMIT, ROLLBACK, SAVEPOINT | Transactions manage karta hai |

**Kaise bologe interviewer ko:**
"Sir, main SQL commands ko 4 categories mein divide karta hoon based on unke purpose ke according — DDL structure define karta hai isliye ye auto-commit hota hai, DML data ke saath kaam karta hai isliye rollback possible hai jab tak commit na ho, DCL security handle karta hai, aur TCL transactions control karta hai."

**Common trick question:** *"TRUNCATE DDL hai ya DML?"*
→ TRUNCATE DDL hai, kyunki wo table ka structure reset karta hai (auto-commit hota hai, rollback nahi ho sakta — is baare mein detail Part 3 mein).

---

### 1.2 — SELECT statement ki anatomy

```sql
SELECT emp_name, salary
FROM employees
WHERE dept_id = 10
ORDER BY salary DESC;
```

**Line-by-line (interviewer ko bologe):**
- `SELECT emp_name, salary` — Ye specify karta hai ki humein **kaunse columns chahiye** output mein. Star (`*`) use nahi kiya kyunki production code mein specific columns select karna best practice hai — performance ke liye (unnecessary columns fetch nahi honge) aur readability ke liye.
- `FROM employees` — Ye batata hai data **kaunsi table se** aa raha hai.
- `WHERE dept_id = 10` — Ye **row-level filter** hai — condition match karne wale rows hi consider honge, baaki discard ho jaayenge, **aggregation se pehle**.
- `ORDER BY salary DESC` — Result set ko salary ke hisaab se **descending order** mein sort karta hai; ye sabse last mein execute hota hai (logical processing order Part 3.1 mein cover karenge).

**Important note jo interviewer impress karega:** SQL likhne ka order aur execution ka order alag hota hai —
- **Likhne ka order:** SELECT → FROM → WHERE → GROUP BY → HAVING → ORDER BY
- **Execution ka order:** FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY

Isiliye WHERE clause mein hum SELECT ka alias use nahi kar sakte — kyunki WHERE, SELECT se pehle execute hota hai.

---

### 1.3 — WHERE clause: Operators

```sql
SELECT * FROM employees
WHERE salary > 50000
  AND dept_id IN (10, 20, 30)
  AND emp_name LIKE 'A%'
  AND manager_id IS NOT NULL
  AND hire_date BETWEEN TO_DATE('2020-01-01','YYYY-MM-DD') AND SYSDATE;
```

**Line-by-line (interviewer ko bologe):**
- `salary > 50000` — Comparison operator; numeric filter.
- `AND dept_id IN (10,20,30)` — `IN` isliye use kiya multiple `OR` conditions likhne ki jagah — cleaner aur readable; internally ye `dept_id=10 OR dept_id=20 OR dept_id=30` jaisa hi treat hota hai.
- `emp_name LIKE 'A%'` — Pattern matching; `%` ka matlab hai "zero ya usse zyada koi bhi characters", `_` ek single character ke liye hota hai. `'A%'` matlab naam A se start hona chahiye.
- `manager_id IS NOT NULL` — `IS NULL`/`IS NOT NULL` isliye use karte hain kyunki `NULL` ek "unknown value" hai, use `= NULL` se compare nahi kar sakte (wo hamesha `UNKNOWN` return karega, `TRUE` nahi).
- `hire_date BETWEEN ... AND ...` — Range check; `BETWEEN` **inclusive** hota hai (dono ends included).

**Gotcha jo aksar poochte hain:** *"WHERE salary = NULL kaam kyun nahi karta?"*
→ Kyunki SQL mein NULL ek "absence of value" hai, koi value nahi jise compare kiya ja sake — isliye `IS NULL` use karna padta hai, `=` operator kaam nahi karega.

---

### 1.4 — GROUP BY aur HAVING (WHERE se difference — bahut common question)

```sql
SELECT dept_id, AVG(salary) AS avg_sal
FROM employees
WHERE hire_date > TO_DATE('2018-01-01','YYYY-MM-DD')
GROUP BY dept_id
HAVING AVG(salary) > 40000
ORDER BY avg_sal DESC;
```

**Line-by-line (interviewer ko bologe):**
- `WHERE hire_date > ...` — Ye row-level filter hai, GROUP BY se **pehle** apply hota hai — matlab grouping sirf un rows par hogi jo is condition ko pass karte hain.
- `GROUP BY dept_id` — Rows ko dept_id ke basis par groups mein todta hai, taaki har group ke liye aggregate function (AVG yaha pe) calculate ho sake.
- `HAVING AVG(salary) > 40000` — Ye **group-level filter** hai, jo groups banne ke **baad** apply hota hai — isliye HAVING mein aggregate functions use kar sakte hain, WHERE mein nahi kar sakte.

**Interviewer ko exactly ye line bologe:**
"WHERE row-level filtering karta hai aur ye grouping se pehle chalta hai, isliye ismein aggregate function use nahi kar sakte kyunki aggregate ka matlab hi hota hai groups par calculate karna — jo abhi bane hi nahi. HAVING group-level filtering karta hai, grouping ke baad chalta hai, isliye HAVING mein aggregate functions allowed hain."

---

### 1.5 — Aggregate Functions

```sql
SELECT COUNT(*), COUNT(manager_id), SUM(salary), AVG(salary), MAX(salary), MIN(salary)
FROM employees;
```

**Line-by-line:**
- `COUNT(*)` — Total rows count karta hai, NULLs bhi count hote hain kyunki `*` ka matlab hai "poori row", column-specific nahi.
- `COUNT(manager_id)` — Sirf non-NULL values count karta hai — is line se interviewer ko dikhega ki tumhe pata hai `COUNT(column)` NULLs ko ignore karta hai jabki `COUNT(*)` nahi.
- `SUM`, `AVG`, `MAX`, `MIN` — Ye sab bhi by default NULL values ko ignore karte hain calculation mein.

---

### 1.6 — DISTINCT

```sql
SELECT DISTINCT dept_id FROM employees;
```

**Line-by-line:**
- `DISTINCT` — Duplicate rows ko result se remove karta hai. Yaha unique dept_ids milenge. Note: DISTINCT poori selected row par apply hota hai, agar multiple columns select kiye hain to unka **combination** unique hona chahiye, individual column nahi.

---

### 1.7 — Constraints

```sql
CREATE TABLE departments (
    dept_id     NUMBER PRIMARY KEY,
    dept_name   VARCHAR2(50) UNIQUE NOT NULL,
    budget      NUMBER CHECK (budget > 0),
    status      VARCHAR2(10) DEFAULT 'ACTIVE'
);
```

**Line-by-line (interviewer ko bologe):**
- `PRIMARY KEY` — Uniqueness + NOT NULL, ek table mein sirf **ek hi** primary key ho sakta hai (chahe composite ho).
- `UNIQUE` — Duplicate values allow nahi karta, lekin PK ke unlike, **NULL allow** karta hai (aur multiple NULLs bhi allow karta hai kyunki NULL ≠ NULL).
- `NOT NULL` — Column mandatory hai.
- `CHECK (budget > 0)` — Business rule enforce karta hai row-level pe — database khud validation karta hai, application code pe depend nahi karna padta.
- `DEFAULT 'ACTIVE'` — Agar INSERT ke time value nahi di gayi to ye default value automatically set ho jaayegi.

**Common question:** *"PRIMARY KEY aur UNIQUE mein difference?"*
→ "Sir, dono uniqueness enforce karte hain, lekin Primary Key NOT NULL bhi implicitly enforce karta hai aur ek table mein sirf ek PK ho sakta hai, jabki multiple UNIQUE constraints ho sakte hain aur UNIQUE column mein NULL allow hota hai."

### 1.8 — JOINS (sabse zyada pucha jaane wala topic)

```sql
-- INNER JOIN
SELECT e.emp_name, d.dept_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id;
```

**Line-by-line:**
- `e`, `d` — Table aliases; readability ke liye aur query short karne ke liye, especially jab columns ke naam same hon dono tables mein (jaise `dept_id`).
- `INNER JOIN departments d ON e.dept_id = d.dept_id` — Sirf wahi rows return karega jinka match dono tables mein mile. Agar kisi employee ka dept_id departments table mein exist nahi karta, wo row result mein nahi aayegi.

```sql
-- LEFT JOIN
SELECT e.emp_name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;
```

**Line-by-line:**
- `LEFT JOIN` — **Left table (employees) ki saari rows** result mein aayengi, chahe match mile ya na mile. Agar match nahi mila to right table ke columns (`dept_name`) `NULL` aa jaayenge. Ye use hota hai jab humein "sab employees chahiye, chahe unka department assigned ho ya na ho" jaisi requirement ho.

```sql
-- RIGHT JOIN
SELECT e.emp_name, d.dept_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;
```

**Line-by-line:**
- `RIGHT JOIN` — Isi ka opposite — **right table (departments) ki saari rows** guaranteed aayengi, chahe unme koi employee ho ya na ho. Ye use hota hai jaise "sab departments dikhao, chahe unme employee ho ya na ho".

```sql
-- FULL OUTER JOIN
SELECT e.emp_name, d.dept_name
FROM employees e
FULL OUTER JOIN departments d ON e.dept_id = d.dept_id;
```

**Line-by-line:**
- `FULL OUTER JOIN` — Dono tables ki saari rows aayengi — match mile to combine, na mile to jis side match nahi hua uska column NULL ho jaayega. Basically LEFT JOIN + RIGHT JOIN ka union.

```sql
-- SELF JOIN (manager-employee example — VERY common interview question)
SELECT e.emp_name AS employee, m.emp_name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

**Line-by-line (interviewer ko bologe):**
- `employees e` aur `employees m` — Same table ko do baar alias karke join kar rahe hain — isliye ise **SELF JOIN** kehte hain — jab ek table ke andar hi kisi row ka relation kisi doosri row se ho (jaise employee ka manager bhi ek employee hi hota hai).
- `LEFT JOIN` use kiya taaki wo employees bhi dikhein jinka manager_id NULL hai (jaise CEO, jiska koi manager nahi hota) — agar INNER JOIN use karte to CEO wali row hi gayab ho jaati.

```sql
-- CROSS JOIN
SELECT e.emp_name, d.dept_name
FROM employees e
CROSS JOIN departments d;
```

**Line-by-line:**
- `CROSS JOIN` — Cartesian product deta hai — har employee row ka har department row ke saath combination. Agar employees mein 10 rows hain aur departments mein 5, result mein 50 rows aayengi. Real use-case: jab humein har combination generate karni ho (jaise timetable generation).

**Interview trick:** *"3 tables ko join kaise karoge?"*
```sql
SELECT e.emp_name, d.dept_name, p.project_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id
JOIN projects p ON e.emp_id = p.emp_id;
```
→ "Sir, joins ko chain kar sakte hain — pehla join ek intermediate result set banata hai, uske upar doosra join apply hota hai, sequentially."

---

### 1.9 — Subqueries

```sql
-- Single-row subquery
SELECT emp_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

**Line-by-line:**
- `(SELECT AVG(salary) FROM employees)` — Ye **inner query** pehle independently execute hoti hai, ek single value (avg salary) return karti hai, phir outer query us value ko use karke filter karti hai. Isko **non-correlated subquery** kehte hain kyunki inner query outer query pe depend nahi karti.

```sql
-- Correlated subquery
SELECT e.emp_name, e.salary
FROM employees e
WHERE e.salary > (SELECT AVG(salary) FROM employees WHERE dept_id = e.dept_id);
```

**Line-by-line (interviewer ko bologe):**
- `WHERE dept_id = e.dept_id` — Yaha inner query outer query ke `e.dept_id` ko reference kar rahi hai — isliye ye **correlated subquery** hai. Isका matlab hai ki inner query **har outer row ke liye alag se re-execute** hoti hai — isliye performance-wise ye zyada expensive hota hai non-correlated subquery se, kyunki row-by-row evaluate hota hai.
- Business logic: "har employee ki salary compare karo apne hi department ke average se" — non-correlated subquery se ye possible nahi tha kyunki wo poore table ka ek hi average deta.

```sql
-- EXISTS
SELECT d.dept_name
FROM departments d
WHERE EXISTS (SELECT 1 FROM employees e WHERE e.dept_id = d.dept_id);
```

**Line-by-line:**
- `SELECT 1` — Yaha `1` ek dummy value hai, actual value se farak nahi padta kyunki `EXISTS` sirf ye check karta hai ki koi row return hui ya nahi — isliye convention hai `SELECT 1` likhna (na ki `SELECT *`), thoda cleaner lagta hai aur intent clear karta hai.
- `EXISTS (...)` — Boolean check hai — TRUE agar subquery kam se kam ek row return kare. Ye `IN` se zyada efficient hota hai jab bade datasets ho, kyunki EXISTS pehli matching row milte hi stop kar deta hai (short-circuit), jabki IN poori list build karta hai pehle.

**Common question:** *"IN vs EXISTS — kab kya use karoge?"*
→ "Sir, agar subquery ka result set chhota hai to IN theek hai, but agar outer table bada hai aur hume sirf existence check karni hai (na ki actual values compare karni hain), to EXISTS zyada efficient hai kyunki ye short-circuit evaluation karta hai."

---

### 1.10 — Set Operators

```sql
SELECT emp_name FROM employees WHERE dept_id = 10
UNION
SELECT emp_name FROM employees WHERE dept_id = 20;
```

**Line-by-line:**
- `UNION` — Dono queries ke results combine karta hai aur **duplicates automatically remove** karta hai (isके liye internally ek sort/distinct operation chalta hai — isliye thoda slow hota hai).

```sql
SELECT emp_name FROM employees WHERE dept_id = 10
UNION ALL
SELECT emp_name FROM employees WHERE dept_id = 20;
```
- `UNION ALL` — Same kaam karta hai lekin duplicates remove **nahi** karta — isliye ye `UNION` se **fast** hota hai. Agar pata hai ki duplicates aa hi nahi sakte (jaise yaha dept_id 10 aur 20 alag hain), to hamesha UNION ALL use karo performance ke liye.

```sql
SELECT dept_id FROM employees
INTERSECT
SELECT dept_id FROM departments;
```
- `INTERSECT` — Sirf wo rows return karta hai jo **dono** queries mein common hain.

```sql
SELECT dept_id FROM departments
MINUS
SELECT dept_id FROM employees;
```
- `MINUS` (Oracle) / `EXCEPT` (SQL Server) — First query ke results mein se wo rows hata deta hai jo second query mein bhi present hain — matlab "un departments ko dikhao jinme koi employee nahi hai".

**Rule jo interviewer test karega:** Set operators ke liye dono queries mein **same number of columns** aur **compatible data types** hone chahiye.

---

## PART 2: SQL INTERMEDIATE

### 2.1 — Views vs Materialized Views

```sql
CREATE VIEW emp_dept_view AS
SELECT e.emp_name, e.salary, d.dept_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;
```

**Line-by-line:**
- `CREATE VIEW emp_dept_view AS` — Ek **virtual table** bana rahe hain jo koi actual data store nahi karti, sirf ek stored query hai. Jab bhi is view ko query karoge, underlying query **har baar re-execute** hoti hai, latest data milega.
- Use case: complex joins ko baar-baar likhne se bachne ke liye, aur security ke liye bhi (users ko sirf specific columns dikha sakte ho, poori table access diye bina).

```sql
CREATE MATERIALIZED VIEW emp_dept_mv
BUILD IMMEDIATE
REFRESH ON DEMAND
AS
SELECT e.emp_name, e.salary, d.dept_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;
```

**Line-by-line (interviewer ko bologe):**
- `MATERIALIZED VIEW` — Normal view ke unlike, ye query ka result **physically disk pe store** karta hai — isliye read karna bahut fast hota hai (jaise normal table se select), lekin data **real-time** nahi hota.
- `BUILD IMMEDIATE` — View create hote hi data populate ho jaayega (alternative hai `BUILD DEFERRED`, jisme baad mein manually refresh karna padta hai).
- `REFRESH ON DEMAND` — Data tabhi update hoga jab manually `DBMS_MVIEW.REFRESH` call karenge — automatic nahi hoga. Alternative: `REFRESH ON COMMIT` (har commit pe auto-refresh, but overhead badh jaata hai).
- Use case: reporting/dashboards jahan real-time data zaroori nahi, lekin query speed important hai (kyunki underlying join baar baar recompute nahi karna padta).

**Interviewer ko one-liner:** "View ek stored query hai jo data store nahi karti aur real-time result deti hai; Materialized View data physically store karti hai isliye fast read deti hai lekin stale ho sakti hai jab tak refresh na ho."

---

### 2.2 — Indexes

```sql
CREATE INDEX idx_emp_dept ON employees(dept_id);
```

**Line-by-line:**
- `CREATE INDEX idx_emp_dept` — Ek separate data structure (default: **B-tree**) bana rahe hain jo `dept_id` column ki values ko sorted order mein rakhta hai, saath mein actual row ka pointer (rowid) bhi.
- Kyun use karte hain: bina index ke, database ko `dept_id = 10` dhundhne ke liye **full table scan** karna padega (har row check karni padegi). Index hone se database **binary search jaisi speed** se directly relevant rows tak pahunch jaata hai — O(n) se O(log n) jaisa improvement.

**Index ke types (interviewer ye deep dive karta hai):**
- **B-Tree Index** (default) — General purpose, high-cardinality columns (jaise emp_id, jaha bahut saari unique values hain) ke liye best.
- **Bitmap Index** — Low-cardinality columns ke liye best (jaise `gender`, `status` — jaha values ki variety kam hai) — data warehousing mein common.
- **Unique Index** — PK/UNIQUE constraint ke saath automatically bans jaata hai.
- **Composite Index** — Multiple columns par (jaise `(dept_id, salary)`) — order matter karta hai; leftmost column pe filter ho tabhi index use hoga effectively (leftmost prefix rule).

**Trade-off jo bolna zaroori hai:** "Sir, index SELECT queries ko fast karta hai, lekin INSERT/UPDATE/DELETE thoda slow ho jaate hain kyunki har data-change ke saath index bhi update karna padta hai. Isliye indexes sirf un columns pe banate hain jo frequently WHERE/JOIN/ORDER BY mein use hote hain, har column pe nahi."

---

### 2.3 — Normalization (1NF se BCNF tak)

**Example table (unnormalized):**

| order_id | customer | products |
|---|---|---|
| 1 | Ravi | Pen, Book |

**1NF (First Normal Form):** Har column mein **atomic (single) value** honi chahiye — no multi-valued/repeating groups.
```sql
-- Violates 1NF: 'products' column mein multiple values hain (Pen, Book)
-- Fix: alag row har product ke liye
```
"Sir, 1NF isliye chahiye kyunki agar ek column mein comma-separated multiple values store karenge, to us par query karna (jaise 'sirf Pen order karne walon ko dhundo') bahut mushkil ho jaayega — hume string parsing karni padegi jo inefficient hai."

**2NF:** 1NF + **no partial dependency** — matlab non-key column poore composite primary key pe depend kare, uske sirf ek part pe nahi.

**3NF:** 2NF + **no transitive dependency** — matlab non-key column sirf primary key pe depend kare, kisi doosre non-key column ke through nahi.
- Example: agar `employees` table mein `dept_id` aur `dept_name` dono hain, to `dept_name`, `emp_id` (PK) pe transitively depend karta hai via `dept_id` — isse fix karne ke liye `dept_name` ko alag `departments` table mein nikaal do.

**BCNF (Boyce-Codd Normal Form):** 3NF ka stricter version — har determinant candidate key hona chahiye.

**Interviewer ko bolne wali line:** "Normalization ka main purpose hai **data redundancy kam karna** aur **update anomalies avoid karna** — jaise agar dept_name repeat hoti rahe multiple rows mein aur ek din naam change karna ho, to sab jagah update karna padega; normalized design mein sirf ek jagah update karna padega."

**Trade-off:** Zyada normalization = zyada joins = kabhi kabhi query performance slow ho sakti hai. Isliye reporting/analytics systems mein jaan-boojhkar thoda **denormalization** bhi kiya jaata hai.

---

### 2.4 — Keys

| Key type | Matlab |
|---|---|
| Candidate Key | Wo columns jo uniquely row identify kar sakte hain (multiple ho sakte hain) |
| Primary Key | Candidate keys mein se chosen ek, jo actual PK banaya gaya |
| Alternate Key | Candidate keys jo PK nahi bane |
| Composite Key | Multiple columns milke PK banate hain |
| Foreign Key | Doosri table ke PK ko reference karta hai |
| Surrogate Key | Artificially generated key (jaise auto-increment ID), business meaning nahi rakhta |

**Kaise bologe:** "Candidate keys wo saare columns/combinations hain jo uniquely identify kar sakte hain, unme se ek ko humne Primary Key choose kiya; baaki Alternate Keys keh laate hain."

---

### 2.5 — ACID Properties (Transactions ka foundation)

- **Atomicity** — Transaction ya to poora execute hoga ya bilkul nahi (all or nothing). Agar beech mein fail hua, sab rollback ho jaayega.
- **Consistency** — Transaction database ko ek valid state se doosre valid state mein le jaata hai — sab constraints (PK, FK, CHECK) maintain rehte hain.
- **Isolation** — Concurrent transactions ek doosre ko affect nahi karte, jaise wo alag-alag execute ho rahe hon (isolation levels Part 3 mein).
- **Durability** — Ek baar COMMIT ho gaya, to data permanently save ho jaata hai, chahe system crash ho jaaye.

**Real example jo bolna chahiye:** "Bank transfer ka classic example — agar A se B ko paisa transfer ho raha hai, A ka balance minus hona aur B ka balance plus hona **ek hi transaction** ke andar atomic hona chahiye. Agar A ka balance minus ho gaya lekin system crash ho gaya B ka balance update hone se pehle, to Atomicity ki wajah se poora transaction rollback ho jaayega — A ka balance wapas original ho jaayega."

---

### 2.6 — Transactions: COMMIT, ROLLBACK, SAVEPOINT

```sql
BEGIN
  UPDATE employees SET salary = salary * 1.1 WHERE dept_id = 10;
  SAVEPOINT after_raise;

  DELETE FROM employees WHERE emp_id = 999;
  ROLLBACK TO after_raise;

  COMMIT;
END;
```

**Line-by-line:**
- `UPDATE ... WHERE dept_id = 10` — Ye change abhi sirf **current session** mein visible hai, database mein permanently save nahi hui.
- `SAVEPOINT after_raise` — Ek **checkpoint** bana rahe hain transaction ke beech mein, taaki poore transaction ko rollback kiye bina sirf yaha tak wapas aa sakein.
- `DELETE FROM employees WHERE emp_id = 999` — Ek galat delete maan lo (accidental).
- `ROLLBACK TO after_raise` — Sirf DELETE wala part undo hoga, salary wala UPDATE **safe rahega** kyunki wo savepoint se pehle tha.
- `COMMIT` — Ab jo bhi changes bache hain (salary update), wo **permanently** save ho jaayenge, aur ye visible ho jaayega doosre sessions ko bhi.

**One-liner:** "SAVEPOINT humein poore transaction ko waste kiye bina partial rollback karne deta hai — bade transactions mein bahut useful hai jaha ek chhoti mistake ke liye poora kaam dobara nahi karna padta."

---

### 2.7 — Window Functions (Analytic Functions) — bahut important, advanced roles mein zaroor poochte hain

```sql
SELECT emp_name, dept_id, salary,
       ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn,
       RANK()       OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk,
       DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS drnk
FROM employees;
```

**Line-by-line (interviewer ko bologe):**
- `OVER (PARTITION BY dept_id ORDER BY salary DESC)` — `PARTITION BY` data ko groups mein todta hai (bilkul GROUP BY jaisa), lekin **rows collapse nahi hoti** — matlab har individual row visible rehti hai, sirf ranking/calculation group ke andar hoti hai. Ye GROUP BY se ye bada difference hai.
- `ROW_NUMBER()` — Har row ko ek unique sequential number deta hai (1,2,3,4...) — agar do employees ki salary same bhi ho, dono ko alag number milega.
- `RANK()` — Agar do rows ki value same hai to unhe same rank milega, lekin **agla rank skip** ho jaayega (jaise 1,2,2,4 — 3 skip ho gaya).
- `DENSE_RANK()` — Same rank ties ke liye deta hai, lekin agla rank skip **nahi** hota (1,2,2,3).

**Bahut common interview question — "Nth highest salary nikalo":**
```sql
SELECT * FROM (
    SELECT emp_name, salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) WHERE rnk = 3;
```
**Line-by-line:** Inner query pehle har employee ko unki salary ke basis pe dense rank deti hai (ties handle karne ke liye DENSE_RANK use kiya, RANK nahi — kyunki agar 2 log same 2nd highest salary pe hain, dono ko same rank 2 milna chahiye, aur next distinct salary ko rank 3, na ki 4). Outer query sirf `rnk = 3` wali rows filter karti hai.

```sql
SELECT emp_name, salary,
       LAG(salary) OVER (ORDER BY hire_date) AS prev_emp_salary,
       LEAD(salary) OVER (ORDER BY hire_date) AS next_emp_salary
FROM employees;
```
- `LAG()` — Current row se **pichli row** ki value fetch karta hai (bina self-join kiye!).
- `LEAD()` — Current row se **agli row** ki value fetch karta hai.
- Use case: "consecutive employees ki salary compare karo hire_date ke order mein" — pehle ye self-join se karte the, ab window function se ek line mein ho jaata hai.

```sql
SELECT emp_name, dept_id, salary,
       SUM(salary) OVER (PARTITION BY dept_id) AS dept_total,
       ROUND(salary * 100.0 / SUM(salary) OVER (PARTITION BY dept_id), 2) AS pct_of_dept
FROM employees;
```
- `SUM(salary) OVER (PARTITION BY dept_id)` — Har department ka total salary calculate karta hai, **lekin har individual employee row ke saath dikhata hai** — GROUP BY se ye alag hai kyunki GROUP BY karte to individual employee rows gayab ho jaati, sirf department-level total milta.

---

### 2.8 — CTE (Common Table Expression) — `WITH` clause

```sql
WITH dept_avg AS (
    SELECT dept_id, AVG(salary) AS avg_sal
    FROM employees
    GROUP BY dept_id
)
SELECT e.emp_name, e.salary, d.avg_sal
FROM employees e
JOIN dept_avg d ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_sal;
```

**Line-by-line:**
- `WITH dept_avg AS (...)` — Ek **temporary named result set** define kar rahe hain jo sirf isi query ke scope mein exist karta hai — subquery jaisa hi kaam karta hai, lekin **readability** bahut better hoti hai, especially jab query complex ho ya same subquery multiple jagah use karni ho (CTE ek baar likho, kai baar reference karo).
- Interview mein bolo: "CTE nested subqueries se better hai readability ke liye, aur recursive queries ke liye CTE hi ek tarika hai (subquery se recursive kaam nahi ho sakta)."

**Recursive CTE (org hierarchy ke liye classic example):**
```sql
WITH RECURSIVE emp_hierarchy (emp_id, emp_name, manager_id, lvl) AS (
    -- Anchor member: top-level (CEO, jiska manager NULL hai)
    SELECT emp_id, emp_name, manager_id, 1
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive member: har level pe agli generation dhundo
    SELECT e.emp_id, e.emp_name, e.manager_id, eh.lvl + 1
    FROM employees e
    JOIN emp_hierarchy eh ON e.manager_id = eh.emp_id
)
SELECT * FROM emp_hierarchy ORDER BY lvl;
```

**Line-by-line (interviewer ko bologe):**
- **Anchor member** — Ye recursion ka **base case** hai — jaha se shuru karna hai (top ka employee, jiska manager NULL hai).
- `UNION ALL` — Anchor aur recursive part ko jodta hai; `UNION ALL` isliye kyunki duplicates yaha expected nahi hain aur performance ke liye behtar hai.
- **Recursive member** — `emp_hierarchy` (CTE khud ko) reference kar raha hai — har iteration mein pichli iteration ke result ko use karke agli level ke employees dhundta hai. Ye tab tak chalta rahega jab tak koi naya row match na ho (termination condition).
- `lvl + 1` — Har recursion ke saath hierarchy level badhate jaate hain — taaki pata chale kaun kis level pe hai (CEO=1, unke directs=2, waghera).

### 2.9 — CASE WHEN

```sql
SELECT emp_name, salary,
       CASE
           WHEN salary >= 80000 THEN 'High'
           WHEN salary >= 50000 THEN 'Medium'
           ELSE 'Low'
       END AS salary_band
FROM employees;
```

**Line-by-line:**
- `CASE ... WHEN ... THEN ... ELSE ... END` — SQL ka **if-else** hai. Conditions **top se bottom** evaluate hoti hain, jo pehli condition match ho jaaye uska result use hota hai — isliye order matter karta hai (yaha `>= 80000` pehle check hota hai, tabhi 'High' milega, warna 50000+ walo ko bhi 'High' mil jaata agar order galat hota).
- `ELSE 'Low'` — Agar koi bhi WHEN condition match nahi hui to default value; agar ELSE na ho aur koi match na ho to result `NULL` aayega.

---

## PART 3: SQL ADVANCED

### 3.1 — SQL ki Logical Query Processing Order (bahut important, deep-dive question)

```
1. FROM        -- tables identify aur join ki jaati hain
2. WHERE       -- row-level filtering
3. GROUP BY    -- rows groups mein todi jaati hain
4. HAVING      -- group-level filtering
5. SELECT      -- columns/expressions calculate hote hain
6. DISTINCT    -- duplicates remove
7. ORDER BY    -- final sorting
8. LIMIT/FETCH -- rows limit karna
```

**Interviewer ko exactly ye bologe:** "Sir, likhne ka order SELECT-FROM-WHERE hota hai, lekin database engine isko is order mein execute nahi karta — pehle FROM se data source decide hota hai, phir WHERE se rows filter hoti hain, phir GROUP BY se grouping hoti hai, HAVING se groups filter hote hain, tab jaake SELECT clause evaluate hoti hai jaha column aliases bante hain, aur sabse last mein ORDER BY chalta hai. Isi wajah se hum WHERE clause mein SELECT ka alias use nahi kar sakte, kyunki WHERE, SELECT se pehle chal chuka hota hai — lekin ORDER BY mein alias use kar sakte hain, kyunki wo SELECT ke baad chalta hai."

---

### 3.2 — Execution Plan & Query Optimization

```sql
EXPLAIN PLAN FOR
SELECT emp_name FROM employees WHERE dept_id = 10;

SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
```

**Line-by-line:**
- `EXPLAIN PLAN FOR ...` — Query ko actually execute nahi karta, sirf batata hai database **kaise** execute karega (full table scan karega ya index use karega, konsa join method use hoga — nested loop, hash join, ya merge join).
- `DBMS_XPLAN.DISPLAY` — Oracle mein execution plan ko readable format mein dikhaने ka standard tarika.

**Optimization tips jo bolne chahiye:**
- Query mein `SELECT *` avoid karo — sirf zaroori columns select karo (I/O kam hoga).
- WHERE clause mein column pe function apply mat karo (jaise `WHERE UPPER(emp_name) = 'RAVI'`) — isse index use nahi hota (non-sargable query), function-based index alag banani padegi.
- Zyada joins wali query mein filtering jitni jaldi ho sake, karo, taaki join karne se pehle hi data set chhota ho jaaye.
- Correlated subqueries ki jagah joins/window functions try karo jaha possible ho, kyunki correlated subquery row-by-row re-execute hoti hai.

---

### 3.3 — Isolation Levels & Locking

| Isolation Level | Kya prevent karta hai |
|---|---|
| READ UNCOMMITTED | Kuch nahi — dirty reads possible |
| READ COMMITTED | Dirty reads prevent karta hai (Oracle default) |
| REPEATABLE READ | Dirty + Non-repeatable reads prevent karta hai |
| SERIALIZABLE | Sab kuch prevent karta hai — dirty, non-repeatable, phantom reads |

**Definitions jo bolni chahiye:**
- **Dirty Read** — Ek transaction, doosre uncommitted transaction ka data padh leta hai — agar wo transaction rollback ho gaya to galat data padh liya tha.
- **Non-repeatable Read** — Same transaction mein same row ko do baar padha, lekin beech mein doosre transaction ne update karke commit kar diya — dono baar different value mili.
- **Phantom Read** — Same query do baar chalayi same transaction mein, lekin beech mein doosre transaction ne naya row insert/delete karke commit kar diya — row count hi change ho gaya.

**One-liner:** "Isolation level jitna strict hoga, data utna consistent milega, lekin concurrency (parallel transactions ki performance) utni hi kam ho jaayegi — ye ek trade-off hai."

---

### 3.4 — Common "Write the Query" Interview Problems

**Q: Duplicate rows dhundo/delete karo**
```sql
-- Dhundo
SELECT emp_name, COUNT(*)
FROM employees
GROUP BY emp_name
HAVING COUNT(*) > 1;

-- Delete karo (ek copy rakh ke baaki hatao)
DELETE FROM employees
WHERE emp_id NOT IN (
    SELECT MIN(emp_id)
    FROM employees
    GROUP BY emp_name
);
```
**Line-by-line:** Inner query har `emp_name` group ka **sabse chhota emp_id** nikalti hai (jise hum rakhna chahte hain). Outer DELETE un sab rows ko hata deta hai jinka emp_id is "keep list" mein nahi hai — matlab saare duplicates delete, sirf pehli entry bachegi.

**Q: Department-wise 2nd highest salary**
```sql
SELECT dept_id, emp_name, salary FROM (
    SELECT dept_id, emp_name, salary,
           DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
    FROM employees
) WHERE rnk = 2;
```
**Line-by-line:** `PARTITION BY dept_id` isliye use kiya taaki ranking **har department ke andar alag se** ho, poore table mein ek saath nahi — yehi "Nth highest salary" wali query se iska farak hai (wahan PARTITION nahi tha).

**Q: Employees jo apne manager se zyada kamate hain**
```sql
SELECT e.emp_name, e.salary, m.emp_name AS manager, m.salary AS mgr_salary
FROM employees e
JOIN employees m ON e.manager_id = m.emp_id
WHERE e.salary > m.salary;
```
**Line-by-line:** Ye self-join hai (Part 1.8 mein cover kiya) — `e` ko `m` se join karke same-row comparison possible ho paayi hai bina 2 baar table access kiye manually.

---

## PART 4: PL/SQL FUNDAMENTALS

### 4.1 — PL/SQL kya hai aur SQL se alag kyun?

**Interviewer ka pehla hi question hota hai:** *"SQL aur PL/SQL mein kya difference hai?"*

→ "Sir, SQL ek **declarative** language hai — hum bas ye batate hain ki *kya* chahiye (data), *kaise* nikalna hai wo database engine decide karta hai. SQL mein loops, conditions, variables nahi hote.

PL/SQL (**Procedural Language extension to SQL**) Oracle ka procedural extension hai jo SQL ke upar programming constructs deta hai — variables, loops, IF-ELSE, exception handling, functions, procedures. Isse hum **business logic** database ke andar hi likh sakte hain, jisse:
1. Network round-trips kam hote hain (poora block ek saath server ko bhejte hain, ek-ek statement nahi),
2. Logic reusable ban jaata hai (procedures/functions/packages ke through),
3. Better error handling milta hai (exception blocks),
4. Security better hoti hai (users ko direct table access diye bina procedures ke through access de sakte hain)."

---

### 4.2 — PL/SQL Block Structure

```sql
DECLARE
    v_emp_name  employees.emp_name%TYPE;
    v_salary    NUMBER := 0;
BEGIN
    SELECT emp_name, salary INTO v_emp_name, v_salary
    FROM employees
    WHERE emp_id = 101;

    DBMS_OUTPUT.PUT_LINE('Employee: ' || v_emp_name || ', Salary: ' || v_salary);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employee found with this ID.');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Unexpected error: ' || SQLERRM);
END;
/
```

**Line-by-line (interviewer ko bologe — ye sabse basic aur sabse zaroori cheez hai):**
- `DECLARE` — Ye **optional** section hai jaha hum variables, constants, cursors declare karte hain jo is block ke andar use honge. Agar koi declaration chahiye hi nahi to ye section skip bhi ho sakta hai.
- `v_emp_name employees.emp_name%TYPE` — `%TYPE` attribute use kiya taaki variable ka data type **automatically** us column ke data type se match ho jaaye (yaha `employees.emp_name` ka type). Fayda ye hai ki agar kabhi table ka column type change ho (jaise VARCHAR2(50) se VARCHAR2(100)), to code mein manually change karne ki zaroorat nahi — automatically sync ho jaayega. Hardcode `VARCHAR2(50)` likhne se ye maintenance problem hoti.
- `v_salary NUMBER := 0` — `:=` PL/SQL mein **assignment operator** hai (SQL ke `=` se alag, jo comparison ke liye hai) — yaha variable ko initial value 0 de rahe hain.
- `BEGIN` — Ye **executable section** shuru hota hai — actual logic yaha likha jaata hai; ye mandatory hai.
- `SELECT ... INTO v_emp_name, v_salary` — `INTO` clause PL/SQL mein zaroori hai jab SQL query ka result kisi variable mein store karna ho — pure SQL mein `INTO` nahi likhte, lekin PL/SQL block ke andar SELECT ka result seedha application ko nahi bhej sakte, isliye variable mein daalna padta hai.
- `WHERE emp_id = 101` — Ye query expect karti hai **exactly ek row** return ho, kyunki `INTO` sirf single row handle kar sakta hai — agar 0 rows milein to `NO_DATA_FOUND` exception, agar 1 se zyada milein to `TOO_MANY_ROWS` exception aayega.
- `DBMS_OUTPUT.PUT_LINE(...)` — Oracle ka built-in procedure hai console/screen pe output print karne ke liye (debugging ke liye bahut use hota hai) — `||` string concatenation operator hai.
- `EXCEPTION` — Ye section **optional** hai, lekin production code mein hamesha honi chahiye — yaha runtime errors handle karte hain taaki program crash na ho, gracefully handle ho.
- `WHEN NO_DATA_FOUND THEN` — Ye ek **predefined Oracle exception** hai jo tab automatically raise hoti hai jab SELECT INTO ko koi row nahi milti.
- `WHEN OTHERS THEN` — Ye ek **catch-all** handler hai jo baaki saari exceptions ko pakadta hai jo specifically handle nahi ki gayi — hamesha **sabse last** mein rakhte hain, kyunki ye sab kuch catch kar leta hai, agar upar rakhenge to specific handlers kabhi trigger hi nahi honge.
- `SQLERRM` — Current error ka **message** deta hai (readable string), aur `SQLCODE` uska **numeric code** deta hai.
- `END;` — Block khatam.
- `/` — Ye slash SQL*Plus/SQL Developer ko batata hai ki "is block ko ab execute karo" — ye PL/SQL syntax ka part nahi hai, ye tool ka command hai jo block terminator ka kaam karta hai.

---

### 4.3 — Data Types: %TYPE aur %ROWTYPE

```sql
DECLARE
    v_salary   employees.salary%TYPE;      -- single column ka type
    v_emp_row  employees%ROWTYPE;          -- poori row ka structure
BEGIN
    SELECT salary INTO v_salary FROM employees WHERE emp_id = 101;

    SELECT * INTO v_emp_row FROM employees WHERE emp_id = 101;
    DBMS_OUTPUT.PUT_LINE(v_emp_row.emp_name || ' - ' || v_emp_row.salary);
END;
/
```

**Line-by-line:**
- `%TYPE` — Ek **single column** ka data type copy karta hai variable mein.
- `%ROWTYPE` — Poori table (ya cursor) ki **row-structure** copy karta hai ek variable mein — matlab `v_emp_row` ek record hai jisme har column ke liye ek field hai (`v_emp_row.emp_name`, `v_emp_row.salary`, etc). Fayda: agar table mein naya column add ho jaaye, `SELECT *` wali query automatically usko bhi le legi bina code change kiye.
- `v_emp_row.emp_name` — Dot notation se record ke andar ke individual field ko access karte hain.

---

### 4.4 — Control Structures

**IF-ELSIF-ELSE:**
```sql
DECLARE
    v_salary employees.salary%TYPE := 55000;
BEGIN
    IF v_salary >= 80000 THEN
        DBMS_OUTPUT.PUT_LINE('High');
    ELSIF v_salary >= 50000 THEN
        DBMS_OUTPUT.PUT_LINE('Medium');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Low');
    END IF;
END;
/
```
**Line-by-line:** `ELSIF` (single word, **na ki** `ELSE IF` do words mein — ye common typo/trick question hai) — conditions top se bottom evaluate hoti hain, pehli TRUE milte hi baaki skip ho jaate hain. `END IF;` zaroori hai block close karne ke liye.

**Basic LOOP:**
```sql
DECLARE
    v_counter NUMBER := 1;
BEGIN
    LOOP
        EXIT WHEN v_counter > 5;
        DBMS_OUTPUT.PUT_LINE('Counter: ' || v_counter);
        v_counter := v_counter + 1;
    END LOOP;
END;
/
```
**Line-by-line:** `LOOP ... END LOOP` — infinite loop by default, isliye **hamesha ek exit condition** honi chahiye warna infinite chalega. `EXIT WHEN condition` — condition true hote hi loop turant break ho jaata hai — ye `IF condition THEN EXIT; END IF;` likhne ka shortcut hai.

**WHILE LOOP:**
```sql
DECLARE
    v_counter NUMBER := 1;
BEGIN
    WHILE v_counter <= 5 LOOP
        DBMS_OUTPUT.PUT_LINE('Counter: ' || v_counter);
        v_counter := v_counter + 1;
    END LOOP;
END;
/
```
**Line-by-line:** `WHILE condition LOOP` — condition **shuru mein hi check** hoti hai, matlab agar condition pehle hi false hai to loop body **ek baar bhi execute nahi hoga** (basic LOOP se ye difference hai, jaha body kam se kam ek baar chalta hai agar EXIT WHEN neeche ho).

**FOR LOOP:**
```sql
BEGIN
    FOR i IN 1..5 LOOP
        DBMS_OUTPUT.PUT_LINE('Value: ' || i);
    END LOOP;

    FOR i IN REVERSE 1..5 LOOP
        DBMS_OUTPUT.PUT_LINE('Reverse: ' || i);
    END LOOP;
END;
/
```
**Line-by-line:** `FOR i IN 1..5 LOOP` — `i` ko explicitly declare nahi karna padta, PL/SQL **implicitly** declare kar deta hai aur automatically increment karta hai — isliye FOR loop sabse concise hai jab hume exact iteration count pata ho. `REVERSE` keyword se loop ulta chalta hai (5,4,3,2,1), lekin range abhi bhi `1..5` hi likhte hain — `5..1` nahi likhte, warna loop **bilkul chalega hi nahi** (ye ek common gotcha hai).

---

### 4.5 — Cursors: Implicit vs Explicit

**Implicit Cursor** — Har SQL DML statement (SELECT INTO, INSERT, UPDATE, DELETE) ke liye Oracle **automatically** ek cursor banata hai jise hum `SQL%` prefix se access karte hain.

```sql
BEGIN
    UPDATE employees SET salary = salary * 1.1 WHERE dept_id = 10;

    IF SQL%FOUND THEN
        DBMS_OUTPUT.PUT_LINE(SQL%ROWCOUNT || ' rows updated.');
    ELSE
        DBMS_OUTPUT.PUT_LINE('No rows matched.');
    END IF;
END;
/
```
**Line-by-line:** `SQL%FOUND` — Boolean, TRUE agar kam se kam ek row affect hui. `SQL%ROWCOUNT` — Kitni rows affect hui uski count deta hai. Ye implicit cursor attributes hain — humne explicitly koi cursor declare nahi kiya, Oracle ne khud handle kiya `SQL` naam ke cursor ke through.

**Explicit Cursor** — Jab SELECT query **multiple rows** return karti hai aur hume unhe row-by-row process karna hai (kyunki `INTO` sirf single row handle karta hai), tab explicit cursor declare karte hain.

```sql
DECLARE
    CURSOR c_emp IS
        SELECT emp_name, salary FROM employees WHERE dept_id = 10;

    v_name   employees.emp_name%TYPE;
    v_salary employees.salary%TYPE;
BEGIN
    OPEN c_emp;
    LOOP
        FETCH c_emp INTO v_name, v_salary;
        EXIT WHEN c_emp%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_name || ': ' || v_salary);
    END LOOP;
    CLOSE c_emp;
END;
/
```

**Line-by-line (ye bahut common interview question hai — 4-step cursor lifecycle):**
- `CURSOR c_emp IS SELECT ...` — Query ko ek naam (`c_emp`) de rahe hain, **abhi execute nahi hui hai**, sirf define ki hai.
- `OPEN c_emp` — Ab query **actually execute** hoti hai database mein, aur result set memory mein ready ho jaata hai — cursor ek internal pointer maintain karta hai jo **pehli row se pehle** point karta hai.
- `FETCH c_emp INTO v_name, v_salary` — Cursor pointer ko **agli row** pe le jaata hai aur uski values variables mein daal deta hai.
- `EXIT WHEN c_emp%NOTFOUND` — `%NOTFOUND` TRUE ho jaata hai jab FETCH ko koi naya row nahi milta (matlab saari rows process ho chuki) — isliye ye check **FETCH ke turant baad** hona chahiye, loop ke start mein nahi, warna last row **do baar process** ho jaayegi (bahut common bug/gotcha jo interviewer test karta hai).
- `CLOSE c_emp` — Cursor ko close karna **mandatory** hai — isse memory/resources release hote hain. Agar close nahi kiya to resource leak ho sakta hai, especially loops mein cursor baar baar open karte waqt.

**Cursor attributes summary jo yaad rakhne chahiye:**
| Attribute | Matlab |
|---|---|
| `%FOUND` | TRUE agar last fetch ne row di |
| `%NOTFOUND` | TRUE agar last fetch ko row nahi mili |
| `%ROWCOUNT` | Ab tak kitni rows fetch ho chuki |
| `%ISOPEN` | TRUE agar cursor abhi open hai |

---

## PART 5: PL/SQL INTERMEDIATE

### 5.1 — Cursor FOR Loop & Parameterized Cursors

```sql
BEGIN
    FOR emp_rec IN (SELECT emp_name, salary FROM employees WHERE dept_id = 10) LOOP
        DBMS_OUTPUT.PUT_LINE(emp_rec.emp_name || ': ' || emp_rec.salary);
    END LOOP;
END;
/
```

**Line-by-line (interviewer isko "cleaner way" bolega):**
- `FOR emp_rec IN (SELECT ...) LOOP` — Ye **implicitly** OPEN, FETCH (loop ke andar automatically), aur CLOSE — teeno kar deta hai humare liye. `emp_rec` ek **implicit record** hai (`%ROWTYPE` jaisa) jo har iteration mein current row hold karta hai.
- Fayda: Explicit cursor (Part 4.5) mein humne manually OPEN/FETCH/EXIT WHEN/CLOSE likha — yaha ye sab automatic hai, code chhota aur clean hota hai. **Interview mein bolo:** "Jab simple row-by-row processing chahiye ho aur explicit control ki zaroorat na ho, tab cursor FOR loop use karta hoon kyunki ye resource management (open/close) khud handle karta hai — kam error-prone hai."

**Parameterized Cursor:**
```sql
DECLARE
    CURSOR c_emp(p_dept_id NUMBER) IS
        SELECT emp_name, salary FROM employees WHERE dept_id = p_dept_id;
BEGIN
    FOR emp_rec IN c_emp(10) LOOP
        DBMS_OUTPUT.PUT_LINE(emp_rec.emp_name);
    END LOOP;

    FOR emp_rec IN c_emp(20) LOOP
        DBMS_OUTPUT.PUT_LINE(emp_rec.emp_name);
    END LOOP;
END;
/
```
**Line-by-line:** `CURSOR c_emp(p_dept_id NUMBER)` — Cursor ko ek **parameter** de rahe hain, taaki same cursor definition ko different dept_id ke saath **reuse** kar sakein bina naya cursor banaye — `c_emp(10)` aur `c_emp(20)` dono same cursor ko alag values ke saath call kar rahe hain.

---

### 5.2 — Exception Handling (Detail)

**Types of exceptions:**
1. **Predefined** — Oracle ke built-in (jaise `NO_DATA_FOUND`, `TOO_MANY_ROWS`, `ZERO_DIVIDE`, `DUP_VAL_ON_INDEX`, `VALUE_ERROR`, `INVALID_NUMBER`).
2. **Non-predefined (Oracle error, but no name)** — `PRAGMA EXCEPTION_INIT` se naam do.
3. **User-defined** — Business logic ke apne exceptions.

```sql
DECLARE
    -- User-defined exception
    e_negative_salary EXCEPTION;

    -- Non-predefined: Oracle error -2292 (child record found) ko naam de rahe hain
    e_child_record_exists EXCEPTION;
    PRAGMA EXCEPTION_INIT(e_child_record_exists, -2292);

    v_salary employees.salary%TYPE := -500;
BEGIN
    IF v_salary < 0 THEN
        RAISE e_negative_salary;
    END IF;

EXCEPTION
    WHEN e_negative_salary THEN
        DBMS_OUTPUT.PUT_LINE('Error: Salary cannot be negative.');
    WHEN e_child_record_exists THEN
        DBMS_OUTPUT.PUT_LINE('Cannot delete: child records exist.');
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No matching record.');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error Code: ' || SQLCODE || ', Message: ' || SQLERRM);
        RAISE;
END;
/
```

**Line-by-line (interviewer ke liye ye complete answer hai):**
- `e_negative_salary EXCEPTION` — Custom exception declare kar rahe hain — ye koi Oracle built-in nahi hai, humari apni business rule hai.
- `RAISE e_negative_salary` — Manually is exception ko trigger kar rahe hain jab condition match ho.
- `e_child_record_exists EXCEPTION; PRAGMA EXCEPTION_INIT(e_child_record_exists, -2292)` — Oracle error `-2292` (foreign key violation — child record exist karta hai) ko ek readable naam de rahe hain. `PRAGMA` ek **compiler directive** hai jo compile-time pe hi is naam ko us error number se **link** kar deta hai. Bina isके, humein `WHEN OTHERS` mein `SQLCODE = -2292` check karna padta — ye zyada readable approach hai.
- `WHEN OTHERS THEN ... RAISE;` — `WHEN OTHERS` mein error print karne ke baad `RAISE;` (bina argument ke) likha hai — ye **original exception ko re-raise** karta hai taaki calling program ko bhi pata chale ki error hua tha, sirf yahi block chup-chaap ise swallow na kar le. Ye best practice hai — silently exceptions ko ignore karna production bugs ka common source hai.

**RAISE_APPLICATION_ERROR — custom error application ko bhejne ke liye:**
```sql
CREATE OR REPLACE PROCEDURE update_salary(p_emp_id NUMBER, p_new_salary NUMBER) AS
BEGIN
    IF p_new_salary < 0 THEN
        RAISE_APPLICATION_ERROR(-20001, 'Salary cannot be negative.');
    END IF;

    UPDATE employees SET salary = p_new_salary WHERE emp_id = p_emp_id;
END;
/
```
**Line-by-line:** `RAISE_APPLICATION_ERROR(-20001, 'message')` — Ye ek **user-defined error** raise karta hai jise calling application (Java/Python/frontend) bhi catch kar sakta hai with a proper error code aur message. Error number **-20000 se -20999** ke range mein hona chahiye — ye range Oracle ne specially **user-defined errors ke liye reserve** ki hai, isse Oracle ke apne internal error codes se clash nahi hota.

---

### 5.3 — Procedures

```sql
CREATE OR REPLACE PROCEDURE give_raise (
    p_emp_id     IN  NUMBER,
    p_percent    IN  NUMBER,
    p_new_salary OUT NUMBER
) AS
BEGIN
    UPDATE employees
    SET salary = salary * (1 + p_percent/100)
    WHERE emp_id = p_emp_id;

    SELECT salary INTO p_new_salary FROM employees WHERE emp_id = p_emp_id;

    COMMIT;
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
        RAISE;
END give_raise;
/
```

**Line-by-line:**
- `CREATE OR REPLACE PROCEDURE` — `OR REPLACE` isliye use kiya taaki agar procedure already exist karti hai to usko drop karke dobara banane ki zaroorat na pade — seedha overwrite ho jaayegi. Development mein ye standard practice hai.
- `p_emp_id IN NUMBER` — **IN parameter** — sirf **value pass karne ke liye**, procedure ke andar iski value read kar sakte hain lekin caller ko wapas kuch nahi milega isse. Ye **default mode** hai (agar kuch na likhein to IN hi maana jaata hai).
- `p_percent IN NUMBER` — Same, input value.
- `p_new_salary OUT NUMBER` — **OUT parameter** — Procedure ke andar iski value **set** ki jaati hai, aur caller ko wapas bheji jaati hai — jaise "return value" jaisa, lekin multiple OUT parameters ho sakte hain (function mein sirf ek RETURN value hoti hai, procedure mein multiple OUT ho sakte hain — ye bahut common difference-question hai).
- `COMMIT` — Changes ko permanent bana rahe hain **procedure ke andar hi** — production mein isse thoda dhyan se karna chahiye, kyunki agar procedure ko koi bada transaction call kar raha hai, andar ka COMMIT poore transaction ko commit kar dega (isliye kai jagah COMMIT caller pe chhod dete hain, procedure mein nahi karte).
- `EXCEPTION ... ROLLBACK; RAISE;` — Agar kuch fail hua to changes wapas undo karo, aur error ko upar propagate karo taaki caller ko pata chale.
- `END give_raise;` — Procedure/function ke end mein naam likhna **optional hai lekin best practice hai**, especially lambi procedures mein readability ke liye.

**Calling the procedure:**
```sql
DECLARE
    v_new_sal NUMBER;
BEGIN
    give_raise(101, 10, v_new_sal);
    DBMS_OUTPUT.PUT_LINE('New Salary: ' || v_new_sal);
END;
/
```

---

### 5.4 — Functions (Procedures se difference — bahut common question)

```sql
CREATE OR REPLACE FUNCTION get_annual_salary (p_emp_id IN NUMBER)
RETURN NUMBER
AS
    v_monthly_salary NUMBER;
BEGIN
    SELECT salary INTO v_monthly_salary FROM employees WHERE emp_id = p_emp_id;
    RETURN v_monthly_salary * 12;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RETURN 0;
END get_annual_salary;
/
```

**Line-by-line:**
- `RETURN NUMBER` — Function signature mein **return type declare karna mandatory** hai — ye function aur procedure ka sabse basic structural difference hai.
- `RETURN v_monthly_salary * 12` — Function **exactly ek value return** karti hai (procedure multiple OUT params de sakti hai, function sirf ek).
- `RETURN 0` (exception mein) — Function ke har path mein **kuch na kuch return hona chahiye**, warna compile/runtime error aayega.

**Function ko query ke andar bhi use kar sakte hain (procedure nahi kar sakte):**
```sql
SELECT emp_name, get_annual_salary(emp_id) AS annual_sal FROM employees;
```

**Interviewer ko table format mein bologe:**

| Aspect | Procedure | Function |
|---|---|---|
| Return | 0 ya multiple values (OUT params ke through) | Exactly 1 value (RETURN se) |
| SQL mein use | Nahi kar sakte SELECT ke andar | Kar sakte hain (`SELECT func(x) FROM ...`) |
| Call karna | Standalone statement se (`EXEC proc_name`) | Expression ke part ke roop mein |
| Purpose | Action perform karna (jaise DML) | Value calculate karke return karna |

**Gotcha:** Agar function ke andar DML (INSERT/UPDATE/DELETE) hai aur usko SQL query ke andar call kiya (jaise SELECT mein), to error aayega ("cannot perform DML from within a query") — isliye functions jo SELECT mein use hone hain, unme DML avoid karte hain, ya `PRAGMA AUTONOMOUS_TRANSACTION` use karte hain (Part 6.6 mein).

---

### 5.5 — Packages

**Package Specification (interface — kya-kya available hai, ye batata hai):**
```sql
CREATE OR REPLACE PACKAGE emp_pkg AS
    PROCEDURE give_raise(p_emp_id NUMBER, p_percent NUMBER);
    FUNCTION get_annual_salary(p_emp_id NUMBER) RETURN NUMBER;
    v_company_name CONSTANT VARCHAR2(50) := 'Kashi Corp';
END emp_pkg;
/
```

**Package Body (implementation — actual logic yaha likha jaata hai):**
```sql
CREATE OR REPLACE PACKAGE BODY emp_pkg AS

    PROCEDURE give_raise(p_emp_id NUMBER, p_percent NUMBER) AS
    BEGIN
        UPDATE employees SET salary = salary * (1 + p_percent/100) WHERE emp_id = p_emp_id;
    END give_raise;

    FUNCTION get_annual_salary(p_emp_id NUMBER) RETURN NUMBER AS
        v_sal NUMBER;
    BEGIN
        SELECT salary INTO v_sal FROM employees WHERE emp_id = p_emp_id;
        RETURN v_sal * 12;
    END get_annual_salary;

END emp_pkg;
/
```

**Line-by-line (interviewer ke liye ye conceptual answer hai):**
- **Package Specification** — Ye ek **public interface** hai — jo bhi procedures/functions/variables yaha declare honge, wahi **bahar se call kiye ja sakte hain** (jaise `emp_pkg.give_raise(...)`). Ye batata hai "kya available hai", implementation nahi dikhata.
- **Package Body** — Yaha **actual implementation** likhi jaati hai. Spec mein declare kiye har procedure/function ka matching body yaha likhna zaroori hai.
- Package ke andar body mein aise bhi procedures/functions ho sakte hain jo **spec mein declare nahi hain** — unhe **private** kehte hain, sirf package ke andar hi call ho sakte hain, bahar se nahi.

**Package use karne ke fayde (interview mein bolna zaroori hai):**
1. **Modularity** — Related procedures/functions ek logical unit mein group ho jaate hain (jaise sab employee-related operations `emp_pkg` mein).
2. **Encapsulation** — Private helper procedures ko bahar se hide kar sakte hain, sirf public interface expose hoti hai.
3. **Performance** — Jab package ka koi bhi ek object call hota hai, **poora package memory mein load** ho jaata hai (SGA ke shared pool mein) — agli baar koi doosra object call hoga to **disk se dobara load nahi karna padega** — isse performance improve hoti hai.
4. **Package-level (global) variables/state** — Ek session ke duration tak values yaad rakh sakte hain, jaise ek counter jo poori session mein persist rahe — ye standalone procedures/functions mein possible nahi hai.

**Calling:**
```sql
BEGIN
    emp_pkg.give_raise(101, 10);
    DBMS_OUTPUT.PUT_LINE(emp_pkg.get_annual_salary(101));
END;
/
```

---

## PART 6: PL/SQL ADVANCED

### 6.1 — Triggers

```sql
CREATE OR REPLACE TRIGGER trg_before_salary_update
BEFORE UPDATE OF salary ON employees
FOR EACH ROW
WHEN (NEW.salary < OLD.salary)
DECLARE
    v_dummy NUMBER;
BEGIN
    IF :NEW.salary < 0 THEN
        RAISE_APPLICATION_ERROR(-20002, 'Salary cannot be negative.');
    END IF;

    INSERT INTO salary_audit(emp_id, old_salary, new_salary, changed_on)
    VALUES (:OLD.emp_id, :OLD.salary, :NEW.salary, SYSDATE);
END;
/
```

**Line-by-line (interviewer ke liye ye complete, detailed answer hai):**
- `BEFORE UPDATE OF salary ON employees` — Ye specify karta hai trigger **kab** fire hoga: `salary` column update hone **se pehle**, aur **sirf** tab jab `salary` column hi update ho raha ho (agar sirf `emp_name` update ho raha ho to ye trigger fire hi nahi hoga) — ye targeted trigger hai, performance ke liye acha hai kyunki har UPDATE pe fire nahi hota.
- `FOR EACH ROW` — Ye batata hai ye ek **row-level trigger** hai — matlab agar UPDATE statement 100 rows affect kar raha hai, to trigger **100 baar** fire hoga, har row ke liye ek baar. Agar `FOR EACH ROW` na likhein to ye **statement-level trigger** ban jaayega — jo poore UPDATE statement ke liye **sirf ek baar** fire hota hai, chahe 1 row affect ho ya 1000.
- `WHEN (NEW.salary < OLD.salary)` — Ye ek **additional filter condition** hai — trigger sirf tab fire hoga jab naya salary purane se **kam** ho (matlab salary cut ho rahi ho) — is line mein `:` colon **nahi** lagta (sirf trigger body ke andar lagta hai, `WHEN` clause mein nahi — ye ek common syntax gotcha hai).
- `:NEW.salary` aur `:OLD.salary` — Row-level triggers mein `:OLD` **update se pehle wali value** hoti hai, `:NEW` **update ke baad wali value**. Colon (`:`) prefix isliye lagta hai kyunki ye **pseudo-records** hain (bind variables jaisa treat hote hain), normal PL/SQL variables nahi.
  - INSERT trigger mein sirf `:NEW` available hota hai (`:OLD` sab NULL hoga, kyunki row abhi exist hi nahi karti thi).
  - DELETE trigger mein sirf `:OLD` available hota hai (`:NEW` sab NULL, kyunki row delete ho rahi hai, uska "after" state hai hi nahi).
  - UPDATE trigger mein dono available hote hain.
- `INSERT INTO salary_audit(...)` — Ye ek **audit trail** bana rahe hain — har salary change ka record rakh rahe hain, taaki baad mein pata chal sake kab kisne kitni salary change ki — ye triggers ka ek **bahut common real-world use case** hai.

**BEFORE vs AFTER trigger — kab kya use karoge (bahut common question):**
- **BEFORE trigger** — Tab use karo jab validation karni ho ya `:NEW` values ko **modify** karna ho actual DML hone se pehle (jaise auto-populate karna, ya invalid data ko reject karna).
- **AFTER trigger** — Tab use karo jab actual operation ke baad koi cheez trigger karni ho, jaise audit logging (kyunki tab tak humein pata hai operation successfully ho chuka), ya kisi doosri table mein cascading update karna.

**Mutating Table Error (advanced gotcha jo senior interviews mein pucha jaata hai):**
"Sir, agar main ek row-level trigger ke andar **usi table** ko query/modify karne ki koshish karoon jis pe trigger laga hai, to `ORA-04091: table is mutating` error aayega — kyunki jab tak poora statement complete nahi hota, us table ki state 'inconsistent'/'changing' maani jaati hai. Isko avoid karne ke liye ya to statement-level trigger use karte hain, ya compound triggers (Oracle 11g+), jisme hum statement-level aur row-level dono actions ek hi trigger mein control kar sakte hain."

---

### 6.2 — Collections (VARRAY, Nested Table, Associative Array)

**Associative Array (Index-by Table) — sabse zyada use hone wala:**
```sql
DECLARE
    TYPE t_salary_map IS TABLE OF NUMBER INDEX BY VARCHAR2(50);
    v_salaries t_salary_map;
BEGIN
    v_salaries('Ravi') := 50000;
    v_salaries('Priya') := 60000;

    DBMS_OUTPUT.PUT_LINE(v_salaries('Ravi'));
END;
/
```
**Line-by-line:** `TYPE t_salary_map IS TABLE OF NUMBER INDEX BY VARCHAR2(50)` — Ye ek key-value structure define kar raha hai (jaise ek dictionary/HashMap Java ke) — `NUMBER` values honge, aur unko access karne ki key `VARCHAR2` (naam) hogi, na ki numeric index. `INDEX BY` isliye special hai — normal arrays sirf integer index se access hote hain, ye string se bhi ho sakta hai.

**VARRAY (fixed-size array):**
```sql
DECLARE
    TYPE t_names IS VARRAY(5) OF VARCHAR2(50);
    v_names t_names := t_names('Ravi', 'Priya', 'Amit');
BEGIN
    DBMS_OUTPUT.PUT_LINE(v_names(1));   -- PL/SQL mein indexing 1 se start hoti hai
    DBMS_OUTPUT.PUT_LINE(v_names.COUNT);
END;
/
```
**Line-by-line:** `VARRAY(5)` — Maximum **5 elements** hi hold kar sakta hai — size fixed hai declaration ke time pe hi, isse zyada elements add nahi kar sakte (`SUBSCRIPT_BEYOND_COUNT` exception aayegi). Use case: jab hume pata ho maximum kitne elements honge (jaise "week ke 7 din").
- `v_names(1)` — **PL/SQL mein indexing 1 se start hoti hai**, 0 se nahi (Java/Python jaisi languages se ye bada difference hai — interviewer isse zaroor test karta hai).

**Nested Table (dynamic size, no upper bound):**
```sql
DECLARE
    TYPE t_names IS TABLE OF VARCHAR2(50);
    v_names t_names := t_names('Ravi', 'Priya');
BEGIN
    v_names.EXTEND;
    v_names(3) := 'Amit';
    DBMS_OUTPUT.PUT_LINE(v_names.COUNT);
END;
/
```
**Line-by-line:** VARRAY ke unlike, Nested Table ki koi fixed upper limit nahi hoti — `.EXTEND` method se dynamically size badha sakte hain. `.COUNT` — kitne elements hain, batata hai (ye collection method hai, saare collection types mein available hai `.COUNT`, `.FIRST`, `.LAST`, `.EXISTS`, `.DELETE` jaise methods ke saath).

**One-liner jo table format mein bologe:**

| Type | Size | Sparse (gaps allowed)? | Index type |
|---|---|---|---|
| Associative Array | Unbounded | Yes | Integer ya String |
| Nested Table | Unbounded (dynamic) | Yes (after DELETE) | Integer only |
| VARRAY | Fixed max size | No | Integer only, dense |

---

### 6.3 — BULK COLLECT & FORALL (Performance ke liye — senior-level question)

**Problem jo interviewer describe karega:** "Agar tumhe 1 lakh rows process karni hain cursor se, row-by-row loop mein, to performance kaisi hogi?"

→ Answer: "Sir, agar hum cursor loop mein ek-ek row process karte hain, to har iteration mein PL/SQL engine aur SQL engine ke beech ek **context switch** hota hai — jo bahut expensive hai bade data volumes ke liye. Isko solve karne ke liye **BULK COLLECT** aur **FORALL** use karte hain, jo multiple rows ko **ek hi context switch mein** batch process karte hain."

```sql
DECLARE
    TYPE t_emp_ids IS TABLE OF employees.emp_id%TYPE;
    v_emp_ids t_emp_ids;
BEGIN
    SELECT emp_id
    BULK COLLECT INTO v_emp_ids
    FROM employees
    WHERE dept_id = 10;

    FORALL i IN 1..v_emp_ids.COUNT
        UPDATE employees SET salary = salary * 1.1 WHERE emp_id = v_emp_ids(i);

    DBMS_OUTPUT.PUT_LINE(v_emp_ids.COUNT || ' employees updated.');
    COMMIT;
END;
/
```

**Line-by-line:**
- `BULK COLLECT INTO v_emp_ids` — Normal `SELECT INTO` sirf ek row le sakta hai, lekin `BULK COLLECT INTO` **saari matching rows ek hi shot mein** collection variable mein le leta hai — ek single context switch mein, na ki row-by-row.
- `FORALL i IN 1..v_emp_ids.COUNT` — Ye ek **normal FOR loop nahi hai** — ye Oracle ko batata hai ki niche wala UPDATE statement **saare iterations ke liye ek hi batch mein bhej do** SQL engine ko, ek-ek karke nahi. Isse bhi sirf **ek** context switch hota hai poori batch ke liye, chahe 100 rows ho ya 1 lakh.
- **Important gotcha:** FORALL ke andar **sirf ek hi DML statement** ho sakta hai — agar do statements chahiye (jaise ek UPDATE aur ek INSERT), to do alag FORALL blocks likhne padenge.

**Interview mein one-liner:** "BULK COLLECT reading ke liye hai (SELECT ka result multiple rows mein collection mein le lena), FORALL writing ke liye hai (collection ke data se multiple DML ek batch mein karna) — dono milke row-by-row processing ke performance overhead ko eliminate karte hain, especially large datasets (10K+ rows) ke liye."

**LIMIT clause (memory control ke liye — bahut bade datasets ke liye zaroori):**
```sql
DECLARE
    CURSOR c_emp IS SELECT emp_id FROM employees;
    TYPE t_ids IS TABLE OF employees.emp_id%TYPE;
    v_ids t_ids;
BEGIN
    OPEN c_emp;
    LOOP
        FETCH c_emp BULK COLLECT INTO v_ids LIMIT 1000;
        EXIT WHEN v_ids.COUNT = 0;

        FORALL i IN 1..v_ids.COUNT
            UPDATE employees SET salary = salary * 1.05 WHERE emp_id = v_ids(i);
    END LOOP;
    CLOSE c_emp;
    COMMIT;
END;
/
```
**Line-by-line:** `LIMIT 1000` — Bina LIMIT ke, agar table mein 50 lakh rows hain, to `BULK COLLECT` sab kuch **ek saath PGA memory** mein le lega — memory overflow ho sakta hai. `LIMIT` batasch size ko control karta hai (yaha ek baar mein sirf 1000 rows fetch honge), taaki memory usage manageable rahe, aur ye poora loop tab tak chalega jab tak koi row bachi ho (`EXIT WHEN v_ids.COUNT = 0`).

---

### 6.4 — Dynamic SQL (EXECUTE IMMEDIATE)

```sql
DECLARE
    v_table_name VARCHAR2(30) := 'employees';
    v_sql        VARCHAR2(200);
    v_count      NUMBER;
BEGIN
    v_sql := 'SELECT COUNT(*) FROM ' || v_table_name || ' WHERE dept_id = :1';

    EXECUTE IMMEDIATE v_sql INTO v_count USING 10;

    DBMS_OUTPUT.PUT_LINE('Count: ' || v_count);
END;
/
```

**Line-by-line (interviewer ke liye important nuance):**
- `v_sql := 'SELECT COUNT(*) FROM ' || v_table_name || ...` — Query ko **runtime pe string ke roop mein** build kar rahe hain, kyunki table ka naam khud hi ek variable hai — ye **static SQL** mein possible nahi hai (static SQL mein table/column names compile-time pe fixed hone chahiye).
- `EXECUTE IMMEDIATE v_sql INTO v_count USING 10` — Ye string ko **actual SQL statement** ki tarah execute karta hai. `USING 10` — Ye ek **bind variable** pass kar raha hai (query ke andar `:1` placeholder ke corresponding), **string concatenation se nahi**.
- **Bahut important security point jo bolna chahiye:** "Sir, maine yaha bind variable (`USING 10`) use kiya, na ki value ko directly string mein concatenate kiya (jaise `... WHERE dept_id = ' || p_dept_id`) — isse do fayde hote hain: pehla, **SQL injection se bachaav** hota hai kyunki value ko literal data ki tarah treat kiya jaata hai, code ki tarah nahi; doosra, **performance** better hoti hai kyunki Oracle same query structure ko cache karke reuse kar sakta hai (bind variables alag hone par bhi), jabki concatenated values wali har query Oracle ko **naya** query lagti hai (hard-parsing baar baar hoti hai)."

**Use case:** Dynamic SQL tab use karte hain jab query ka structure runtime pe pata chalta hai — jaise table/column name parameter se aa raha ho, ya WHERE clause conditionally badalti ho.

---

### 6.5 — REF CURSOR (Strong vs Weak)

```sql
CREATE OR REPLACE PROCEDURE get_employees(p_dept_id IN NUMBER, p_cursor OUT SYS_REFCURSOR) AS
BEGIN
    OPEN p_cursor FOR
        SELECT emp_name, salary FROM employees WHERE dept_id = p_dept_id;
END;
/
```

**Line-by-line:**
- `p_cursor OUT SYS_REFCURSOR` — Ye ek **weak REF CURSOR** hai (Oracle ka built-in generic cursor type) — koi fixed return-structure define nahi ki, isliye ye **kisi bhi query** ka result hold kar sakta hai. Isko **OUT parameter** ki tarah pass kar rahe hain taaki calling program (jaise Java/Python application, ya doosra PL/SQL block) is result set ko access kar sake.
- **Real use case:** Jab humein result set **procedure se bahar** (application layer tak) bhejni ho — jaise Java application ek stored procedure call karta hai aur result set **ResultSet** object ki tarah wapas leta hai — ye tabhi possible hai jab hum REF CURSOR use karein, normal cursor sirf PL/SQL ke andar hi kaam karta hai.

**Strong vs Weak REF CURSOR:**
```sql
TYPE t_emp_cursor IS REF CURSOR RETURN employees%ROWTYPE;   -- Strong: fixed return type
TYPE t_generic_cursor IS REF CURSOR;                          -- Weak: koi bhi structure
```
- **Strong REF CURSOR** — Return type fixed hai declaration ke time (`employees%ROWTYPE`) — compile-time pe hi type-checking ho jaati hai, safer hai.
- **Weak REF CURSOR** (`SYS_REFCURSOR` bhi isi ka predefined version hai) — Koi fixed return type nahi, **flexible** hai, kisi bhi query ke saath use ho sakta hai — isliye ye zyada commonly use hota hai jab generality chahiye.

---

### 6.6 — Autonomous Transactions

```sql
CREATE OR REPLACE PROCEDURE log_error(p_message VARCHAR2) AS
    PRAGMA AUTONOMOUS_TRANSACTION;
BEGIN
    INSERT INTO error_log(message, logged_on) VALUES (p_message, SYSDATE);
    COMMIT;
END;
/
```

**Line-by-line:**
- `PRAGMA AUTONOMOUS_TRANSACTION` — Ye is procedure ko ek **independent transaction** bana deta hai — matlab is procedure ke andar ka COMMIT/ROLLBACK, **calling (parent) transaction ko bilkul affect nahi karega**, aur na hi parent transaction ka commit/rollback ispe asar dalega.
- **Real use case jo interviewer expect karta hai:** "Maan lo main ek bade transaction ke beech mein hoon jo fail ho gaya aur main ROLLBACK karne wala hoon — lekin main chahta hoon ki **error log to permanently save ho jaaye**, chahe baaki poora transaction rollback ho jaaye. Agar main normal procedure use karta error logging ke liye, to uska INSERT bhi parent transaction ke saath hi ROLLBACK ho jaata. Isliye error-logging jaisa kaam autonomous transaction mein karte hain — taaki wo apne aap mein independent rahe."
- Is procedure mein `COMMIT` likhna **mandatory** hai — agar autonomous transaction khatam hone tak commit/rollback nahi kiya, to `ORA-06519` error aayega.

---

## PART 7: RAPID-FIRE INTERVIEW Q&A (Last-minute revision)

**Q1. DELETE vs TRUNCATE vs DROP?**
→ "DELETE ek DML command hai, row-by-row deletes karta hai, WHERE clause use kar sakte hain, rollback possible hai commit se pehle, triggers fire hote hain. TRUNCATE ek DDL command hai, poori table ka data ek saath remove karta hai (high-watermark reset karta hai), WHERE clause nahi le sakte, auto-commit hota hai (rollback nahi ho sakta), triggers fire nahi hote — isliye TRUNCATE, DELETE se **bahut fast** hota hai bade tables ke liye. DROP poori table hi structure sahit delete kar deta hai database se."

**Q2. WHERE vs HAVING?**
→ "WHERE row-level filter hai, grouping se pehle chalta hai, aggregate functions use nahi kar sakte. HAVING group-level filter hai, grouping ke baad chalta hai, aggregate functions use kar sakte hain."

**Q3. UNION vs UNION ALL?**
→ "UNION duplicates remove karta hai (implicit sort/distinct ki wajah se slow), UNION ALL nahi karta (fast)."

**Q4. Primary Key vs Unique Key?**
→ "PK: NOT NULL + unique, ek table mein ek hi. UNIQUE: NULL allow karta hai, multiple ho sakte hain."

**Q5. Clustered vs Non-clustered Index? (SQL Server context mein pucha jaata hai, Oracle mein IOT similar concept hai)**
→ "Clustered index actual data ko physically us order mein store karta hai (ek table mein sirf ek clustered index ho sakta hai — usually PK). Non-clustered index ek separate structure hai jo pointer rakhta hai actual data tak — ek table mein multiple non-clustered indexes ho sakte hain."

**Q6. Function vs Stored Procedure?**
→ Part 5.4 dekho — exactly ek value return karta hai vs multiple/zero, SQL mein use ho sakta hai vs nahi.

**Q7. `%TYPE` vs `%ROWTYPE`?**
→ "%TYPE ek single column ka type leta hai, %ROWTYPE poori row ka structure (record) leta hai."

**Q8. Trigger vs Procedure?**
→ "Procedure explicitly call karna padta hai (`EXEC proc_name`), Trigger **automatically** fire hota hai kisi DML event (INSERT/UPDATE/DELETE) ke response mein — hum ise directly call nahi karte."

**Q9. NULL ke saath comparison kaise hota hai?**
→ "NULL kisi bhi value ke equal nahi hota, khud NULL ke bhi nahi (`NULL = NULL` bhi UNKNOWN return karta hai, TRUE nahi) — isliye `IS NULL`/`IS NOT NULL` use karte hain, `=`/`!=` nahi."

**Q10. Correlated Subquery vs Non-correlated Subquery?**
→ Part 1.9 dekho — correlated outer query pe depend karta hai aur row-by-row re-execute hota hai, non-correlated independently ek baar chalta hai.

**Q11. RANK() vs DENSE_RANK() vs ROW_NUMBER()?**
→ "ROW_NUMBER unique sequential number deta hai chahe ties ho. RANK ties ko same rank deta hai lekin agla number skip karta hai. DENSE_RANK ties ko same rank deta hai bina skip kiye."

**Q12. Index kab nahi banana chahiye?**
→ "Chhoti tables pe (jaha full scan hi fast hai), columns jo frequently update hote hain (index maintenance overhead badh jaayega), aur low-cardinality columns pe B-tree index (waha bitmap index better hai)."

**Q13. Exception handling mein WHEN OTHERS ko sabse last kyun rakhte hain?**
→ "Kyunki WHEN OTHERS ek catch-all hai jo baaki saari exceptions ko match kar leta hai — agar ise upar rakh diya to specific handlers (jaise NO_DATA_FOUND) kabhi trigger hi nahi honge, unreachable code ban jaayega."

**Q14. `COMMIT` ke baad `ROLLBACK` kaam karega?**
→ "Nahi, ek baar COMMIT ho gaya, changes permanent ho jaate hain, ROLLBACK sirf **usi transaction ke andar, commit se pehle** ke changes undo kar sakta hai."

**Q15. Mutating Table Error kya hai?**
→ Part 6.1 dekho.

**Q16. Cursor ke 4 attributes bolo.**
→ `%FOUND`, `%NOTFOUND`, `%ROWCOUNT`, `%ISOPEN` (Part 4.5 dekho).

**Q17. PL/SQL mein array indexing kaha se start hoti hai?**
→ "1 se — Java/Python/C ke unlike, jaha 0 se start hoti hai."

**Q18. Views ka use kyun karte hain?**
→ "Complex queries ko simplify karne ke liye, security ke liye (sensitive columns hide karna), aur data abstraction ke liye."

**Q19. Composite index mein column order kyun matter karta hai?**
→ "Leftmost-prefix rule ke wajah se — agar index `(dept_id, salary)` pe hai, to query `WHERE dept_id = 10` ya `WHERE dept_id = 10 AND salary > 5000` mein index use hoga, lekin sirf `WHERE salary > 5000` mein **use nahi hoga** kyunki leftmost column (`dept_id`) filter mein hi nahi hai."

**Q20. Package ke fayde kya hain?**
→ Part 5.5 dekho — modularity, encapsulation, performance (memory mein poora package cache hota hai), package-level global state.

---

## PART 8: TRICKY / GOTCHA QUESTIONS (Ye interviewer ka favourite hota hai — depth test karta hai)

**Gotcha 1:** *"Agar main WHERE clause mein SELECT ka column alias use karoon to kya hoga?"*
→ Error aayega, kyunki WHERE, SELECT se pehle execute hota hai (Part 3.1 — logical processing order). Alias abhi tak "bana" hi nahi hai jab WHERE evaluate ho raha hai.

**Gotcha 2:** *"COUNT(*) aur COUNT(column_name) mein performance difference hota hai?"*
→ "Modern Oracle optimizer mein practically koi major difference nahi hai, dono similar tarike se optimize hote hain — lekin logically COUNT(*) rows count karta hai (NULLs included), COUNT(column) sirf non-NULL values count karta hai."

**Gotcha 3:** *"FOR loop mein `REVERSE 5..1` likhne se kya hoga?"*
→ Loop **bilkul chalega hi nahi** — range hamesha chhoti se badi value ki taraf hi likhni hoti hai (`1..5`), `REVERSE` keyword khud hi order ulta kar deta hai. `5..1` ek invalid/empty range maani jaati hai.

**Gotcha 4:** *"BEFORE trigger mein `:NEW.column_name` ki value ko modify kar sakte hain?"*
→ "Haan, BEFORE trigger mein `:NEW` values ko modify kar sakte hain (jaise auto-populate karne ke liye) kyunki actual DML abhi hua hi nahi hai. AFTER trigger mein `:NEW` ko modify **nahi** kar sakte, kyunki tab tak row already committed-state mein aa chuki hoti hai."

**Gotcha 5:** *"Function ke andar COMMIT/ROLLBACK likh sakte hain agar function ko SELECT query mein call kar rahe hain?"*
→ "Nahi — agar function SQL statement (jaise SELECT) ke andar se call ho raha hai, to usmein transaction control statements (COMMIT/ROLLBACK) allowed nahi hain, error aayega — jab tak `PRAGMA AUTONOMOUS_TRANSACTION` use na karein."

**Gotcha 6:** *"Cursor ke FETCH ke baad `%NOTFOUND` check karne se pehle EXIT likh diya to?"*
→ Bug aayega — last valid row **do baar process** ho jaayegi, kyunki `%NOTFOUND` check FETCH ke turant baad hona chahiye, na ki loop ke start mein.

**Gotcha 7:** *"View update karne se underlying table update ho jaati hai?"*
→ "Simple views (single table, no aggregate/join/DISTINCT/GROUP BY) mein haan, DML operations propagate ho jaate hain underlying table tak. Complex views (joins, aggregates, GROUP BY wali) generally **updatable nahi** hoti — unme DML allowed nahi hoga directly."

**Gotcha 8:** *"NULL ko kisi aggregate function (SUM, AVG) mein include kiya to result kya aayega?"*
→ "Aggregate functions NULLs ko automatically **ignore** kar dete hain calculation se — agar poori column hi NULL ho to SUM/AVG bhi NULL return karega, lekin agar kuch values non-NULL hain to sirf unhi pe calculation hogi."

**Gotcha 9:** *"Ek hi query mein GROUP BY aur ORDER BY dono ho sakte hain?"*
→ "Haan bilkul, aur ORDER BY hamesha last mein hi likha jaata hai, GROUP BY ke result (grouped rows) ko sort karta hai."

**Gotcha 10:** *"Do NULL values compare karne pe `IN` operator kya karega?"*
→ "`IN` list mein agar NULL ho (jaise `WHERE dept_id IN (10, 20, NULL)`), to wo NULL comparisons mein contribute nahi karta — sirf 10 aur 20 match honge, ye ek common trap hai jaha log expect karte hain NULL bhi match ho jaayega."

---

## PART 9: Quick Checklist — Interview se pehle ek baar zaroor revise karo

- [ ] Joins ke saare types (INNER, LEFT, RIGHT, FULL, SELF, CROSS) — line-by-line samjha sakte ho?
- [ ] Nth highest salary query (DENSE_RANK wali) bina dekhe likh sakte ho?
- [ ] WHERE vs HAVING, DELETE vs TRUNCATE vs DROP, Function vs Procedure — bina ruke bol sakte ho?
- [ ] Cursor lifecycle (DECLARE → OPEN → FETCH → CLOSE) samjha sakte ho, %NOTFOUND ka sahi placement?
- [ ] Exception handling (predefined, PRAGMA EXCEPTION_INIT, RAISE_APPLICATION_ERROR) ka farak?
- [ ] BEFORE vs AFTER trigger, row-level vs statement-level, mutating table error?
- [ ] BULK COLLECT + FORALL kyun use karte hain (context switch explanation)?
- [ ] Package spec vs body, aur package ke 4 fayde?
- [ ] Normalization 1NF-3NF ek real example ke saath?
- [ ] Window functions (ROW_NUMBER, RANK, DENSE_RANK, LAG, LEAD) ka farak?

---

*Ye guide tumhare liye ek complete, self-contained SQL + PL/SQL interview reference hai — basic se lekar advanced tak, har concept "kyun" ke saath. Isko revise karte time, khud interviewer bankar apne aap ko explain karo — agar wo smoothly nikal jaaye, tum ready ho.*
