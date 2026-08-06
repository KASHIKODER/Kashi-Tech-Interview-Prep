# SQL Interview Practice — Full Solutions (Category 5-8, Q33-Q70)
### Continuation of "SQL_Interview_Practice_Q1-Q32.md" — same schema, same format (interview-phrasing → thought process → query → line-by-line → naya concept → follow-ups → follow-up solutions)

> **Dialect:** MySQL 8.x. Jahan Oracle/SQL Server syntax alag hoga, wahan short note diya jaayega.
> **Schema:** Bilkul wahi jo Q1-32 wali file mein tha — bas ek nayi table `Logins` add ki hai (streak/gap wale advanced questions ke liye).

```sql
Departments(department_id, department_name)
Employees(employee_id, employee_name, department_id, salary, manager_id, hire_date, gender)
Customers(customer_id, customer_name)
Products(product_id, product_name, category, price)
Orders(order_id, customer_id, order_date, amount, product_id)
Projects(project_id, project_name, employee_id)
Logins(user_id, login_date)          -- NAYI TABLE, sirf is file ke Category 7-8 questions ke liye
```

**Reminder — Departments, Employees, Customers, Products, Orders, Projects data bilkul wahi hai jo pehli file mein tha.** (Agar bhool gaye ho, ek baar `SQL_Interview_Practice_Q1-Q32.md` ka top revise kar lo — Ravi/Priya/Amit/Sneha/Kunal/Neha/Arjun/Ananya wali 8-employee table.)

**Logins (nayi table)**
| user_id | login_date |
|---|---|
| U1 | 2023-01-01 |
| U1 | 2023-01-02 |
| U1 | 2023-01-03 |
| U1 | 2023-01-05 |
| U1 | 2023-01-06 |
| U1 | 2023-01-07 |
| U2 | 2023-01-01 |
| U2 | 2023-01-10 |

*(Note: U1 ke 2 alag streaks hain — Jan 1-3 aur Jan 5-7, beech mein Jan 4 ka gap hai. U2 ke sirf 2 isolated logins hain, 9 din ke gap ke saath — ye jaan-boojhkar rakha hai taaki "consecutive streak" aur "gap detection" dono patterns properly demonstrate ho sakein.)*

---

## CATEGORY 5 — SUBQUERIES

### Q33. Employees earning more than the company average salary

**Interview mein aise poocha jaayega:**
> "Write a query to find employees who earn more than the company's overall average salary."

**Thought Process:**
- Output: employee records — filter condition salary pe, lekin threshold koi fixed number nahi, ek **calculated value** hai (company average).
- Keyword pakdo: **"more than the company's overall average"** → pehle average nikaalna padega (ek subquery se), phir har employee ki salary us average se compare karni hai.
- Ye **non-correlated subquery** hai — inner query (`AVG(salary)`) sirf **ek baar** chalegi, independently, poori table pe.

**Solution Query:**
```sql
SELECT employee_name, salary
FROM Employees
WHERE salary > (SELECT AVG(salary) FROM Employees);
```

**Line-by-Line:**
- `(SELECT AVG(salary) FROM Employees)` — Ye inner query **poori table** ka average nikalti hai, ek single number return karti hai — is number ka outer query ke kisi row se koi lena-dena nahi (isliye non-correlated).
- `WHERE salary > ...` — Outer query ka har employee is single number ke against compare hota hai.

**Naya Concept — Non-Correlated Subquery in `WHERE`:**
"Sir, ye ek non-correlated subquery hai — matlab inner query outer query ke kisi column ko reference nahi karti, isliye wo sirf **ek baar** execute hoti hai, chahe outer table mein kitni bhi rows ho. Performance ke liye ye **correlated subquery se better** hai, jo har outer row ke liye baar-baar re-execute hoti hai (Q34 mein wo dekhenge)."

**Expected Output (is sample data pe):** Company average = 65875. Ravi(75000), Amit(90000), Neha(95000) — 3 rows.

**Follow-up Questions:**
- Ye subquery `WHERE` mein hai — kya ye `HAVING` mein bhi ho sakti hai? Farak kya hoga?
- Agar `AVG(salary)` ke bajaye `MAX(salary)` use karte, to kaunsa employee qualify karta?
- Isi query ko `JOIN` use karke (subquery ke bina) kaise likhoge?

**Follow-up Solutions:**
- **WHERE vs HAVING subquery:** `WHERE` mein subquery row-level filter karti hai (individual employees compare hote hain) — jaisa yaha kiya. `HAVING` mein subquery **group-level** filter karti hai (Q21 dekho, wahan departments ke average ko company average se compare kiya tha, groups ke against). Dono jagah subquery same tarike se kaam karti hai, bas comparison ka "level" (row vs group) alag hota hai.
- **`MAX(salary)` use karte:** `WHERE salary > (SELECT MAX(salary) FROM Employees)` — koi bhi employee is condition ko satisfy **nahi** kar sakta (kyunki koi bhi apni khud ki max salary se zyada nahi ho sakta) — result **empty** aayega. Interview mein ye ek acha logic-check question hai.
- **JOIN se rewrite (subquery ke bina):**
  ```sql
  SELECT e.employee_name, e.salary
  FROM Employees e
  CROSS JOIN (SELECT AVG(salary) AS avg_sal FROM Employees) a
  WHERE e.salary > a.avg_sal;
  ```
  Yaha `CROSS JOIN` se ek single-row calculated table (`avg_sal`) ko **har row ke saath** jodte hain, phir `WHERE` mein compare karte hain — functionally subquery jaisa hi result, bas syntax alag hai.

---

### Q34. Employees earning more than their own department's average salary (correlated subquery)

**Interview mein aise poocha jaayega:**
> "Write a query to find employees who earn more than the average salary of their own department."

**Thought Process:**
- Output: employee records — lekin threshold ab **har employee ke liye alag** hai (uske apne department ka average), na ki ek fixed company-wide number.
- Keyword pakdo: **"their own department's average"** → ye per-employee, per-department comparison hai — is baar inner query ko outer query ke `department_id` ka reference chahiye — matlab inner query **outer row pe depend** karti hai → ye **correlated subquery** hai.

**Solution Query:**
```sql
SELECT e.employee_name, e.department_id, e.salary
FROM Employees e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM Employees e2
    WHERE e2.department_id = e.department_id
);
```

**Line-by-Line:**
- `Employees e` (outer) aur `Employees e2` (inner) — Same table ko do baar alias kiya hai, jaise self join mein karte hain — bas yaha join nahi, subquery hai.
- `WHERE e2.department_id = e.department_id` — Ye **correlation** hai — inner query outer query ke current row ke `department_id` ko reference kar rahi hai. Isliye inner query **har outer row ke liye alag se, naye sirse** chalti hai.
- `WHERE e.salary > (...)` — Outer employee ki salary compare hoti hai **sirf uske apne department** ke average se, na ki poori company ke average se.

**Naya Concept — Correlated Subquery:**
"Sir, correlated subquery wo hoti hai jo outer query ke **kisi column ko reference karti hai** (yaha `e.department_id`) — isliye ye ek independent, single-value query nahi hai, balki **outer query ke har row ke liye baar-baar re-evaluate** hoti hai. Agar Employees table mein 1000 rows hain, to ye inner query **1000 baar** chalegi — isliye ye performance-wise Q33 wali non-correlated subquery se **zyada expensive** hai. Lekin jab requirement hi row-specific ho (jaise 'apne department ka average'), to correlated subquery hi zaroori hoti hai."

**Expected Output (is sample data pe):** dept10(avg 59000): Ravi(75000)✓, Priya(62000)✓. dept20(avg 76000): Amit(90000)✓. dept30(avg 75000): Neha(95000)✓. Total 4 rows: Ravi, Priya, Amit, Neha.

**Follow-up Questions:**
- Correlated subquery non-correlated se performance mein kyun expensive hoti hai?
- Isi query ko window function se (bina correlated subquery ke) kaise likhoge?
- Arjun Singh (NULL department) ke case mein ye query kya karegi?

**Follow-up Solutions:**
- **Performance ka reason:** Non-correlated subquery sirf **ek baar** chalti hai poori query mein, uska result reuse hota hai. Correlated subquery database ko **har outer row ke liye ek naya, chhota query execute** karne padta hai — matlab agar outer table mein N rows hain, to inner query bhi effectively N baar chalti hai (conceptually — modern optimizers isko kabhi-kabhi smartly rewrite bhi kar dete hain, lekin worst-case yehi hai). Isliye bade tables pe correlated subqueries slow ho sakti hain.
- **Window function se rewrite (bina correlated subquery):**
  ```sql
  SELECT employee_name, department_id, salary
  FROM (
      SELECT employee_name, department_id, salary,
             AVG(salary) OVER (PARTITION BY department_id) AS dept_avg
      FROM Employees
  ) t
  WHERE salary > dept_avg;
  ```
  Ye `AVG() OVER (PARTITION BY ...)` (Category 7 ka window function) ek hi pass mein **har row ke against uske department ka average** calculate kar deta hai — correlated subquery se generally **zyada efficient** hai bade datasets pe, kyunki database ko baar-baar chhote queries nahi chalane padte.
- **Arjun Singh (NULL dept) ka case:** Inner query `WHERE e2.department_id = e.department_id` mein jab `e.department_id` khud NULL hai, to `NULL = NULL` comparison **UNKNOWN** deta hai (Q9 wala three-valued logic) — isliye Arjun ke liye inner query **koi bhi row match nahi karegi**, uska department-average `NULL` aayega, aur `e.salary > NULL` bhi UNKNOWN hoga — matlab Arjun **kabhi bhi** is result mein nahi aayega, chahe uski salary kitni bhi ho. Ye ek important edge-case hai jo interviewer specifically test kar sakta hai.

---

### Q35. Second highest salary using subquery (no window function)

**Interview mein aise poocha jaayega:**
> "Write a query to find the second-highest salary from the Employees table, without using a window function."

**Thought Process:**
- Output: ek single value — second highest salary.
- Keyword pakdo: **"without using a window function"** → interviewer explicitly window function (Category 7) block kar raha hai, purani-style subquery approach expect kar raha hai.
- Logic: "second highest" = "highest salary jo **sabse bade** (overall highest) se chhoti ho" — matlab pehle highest nikaalo, phir uss se chhoti sabse badi value dhundo.

**Solution Query:**
```sql
SELECT MAX(salary) AS second_highest_salary
FROM Employees
WHERE salary < (SELECT MAX(salary) FROM Employees);
```

**Line-by-Line:**
- `(SELECT MAX(salary) FROM Employees)` — Inner query overall **highest** salary nikalti hai (95000, Neha ki).
- `WHERE salary < (...)` — Outer query sirf un rows ko consider karti hai jinki salary highest se **kam** ho — matlab Neha khud is set se bahar ho jaati hai.
- `MAX(salary)` (outer) — Ab is chhote set mein se **highest** nikalte hain — jo automatically "second highest overall" ban jaata hai.

**Naya Concept — Nested `MAX` Trick for Nth-Highest (Without Window Functions):**
"Ye ek classic pre-window-function-era trick hai — 'sabse bade se chhota, phir usme sabse bada' — ye recursively Nth highest tak bhi extend ho sakta hai (Q36 mein dekhenge), lekin har extra level ke liye ek extra nested subquery chahiye hoti hai, jo readability kam karti jaati hai jitna N badhta hai."

**Expected Output (is sample data pe):** 90000 (Amit Verma ki salary).

**Follow-up Questions:**
- Agar sabki salary same ho (koi variation na ho), to ye query kya return karegi?
- Isi query mein employee ka **naam bhi** chahiye ho, to kya badlega?
- `LIMIT`/`OFFSET` use karke isi kaam ko kaise karoge?

**Follow-up Solutions:**
- **Sab salary same hone par:** Agar saari salaries equal hon (jaise sab 50000), to `WHERE salary < (SELECT MAX(salary)...)` **kisi bhi row ko match nahi karega** (kyunki koi bhi value apne se strictly kam nahi ho sakti) — outer `MAX(salary)` **NULL** return karega (empty set ka aggregate NULL hota hai). Ye batata hai ki agar distinct salaries hi na ho, to "second highest" ka concept hi meaningless ho jaata hai — interviewer ye edge-case zaroor poochta hai.
- **Employee naam bhi chahiye:**
  ```sql
  SELECT employee_name, salary
  FROM Employees
  WHERE salary = (
      SELECT MAX(salary) FROM Employees
      WHERE salary < (SELECT MAX(salary) FROM Employees)
  );
  ```
  Yaha humein pehle wahi second-highest **value** nikalni padi (nested subquery se), phir us value ke barabar employee dhundhna padा — do-level nesting.
- **`LIMIT`/`OFFSET` approach (dialect-specific, MySQL mein chalta hai):**
  ```sql
  SELECT DISTINCT salary
  FROM Employees
  ORDER BY salary DESC
  LIMIT 1 OFFSET 1;
  ```
  `DISTINCT` isliye zaroori hai taaki agar top 2 salaries mein tie ho, to duplicate na aaye. `LIMIT 1 OFFSET 1` ka matlab hai "sort karke, pehli row skip karo, agli 1 row do" — matlab 2nd position.

---

### Q36. Nth highest salary using nested subquery approach

**Interview mein aise poocha jaayega:**
> "Write a query to find the 3rd-highest salary using a nested subquery approach."

**Thought Process:**
- Ye Q35 ka hi **generalized version** hai — "second highest" ke bajaye ab koi bhi N (yaha 3) chahiye.
- Keyword pakdo: **"3rd-highest"** → distinct salaries ko descending sort karo, top N nikaalo, unme se **sabse chhoti** (last wali) hi asal mein Nth-highest hai.

**Solution Query:**
```sql
SELECT MIN(salary) AS third_highest_salary
FROM (
    SELECT DISTINCT salary
    FROM Employees
    ORDER BY salary DESC
    LIMIT 3
) AS top_three;
```

**Line-by-Line:**
- `SELECT DISTINCT salary ... ORDER BY salary DESC LIMIT 3` — Inner query top **3 distinct** salaries nikalti hai, descending order mein. `DISTINCT` zaroori hai warna agar koi salary repeat ho (jaise 62000), to wo do baar count ho jaayegi aur galat "Nth" mil sakta hai.
- `MIN(salary)` (outer) — In top-3 mein se **sabse chhoti** value hi effectively "3rd highest" hai — kyunki agar ye top-3 list descending hai, to list ka last element hi 3rd position hai.

**Naya Concept — Generalizing "Nth Highest" Pattern:**
"Ye pattern N ke kisi bhi value ke liye kaam karta hai — sirf `LIMIT` ki value badalni padegi. Lekin har N ke liye query ko manually change karna padta hai (hardcoded N), jo flexible nahi hai. Category 7 mein `DENSE_RANK()` se ye same kaam ek **parameterized** tarike se hoga, jaha N ko `WHERE rnk = N` mein easily badal sakte hain."

**Expected Output (is sample data pe):** Distinct salaries descending: 95000, 90000, 75000, ... — top 3 = 95000, 90000, 75000. `MIN` of these = **75000** (Ravi Kumar ki salary).

**Follow-up Questions:**
- Agar `DISTINCT` na likhein aur koi salary duplicate ho, to result kaise galat ho sakta hai?
- Isi approach se 5th-highest salary kaise nikaaloge?
- Ye approach `DENSE_RANK()` se kaise behtar/kamzor hai flexibility ke terms mein?

**Follow-up Solutions:**
- **`DISTINCT` hataने ka effect:** Agar `DISTINCT` na ho aur top salaries mein duplicate ho (jaise agar 62000 top-3 mein aata, jo yaha nahi hai lekin maan lo hota), to `LIMIT 3` **duplicate values ko bhi separately count** kar leta — matlab asal mein sirf 2 distinct salaries hi mil pati "3 rows" ke naam pe, aur `MIN` ek galat (duplicate) value de deta jo actual 3rd distinct highest nahi hoti.
- **5th-highest:** Bas `LIMIT` ki value badlo:
  ```sql
  SELECT MIN(salary) AS fifth_highest
  FROM (SELECT DISTINCT salary FROM Employees ORDER BY salary DESC LIMIT 5) AS top_five;
  ```
- **`DENSE_RANK()` se comparison:** Ye nested-subquery approach **kaam karta hai** lekin har N ke liye ek naya, alag query likhna padta hai (`LIMIT 3`, `LIMIT 5`, etc. hardcoded) — flexible nahi hai agar N runtime pe decide karna ho. `DENSE_RANK()` approach (Category 7) mein N sirf ek `WHERE rnk = N` condition hai — same query structure, bas N ki value change karo, ya application se parameter bhi pass kar sakte ho. Isliye production code mein window-function approach **zyada maintainable** maana jaata hai.

---

### Q37. Departments with no employees (`NOT IN` / `NOT EXISTS`)

**Interview mein aise poocha jaayega:**
> "Write a query to find departments that currently have no employees."

**Thought Process:**
- Output: department records jinka Employees table mein **koi trace hi nahi** hai.
- Keyword pakdo: **"no employees"** → ye anti-join pattern hai (Q29 mein `LEFT JOIN + IS NULL` se kiya tha), ab isi ko **subquery approach** (`NOT IN` / `NOT EXISTS`) se karenge.
- **Important gotcha:** `Employees.department_id` mein NULL possible hai (Arjun Singh) — `NOT IN` NULL ke saath **poori tarah break** ho sakta hai, isliye subquery mein explicitly NULLs exclude karne padenge.

**Solution Query (safe version, NOT IN ke saath):**
```sql
SELECT department_id, department_name
FROM Departments
WHERE department_id NOT IN (
    SELECT department_id FROM Employees WHERE department_id IS NOT NULL
);
```

**Line-by-Line:**
- `SELECT department_id FROM Employees WHERE department_id IS NOT NULL` — Inner query un saare department_ids ki list deti hai jo **kisi na kisi employee ne use kiya hai** — `IS NOT NULL` explicitly likha hai taaki list mein koi NULL na aaye.
- `WHERE department_id NOT IN (...)` — Departments table se wo rows chuno jinka ID is list mein **nahi** hai.

**Naya Concept — `NOT IN` ka NULL-Trap (bahut critical, MUST-know for interviews):**
"Sir, agar main `WHERE department_id IS NOT NULL` wala filter **inner query se hata deta**, to bahut khatarnak cheez hoti: `Employees.department_id` mein Arjun Singh ki wajah se ek `NULL` hai. Jab `NOT IN` list mein **ek bhi NULL** ho, to poori `NOT IN` condition **har row ke liye UNKNOWN** ban jaati hai — matlab poori outer query **empty result** de degi, chahe departments unmatched hi kyun na hon! Ye ek **bahut common production bug** hai — isliye jab bhi `NOT IN` use karo aur subquery ke column mein NULL possible ho, to explicitly `IS NOT NULL` filter add karna **mandatory** hai."

**Alternative (aur zyada safe) — `NOT EXISTS`:**
```sql
SELECT d.department_id, d.department_name
FROM Departments d
WHERE NOT EXISTS (
    SELECT 1 FROM Employees e WHERE e.department_id = d.department_id
);
```
`NOT EXISTS` NULL-trap se **immune** hai, kyunki ye row-by-row existence check karta hai (list-membership nahi), isliye NULL values automatically issue nahi karte.

**Expected Output (is sample data pe):** Marketing (dept 40) — 1 row.

**Follow-up Questions:**
- Agar main `IS NOT NULL` filter hata doon inner query se, to kya hoga poore result ka?
- `NOT EXISTS` NULL-safe kyun hai jabki `NOT IN` nahi hai?
- Isi kaam ke liye `LEFT JOIN + IS NULL` (Q29 wala) use karo to result same aayega kya?

**Follow-up Solutions:**
- **`IS NOT NULL` filter hataने par:** Poori outer query **empty result set** de degi (0 rows), Marketing department bhi result se gayab ho jaayegi — jabki actual answer Marketing hi hai! Ye bug **silently** hota hai, koi error nahi aata, isliye production mein detect karna mushkil hota hai jab tak koi specifically test na kare.
- **`NOT EXISTS` NULL-safe kyun hai:** `NOT EXISTS` **row-existence** check karta hai — har outer row ke liye ye poochta hai "kya koi row hai jo is condition ko match kare?" NULL values yaha ek "value jo match nahi hoti" ki tarah naturally handle ho jaate hain, kyunki hum list-membership check nahi kar rahe, hum "koi matching row exist karti hai ya nahi" check kar rahe hain — NULL wali row bhi bas "match nahi hui" categorize ho jaati hai, poori query ko UNKNOWN mein nahi convert karti.
- **`LEFT JOIN + IS NULL` se same result:** Haan, bilkul same result aayega:
  ```sql
  SELECT d.department_id, d.department_name
  FROM Departments d
  LEFT JOIN Employees e ON d.department_id = e.department_id
  WHERE e.employee_id IS NULL;
  ```
  Teeno approaches (`NOT IN` with explicit NULL-filter, `NOT EXISTS`, `LEFT JOIN + IS NULL`) **logically equivalent** hain is use-case ke liye — `NOT EXISTS` aur `LEFT JOIN` generally **safer aur performance mein behtar** maane jaate hain `NOT IN` se, especially bade datasets pe.

---

### Q38. Customers jo Customers table mein hain lekin kabhi order nahi kiya (`NOT EXISTS` vs `NOT IN`)

**Interview mein aise poocha jaayega:**
> "Using a subquery (not a join), find customers who have never placed an order."

**Thought Process:**
- Ye Q29 (Products never sold) aur Q27 (LEFT JOIN approach) jaisa hi **result** hai, lekin interviewer explicitly **subquery approach** maang raha hai, join nahi.
- Keyword pakdo: **"never placed an order"** → anti-existence check, `NOT EXISTS` ya `NOT IN` (NULL-safe tarike se) use karenge.

**Solution Query (NOT EXISTS — zyada safe approach):**
```sql
SELECT c.customer_id, c.customer_name
FROM Customers c
WHERE NOT EXISTS (
    SELECT 1 FROM Orders o WHERE o.customer_id = c.customer_id
);
```

**Line-by-Line:**
- `NOT EXISTS (SELECT 1 FROM Orders o WHERE o.customer_id = c.customer_id)` — Ye ek **correlated subquery** hai (Q34 jaisa concept) — har customer ke liye check karta hai ki Orders table mein uska koi record hai ya nahi. Agar nahi hai, to `NOT EXISTS` true ban jaata hai aur wo customer result mein aata hai.

**Naya Concept — `NOT EXISTS` (Correlated) vs `NOT IN` — Kab kaunsa use karo:**
"Is sample data mein `Orders.customer_id` mein koi NULL nahi hai, isliye yaha `NOT IN` bhi safely kaam kar jaata:
```sql
SELECT customer_id, customer_name
FROM Customers
WHERE customer_id NOT IN (SELECT customer_id FROM Orders);
```
Lekin main **hamesha `NOT EXISTS` ko prefer karta hoon** production code mein, kyunki agar kal ko `Orders.customer_id` mein koi NULL value aa gayi (jaise koi guest-checkout order jiska customer_id capture na ho paya), to `NOT IN` wali query **silently poori tarah break** ho jaayegi (Q37 wala trap), jabki `NOT EXISTS` unaffected rahegi. Defensive coding ke liye `NOT EXISTS` safer default hai."

**Expected Output (is sample data pe):** Aarushi Bose (C4) — 1 row.

**Follow-up Questions:**
- Is specific dataset mein `NOT IN` safely kyun kaam kar jaata hai (koi problem nahi hai)?
- `NOT EXISTS` ke andar `SELECT 1` kyun likha hai, `SELECT *` kyun nahi?
- Performance ke terms mein `NOT EXISTS` aur `LEFT JOIN + IS NULL` (Q29) mein kya farak hota hai bade datasets pe?

**Follow-up Solutions:**
- **Is dataset mein `NOT IN` safe kyun hai:** Kyunki `Orders.customer_id` column mein humare sample data mein **koi NULL value nahi hai** — saare 5 orders ka customer_id valid (C1/C2/C3) hai. NULL-trap sirf tab activate hota hai jab subquery ke column mein kam se kam ek NULL ho. Lekin production mein humein hamesha ye **guarantee nahi hoti** ki future mein data clean rahega — isliye defensive coding ke liye `NOT EXISTS` ko default banana better practice hai, chahe abhi ke data mein problem na ho.
- **`SELECT 1` ka reason:** `EXISTS`/`NOT EXISTS` sirf ye check karte hain ki koi row return hui ya nahi — **actual column values se koi matlab nahi hai**. `SELECT 1` ek convention hai jo batata hai "hume yaha values ki parwah nahi, sirf existence check karni hai" — `SELECT *` likhne se database ko unnecessary saare columns fetch karne ka "appearance" milta hai (real optimizers ise anyway optimize kar dete hain, lekin `SELECT 1` intent zyada clearly communicate karta hai, readable code ke liye).
- **`NOT EXISTS` vs `LEFT JOIN + IS NULL` performance:** Dono logically equivalent hain aur modern databases (MySQL 8, PostgreSQL, Oracle) **dono ko internally similar execution plans** mein optimize kar dete hain — koi bada consistent performance difference nahi hota zyadatar cases mein. Historically `NOT EXISTS` ko thoda better mana jaata tha bade anti-join cases mein (kyunki ye "first match milte hi stop" wala short-circuit behaviour rakhta hai), lekin modern query optimizers dono approaches ko often same plan mein convert kar dete hain. Interview mein bolo: "Dono functionally correct hain, main readability aur team-convention ke hisaab se choose karta hoon, lekin bade production systems mein actual `EXPLAIN PLAN` check karke decide karna best practice hai."

---

### Q39. Products priced higher than the average price of their own category (correlated subquery)

**Interview mein aise poocha jaayega:**
> "Write a query to find products priced higher than the average price of their own category."

**Thought Process:**
- Ye Q34 jaisa hi pattern hai (own-group-average comparison), bas Employees/department ke bajaye Products/category pe.
- Keyword pakdo: **"their own category"** → correlated subquery, `category` column pe correlate karenge.
- **Important:** Products table mein ek product (`Gadget E`) ki `price` `NULL` hai — is NULL ka `AVG` aur final comparison pe kya asar padta hai, ye dhyan se dekhna hai.

**Solution Query:**
```sql
SELECT p.product_name, p.category, p.price
FROM Products p
WHERE p.price > (
    SELECT AVG(p2.price)
    FROM Products p2
    WHERE p2.category = p.category
);
```

**Line-by-Line:**
- `WHERE p2.category = p.category` — Correlation: inner query sirf **usi category** ke products ka average nikalti hai jis category ka current outer product hai.
- `WHERE p.price > (...)` — Outer product ki price compare hoti hai apni category ke average se.
- **NULL ka effect:** `Gadget E` (P5, Electronics, price NULL) — jab ye outer row ban’ti hai, to `p.price > (...)` mein `NULL > <kuch bhi>` hamesha `UNKNOWN` hota hai — isliye P5 **kabhi bhi** is result mein nahi aayega, chahe uski (unknown) price kitni bhi ho. Saath hi, `AVG(p2.price)` calculate karte waqt bhi NULL prices **automatically ignore** hoti hain (Electronics ka average sirf P1 aur P2 ki price se banta hai, P5 ki NULL price count hi nahi hoti).

**Naya Concept — NULL ka Dual Effect (Aggregate mein Ignore, Comparison mein Exclude):**
"Ye question dikhata hai ki NULL do jagah alag tarike se react karta hai: (1) jab **aggregate function** (`AVG`) calculate ho rahi ho, NULL ko silently **ignore** kar diya jaata hai (na numerator, na denominator mein count hota); (2) jab **comparison** (`>`, `<`, `=`) ho rahi ho involving a NULL value, result `UNKNOWN` aata hai aur wo row **exclude** ho jaati hai `WHERE` se. Dono cases mein NULL 'chup-chaap gayab' ho jaata hai, bas mechanism alag hai."

**Expected Output (is sample data pe):** Electronics avg = (600+400)/2 = 500 (P5's NULL ignored). Widget A (P1, 600) > 500 ✓. Widget B (P2, 400) — no. Home avg = (500+250)/2 = 375. Gadget C (P3, 500) > 375 ✓. Gadget D (P4, 250) — no. **Result: Widget A, Gadget C — 2 rows.**

**Follow-up Questions:**
- Gadget E (NULL price) is result mein kabhi kyun nahi aa sakta?
- Agar Gadget E ki NULL price `AVG` mein 0 ki tarah treat hoti (jo nahi hoti), to Electronics ka average kya hota, aur result kaise badalta?
- Isi query ko window function se kaise likhoge?

**Follow-up Solutions:**
- **Gadget E kabhi kyun nahi aayega:** Jaisa upar explain kiya, `p.price > <koi bhi number>` jab `p.price` khud `NULL` hai, to result hamesha `UNKNOWN` hoga — chahe average kuch bhi ho. `WHERE` clause sirf `TRUE` rows rakhta hai, isliye Gadget E structurally **hamesha exclude** hogi is tarah ki kisi bhi comparison-based query se, jab tak specifically `OR price IS NULL` add na kiya jaaye (Q14 wala concept).
- **Agar NULL ko 0 treat karte (hypothetically):** Electronics avg (600+400+0)/3 = 333.33 ban jaata (kam ho jaata, kyunki denominator mein bhi 3 count hota). Isse Widget B (400) bhi qualify kar jaata (400 > 333.33), jo **galat** hota — kyunki hume Gadget E ki actual price pata hi nahi hai, use "0" maan lena ek **incorrect assumption** hoga. Yehi wajah hai ki SQL NULL ko 0 nahi, balki "unknown, exclude from calculation" maanta hai — ye mathematically zyada sahi approach hai.
- **Window function se rewrite:**
  ```sql
  SELECT product_name, category, price
  FROM (
      SELECT product_name, category, price,
             AVG(price) OVER (PARTITION BY category) AS category_avg
      FROM Products
  ) t
  WHERE price > category_avg;
  ```
  Yaha bhi NULL price wala P5 automatically exclude hoga (`NULL > category_avg` UNKNOWN), aur `AVG() OVER PARTITION BY` bhi NULL ko ignore karke average calculate karega — behaviour bilkul same, bas ek hi query mein "har row ke against uski category ka average" mil jaata hai, alag subquery declare karne ki zaroorat nahi.

---

### Q40. Employee with the highest salary in each department (subquery approach, not window function)

**Interview mein aise poocha jaayega:**
> "Without using a window function, find the employee with the highest salary in each department."

**Thought Process:**
- Ye Q18 ka **extension** hai — Q18 mein humne sirf `MAX(salary)` (number) nikaala tha department-wise; ab humein us salary wale **employee ka naam bhi** chahiye — ye plain `GROUP BY + MAX` se possible nahi (kyunki GROUP BY sirf aggregate columns hi expose karta hai, non-aggregated columns jaise `employee_name` allowed nahi hote bina unhe bhi GROUP BY mein daale).
- Keyword pakdo: **"employee with the highest salary"** (naam bhi chahiye) → correlated subquery zaroori hai, jo har employee ki salary ko uske department ke max se compare kare.
- **NULL department ka special handling:** Arjun Singh ka `department_id` NULL hai — normal `=` comparison NULL ke saath fail hoti hai (Q9 jaisa), isliye **NULL-safe equality operator** (`<=>`) use karna padega taaki Arjun bhi correctly apne (NULL) "department group" mein compare ho sake.

**Solution Query:**
```sql
SELECT e.employee_name, e.department_id, e.salary
FROM Employees e
WHERE e.salary = (
    SELECT MAX(e2.salary)
    FROM Employees e2
    WHERE e2.department_id <=> e.department_id
);
```

**Line-by-Line:**
- `WHERE e2.department_id <=> e.department_id` — `<=>` MySQL ka **NULL-safe equal operator** hai — ye normal `=` jaisa hi kaam karta hai non-NULL values ke liye, lekin `NULL <=> NULL` **TRUE** return karta hai (jabki normal `=` mein `NULL = NULL` UNKNOWN hota hai). Isse Arjun Singh (NULL dept) bhi correctly apne (akele) group mein match ho jaata hai.
- `WHERE e.salary = (SELECT MAX(...) ...)` — Har employee ki salary uske department ke max se compare hoti hai — sirf wahi employee match karega jiski salary **exactly** us department ke max ke barabar ho (matlab wo khud hi highest-paid hai apne department mein).

**Naya Concept — NULL-Safe Equality Operator (`<=>`):**
"Sir, MySQL mein ek special operator `<=>` hai jo `=` jaisa hi kaam karta hai, lekin NULL ko bhi ek 'comparable value' ki tarah treat karta hai — matlab `NULL <=> NULL` **TRUE** deta hai, jabki standard `= ` mein `NULL = NULL` hamesha `UNKNOWN`/false-jaisa treat hota hai. Ye tab useful hota hai jab humein explicitly NULL-to-NULL matching chahiye ho, jaise yaha 'Arjun apne (NULL) department-group ke andar hi apna max salary check kare'. Oracle mein isका sabse close equivalent hai `DECODE(a, b, 1, 0) = 1` ya `NVL` tricks; ANSI-standard tarika hai `IS NOT DISTINCT FROM`।"

**Expected Output (is sample data pe):** Ravi Kumar (dept10, 75000), Amit Verma (dept20, 90000), Neha Gupta (dept30, 95000), Arjun Singh (NULL dept, 48000 — apne akele group mein khud hi max hai).

**Follow-up Questions:**
- Agar `<=>` ki jagah normal `=` use karte, to Arjun Singh ka kya hota is result mein?
- Agar do employees ek hi department mein **same highest salary** share karte (tie), to ye query kya karegi?
- Ye same kaam window function (`RANK()`) se kaise karoge, aur wo approach kyun generally preferred hai?

**Follow-up Solutions:**
- **Normal `=` use karte to:** `e2.department_id = e.department_id` jab `e.department_id` NULL hai, ye comparison **hamesha UNKNOWN** hoga — matlab Arjun ke liye inner subquery **koi row match nahi karegi**, `MAX()` ek empty-set pe chalega jo `NULL` return karta hai. Phir outer `e.salary = NULL` bhi UNKNOWN — Arjun **result se gayab** ho jaayega, jo galat hai kyunki wo apne akele department-group mein khud hi highest-paid hai. Yehi wajah hai `<=>` zaroori tha.
- **Tie hone par (do employees same highest salary share karein):** Ye query **dono** employees ko return karegi — kyunki `WHERE e.salary = (SELECT MAX(...))` condition dono ke liye equally true hogi. Ye actually **correct aur desirable** behaviour hai (agar sach mein tie hai, to dono ko "highest-paid" dikhana chahiye) — lekin agar interviewer specifically "sirf ek row per department" chahta ho ties ke bawajood, to `LIMIT` ya `ROW_NUMBER()` (with a deterministic tie-breaker) chahiye hoga.
- **Window function approach (preferred):**
  ```sql
  SELECT employee_name, department_id, salary
  FROM (
      SELECT employee_name, department_id, salary,
             RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
      FROM Employees
  ) t
  WHERE rnk = 1;
  ```
  Ye approach **generally preferred** hai kyunki: (1) NULL department automatically ek apna partition ban jaata hai bina kisi `<=>` trick ke, (2) ties automatically handle hote hain (`RANK() = 1` dono tied employees ko dega), (3) ek hi pass mein saare departments ke liye calculate ho jaata hai, correlated subquery ki tarah baar-baar re-execute nahi karna padta — performance aur readability dono behtar.

---

## CATEGORY 6 — CTE (Common Table Expressions)

### Q41. Department average salary query ko CTE se rewrite karo (readability ke liye)

**Interview mein aise poocha jaayega:**
> "Rewrite the department-wise average salary query using a CTE (WITH clause) for better readability."

**Thought Process:**
- Ye Q16 (department-wise average) ka hi kaam hai, bas ab `WITH` clause use karke likhna hai.
- Keyword pakdo: **"using a CTE for better readability"** → koi naya logic nahi hai, sirf presentation/structure badalni hai.

**Solution Query:**
```sql
WITH dept_avg AS (
    SELECT department_id, AVG(salary) AS avg_salary
    FROM Employees
    GROUP BY department_id
)
SELECT * FROM dept_avg
ORDER BY avg_salary DESC;
```

**Line-by-Line:**
- `WITH dept_avg AS (...)` — Ek **temporary, named result set** define kar rahe hain jo sirf isi query ke scope mein exist karta hai — jaise ek "virtual table" jo sirf is query ke duration ke liye bani hai.
- `SELECT * FROM dept_avg` — Ab is named result ko normal table ki tarah query kar sakte hain — yaha sirf sort kar rahe hain, lekin future queries mein isi CTE ko multiple baar reference bhi kar sakte the.

**Naya Concept — CTE (`WITH` clause):**
"CTE aur subquery **functionally kaafi similar** hote hain — dono ek intermediate result set banate hain jise outer query use karti hai. Farak sirf **readability aur reusability** mein hai: subquery ko nested likhna padta hai (query ke andar query), jabki CTE ko **upar, alag se naam dekar** define karte hain aur phir neeche seedhe reference karte hain — jaise koi variable declare karke use kar rahe ho. Jab query complex ho jaaye ya same subquery ko 2-3 jagah use karna ho, CTE bahut zyada readable ho jaata hai."

**Expected Output (is sample data pe):** Engineering(76000), HR(75000), Sales(59000), NULL-dept(48000) — descending order mein.

**Follow-up Questions:**
- CTE aur subquery mein performance ka koi farak hota hai?
- Ek hi query mein multiple CTEs (comma se separate karke) kaise define karte hain?
- CTE ka result agla query call karne pe fir se calculate hota hai, ya cache ho jaata hai?

**Follow-up Solutions:**
- **Performance farak:** Zyadatar modern databases (MySQL 8+, PostgreSQL, Oracle) mein **non-recursive CTE aur equivalent subquery ka execution plan same hota hai** — optimizer dono ko internally similar tarike se treat karta hai. Kuch databases (jaise purane PostgreSQL versions) CTE ko "optimization fence" ki tarah treat karte the (matlab CTE ko pehle poora materialize karte the, phir outer query chalate the) — jo kabhi-kabhi slow bhi ho sakta tha agar CTE bada ho aur outer query use heavily filter kare. MySQL 8 mein aisi koi major penalty nahi hai zyadatar cases mein. Interview mein bolo: "Readability ke liye CTE prefer karta hoon, performance zyadatar same hi rehta hai, lekin bade CTEs pe `EXPLAIN` check karna best practice hai."
- **Multiple CTEs ek saath:**
  ```sql
  WITH dept_avg AS (
      SELECT department_id, AVG(salary) AS avg_salary FROM Employees GROUP BY department_id
  ),
  dept_count AS (
      SELECT department_id, COUNT(*) AS emp_count FROM Employees GROUP BY department_id
  )
  SELECT a.department_id, a.avg_salary, c.emp_count
  FROM dept_avg a
  JOIN dept_count c ON a.department_id <=> c.department_id;
  ```
  Comma (`,`) se separate karke multiple CTEs define kar sakte hain — har ek independent hai, aur baad wale CTEs pehle wale ko bhi reference kar sakte hain agar zaroorat ho.
- **Re-calculation vs caching:** Ek hi query-execution ke andar, agar CTE ko **multiple baar reference** kiya jaaye, to zyadatar databases usko **ek hi baar evaluate** karte hain aur result reuse karte hain (kam se kam MySQL 8 aur PostgreSQL mein ye common hai, though ye guarantee nahi, database-dependent hai). Lekin agla/naya query call (agli baar jab tum query run karoge) — CTE **phir se poori tarah re-calculate** hoga, kyunki CTE **persist nahi hota** — ye sirf ek query ke scope tak zinda rehta hai, jaise Materialized View (Part 2.1 wali PL/SQL guide mein) ke uske viprit — MV disk pe persist karta hai, CTE nahi.

---

### Q42. CTE se department-wise total salary nikaalo, phir wo departments dhundo jo company ke total salary ka 20%+ contribute karte hain

**Interview mein aise poocha jaayega:**
> "Using CTEs, find which departments contribute more than 20% of the company's total salary expenditure."

**Thought Process:**
- Ye ek **multi-step calculation** hai: (1) har department ka total salary nikaalo, (2) company ka poora total salary nikaalo, (3) dono ko compare karke percentage nikaalo, (4) filter karo jo 20% se zyada ho.
- Keyword pakdo: **"multi-step"** → ye complexity itni hai ki ek single query mein karna mushkil/unreadable ho jaata — isliye do CTEs banayenge, ek department totals ke liye, ek company total ke liye, phir dono ko combine karenge.

**Solution Query:**
```sql
WITH dept_totals AS (
    SELECT department_id, SUM(salary) AS dept_total
    FROM Employees
    GROUP BY department_id
),
company_total AS (
    SELECT SUM(salary) AS total_salary FROM Employees
)
SELECT dt.department_id, dt.dept_total,
       ROUND(dt.dept_total * 100.0 / ct.total_salary, 2) AS pct_contribution
FROM dept_totals dt
CROSS JOIN company_total ct
WHERE dt.dept_total > 0.20 * ct.total_salary;
```

**Line-by-Line:**
- `dept_totals AS (...)` — Pehla CTE: har department ka total salary (SUM), GROUP BY department_id.
- `company_total AS (...)` — Doosra CTE: poori company ka total salary — bina kisi GROUP BY ke, ek single-row result.
- `FROM dept_totals dt CROSS JOIN company_total ct` — Kyunki `company_total` sirf **ek row** deta hai, `CROSS JOIN` se ye ek row har `dept_totals` row ke saath "duplicate" ho jaati hai — effectively har department row ko company_total ka number "attach" ho jaata hai comparison ke liye.
- `WHERE dt.dept_total > 0.20 * ct.total_salary` — Filter: sirf wo departments jinka total, company total ka 20% se zyada ho.

**Naya Concept — `CROSS JOIN` ek Single-Row CTE ke saath (Scalar Broadcasting Pattern):**
"Jab humein ek **single calculated value** (jaise company total) ko **har row ke saath** compare/use karna ho, to `CROSS JOIN` ek elegant tarika hai — kyunki single-row table ke saath cross join karne se koi row-multiplication nahi hoti (Q32 wala fan-out issue yaha nahi aata, kyunki doosri table mein sirf 1 hi row hai), bas wo ek value **har row ke saath 'broadcast'** ho jaata hai, jaise Excel mein ek fixed cell ko formula mein reference karte ho."

**Expected Output (is sample data pe):** Company total = 527000. 20% threshold = 105400. dept10(177000, 33.59%)✓, dept20(152000, 28.84%)✓, dept30(150000, 28.46%)✓, NULL-dept(48000, 9.11%) — exclude. **3 rows: Sales, Engineering, HR.**

**Follow-up Questions:**
- `CROSS JOIN` yaha row-duplication trap (Q32 wala) create kyun nahi karta?
- Isi query ko `CROSS JOIN` ke bina, sirf subquery se kaise likhoge?
- Agar `company_total` CTE khud `dept_totals` CTE ko reference kare (SUM of SUMs), to kya wo bhi possible hai?

**Follow-up Solutions:**
- **CROSS JOIN yaha safe kyun hai:** Q32 ka fan-out problem tab hota hai jab doosri table mein **ek se zyada matching rows** hon per outer-row (one-to-many relationship). Yaha `company_total` CTE mein sirf **ek hi row** hai (poori company ka ek single number) — isliye har `dept_totals` row ko exactly **ek hi** company_total row milegi, koi duplication nahi hogi. Row-count same rahega jitna `dept_totals` mein tha.
- **Bina CROSS JOIN, sirf subquery se:**
  ```sql
  SELECT department_id, SUM(salary) AS dept_total,
         ROUND(SUM(salary) * 100.0 / (SELECT SUM(salary) FROM Employees), 2) AS pct_contribution
  FROM Employees
  GROUP BY department_id
  HAVING SUM(salary) > 0.20 * (SELECT SUM(salary) FROM Employees);
  ```
  Yaha company total ke liye ek non-correlated subquery use kiya (jaisa Q21 mein kiya tha) — ye bhi functionally same result deta hai, bas CTE ki jagah inline subquery hai. Dono approach valid hain — CTE version zyada readable hai jab logic complex ho jaaye.
- **Ek CTE doosre CTE ko reference kare:** Haan, bilkul kar sakte ho:
  ```sql
  WITH dept_totals AS (
      SELECT department_id, SUM(salary) AS dept_total FROM Employees GROUP BY department_id
  ),
  company_total AS (
      SELECT SUM(dept_total) AS total_salary FROM dept_totals
  )
  SELECT dt.department_id, dt.dept_total, ct.total_salary
  FROM dept_totals dt CROSS JOIN company_total ct;
  ```
  Yaha `company_total` ab `dept_totals` (pehla CTE) ke upar hi `SUM` kar raha hai, seedha `Employees` table pe nahi — ye mathematically same result dega (SUM of department-sums = overall sum), aur ye batata hai ki **CTEs sequentially ek doosre ko reference kar sakte hain**, jaise variables chain mein use hote hain.

---

### Q43. CTE se top 3 overall earners nikaalo, phir unki department name join karo

**Interview mein aise poocha jaayega:**
> "Using a CTE, find the top 3 highest-paid employees overall, then display their department names as well."

**Thought Process:**
- Do steps hain: (1) top 3 earners nikaalo (poori company mein se, department se independent), (2) unki department_name lao join karke.
- Keyword pakdo: **"top 3... then display department names"** — sequential steps ka signal hai — CTE pehla step karega, phir outer query join karke second step karegi.

**Solution Query:**
```sql
WITH top_earners AS (
    SELECT employee_id, employee_name, salary, department_id
    FROM Employees
    ORDER BY salary DESC
    LIMIT 3
)
SELECT te.employee_name, te.salary, d.department_name
FROM top_earners te
JOIN Departments d ON te.department_id = d.department_id;
```

**Line-by-Line:**
- `top_earners AS (... ORDER BY salary DESC LIMIT 3)` — CTE ke andar bhi `ORDER BY` + `LIMIT` use kar sakte hain — ye top 3 highest-salary employees ko unke `department_id` samet capture kar leta hai.
- `JOIN top_earners te ... JOIN Departments d` — Ab is chhote 3-row result ko Departments se join karke naam laate hain — ye join sirf 3 rows pe chal raha hai (poori Employees table pe nahi), jo thoda efficient bhi hai.

**Naya Concept — CTE ko Pre-Filter ki Tarah Use Karna (Filter-Then-Join Pattern):**
"Ye pattern useful hai jab humein **pehle ek chhota, specific subset** nikalna ho (jaise top-N), aur **uske baad hi** enrichment (jaise department name) chahiye ho. CTE mein filtering pehle karke, phir sirf us chhote result ko join karna — bade table ko pehle hi join karke phir top-N nikalne se **zyada efficient** ho sakta hai, kyunki join sirf 3 rows pe ho raha hai, poori Employees table pe nahi."

**Expected Output (is sample data pe):** Neha Gupta(95000, HR), Amit Verma(90000, Engineering), Ravi Kumar(75000, Sales).

**Follow-up Questions:**
- Agar humne pehle `JOIN Departments` kiya hota, phir `LIMIT 3` lagate, to kya wahi result aata?
- Top 3 mein agar koi employee ka department NULL hota (jaise Arjun agar top-3 mein hota), to `JOIN Departments` ke saath kya hota?
- CTE ke bina, isi kaam ke liye ek single query kaise likhoge?

**Follow-up Solutions:**
- **JOIN pehle, LIMIT baad mein karte to:** Agar `JOIN Departments` pehle karte, poori Employees table (8 rows) Departments se combine hoti (Arjun ki row drop ho jaati kyunki uska dept NULL hai — INNER JOIN), phir us 7-row result pe `ORDER BY salary DESC LIMIT 3` lagate — is specific case mein **result same hi aata** (kyunki jo bhi top-3 the, unka department valid hai, Arjun waise bhi top-3 mein nahi hai) — lekin agar Arjun (NULL dept) top-3 mein hota, to ye do approaches **different results** dete: "CTE pehle LIMIT" wala Arjun ko top-3 mein include karta but phir join usko NULL department ke saath ya poori tarah drop kar deta (INNER JOIN ki wajah se), jabki "JOIN pehle" wala Arjun ko already step 1 mein hi hata deta, aur top-3 mein koi **chautha (4th)** employee upar aa jaata uski jagah. **Ye ek subtle lekin important ordering bug hai** jo interviewer specifically test karta hai.
- **Agar top-3 mein NULL-dept employee hota:** `JOIN Departments d ON te.department_id = d.department_id` ek `INNER JOIN` hai, isliye wo employee (NULL dept ke saath) is result se **poori tarah drop** ho jaata — sirf 2 rows dikhti "top 3" ke naam pe. Agar sabko dikhana ho (department name NULL ke saath bhi), to `LEFT JOIN` use karna padega.
- **CTE ke bina, single query:**
  ```sql
  SELECT e.employee_name, e.salary, d.department_name
  FROM Employees e
  LEFT JOIN Departments d ON e.department_id = d.department_id
  ORDER BY e.salary DESC
  LIMIT 3;
  ```
  Ye bhi same result deta hai (agar `LEFT JOIN` use karein taaki NULL-dept employee bhi dikhe agar wo top-3 mein aaye) — CTE yaha strictly zaroori nahi thi, lekin jaise-jaise query complex hoti jaati (agar multiple filtering steps hote), CTE version zyada readable reh jaata.

---

### Q44. Recursive CTE: employee → manager → manager ka manager, poori reporting hierarchy banao

**Interview mein aise poocha jaayega:**
> "Using a recursive CTE, build the complete reporting hierarchy showing each employee's level in the organization."

**Thought Process:**
- Output: har employee ka naam, aur uska **hierarchy level** (1 = top, 2 = unke direct reports, etc.).
- Keyword pakdo: **"complete reporting hierarchy"**, "level" → ye ek **unknown-depth tree traversal** hai — humein pata nahi hierarchy kitni levels deep hai, isliye normal JOIN se ye nahi ho sakta (fixed number of self-joins chahiye hote) — **Recursive CTE** hi is kaam ke liye designed hai.
- Recursive CTE ke 2 parts hote hain: **anchor member** (base case — top-level employees) aur **recursive member** (jo baar-baar apne aap ko reference karke agli level dhundta hai).

**Solution Query:**
```sql
WITH RECURSIVE emp_hierarchy AS (
    -- Anchor member: top-level employees (jinka manager NULL hai)
    SELECT employee_id, employee_name, manager_id, 1 AS level
    FROM Employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive member: har baar agli generation dhundo
    SELECT e.employee_id, e.employee_name, e.manager_id, eh.level + 1
    FROM Employees e
    JOIN emp_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT * FROM emp_hierarchy
ORDER BY level, employee_name;
```

**Line-by-Line:**
- `WITH RECURSIVE` — MySQL mein recursive CTE define karne ke liye ye explicit keyword likhna zaroori hai (sirf `WITH` likhne se recursion allowed nahi hoga).
- **Anchor member** (`WHERE manager_id IS NULL`) — Ye recursion ka **starting point / base case** hai. Yaha Ravi, Amit, **aur Arjun bhi** shamil honge — kyunki Arjun ka bhi `manager_id` NULL hai (uska department NULL hai, lekin manager bhi NULL hai — wo bhi ek "top-level, isolated" employee hai). Sabko `level = 1` milta hai.
- `UNION ALL` — Anchor aur recursive part ko jodta hai; `UNION ALL` (na ki `UNION`) isliye kyunki duplicates yaha expected nahi hain, aur performance behtar hoti hai.
- **Recursive member** (`JOIN emp_hierarchy eh ON e.manager_id = eh.employee_id`) — Ye khud **apne aap** (`emp_hierarchy`) ko reference kar raha hai — har iteration mein, pichli iteration ke result (jo abhi tak "hierarchy mein add ho chuke" employees hain) ko use karke, unke **direct reports** dhundta hai. Ye tab tak repeat hota hai jab tak koi naya match na mile (termination condition automatically achieve hota hai jab koi employee bacha na ho jiska manager already list mein na ho).
- `eh.level + 1` — Har recursive step ke saath level ek badhta jaata hai, taaki pata chale kaun kis depth pe hai.

**Naya Concept — Recursive CTE:**
"Recursive CTE tab use hota hai jab data ka structure **tree ya graph jaisa** ho (jaise organization hierarchy, category-subcategory tree, ya bill-of-materials), aur depth **fixed/known na ho**. Normal self-join sirf ek fixed number of levels handle kar sakta hai (jitni baar tum manually join likho), lekin recursive CTE **khud-ba-khud** jitni bhi levels ho utni traverse kar leta hai, bina hardcoded joins ke."

**Expected Output (is sample data pe):**
| level | employees |
|---|---|
| 1 | Ravi Kumar, Amit Verma, Arjun Singh (sab manager_id NULL) |
| 2 | Priya Sharma, Kunal Joshi, Ananya Iyer (Ravi ke reports), Sneha Rao (Amit ki report) |
| 3 | Neha Gupta (Kunal ki report) |

**Follow-up Questions:**
- Arjun Singh level 1 mein kyun aa gaya, jabki wo "senior" nahi lagta business-sense mein?
- Agar organization mein cyclical reporting ho jaaye (galti se A, B ka manager ho aur B, A ka), to recursive CTE ka kya hoga?
- MySQL se pehle (jaise 8.0 se pehle ke versions), recursive hierarchies kaise handle ki jaati thi?

**Follow-up Solutions:**
- **Arjun level 1 mein kyun:** Kyunki recursive CTE ka anchor member sirf ye check karta hai `manager_id IS NULL` — aur Arjun ka `manager_id` bhi NULL hai (bhale hi uska department bhi NULL ho). Data ke hisaab se ye technically correct hai — Arjun ke paas koi manager record nahi hai, isliye query use "top-level" maanti hai. Real business mein ho sakta hai Arjun actually kisi ka report ho lekin data-entry mein manager_id set hi na hua ho — ye ek acha discussion point hai ki **query logic hamesha data-quality assumptions pe depend karta hai**.
- **Cyclical reporting (A→B→A):** Recursive CTE **infinite loop** mein chala jaayega, kyunki har iteration mein A aur B ek doosre ko baar-baar "discover" karte rahenge, koi terminating condition nahi milegi. MySQL 8 mein by default ek safety limit hota hai (`cte_max_recursion_depth`, default 1000) jo query ko ek point ke baad **error de kar rok deta hai** ("Recursion limit exceeded") — taaki server hang na ho. Real-world data mein cyclical management hierarchy ek **data integrity bug** hai jo prevent kiya jaana chahiye (jaise CHECK constraint ya application-level validation se).
- **MySQL 8 se pehle:** Recursive CTE syntax (`WITH RECURSIVE`) MySQL 8.0 mein hi introduce hua tha (pehle MySQL versions mein ye feature nahi tha). Uske pehle hierarchies handle karne ke common tarike the: (1) **application-level recursion** (multiple queries application code se chalana, level-by-level), (2) **stored procedures** with loops (jaisa humari PL/SQL guide mein cover kiya tha), (3) special **adjacency-list-to-nested-set conversion** techniques (data ko pre-process karke ek "left/right" numbering scheme mein store karna jo bina recursion ke hierarchy query karne deta), ya (4) simple fixed-depth self-joins agar hierarchy ki depth known aur chhoti ho.

---

### Q45. Recursive CTE: ek manager ke total direct + indirect reports count karo

**Interview mein aise poocha jaayega:**
> "Using a recursive CTE, find the total number of direct and indirect reports under Ravi Kumar (employee_id 101)."

**Thought Process:**
- Ye Q44 ka hi **extension/application** hai — ab humein poori hierarchy nahi, balki ek **specific manager ke neeche ki poori subtree** ka size chahiye.
- Keyword pakdo: **"direct and indirect reports"** → ye phir se ek unknown-depth traversal hai, lekin is baar **ek specific starting point** (employee 101) se **neeche ki taraf**.

**Solution Query:**
```sql
WITH RECURSIVE reports AS (
    -- Anchor: seedhe (direct) reports of employee 101
    SELECT employee_id, employee_name
    FROM Employees
    WHERE manager_id = 101

    UNION ALL

    -- Recursive: un reports ke bhi reports (indirect)
    SELECT e.employee_id, e.employee_name
    FROM Employees e
    JOIN reports r ON e.manager_id = r.employee_id
)
SELECT COUNT(*) AS total_reports
FROM reports;
```

**Line-by-Line:**
- **Anchor member** (`WHERE manager_id = 101`) — Ye Q44 se **alag** hai — yaha anchor "top of company" nahi, balki "seedhe Ravi (101) ke reports" hai. Ye **starting point ko customize** karta hai kis employee ke "neeche" ki hierarchy chahiye.
- **Recursive member** — Same logic jaisa Q44 — har naye-mile employee ke apne reports dhundo, tab tak jab tak koi naya na mile.
- `COUNT(*)` (outer) — Poori `reports` CTE (jisme direct + indirect sab shamil hain) ki row-count nikalte hain.

**Naya Concept — Recursive CTE with Custom Anchor (Subtree Traversal):**
"Q44 mein humne **poori company ki hierarchy** banayi thi (root se). Yaha humein sirf **ek specific employee ke neeche ka subtree** chahiye — isliye anchor member ko `manager_id IS NULL` (poori company ka root) ki jagah `manager_id = 101` (specific starting point) banaya. Yehi flexibility recursive CTE ki sabse badi taaqat hai — ek hi pattern, bas anchor badalne se, alag-alag traversal problems solve kar sakta hai."

**Expected Output (is sample data pe):** Ravi(101) ke direct reports: Priya(102), Kunal(105), Ananya(108). Kunal ka indirect report: Neha(106). **Total = 4.**

**Follow-up Questions:**
- Agar humein sirf **direct** reports ki count chahiye ho (indirect nahi), to query kaise simplify hogi?
- Isi query se, poori company ke **har manager** ke liye (na sirf Ravi ke liye) total-reports count kaise nikaaloge ek saath?
- Employee_id ko hardcode (101) karne ke bajaye, isko ek parameter jaisa flexible kaise banaoge?

**Follow-up Solutions:**
- **Sirf direct reports (indirect nahi):** Recursive CTE ki zaroorat hi nahi padegi, simple query kaafi hai:
  ```sql
  SELECT COUNT(*) AS direct_reports FROM Employees WHERE manager_id = 101;
  ```
  Recursive CTE **sirf tab zaroori hai** jab "indirect" (multi-level) reports bhi chahiye hon — agar sirf ek level chahiye, plain `WHERE` filter kaafi hai.
- **Har manager ke liye ek saath (poori company ke liye):** Ye zyada complex hai kyunki har manager ka apna subtree hai. Ek approach: recursive CTE mein "starting manager" ko bhi track karo:
  ```sql
  WITH RECURSIVE org_chain AS (
      SELECT employee_id, manager_id AS top_manager
      FROM Employees
      WHERE manager_id IS NOT NULL

      UNION ALL

      SELECT oc.employee_id, e.manager_id
      FROM org_chain oc
      JOIN Employees e ON oc.top_manager = e.employee_id
      WHERE e.manager_id IS NOT NULL
  )
  SELECT top_manager AS manager_id, COUNT(*) AS total_reports
  FROM (
      SELECT DISTINCT employee_id, top_manager FROM org_chain
      UNION
      SELECT employee_id, manager_id AS top_manager FROM Employees WHERE manager_id IS NOT NULL
  ) all_relations
  GROUP BY top_manager;
  ```
  *(Ye query thodi advanced hai — interview mein agar itna deep pucha jaaye, to concept explain karna zyada important hai implementation se: "har employee se upar ki taraf chain follow karo jab tak root na mile, aur beech ke har manager ke against ek entry bana lo.")*
- **Hardcoded 101 ko parameterize karna:** Pure SQL query mein directly parameter nahi hota, lekin: (1) Application code se (jaise Python/Java) query string mein value inject karo (bind parameter ki tarah, SQL injection se bachte hue — jaisa PL/SQL guide mein `EXECUTE IMMEDIATE ... USING` dekha tha), ya (2) Ek **Stored Procedure** banao jisme `manager_id` ek `IN` parameter ho:
  ```sql
  CREATE PROCEDURE get_total_reports(IN p_manager_id INT)
  BEGIN
      WITH RECURSIVE reports AS (
          SELECT employee_id FROM Employees WHERE manager_id = p_manager_id
          UNION ALL
          SELECT e.employee_id FROM Employees e JOIN reports r ON e.manager_id = r.employee_id
      )
      SELECT COUNT(*) FROM reports;
  END;
  ```
  Ab `CALL get_total_reports(101);` ya `CALL get_total_reports(103);` — dono ke liye reusable ho gaya.

---

## CATEGORY 7 — WINDOW FUNCTIONS

### Q46. Assign row numbers to employees ordered by salary descending

**Interview mein aise poocha jaayega:**
> "Assign a unique row number to each employee based on their salary, in descending order."

**Thought Process:**
- Output: har employee row ke saath, ek naya column jo unki salary-based position bataye.
- Keyword pakdo: **"unique row number"** → `ROW_NUMBER()` window function, `OVER (ORDER BY salary DESC)` ke saath.
- Important: humein individual employee rows chahiye (na ki grouped/collapsed) — isliye `GROUP BY` nahi, window function chahiye jo rows collapse na kare.

**Solution Query:**
```sql
SELECT employee_name, salary,
       ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num
FROM Employees;
```

**Line-by-Line:**
- `ROW_NUMBER()` — Har row ko ek **unique, sequential number** deta hai (1, 2, 3, ...) — chahe salary mein ties ho ya na ho, har row ko alag number milega.
- `OVER (ORDER BY salary DESC)` — `OVER` clause window function ka "window" (context) define karta hai — yaha bata rahe hain ki numbering **salary descending order** ke hisaab se honi chahiye.

**Naya Concept — Window Functions (Basics):**
"Window functions `GROUP BY` se fundamentally alag hain — `GROUP BY` rows ko **collapse** kar deta hai (multiple rows → ek row per group), jabki window function **har individual row ko as-is rakhta hai**, bas uske saath ek calculated value (jaise rank, running total) add kar deta hai. `OVER()` clause hi batata hai ki calculation kis 'window' (context) mein honi chahiye — poori table mein, ya kisi partition ke andar."

**Expected Output (is sample data pe):** Neha(95000)=1, Amit(90000)=2, Ravi(75000)=3, phir Priya/Sneha(62000) mein se koi ek=4, doosra=5 (tie ka order arbitrary hai bina secondary `ORDER BY` ke), Kunal(55000)=6, Arjun(48000)=7, Ananya(40000)=8.

**Follow-up Questions:**
- Priya aur Sneha dono ki salary 62000 hai — `ROW_NUMBER()` unhe kaunsa number dega, aur kaun sa pehle aayega?
- `ROW_NUMBER()` aur simple `LIMIT`/`OFFSET` mein farak kya hai — dono se "top N" nikal sakte hain na?
- Agar hum `PARTITION BY department_id` bhi add kar dein, to numbering kaise badlegi?

**Follow-up Solutions:**
- **Priya/Sneha tie ka behaviour:** `ROW_NUMBER()` **hamesha** unique numbers deta hai (chahe values tied hon) — lekin **kaun pehle aayega ye undefined hai** jab tak `ORDER BY` mein koi tie-breaker na ho. Deterministic result ke liye:
  ```sql
  SELECT employee_name, salary,
         ROW_NUMBER() OVER (ORDER BY salary DESC, employee_id ASC) AS row_num
  FROM Employees;
  ```
  Ab `employee_id` tie-breaker ban jaata hai — Priya(102) hamesha Sneha(104) se pehle aayegi (chhota employee_id pehle).
- **`ROW_NUMBER()` vs `LIMIT`/`OFFSET`:** Dono "top N" ke liye use ho sakte hain, lekin `ROW_NUMBER()` zyada **flexible** hai — jaise agar tumhe "top 3 **per department**" chahiye (Q48), to `LIMIT` akela ye nahi kar sakta (wo poori table pe kaam karta hai, per-group nahi), jabki `ROW_NUMBER() OVER (PARTITION BY department_id ...)` ye asaani se kar sakta hai. `LIMIT`/`OFFSET` simple, single-level top-N ke liye theek hai; `ROW_NUMBER()` grouped/partitioned top-N ke liye zaroori hai.
- **`PARTITION BY department_id` add karne se:** Ab numbering **har department ke andar restart** ho jaayegi:
  ```sql
  SELECT employee_name, department_id, salary,
         ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS row_num
  FROM Employees;
  ```
  dept10 mein Ravi=1, Priya=2, Ananya=3 (independently 1 se shuru); dept20 mein Amit=1, Sneha=2; dept30 mein Neha=1, Kunal=2 — har department apni khud ki "1 se counting" shuru karta hai, jaisa `GROUP BY` groups banata hai, bas rows collapse nahi hoti.

---

### Q47. Rank employees within each department by salary (`RANK` vs `DENSE_RANK`)

**Interview mein aise poocha jaayega:**
> "Rank employees within each department based on salary, and explain the difference between RANK and DENSE_RANK."

**Thought Process:**
- Keyword pakdo: **"within each department"** → `PARTITION BY department_id` chahiye. **"RANK vs DENSE_RANK"** → dono use karke dikhana hai, farak explain karna hai.
- **Important note:** Is dataset mein **kisi bhi single department ke andar** koi salary-tie nahi hai (dept10: 75000/62000/40000, dept20: 90000/62000, dept30: 95000/55000— sab distinct within their own department) — isliye department-level partition mein `RANK` aur `DENSE_RANK` ka output **same** aayega. Farak dikhane ke liye, company-wide (bina partition ke) ranking bhi saath mein dikhaenge, jaha Priya/Sneha (dono 62000) ka tie hai.

**Solution Query (department-partitioned — jaisa poocha gaya):**
```sql
SELECT employee_name, department_id, salary,
       RANK()       OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk,
       DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS dense_rnk
FROM Employees;
```

**Line-by-Line:**
- `RANK() OVER (PARTITION BY department_id ORDER BY salary DESC)` — Har department ke andar salary descending order mein rank deta hai; agar ties hon, same rank milta hai lekin **agla rank skip** ho jaata hai.
- `DENSE_RANK()` — Same logic, lekin ties ke baad **rank skip nahi hota**.

**Naya Concept — `RANK()` vs `DENSE_RANK()` (Skip vs No-Skip on Ties):**
"Is specific department-wise dataset mein koi tie nahi hai, isliye `RANK` aur `DENSE_RANK` ka output identical aayega har department mein — lekin **agar tie hoti**, farak clearly dikhta. Chalo company-wide (bina partition) example se dekhte hain, jaha Priya aur Sneha dono ki salary 62000 hai:"

```sql
SELECT employee_name, salary,
       RANK()       OVER (ORDER BY salary DESC) AS rnk,
       DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rnk
FROM Employees;
```
| employee_name | salary | rnk | dense_rnk |
|---|---|---|---|
| Neha Gupta | 95000 | 1 | 1 |
| Amit Verma | 90000 | 2 | 2 |
| Ravi Kumar | 75000 | 3 | 3 |
| Priya Sharma | 62000 | 4 | 4 |
| Sneha Rao | 62000 | 4 | 4 |
| Kunal Joshi | 55000 | **6** | **5** |
| Arjun Singh | 48000 | **7** | **6** |
| Ananya Iyer | 40000 | **8** | **7** |

"Yaha clearly dikhta hai — dono (Priya, Sneha) ko `rnk=4` aur `dense_rnk=4` milta hai (tie). Lekin agli row (Kunal) ke liye `RANK` **6** deta hai (5 ko skip kar diya, kyunki 2 employees already rank 4 le chuke the), jabki `DENSE_RANK` **5** deta hai (koi skip nahi, bस agla sequential number)."

**Expected Output (department-partitioned, original question ka):** Har department mein RANK = DENSE_RANK (koi tie nahi hai within department).

**Follow-up Questions:**
- `RANK` aur `DENSE_RANK` mein se konsa use karoge agar business requirement ho "top 3 unique salary levels dikhao, chahe kitne bhi employees share karein"?
- Is dataset mein department-partitioned ranking mein RANK aur DENSE_RANK same kyun hain?
- `ROW_NUMBER()`, `RANK()`, aur `DENSE_RANK()` — teeno ek saath ek hi query mein use karke farak dikhao.

**Follow-up Solutions:**
- **Top 3 unique salary levels:** `DENSE_RANK()` use karoge, kyunki wo "levels" ko sahi tarike se represent karta hai — `WHERE dense_rnk <= 3` sabhi employees dega jo top-3 **distinct salary values** mein aate hain, chahe kitne bhi log ek salary share karein. `RANK` use karte to, agar top-2 mein tie ho, to teesra "level" (jo actually 4th position hai numerically) miss ho sakta tha ya extra rows aa sakti thi confusion ke saath.
- **Department-partitioned mein same kyun hain:** Kyunki jaisa upar note kiya, **is specific sample data mein kisi bhi single department ke andar koi do employees ki salary equal nahi hai** — dept10(75000,62000,40000), dept20(90000,62000), dept30(95000,55000) — sab unique within their own group. `RANK` aur `DENSE_RANK` sirf **tie hone par** alag behave karte hain; jab ties hi na ho, dono hamesha same result denge (1,2,3,4... sequential, no skips needed).
- **Teeno ek saath (company-wide, tie ke saath):**
  ```sql
  SELECT employee_name, salary,
         ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num,
         RANK()       OVER (ORDER BY salary DESC) AS rnk,
         DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rnk
  FROM Employees;
  ```
  Priya aur Sneha (62000 tie) ke liye: `ROW_NUMBER` unhe **alag-alag** numbers dega (4, 5 — chahe koi bhi pehle aaye, arbitrary without tie-breaker), `RANK` **dono ko 4** dega phir agle ko 6 (skip), `DENSE_RANK` **dono ko 4** dega phir agle ko 5 (no skip). Teeno ka ye ek-saath comparison interview mein bahut common poocha jaata hai.

---

### Q48. Top 3 salaries in each department

**Interview mein aise poocha jaayega:**
> "Find the top 3 highest-paid employees in each department."

**Thought Process:**
- Keyword pakdo: **"top 3 ... in each department"** → partition-wise top-N — `DENSE_RANK() OVER (PARTITION BY ...)` phir `WHERE rank <= 3`, ya `ROW_NUMBER()`.
- **Important choice: `DENSE_RANK` vs `ROW_NUMBER` yaha** — agar department mein 3rd aur 4th position pe tie ho, `ROW_NUMBER` sirf ek ko rakhega (arbitrary), `DENSE_RANK` dono ko rakhega (kyunki dono "same level 3" hain). Business requirement pe depend karta hai kaunsa sahi hai.

**Solution Query:**
```sql
SELECT employee_name, department_id, salary
FROM (
    SELECT employee_name, department_id, salary,
           DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
    FROM Employees
) ranked
WHERE rnk <= 3;
```

**Line-by-Line:**
- Inner query: har department ke andar salary-wise `DENSE_RANK` calculate karti hai.
- **Important syntax note:** Window function ka result seedha `WHERE` mein use **nahi** kar sakte (`WHERE rnk <= 3` seedha bahar nahi likh sakte, error aayega) — isliye inner query ko ek **subquery/derived table** mein wrap karke, outer `WHERE` mein filter karte hain. Ye ek bahut common syntax gotcha hai.
- `WHERE rnk <= 3` (outer) — Ab is derived table pe normal `WHERE` filter laga sakte hain, kyunki `rnk` ab ek **regular column** ban chuka hai, window function nahi rahi.

**Naya Concept — Window Functions `WHERE` mein Directly Use Nahi Ho Sakte:**
"Sir, `WHERE` clause `GROUP BY`/`SELECT` se **pehle** execute hota hai (logical processing order), jabki window functions `SELECT` clause ke saath, usi stage pe evaluate hoti hain. Isliye jab tak `SELECT` complete na ho jaaye, window function ka result exist hi nahi karta — `WHERE` usko directly reference nahi kar sakta. Solution: window function ko ek subquery/CTE mein compute karo, phir uske result ko outer `WHERE` (ya CTE ke case mein bhi ek outer SELECT) mein filter karo."

**Expected Output (is sample data pe):** Is dataset mein har department mein already ≤3 employees hain (dept10=3, dept20=2, dept30=2, NULL-dept=1), isliye **saare employees hi qualify** karenge `rnk <= 3` condition mein — ye pattern demonstrate karta hai, lekin bade dataset mein (jaha departments mein 5-10+ employees hote) ye genuinely filter karta.

**Follow-up Questions:**
- `WHERE rnk <= 3` ko seedha bina subquery ke likhne ki koshish karo — kaunsa error aata hai?
- Agar `ROW_NUMBER()` use karte is jagah `DENSE_RANK()` ke bajaye, to kya farak padta agar 3rd/4th position pe tie hoti?
- CTE use karke isi query ko subquery ke bajaye kaise rewrite karoge?

**Follow-up Solutions:**
- **Direct `WHERE rnk <= 3` likhne ka error:** MySQL mein error aayega jaisa: `Invalid use of window function`. Ye isliye kyunki `WHERE` clause logical processing order mein window functions se **pehle** execute hoti hai — us stage pe `rnk` column exist hi nahi karta abhi. Yehi wajah hai `HAVING` mein bhi window function directly use nahi hoti — sirf ek layer bahar (subquery/CTE) mein wrap karke hi filter possible hai.
- **`ROW_NUMBER()` use karte agar tie hoti:** Maan lo dept10 mein 3rd aur 4th position pe salary tie hoti (jo abhi nahi hai) — `ROW_NUMBER()` unhe **alag-alag numbers** (3 aur 4) deta, aur `WHERE rn <= 3` sirf **ek** ko rakhta (arbitrary, jo bhi internally pehle process hua), doosre ko **chhod deta** — jabki business shayad chahta ki dono "3rd position" wale dikhein kyunki unki salary equal hai. `DENSE_RANK` is case mein dono ko rakhta (dono ko rank 3 milta), jo zyada "fair" representation hai jab ties involved hon. Interview mein bolo: "Agar exact N rows chahiye ties ke bawajood, ROW_NUMBER use karta hoon; agar 'top N salary-levels' chahiye chahe kitne bhi employees share karein, DENSE_RANK use karta hoon."
- **CTE se rewrite:**
  ```sql
  WITH ranked_employees AS (
      SELECT employee_name, department_id, salary,
             DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
      FROM Employees
  )
  SELECT employee_name, department_id, salary
  FROM ranked_employees
  WHERE rnk <= 3;
  ```
  Functionally bilkul same — bas subquery ki jagah CTE use kiya, readability ke liye (Q41 wala concept).

---

### Q49. Second-highest salary using `DENSE_RANK()`

**Interview mein aise poocha jaayega:**
> "Find the second-highest salary using a window function."

**Thought Process:**
- Ye Q35 ka hi (nested-subquery approach) window-function version hai.
- Keyword pakdo: **"second-highest"**, "using a window function" → `DENSE_RANK()` (na ki `RANK`, kyunki agar top-2 mein tie ho, RANK "second highest" ko galat represent kar sakta — Q47 wala concept yaha bhi apply hota hai) `WHERE rnk = 2`.

**Solution Query:**
```sql
SELECT employee_name, salary
FROM (
    SELECT employee_name, salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM Employees
) ranked
WHERE rnk = 2;
```

**Line-by-Line:**
- Inner query: poori company (bina partition) ke salaries ko descending `DENSE_RANK` deta hai.
- `WHERE rnk = 2` — Sirf wo rows jinka dense-rank exactly 2 ho — matlab "doosri sabse badi distinct salary value" wale employees.

**Naya Concept (recap) — `DENSE_RANK` Nth-Highest ke liye kyun `RANK` se safer hai:**
"Agar sabse zyada salary (rank 1) pe **do employees tie** hote (jo abhi nahi hai, lekin hypothetically), to `RANK()` unhe dono rank-1 dega, aur agle unique salary ko seedha **rank 3** de dega (2 ko skip karke) — matlab `WHERE rnk = 2` **koi row nahi** dega, jo galat hai kyunki "second highest distinct salary" to exist karti hai! `DENSE_RANK` is problem se immune hai, kyunki wo kabhi rank skip nahi karta — `WHERE rnk = 2` hamesha sahi 'doosri distinct salary' dega."

**Expected Output (is sample data pe):** 90000 (Amit Verma) — kyunki 95000 (Neha) rank 1 hai, 90000 rank 2.

**Follow-up Questions:**
- Agar `RANK()` use karte is jagah `DENSE_RANK()` ke, to kya galat ho sakta tha (hypothetically, agar top salary pe tie hoti)?
- Employee ka naam bhi nahi, sirf salary **value** chahiye ho, to query kaise simplify hogi?
- Isi pattern se 4th-highest salary kaise nikaaloge?

**Follow-up Solutions:**
- **`RANK()` ka hypothetical problem:** Jaisa upar Naya Concept mein explain kiya — agar rank-1 pe tie hoti (do employees 95000 kamate), `RANK()` dono ko rank 1 dega, aur teesre-highest-salary-wale employee ko seedha **rank 3** milega (rank 2 completely skip ho jaata, kyunki 2 log pehle hi rank 1 le chuke). Isliye `WHERE rnk = 2` **empty result** dega — jo misleading hai, kyunki business-sense mein "second highest salary" ka concept exist karta hai (bas ties handle karne ka tarika match nahi kar raha). `DENSE_RANK` ye guarantee karta hai ki `rnk = 2` **hamesha** doosri unique/distinct salary value ko refer karega, chahe rank-1 pe kitni bhi ties ho.
- **Sirf salary value chahiye (naam nahi):**
  ```sql
  SELECT DISTINCT salary
  FROM (
      SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
      FROM Employees
  ) ranked
  WHERE rnk = 2;
  ```
  `DISTINCT` yaha technically zaroori nahi hai kyunki `DENSE_RANK = 2` matlab hi hai ki salary **same** honi chahiye un saari rows mein — lekin agar multiple employees ye salary share karte hain, `DISTINCT` na likhne se same salary value multiple baar print hogi (ek per matching employee) — agar sirf **ek value** chahiye result mein, `DISTINCT` ya `LIMIT 1` add karo.
- **4th-highest salary:** Bas `WHERE rnk = 2` ko `WHERE rnk = 4` kar do — pattern bilkul same rehta hai, sirf N badalna hai. Yehi is approach ka sabse bada advantage hai Q36 wale nested-`MAX`-subquery approach ke muqable — N badalna sirf ek number change karna hai, poori query-structure nahi.

---

### Q50. Running total of revenue ordered by date

**Interview mein aise poocha jaayega:**
> "Calculate the running total (cumulative sum) of order revenue, ordered by date."

**Thought Process:**
- Output: har order ke saath, us order tak ka **cumulative sum** — matlab "is order tak total kitna revenue aa chuka hai."
- Keyword pakdo: **"running total"**, "cumulative" → `SUM() OVER (ORDER BY ...)` — bina `PARTITION BY` ke, matlab poori table ek hi "window" mein.

**Solution Query:**
```sql
SELECT order_id, order_date, amount,
       SUM(amount) OVER (ORDER BY order_date) AS running_total
FROM Orders
ORDER BY order_date;
```

**Line-by-Line:**
- `SUM(amount) OVER (ORDER BY order_date)` — Ye ek **cumulative sum** hai — jab `OVER()` ke andar `PARTITION BY` na ho lekin `ORDER BY` ho, to `SUM` by default **"running"** mode mein chalti hai: har row ke liye, us row **tak** (date order mein) ke saare amounts jod deti hai — poore table ka total nahi, sirf "ab tak ka" total.
- Final `ORDER BY order_date` — Ye display order ke liye hai (taaki result readable ho, date-wise sorted dikhe) — window function ke andar wala `ORDER BY` calculation ke liye tha, ye wala sirf presentation ke liye hai.

**Naya Concept — Running Total Pattern (`SUM() OVER (ORDER BY ...)`):**
"Jab `SUM() OVER()` ke saath sirf `ORDER BY` ho (`PARTITION BY` na ho), to ye default behaviour se ek 'running/cumulative' window banati hai — technically iska poora naam hai `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` (jo default hai jab ORDER BY diya ho) — matlab 'sabse shuru se, current row tak'. Agar `PARTITION BY` bhi add kar do, to ye running total **har partition ke andar alag se restart** hoga (Q51 mein dekhenge)."

**Expected Output (is sample data pe, date-order mein):** O1(Jan10,1200)→1200, O3(Jan20,500)→1700, O2(Feb15,800)→2500, O5(Feb25,700)→3200, O4(Mar5,300)→3500.

**Follow-up Questions:**
- Agar do orders ki **date same** ho, to running total kaise calculate hoga unke beech mein?
- `SUM() OVER (ORDER BY ...)` aur `SUM() OVER (PARTITION BY ... ORDER BY ...)` mein farak kya hai?
- Ye "running total" ko explicitly `ROWS BETWEEN` syntax se kaise likh sakte hain (bina default behaviour pe depend kiye)?

**Follow-up Solutions:**
- **Same date wale orders:** Agar do orders ki `order_date` bilkul same ho, to default `RANGE` mode mein **dono ko ek saath "same group"** treat kiya jaata hai — matlab dono ko **same running_total value** milegi (jo un dono ko include karke calculate hui ho), na ki ek ke baad doosre ko incrementally. Ye ek subtle behaviour hai jo `RANGE` vs `ROWS` framing ka farak hai (neeche explain kiya hai).
- **`PARTITION BY` ke saath farak:** `SUM() OVER (ORDER BY order_date)` (bina partition) **poori table** ko ek single running sequence maanta hai. `SUM() OVER (PARTITION BY customer_id ORDER BY order_date)` (Q51 mein) **har customer ke liye alag se, independently** running total restart karega — jaise `GROUP BY` groups banata hai, bas rows collapse nahi hoti.
- **Explicit `ROWS BETWEEN` syntax:**
  ```sql
  SELECT order_id, order_date, amount,
         SUM(amount) OVER (
             ORDER BY order_date
             ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
         ) AS running_total
  FROM Orders;
  ```
  `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` explicitly batata hai: "sabse pehli row se lekar current row tak, sab include karo." Interview mein `ROWS` vs default `RANGE` ka farak bolna advanced knowledge dikhata hai: `ROWS` **physical row-position** based hai (chahe values same hon ya na hon, har row alag treat hoti hai), jabki `RANGE` **value-based peer groups** banata hai (same `ORDER BY` value wali saari rows ko ek "peer group" maanta hai aur unhe same cumulative value deta hai) — isliye agar tie-values ka precise, deterministic row-by-row running total chahiye (na ki tied rows ko group treat karna), `ROWS` explicitly likhna safer hai.

---

### Q51. Running total of revenue per customer (`PARTITION BY`)

**Interview mein aise poocha jaayega:**
> "Calculate the running total of order revenue for each customer separately."

**Thought Process:**
- Ye Q50 ka hi extension hai, bas "for each customer separately" — matlab running total **har customer ke liye alag se restart** honi chahiye.
- Keyword pakdo: **"for each customer separately"** → `PARTITION BY customer_id` add karna hai `SUM() OVER()` mein.

**Solution Query:**
```sql
SELECT customer_id, order_id, order_date, amount,
       SUM(amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS running_total
FROM Orders
ORDER BY customer_id, order_date;
```

**Line-by-Line:**
- `PARTITION BY customer_id` — Data ko customer-wise "buckets" mein todta hai — running total calculation **har bucket ke andar independently** hoti hai.
- `ORDER BY order_date` (window ke andar) — Har customer ke bucket ke andar, date-wise order tय karta hai ki cumulative sum kis sequence mein badhega.

**Naya Concept — `PARTITION BY` + `ORDER BY` Combine (Per-Group Running Total):**
"Ye pattern (`PARTITION BY X ORDER BY Y`) **bahut common** hai real-world analytics mein — jaise 'per-customer running total', 'per-region cumulative sales', 'per-employee cumulative attendance'. `PARTITION BY` batata hai 'kis group ke andar reset karna hai', `ORDER BY` batata hai 'kis sequence mein accumulate karna hai'."

**Expected Output (is sample data pe):** C1: O1(Jan10,1200)→1200, O2(Feb15,800)→2000. C2: O3(Jan20,500)→500, O5(Feb25,700)→1200. C3: O4(Mar5,300)→300.

**Follow-up Questions:**
- Agar `PARTITION BY` hata dein is query se, to result Q50 jaisa hi ban jaayega kya?
- Har customer ka **final (poora) total** bhi chahiye ho har row ke saath (running total ke alawa), to kaise nikaaloge?
- Ye pattern `GROUP BY customer_id, SUM(amount)` se kaise fundamentally alag hai?

**Follow-up Solutions:**
- **`PARTITION BY` hataने par:** Haan, bilkul — bina `PARTITION BY` ke, ye query Q50 jaisi ban jaati hai (poori table ek single running sequence, customer-boundaries ka koi matlab nahi rehta). `PARTITION BY` hi hai jo "per-group restart" ka behaviour deta hai.
- **Final total bhi saath mein (running + grand total dono):**
  ```sql
  SELECT customer_id, order_id, order_date, amount,
         SUM(amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS running_total,
         SUM(amount) OVER (PARTITION BY customer_id) AS customer_final_total
  FROM Orders
  ORDER BY customer_id, order_date;
  ```
  Notice karo doosri `SUM() OVER (PARTITION BY customer_id)` mein **koi `ORDER BY` nahi hai** — jab `ORDER BY` na ho window ke andar, to `SUM` by default **poore partition ka total** deta hai (running nahi), aur ye value **har row mein same** rehti hai us customer ke liye (chahe running_total badalta rahe). Ye ek acha demonstration hai ki `ORDER BY` ki presence/absence hi decide karti hai "running" vs "total" behaviour.
- **`GROUP BY` se fundamental farak:** `GROUP BY customer_id, SUM(amount)` **rows ko collapse** kar deta — result mein sirf **ek row per customer** aati (uska grand total), individual orders ki details **gayab** ho jaati. Window function (`SUM() OVER (PARTITION BY ...)`) **har individual order-row ko bhi zinda rakhta hai**, saath mein customer-level calculation bhi attach kar deta hai — dono cheezein (row-level detail + group-level aggregate) ek saath dikh jaati hain, jo `GROUP BY` se possible nahi hai bina extra join/subquery ke.

---

### Q52. Har employee ki salary compare karo pichle employee (hire_date order mein) se — `LAG`

**Interview mein aise poocha jaayega:**
> "For each employee, show the difference between their salary and the salary of the previously-hired employee."

**Thought Process:**
- Output: har employee ke saath, "pichle" (hire_date order mein) employee ki salary, aur difference.
- Keyword pakdo: **"previously-hired employee"** → `LAG()` window function — ye current row se **pichli row** (sequence mein) ki value fetch karta hai, bina self-join kiye.

**Solution Query:**
```sql
SELECT employee_name, hire_date, salary,
       LAG(salary) OVER (ORDER BY hire_date) AS prev_hired_salary,
       salary - LAG(salary) OVER (ORDER BY hire_date) AS salary_diff
FROM Employees
ORDER BY hire_date;
```

**Line-by-Line:**
- `LAG(salary) OVER (ORDER BY hire_date)` — `hire_date` ke ascending order mein, current row se **ek row peeche** ki `salary` value fetch karta hai — matlab "jo employee mujhse pehle hire hua tha, uski salary kya thi."
- Pehli row (sabse pehle hire hua employee) ke liye `LAG` **NULL** dega, kyunki uske "pehle" koi row hi nahi hai.
- `salary - LAG(salary) OVER (...)` — Simple arithmetic difference — current employee ki salary minus unse pehle hire hue employee ki salary.

**Naya Concept — `LAG()` (aur uska partner `LEAD()`):**
"`LAG()` current row se **peeche** (previous) ki value deta hai, `LEAD()` current row se **aage** (next) ki value deta hai — dono ke bina, ye comparison self-join se karna padta (jaisa Q26/Q30 mein dekha), jo zyada complex hota. `LAG`/`LEAD` ek simple, direct tarika dete hain 'adjacent row comparison' ke liye — bahut common use-case hai time-series data mein (jaise 'is mahine ka revenue pichle mahine se kitna alag hai')."

**Expected Output (is sample data pe, hire_date ascending order mein):** Amit(2018-11-23,90000, prev=NULL), Ravi(2019-03-14,75000, prev=90000, diff=-15000), Priya(2020-06-01,62000, prev=75000, diff=-13000), Sneha(2021-01-15,62000, prev=62000, diff=0), Kunal(2022-07-19,55000, prev=62000, diff=-7000), Neha(2023-02-10,95000, prev=55000, diff=+40000), Arjun(2023-05-05,48000, prev=95000, diff=-47000), Ananya(2023-08-20,40000, prev=48000, diff=-8000).

**Follow-up Questions:**
- Pehle hire hue employee (Amit) ke liye `prev_hired_salary` NULL kyun aata hai — kya ye handle karna zaroori hai?
- `LAG(salary, 2)` likhne se kya hoga — "2 pehle" wale ki value?
- `LEAD()` se ye query kaise badlegi agar humein "agle hire hue employee" se compare karna ho?

**Follow-up Solutions:**
- **Pehli row ka NULL handling:** Ye **expected behaviour** hai — pehle hire hue employee ke "pehle" koi employee hai hi nahi, isliye `LAG` ka NULL ek **valid, meaningful result** hai (matlab "no previous data available"), error nahi hai. Agar business requirement ho ki NULL ki jagah 0 ya koi default dikhe, to `LAG(salary, 1, 0) OVER (...)` use kar sakte ho — `LAG` ka teesra parameter **default value** specify karta hai jab previous row exist na kare.
- **`LAG(salary, 2)`:** Ye current row se **2 rows peeche** ki value deta hai (na ki 1) — dusra parameter "kitni rows peeche jaana hai" specify karta hai (default 1 hota hai agar na likho). Jaise Sneha (4th hired) ke liye `LAG(salary, 2)` Amit (2nd position se 2 peeche... wait, Sneha 4th position pe hai, 2 peeche matlab Ravi, jo 2nd position pe hai) ki salary dega.
- **`LEAD()` se rewrite (agle employee se compare):**
  ```sql
  SELECT employee_name, hire_date, salary,
         LEAD(salary) OVER (ORDER BY hire_date) AS next_hired_salary
  FROM Employees
  ORDER BY hire_date;
  ```
  Ab **last** hire hue employee (Ananya) ke liye `LEAD` NULL dega (kyunki uske "aage" koi row nahi hai) — logic bilkul mirror hai `LAG` ka, bas direction ulti.

---

### Q53. Har customer ka sabse recent order (`ROW_NUMBER` + `PARTITION BY`)

**Interview mein aise poocha jaayega:**
> "Find the most recent order for each customer."

**Thought Process:**
- Output: har customer ka **sirf ek** order — jo sabse latest (recent) ho.
- Keyword pakdo: **"most recent ... for each"** → per-group top-1 pattern — `ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC)` phir `WHERE rn = 1`.

**Solution Query:**
```sql
SELECT customer_id, order_id, order_date, amount
FROM (
    SELECT customer_id, order_id, order_date, amount,
           ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS rn
    FROM Orders
) ranked
WHERE rn = 1;
```

**Line-by-Line:**
- `PARTITION BY customer_id ORDER BY order_date DESC` — Har customer ke andar, orders ko **date descending** (sabse recent pehle) order karta hai.
- `ROW_NUMBER()` — Har partition ke andar 1 se start hoti numbering deta hai — matlab har customer ka sabse recent order `rn = 1` payega.
- `WHERE rn = 1` (outer) — Sirf wo "sabse recent" wali row rakho, har customer se ek.

**Naya Concept — "Top-1-Per-Group" Pattern (bahut common real-world requirement):**
"Ye ek **bahut frequently asked** pattern hai — 'latest record per group', 'highest-value record per category', 'first record per user' — sab isi template ko follow karte hain: `ROW_NUMBER() OVER (PARTITION BY group_column ORDER BY relevant_column DESC/ASC)`, phir `WHERE rn = 1`. Interview mein isko turant pehchano jab bhi 'each X ka latest/most-recent/highest Y' jaisa phrasing dikhe."

**Expected Output (is sample data pe):** C1 → O2(Feb15, 800) — Feb15 > Jan10. C2 → O5(Feb25, 700) — Feb25 > Jan20. C3 → O4(Mar5, 300) — sirf ek hi order hai uska.

**Follow-up Questions:**
- Agar `ROW_NUMBER()` ki jagah `RANK()` use karte, to kya farak padta agar do orders **same date** pe ho?
- C4 (Aarushi, jisne kabhi order nahi kiya) is result mein kyun nahi dikhegi?
- Isi kaam ko `GROUP BY customer_id, MAX(order_date)` se karne ki koshish karo — kya problem aayegi agar order ki **poori details** (amount, order_id) bhi chahiye ho?

**Follow-up Solutions:**
- **`RANK()` use karte agar same-date tie hoti:** Agar kisi customer ke 2 orders **exactly same date** pe hote (jo abhi nahi hai humare data mein), `RANK()` dono ko `rnk = 1` deta (dono ko "most recent" maan leta, kyunki unki date equal hai) — `WHERE rnk = 1` **dono rows** return karta. `ROW_NUMBER()` in dono ko **alag numbers** (1 aur 2) deta hai (arbitrary order tie ke andar), aur `WHERE rn = 1` sirf **ek** ko rakhta. Business requirement pe depend karta hai: agar "sach mein tie hai to dono dikhao" chahiye, `RANK` use karo; agar "hamesha exactly ek row per customer" chahiye (chahe tie ho), `ROW_NUMBER` use karo (best practice: tie-breaker bhi add karo `ORDER BY order_date DESC, order_id DESC` jaisa, taaki result deterministic rahe).
- **C4 kyun nahi dikhegi:** Kyunki ye query `Orders` table se shuru ho rahi hai (`FROM Orders`), aur Aarushi ka Orders table mein **koi record hi nahi hai** — usska partition banega hi nahi. Agar C4 ko bhi (with NULL order details) dikhana ho, to `Customers` table se `LEFT JOIN` karna padega pehle (Q27 wala concept), phir uske upar ye window function apply karni padegi.
- **`GROUP BY customer_id, MAX(order_date)` ki problem:** Ye approach sirf **latest date** de sakta hai, lekin us date se judi **baaki details** (order_id, amount) nahi de sakta seedhe — kyunki `GROUP BY` ke saath sirf aggregate columns (`MAX(order_date)`) ya group-by columns (`customer_id`) hi `SELECT` mein allowed hain, `order_id`/`amount` jaise non-aggregated columns **directly nahi le sakte** (MySQL kabhi-kabhi silently ek "random" matching row ka non-aggregate column de deta hai `ONLY_FULL_GROUP_BY` mode off hone par, jo **unreliable aur dangerous** hai production mein). Isliye jab poori row ki details chahiye ho (na sirf ek aggregate value), `ROW_NUMBER()`/`RANK()` approach hi sahi aur reliable tarika hai — ye interview mein bolne wala ek **bahut important point** hai.

---

### Q54. Month-over-month revenue growth calculate karo

**Interview mein aise poocha jaayega:**
> "Calculate the month-over-month percentage growth in total revenue."

**Thought Process:**
- Output: har mahine ka total revenue, aur pichle mahine se **kitna % badla/ghata**.
- Keyword pakdo: **"month-over-month"** → pehle month-wise revenue aggregate karna hoga (`GROUP BY` month), phir `LAG()` se pichle mahine ki value la kar % growth calculate karna hoga — ye ek **do-step process** hai: aggregate first, phir window function.

**Solution Query:**
```sql
WITH monthly_revenue AS (
    SELECT YEAR(order_date) AS yr, MONTH(order_date) AS mo, SUM(amount) AS revenue
    FROM Orders
    GROUP BY YEAR(order_date), MONTH(order_date)
)
SELECT yr, mo, revenue,
       LAG(revenue) OVER (ORDER BY yr, mo) AS prev_month_revenue,
       ROUND(
           (revenue - LAG(revenue) OVER (ORDER BY yr, mo)) * 100.0
           / LAG(revenue) OVER (ORDER BY yr, mo), 2
       ) AS pct_growth
FROM monthly_revenue
ORDER BY yr, mo;
```

**Line-by-Line:**
- `monthly_revenue` CTE — Pehle `Orders` ko `YEAR`+`MONTH` ke basis par group karke, har mahine ka total revenue nikaalte hain — ye Q20/Q28 wala hi `GROUP BY` + date-function pattern hai.
- `LAG(revenue) OVER (ORDER BY yr, mo)` — Ab is monthly-aggregated result pe, **pichle mahine** ka revenue nikaalte hain (chronological order mein).
- `(revenue - LAG(revenue)) * 100.0 / LAG(revenue)` — Standard percentage-growth formula: `(naya - purana) / purana * 100`.

**Naya Concept — Aggregate-Then-Window Pattern (CTE + LAG Combine):**
"Ye pattern dikhata hai ki **aggregation aur window functions ko chain** kaise karte hain — pehle raw data (individual orders) ko ek **meaningful grain** (monthly totals) tak aggregate karo (CTE mein), phir us aggregated result ke **upar** window function (`LAG`) apply karo time-series comparison ke liye. Bahut common real-world analytics pattern hai — 'day-over-day', 'week-over-week', 'year-over-year' sab isi template ko follow karte hain."

**Expected Output (is sample data pe):** Jan-2023(revenue=1700, prev=NULL, growth=NULL), Feb-2023(revenue=1500, prev=1700, growth=-11.76%), Mar-2023(revenue=300, prev=1500, growth=-80.00%).

**Follow-up Questions:**
- Pehle mahine (Jan) ka `pct_growth` NULL kyun aayega, aur ise kaise handle karoge agar 0% dikhana ho?
- Agar `LAG(revenue)` ki value **0** ho (jaise koi mahina jisme koi order na aaya ho), to `pct_growth` calculation mein kya problem aa sakti hai?
- Isi ko "year-over-year" (isi mahine ka pichle saal se comparison) mein kaise badloge?

**Follow-up Solutions:**
- **Jan ka NULL aur handling:** Jan sabse pehla mahina hai is data mein, isliye `LAG(revenue)` uske liye NULL dega (koi "pichla mahina" hai hi nahi dataset mein) — `pct_growth` bhi automatically NULL ban jaayega (kyunki NULL involved comparison mein). Agar 0% (ya "N/A" text) dikhana ho:
  ```sql
  COALESCE(
      ROUND((revenue - LAG(revenue) OVER (ORDER BY yr, mo)) * 100.0 / LAG(revenue) OVER (ORDER BY yr, mo), 2),
      0
  ) AS pct_growth
  ```
  `COALESCE(value, 0)` — Agar `value` NULL hai, to `0` return karta hai, warna `value` khud. Ye ek bahut common NULL-handling function hai (Oracle mein isका equivalent `NVL` hai).
- **`LAG(revenue) = 0` ka danger:** Agar pichle mahine ka revenue `0` hota, to `(revenue - 0) * 100.0 / 0` — ye **division by zero** error dega! Isse bachne ke liye:
  ```sql
  CASE
      WHEN LAG(revenue) OVER (ORDER BY yr, mo) = 0 THEN NULL
      ELSE ROUND((revenue - LAG(revenue) OVER (ORDER BY yr, mo)) * 100.0 / LAG(revenue) OVER (ORDER BY yr, mo), 2)
  END AS pct_growth
  ```
  Ye ek **bahut important defensive-coding point** hai jo interviewer specifically test karta hai — "agar denominator 0 ho sakta ho, to divide karne se pehle check karo."
- **Year-over-year version:** `LAG(revenue)` ki jagah `LAG(revenue, 12)` use karoge (agar data monthly grain mein hai aur continuous months hain, 12 mahine peeche jaane ke liye) — YA zyada robust tarika: `self-join`/`window` par `yr = yr - 1 AND mo = mo` explicitly match karna, kyunki `LAG(revenue, 12)` sirf tab sahi kaam karega jab **beech mein koi mahina missing na ho** (agar koi mahina data mein hi nahi hai, to "12 rows peeche" aur "12 months peeche" match nahi karenge).

---

### Q55. Kisi user ke login dates mein 2+ din ka gap dhundo (`LAG` + date arithmetic)

**Interview mein aise poocha jaayega:**
> "For each user, find any gap of 2 or more days between consecutive logins."

**Thought Process:**
- Ye `Logins` table (is file ke top pe di gayi) use karega.
- Output: har user ke consecutive logins ke beech ka gap, filter karke sirf **2+ din wale gaps**.
- Keyword pakdo: **"gap ... between consecutive logins"** → `LAG()` se pichla login-date lao (per user), phir `DATEDIFF` se difference nikaalo — aur **important gotcha:** window function ka result seedha `WHERE` mein filter nahi ho sakta (Q48 wala concept), isliye subquery/CTE wrap karna padega.

**Solution Query:**
```sql
SELECT user_id, login_date, prev_login, gap_days
FROM (
    SELECT user_id, login_date,
           LAG(login_date) OVER (PARTITION BY user_id ORDER BY login_date) AS prev_login,
           DATEDIFF(
               login_date,
               LAG(login_date) OVER (PARTITION BY user_id ORDER BY login_date)
           ) AS gap_days
    FROM Logins
) t
WHERE gap_days >= 2;
```

**Line-by-Line:**
- `LAG(login_date) OVER (PARTITION BY user_id ORDER BY login_date)` — Har user ke apne logins ke andar (partition), current login se **pichla** login-date nikalta hai.
- `DATEDIFF(login_date, LAG(...))` — `DATEDIFF(date1, date2)` MySQL mein `date1 - date2` (dinon mein) deta hai — matlab current login aur pichle login ke beech kitne din ka gap tha.
- `WHERE gap_days >= 2` (outer) — Sirf wo gaps rakho jo 2 din ya usse zyada ho — window function ke result ko filter karne ke liye isse **subquery mein wrap** karna zaroori tha (seedha `WHERE` mein window function nahi likh sakte, Q48 wala concept).

**Naya Concept — `DATEDIFF` aur Gap Detection Pattern:**
"Ye pattern — 'consecutive events ke beech gap dhundo' — bahut common hai fraud-detection, churn-analysis, aur activity-monitoring mein (jaise 'user kitne din inactive raha before wapas aane se'). `LAG` + date-difference function ka combination isका standard solution hai."

**Expected Output (is sample data pe):** U1: Jan3 → Jan5 ka gap = 2 din ✓ (qualifies). U2: Jan1 → Jan10 ka gap = 9 din ✓ (qualifies). *(U1 ke baaki consecutive logins — Jan1→Jan2, Jan2→Jan3, Jan5→Jan6, Jan6→Jan7 — sab 1-din gap hain, exclude honge.)*

**Follow-up Questions:**
- Pehla login (har user ka) is result mein kabhi kyun nahi dikhega?
- `DATEDIFF` MySQL-specific hai ya standard SQL — Oracle mein iska equivalent kya hai?
- Isi query se, **sabse bada gap** (maximum inactivity period) har user ke liye kaise nikaaloge?

**Follow-up Solutions:**
- **Pehla login kabhi kyun nahi dikhega:** Kyunki har user ke **sabse pehle** login ke liye `LAG(login_date)` NULL dega (koi "pichla" login hai hi nahi) — aur `DATEDIFF(login_date, NULL)` bhi **NULL** return karta hai — `WHERE gap_days >= 2` mein NULL involve hone se ye comparison UNKNOWN ban jaata hai, aur row automatically exclude ho jaati hai `WHERE` se. Ye **correct behaviour** hai — pehle login ka koi "gap" concept hi nahi banta.
- **`DATEDIFF` ka Oracle equivalent:** Oracle mein dates ko seedhe **subtract** kiya ja sakta hai numbers ki tarah: `login_date - LAG(login_date) OVER (...)` — ye directly **din ki count** (NUMBER) return karta hai, alag function ki zaroorat nahi (Oracle mein DATE arithmetic built-in hai). MySQL mein seedha `-` operator dates pe kaam nahi karta expected way mein, isliye `DATEDIFF()` function explicitly use karna padta hai.
- **Sabse bada gap per user:**
  ```sql
  SELECT user_id, MAX(gap_days) AS max_gap
  FROM (
      SELECT user_id,
             DATEDIFF(
                 login_date,
                 LAG(login_date) OVER (PARTITION BY user_id ORDER BY login_date)
             ) AS gap_days
      FROM Logins
  ) t
  GROUP BY user_id;
  ```
  Yaha humne window-function-wali subquery ke result pe ek **normal `GROUP BY` + `MAX`** laga diya — ye dikhata hai ki window functions aur regular aggregation **combine** ho sakte hain, bas sequence important hai (window function pehle ek layer mein calculate ho, phir uske upar normal aggregation dusre layer mein).

---

### Q56. Har employee ki salary, apne department ke total salary ka kitna % hai

**Interview mein aise poocha jaayega:**
> "For each employee, calculate their salary as a percentage of their department's total salary."

**Thought Process:**
- Output: har employee ki salary, **uske department ke total** ka percentage — har individual row zinda rehni chahiye (na collapse ho).
- Keyword pakdo: **"as a percentage of their department's total"** → `SUM() OVER (PARTITION BY department_id)` se department-total nikaalo (bina rows collapse kiye), phir individual salary ko us total se divide karo.

**Solution Query:**
```sql
SELECT employee_name, department_id, salary,
       SUM(salary) OVER (PARTITION BY department_id) AS dept_total,
       ROUND(salary * 100.0 / SUM(salary) OVER (PARTITION BY department_id), 2) AS pct_of_dept
FROM Employees;
```

**Line-by-Line:**
- `SUM(salary) OVER (PARTITION BY department_id)` — **`ORDER BY` nahi hai yaha** — isliye ye "running total" nahi, balki **poore partition (department) ka total** deta hai, aur ye same value **har row mein repeat** hoti hai us department ke andar (Q51 ke follow-up mein ye concept touch kiya tha).
- `salary * 100.0 / SUM(salary) OVER (...)` — Current row ki individual salary ko us department ke total se divide karke percentage nikaalte hain.

**Naya Concept — Window Function bina `ORDER BY` ke (Group-Total Broadcasting):**
"Jab `OVER()` ke andar sirf `PARTITION BY` ho, `ORDER BY` na ho, to aggregate function **poore partition ka ek fixed total** deta hai, jo **har row mein same** rehta hai us partition ke liye — ye 'running' nahi hai, ye 'broadcast' hai (poore group ka result har member ko mil jaata hai). Ye Q42 ke `CROSS JOIN` wale scalar-broadcasting pattern ka **window-function equivalent** hai — bas yaha humein alag se CTE/CROSS JOIN nahi karna pada, seedhe `OVER (PARTITION BY ...)` ne kaam kar diya."

**Expected Output (is sample data pe):** dept10(total=177000): Ravi=42.37%, Priya=35.03%, Ananya=22.60%. dept20(total=152000): Amit=59.21%, Sneha=40.79%. dept30(total=150000): Neha=63.33%, Kunal=36.67%. NULL-dept(total=48000): Arjun=100.00% (akela hai apne group mein).

**Follow-up Questions:**
- Har department ke saare employees ka `pct_of_dept` jodo — kya total hamesha 100% aana chahiye?
- Isi query ko `GROUP BY` + `JOIN` (bina window function ke) se kaise likhoge — Q42 wale CTE+CROSS JOIN pattern se compare karo?
- Agar `PARTITION BY` hata dein, to `pct_of_dept` ka matlab kya ban jaayega?

**Follow-up Solutions:**
- **Sab pct_of_dept jodne par 100%:** Haan, mathematically **har department ke andar** sab employees ka `pct_of_dept` jodne par **100.00%** (ya rounding errors ki wajah se 99.99%/100.01% jaisa kuch, `ROUND()` ki wajah se) aana chahiye — kyunki har employee ki salary, poore department-total ka ek "hissa" hai, aur saare hisse milkar poora (100%) banate hain. Ye ek acha **sanity-check** hai jo interview mein khud verify karke dikha sakte ho.
- **Bina window function, GROUP BY + JOIN se:**
  ```sql
  WITH dept_totals AS (
      SELECT department_id, SUM(salary) AS dept_total
      FROM Employees
      GROUP BY department_id
  )
  SELECT e.employee_name, e.department_id, e.salary,
         ROUND(e.salary * 100.0 / dt.dept_total, 2) AS pct_of_dept
  FROM Employees e
  JOIN dept_totals dt ON e.department_id <=> dt.department_id;
  ```
  *(Yaha `<=>` NULL-safe operator use kiya taaki Arjun ka NULL department bhi sahi se match ho jaaye, jaisa Q40 mein dekha tha.)* Ye Q42 wale CTE+aggregation pattern se **bahut similar** hai, bas yaha "single company total" ki jagah "per-department totals" ko join kiya — dono approach (window function vs CTE+JOIN) same result dete hain, window function version generally **chhota aur ek-hi-query mein** hota hai, jabki CTE+JOIN version **zyada explicit/traceable** hota hai step-by-step logic ke liye.
- **`PARTITION BY` hataने par:** Agar `PARTITION BY department_id` hata dein, to `SUM(salary) OVER ()` (bina partition) **poori company** ka total salary dega (527000) — aur `pct_of_dept` ka matlab badal kar ban jaayega "employee ki salary, **poori company ke total salary** ka kitna % hai" — department-level comparison nahi rahega, company-wide comparison ban jaayega.

---

## CATEGORY 8 — REAL-WORLD / ADVANCED INTERVIEW PATTERNS

### Q57. Duplicate rows dhundo aur remove karo (sirf ek copy rakhte hue)

**Interview mein aise poocha jaayega:**
> "Suppose the Employees table accidentally has duplicate rows (e.g., Ravi Kumar's record got inserted twice with the same employee_id). Write a query to find and remove the duplicates, keeping only one copy."

**Thought Process:**
- **Important note:** Humara clean sample dataset mein koi literal duplicate rows nahi hain (har employee_id unique hai) — ye ek **hypothetical scenario** hai jo interviewer aksar poochta hai ("maan lo data-entry error ho gaya"). Isliye is question ke liye ek chhota **scratch example** use karenge.
- Keyword pakdo: **"accidentally duplicate"**, "keeping only one copy" → pehle duplicates **dhundo** (GROUP BY + HAVING COUNT > 1 — Q version jaisa pehli file mein tha), phir unhe **delete** karo, ek copy chhodkar.

**Scratch Example (sirf is question ke demonstration ke liye):**
Maan lo `Employees_Staging` table mein Ravi Kumar (employee_id 101) ki entry **galti se 2 baar** insert ho gayi:
| row_ref | employee_id | employee_name |
|---|---|---|
| 1 | 101 | Ravi Kumar |
| 2 | 101 | Ravi Kumar |
| 3 | 102 | Priya Sharma |

**Solution Query (dhundhna):**
```sql
SELECT employee_id, COUNT(*) AS duplicate_count
FROM Employees_Staging
GROUP BY employee_id
HAVING COUNT(*) > 1;
```

**Solution Query (remove karna, MySQL mein — window function + DELETE):**
```sql
DELETE FROM Employees_Staging
WHERE row_ref IN (
    SELECT row_ref FROM (
        SELECT row_ref,
               ROW_NUMBER() OVER (PARTITION BY employee_id ORDER BY row_ref) AS rn
        FROM Employees_Staging
    ) t
    WHERE rn > 1
);
```

**Line-by-Line:**
- **Dhundhna wali query** — Q15 wala hi `GROUP BY + HAVING COUNT(*) > 1` pattern — jo bhi `employee_id` **ek se zyada baar** aaya ho, wo duplicate hai.
- **Remove karne wali query:** Inner-most subquery `ROW_NUMBER() OVER (PARTITION BY employee_id ORDER BY row_ref)` har duplicate-group ke andar rows ko number deti hai (1, 2, 3...) — `rn = 1` wali row ko **"original/keep karne wali"** maan rahe hain, baaki (`rn > 1`) **"extra duplicates"** hain jo delete karne hain.
- `DELETE FROM ... WHERE row_ref IN (subquery)` — MySQL mein directly `DELETE ... FROM (subquery with window function)` allowed nahi hai ek hi statement mein seedhe (self-reference restriction), isliye ek extra subquery-wrapping (`SELECT row_ref FROM (...) t`) trick use karni padti hai.

**Naya Concept — `ROW_NUMBER()` for Deduplication (Delete-Keep-One Pattern):**
"Ye **bahut standard production pattern** hai duplicate rows clean karne ke liye — `PARTITION BY` un columns pe jo "duplicate define karte hain" (yaha `employee_id`), phir `ROW_NUMBER()` se ek 'winner' (`rn=1`, generally sabse purani ya kisi consistent criteria se choose ki hui row) decide karo, baaki sab delete kar do."

**Follow-up Questions:**
- `ROW_NUMBER() ORDER BY row_ref` mein humne koi specific criteria use nahi ki "kaunsi copy rakhni hai" — agar business requirement ho ki **sabse latest** wali copy rakhni hai, to query kaise badlegi?
- Ye dedup pattern `DISTINCT` se kaise alag hai — `DISTINCT` isi kaam ke liye kyun kaafi nahi hai jab table mein ek proper primary key/unique-id column ho?
- Agar duplicates 2 se zyada baar (jaise 3-4 baar) repeat ho rahe hon, to ye query kaam karegi kya bina modification ke?

**Follow-up Solutions:**
- **Latest copy rakhni ho:** `ORDER BY row_ref` ko `ORDER BY row_ref DESC` (agar `row_ref` insertion-sequence represent karta hai, jaise auto-increment ID) ya kisi timestamp column (`ORDER BY created_at DESC`) se replace karo — phir `rn = 1` automatically "sabse latest" copy ko represent karega, aur baaki purani copies delete ho jaayengi.
- **`DISTINCT` kyun kaafi nahi:** `DISTINCT` **poori row ka combination** unique banata hai — agar table mein ek `row_ref` (ya koi bhi unique technical ID) column hai jo har row ke liye **alag** hai (jaisa staging tables mein aksar hota hai, kyunki har INSERT ek naya technical ID leta hai chahe baaki data duplicate ho), to `DISTINCT` **kaam hi nahi karega** — kyunki `row_ref` khud unique hone ki wajah se poori row "unique" dikhegi `DISTINCT` ke liye, chahe business-level duplicate (`employee_id`) same ho. `DISTINCT` sirf tab useful hai jab tumhare paas koi technical unique-ID column hi na ho.
- **3-4 baar duplicate hone par:** Haan, ye query **bina modification ke kaam karegi** — `ROW_NUMBER()` chahe kitne bhi duplicates hon (2, 3, 4, ya usse zyada), har group ke andar 1,2,3,4... sequentially number deta rahega, aur `rn > 1` wale **saare extras** (chahe 1 extra ho ya 5 extra) ko delete kar dega, sirf `rn = 1` wali ek row bachegi. Ye pattern **kisi bhi duplicate-count ke liye scale** karta hai, koi extra logic nahi chahiye.

---

### Q58. Employees jinki salary kisi aur employee ke bilkul same hai

**Interview mein aise poocha jaayega:**
> "Find employees who have the exact same salary as at least one other employee."

**Thought Process:**
- Ye Q17 (HAVING COUNT) wale pattern ka application hai, salary column pe.
- Keyword pakdo: **"same salary as at least one other"** → pehle wo salary-values dhundo jo **ek se zyada employees share karte hain**, phir un values wale saare employees dikhao.

**Solution Query:**
```sql
SELECT employee_name, salary
FROM Employees
WHERE salary IN (
    SELECT salary
    FROM Employees
    GROUP BY salary
    HAVING COUNT(*) > 1
);
```

**Line-by-Line:**
- Inner query: `GROUP BY salary HAVING COUNT(*) > 1` — un saari salary-values ko dhundta hai jo **ek se zyada employees** ki hain (matlab duplicate salary-values).
- `WHERE salary IN (...)` (outer) — Ab un saari matching salary-values wale employees dikhao — **individual employee records** ke saath (na ki sirf grouped/aggregated view).

**Naya Concept — "Find Members of Duplicate Groups" Pattern:**
"Ye pattern Q17 (HAVING se sirf **groups** dikhana) aur is question (HAVING se dhoondi gayi values ka use karke **individual members** dikhana) ke beech ka farak dikhata hai. `HAVING` sirf group-level result deta hai — agar humein **un groups ke andar ki actual rows** chahiye ho, to `HAVING` wali query ko ek **subquery** ki tarah use karke outer query mein filter karna padta hai."

**Expected Output (is sample data pe):** Priya Sharma (62000), Sneha Rao (62000) — dono ki salary same hai.

**Follow-up Questions:**
- Isi query ko `JOIN` use karke (subquery ke bina, self-join se) kaise likhoge?
- Agar humein sirf ye **count** chahiye ki kitne "salary-duplicate pairs" hain, na ki actual employees, to query kaise badlegi?
- `IN` ki jagah `EXISTS` use karke isi query ko kaise rewrite karoge?

**Follow-up Solutions:**
- **Self-Join se rewrite:**
  ```sql
  SELECT DISTINCT e1.employee_name, e1.salary
  FROM Employees e1
  JOIN Employees e2 ON e1.salary = e2.salary AND e1.employee_id != e2.employee_id;
  ```
  Yaha `e1.employee_id != e2.employee_id` zaroori hai taaki employee apne aap ko match na kare (warna har employee apni khud ki salary se match kar jaata, chahe unique ho ya na ho) — `DISTINCT` isliye zaroori hai kyunki agar 3+ employees same salary share karein, to ek employee multiple baar match ho sakta hai (har doosre matching employee ke against ek baar).
- **Sirf count chahiye (kitne "duplicate salary groups" hain):**
  ```sql
  SELECT COUNT(*) AS duplicate_salary_groups
  FROM (
      SELECT salary FROM Employees GROUP BY salary HAVING COUNT(*) > 1
  ) t;
  ```
  Ye batayega "kitni **distinct salary values** hain jo duplicate hain" (is dataset mein sirf 1 — 62000) — na ki kitne employees involved hain.
- **`EXISTS` se rewrite:**
  ```sql
  SELECT e1.employee_name, e1.salary
  FROM Employees e1
  WHERE EXISTS (
      SELECT 1 FROM Employees e2
      WHERE e2.salary = e1.salary AND e2.employee_id != e1.employee_id
  );
  ```
  Ye ek **correlated subquery** (Q34 wala concept) hai — har employee ke liye check karta hai "kya koi *doosra* employee hai jiski salary meri jaisi hai?" Agar haan, to `EXISTS` true, wo row result mein aati hai. Functionally `IN` wale approach jaisa hi result deta hai.

---

### Q59. Department with maximum number of employees

**Interview mein aise poocha jaayega:**
> "Find the department with the highest number of employees."

**Thought Process:**
- Output: sirf **ek** department (ya tie hone par multiple) — jiske paas sabse zyada employees hain.
- Keyword pakdo: **"the department with highest"** (singular, top-1) → `GROUP BY` + `COUNT`, phir sort karke top result nikaalo — `ORDER BY ... DESC LIMIT 1`.

**Solution Query:**
```sql
SELECT department_id, COUNT(*) AS emp_count
FROM Employees
GROUP BY department_id
ORDER BY emp_count DESC
LIMIT 1;
```

**Line-by-Line:**
- `GROUP BY department_id` + `COUNT(*)` — Q15 wala hi pattern, har department ka employee-count nikalta hai.
- `ORDER BY emp_count DESC LIMIT 1` — Sabse zyada count wale department ko upar la kar, sirf pehli row rakhte hain.

**Naya Concept — `LIMIT 1` ka Tie-Handling Gotcha:**
"Agar do departments **tie** karte hain (same maximum count), to `LIMIT 1` **sirf ek** ko dikhayega — jo bhi arbitrarily 'pehli' aa jaaye — doosra tied department **silently miss** ho jaayega. Agar tie hone par **saare** tied departments dikhane hon, `LIMIT` ki jagah `DENSE_RANK() = 1` (window function) approach use karna chahiye, jo Q48/Q49 mein dekha tha."

**Expected Output (is sample data pe):** Sales (dept 10), count = 3.

**Follow-up Questions:**
- Agar Engineering aur HR dono ke paas equal (maan lo 3-3) employees hote, to `LIMIT 1` wali query kya karti — kya ye ek problem hai?
- Isi query ko tie-safe banane ke liye `DENSE_RANK()` se kaise rewrite karoge?
- Department **naam** (na sirf ID) bhi chahiye ho, to query mein kya add karna padega?

**Follow-up Solutions:**
- **Tie hone par `LIMIT 1` ki problem:** Haan, ye ek genuine problem hai — agar Engineering aur HR dono ke paas 3-3 employees hote, `LIMIT 1` sirf **ek** ko (arbitrarily, jo bhi database internally pehle process kare) dikhayega, doosra tied department **result se poori tarah gayab** ho jaayega, jabki logically dono ko "maximum" ki category mein hona chahiye. Ye ek **subtle bug** hai jo demo/interview mein miss ho sakta hai agar specifically test na kiya jaaye.
- **`DENSE_RANK()` se tie-safe version:**
  ```sql
  SELECT department_id, emp_count
  FROM (
      SELECT department_id, COUNT(*) AS emp_count,
             DENSE_RANK() OVER (ORDER BY COUNT(*) DESC) AS rnk
      FROM Employees
      GROUP BY department_id
  ) t
  WHERE rnk = 1;
  ```
  Ye version **saare** tied-for-maximum departments ko dikhayega, chahe kitne bhi ho — `LIMIT 1` ke bajaye `DENSE_RANK() = 1` use karna hamesha safer hai jab "the top X" jaisa singular-sounding requirement ho, lekin ties possible hon.
- **Department naam bhi chahiye:**
  ```sql
  SELECT d.department_name, COUNT(*) AS emp_count
  FROM Employees e
  JOIN Departments d ON e.department_id = d.department_id
  GROUP BY d.department_name
  ORDER BY emp_count DESC
  LIMIT 1;
  ```
  Bas ek `JOIN Departments` add karna padega (Q18 wala pattern) taaki `department_id` ke sath naam bhi mil jaaye.

---

### Q60. Department with highest average salary

**Interview mein aise poocha jaayega:**
> "Find the department with the highest average salary."

**Thought Process:**
- Bilkul Q59 jaisa hi pattern, bas `COUNT(*)` ki jagah `AVG(salary)`.

**Solution Query:**
```sql
SELECT department_id, AVG(salary) AS avg_salary
FROM Employees
GROUP BY department_id
ORDER BY avg_salary DESC
LIMIT 1;
```

**Line-by-Line:**
- Same structure jaisa Q59 — `GROUP BY` + `AVG`, phir `ORDER BY DESC LIMIT 1` se top result.

**Expected Output (is sample data pe):** Engineering (dept 20), avg_salary = 76000.

**Follow-up Questions:**
- Ye query bhi Q59 jaisa tie-related risk rakhti hai — kaise?
- Agar NULL-department group (Arjun akela) ka "average" (48000) accidentally highest ban jaata (hypothetically), to kya wo is result mein "department" ki tarah dikhta?
- Isi ko `RANK()` se kaise likhoge (`DENSE_RANK()` ke bajaye) — kya farak padega yaha?

**Follow-up Solutions:**
- **Tie-related risk:** Bilkul Q59 jaisa hi — agar do departments ka average salary **exactly same** ho, `LIMIT 1` sirf ek ko dikhayega, doosra chhut jaayega. Fix bhi wahi hai — `DENSE_RANK() OVER (ORDER BY AVG(salary) DESC) = 1` approach use karo.
- **NULL-department group agar highest hota:** Haan, `GROUP BY department_id` NULL ko bhi **ek valid group** treat karta hai (Q15 mein dekha tha) — agar uska average sabse zyada hota, to query use ek "department" ki tarah hi dikhati (`department_id = NULL` ke saath), jo confusing ho sakta hai kyunki NULL koi real department nahi hai (ye sirf "unassigned" employees ka placeholder group hai). Production query mein aksar `WHERE department_id IS NOT NULL` filter add karte hain aise cases mein, taaki NULL-group accidentally "winner" na ban jaaye jab wo asal mein koi valid department represent hi nahi karta.
- **`RANK()` use karte (`DENSE_RANK()` ke bajaye):** Is specific case mein (jaha humein sirf "top 1" chahiye), `RANK()` aur `DENSE_RANK()` **same behaviour** denge agar sirf rank-1 filter kar rahe ho (`WHERE rnk = 1`) — farak sirf **tab** aata jab tum 2nd ya 3rd position bhi dhundh rahe ho aur beech mein ties involved hon (Q47/Q49 wala concept). Sirf "the maximum" (rank 1) ke liye, dono equivalent hain.

---

### Q61. Top-3 salaries per department, ties ko sahi tarike se handle karte hue

**Interview mein aise poocha jaayega:**
> "Find the top 3 salary levels in each department, making sure ties are handled correctly (i.e., don't arbitrarily cut off a tied salary)."

**Thought Process:**
- Ye Q48 ka hi **refinement/discussion** hai, jahan interviewer specifically ties ke correct handling pe zor de raha hai.
- Keyword pakdo: **"don't arbitrarily cut off a tied salary"** → clearly `DENSE_RANK()` chahiye, `ROW_NUMBER()` **nahi** (jo ties ko arbitrarily split kar deta).

**Solution Query:**
```sql
SELECT employee_name, department_id, salary
FROM (
    SELECT employee_name, department_id, salary,
           DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS salary_rank
    FROM Employees
) ranked
WHERE salary_rank <= 3;
```

**Line-by-Line:**
- Bilkul Q48 jaisa hi query — `DENSE_RANK()` ka use hi is question ka "correct answer" hai, kyunki ye guarantee karta hai ki agar 3rd position pe do employees tie karte hain, **dono** dikhenge (`ROW_NUMBER()` sirf ek ko dikhata, arbitrarily).

**Naya Concept — Interviewer "Ties" Explicitly Mention Kare To — Red Flag for `ROW_NUMBER()`:**
"Jab bhi interviewer specifically bole 'ties ko handle karo' ya 'kisi ko arbitrarily exclude mat karo', ye ek **clear signal** hai ki wo `DENSE_RANK()` (ya kam se kam `RANK()`, depending on exact semantics chahiye) expect kar raha hai, `ROW_NUMBER()` nahi. Interview mein agar tumne `ROW_NUMBER()` use kiya without justification, interviewer turant follow-up karega 'iska ties ke saath kya hoga?' — isliye pehle se hi sahi choice karna better impression deta hai."

**Expected Output (is sample data pe):** Jaisa Q48 mein dekha, is specific dataset mein har department mein already ≤3 employees hain, isliye result same rahega — lekin **approach ka justification** hi is question ka asli focus hai.

**Follow-up Questions:**
- `RANK()` aur `DENSE_RANK()` mein se is specific requirement ("top 3 salary **levels**") ke liye kaunsa zyada sahi hai, aur kyun?
- Agar requirement ho "exactly top 3 **employees**" (chahe ties ho ya na ho, hamesha 3 rows), to konsi function use karoge?
- Is difference ko ek concrete hypothetical example se explain karo jahan department mein 4 employees hon aur 2nd-3rd position pe tie ho.

**Follow-up Solutions:**
- **`RANK()` vs `DENSE_RANK()` for "top 3 levels":** `DENSE_RANK()` zyada सही hai agar requirement "top 3 **distinct salary values**" hai — kyunki ye ties ke baad bhi sequential numbering maintain karta hai (1,2,2,3 — koi skip nahi), isliye `<= 3` sahi teen "levels" capture karta hai. `RANK()` agar use karte, to ties ke baad number **skip** ho jaata (1,2,2,4) — matlab `<= 3` **sirf 2 distinct levels** capture karta (kyunki teesra level number 4 pe chala gaya, jo filter se bahar hai) — ye galat hota agar "3 levels" hi chahiye the.
- **"Exactly top 3 employees" chahiye (hamesha 3 rows):** `ROW_NUMBER()` use karoge, kyunki wo **hamesha unique, sequential** numbers deta hai — `WHERE rn <= 3` **guaranteed exactly 3 rows per department** dega (agar department mein kam se kam 3 employees hon), chahe ties ho ya na ho. Business requirement "hume exactly N employees chahiye reporting/limit ke liye" jaisa ho, to `ROW_NUMBER()` sahi choice hai (bhale hi ties ko "arbitrarily" split kare).
- **Concrete example (4 employees, 2nd-3rd tie):** Maan lo ek department mein 4 employees hain: A=100, B=90, C=90, D=80.
  - `DENSE_RANK`: A=1, B=2, C=2, D=3. `WHERE rnk <= 3` → **saare 4** employees aayenge (A,B,C,D) — kyunki D ka dense_rank 3 hai.
  - `RANK`: A=1, B=2, C=2, D=4 (skip ho gaya 3). `WHERE rnk <= 3` → sirf **A, B, C** (3 rows) aayenge, D **miss** ho jaayega — jabki agar "top 3 salary levels" chahiye the (100, 90, 80), to D (jiski salary 80 hai, teesra distinct level) galti se exclude ho gaya!
  - `ROW_NUMBER`: A=1, B=2, C=3, D=4 (B/C ka order arbitrary hai bina tie-breaker ke). `WHERE rn <= 3` → **A, B, C** (ya A,C,B depending on internal order) — exactly 3 rows, lekin D poori tarah drop, aur B/C mein se kisko rakha ye bhi arbitrary hai.
  - Yehi wajah hai ki **"top 3 salary levels" ke liye `DENSE_RANK` sabse correct** hai is example mein.

---

### Q62. Har customer ka first order aur latest order date

**Interview mein aise poocha jaayega:**
> "Find the first and the most recent order date for each customer."

**Thought Process:**
- Ye simple `GROUP BY` + `MIN`/`MAX` hai (Q16 jaisa hi pattern, bas date columns pe, do aggregate functions ek saath).

**Solution Query:**
```sql
SELECT customer_id, MIN(order_date) AS first_order, MAX(order_date) AS latest_order
FROM Orders
GROUP BY customer_id;
```

**Line-by-Line:**
- `MIN(order_date)` — Sabse purani (chhoti) date — pehla order.
- `MAX(order_date)` — Sabse recent (badi) date — latest order.
- Dono ek hi `GROUP BY` ke andar ek saath calculate ho rahe hain — koi zaroorat nahi do alag queries likhne ki.

**Naya Concept — Multiple Aggregate Functions Ek Saath:**
"Ek `GROUP BY` ke andar **multiple different aggregate functions** (yaha `MIN` aur `MAX`) ek saath use kar sakte ho, bina kisi extra complexity ke — dono independently, **usi group ke data** par calculate hote hain."

**Expected Output (is sample data pe):** C1(first=Jan10, latest=Feb15), C2(first=Jan20, latest=Feb25), C3(first=latest=Mar5, kyunki sirf ek hi order hai).

**Follow-up Questions:**
- C3 ke liye `first_order` aur `latest_order` same date kyun dikhate hain?
- C4 (jisne kabhi order nahi kiya) is result mein kyun missing hai — jaisa Q19 mein dekha tha?
- Sirf date nahi, us first/latest order ka **poora amount aur order_id** bhi chahiye ho, to query kaise badlegi? (Hint: Q53 wala window-function approach.)

**Follow-up Solutions:**
- **C3 same date kyun:** Kyunki C3 ka **sirf ek hi order** hai (O4, March 5) — jab group mein sirf ek hi row ho, `MIN` aur `MAX` dono **usi ek value** ko return karenge (kyunki "sabse chhota" aur "sabse bada" dono ek hi single value hoti hain jab compare karne ke liye aur kuch hi na ho).
- **C4 missing kyun:** Bilkul Q19 wala hi concept — `GROUP BY` sirf `Orders` table ki **existing rows** pe group banata hai, aur C4 ka Orders table mein koi record hi nahi hai, isliye uska koi group ban hi nahi sakta. Agar C4 ko bhi (NULL dates ke saath) dikhana ho, `Customers LEFT JOIN Orders` phir `GROUP BY` karna padega.
- **Poora order detail (amount, order_id) bhi chahiye:** Simple `MIN`/`MAX` sirf **date** de sakte hain, poori row nahi. Iske liye Q53 wala `ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date)` approach chahiye — do alag queries (ek `ORDER BY order_date ASC` ke saath rn=1 first order ke liye, ek `ORDER BY order_date DESC` ke saath rn=1 latest order ke liye), phir dono ko combine (`UNION` ya separate columns mein `CASE WHEN` se) karna padega — ye complexity dikhata hai ki "sirf value chahiye" (MIN/MAX kaafi hai) aur "poori row chahiye" (window function zaroori hai) mein bada farak hota hai.

---

### Q63. Har user ki consecutive login-days streak nikaalo (Gaps-and-Islands Problem)

**Interview mein aise poocha jaayega:**
> "For each user, identify their consecutive login streaks (i.e., group together consecutive days) and show the start date, end date, and length of each streak."

**Thought Process:**
- Ye ek **classic, advanced SQL pattern** hai jise "Gaps and Islands" problem kehte hain — humein consecutive dates ko automatically "islands" (groups) mein todna hai, bina manually har gap ko check kiye.
- Keyword pakdo: **"consecutive ... group together"** → ye simple `LAG`-based gap-detection (Q55) se ek kadam aage hai — humein pura **streak group** identify karna hai, na ki sirf "gap hai ya nahi" boolean.
- **Core trick:** Agar hum `ROW_NUMBER()` (sequential counter, per user) ko current date se **din ke roop mein subtract** kar dein, to **consecutive dates ke liye ye result hamesha same** rahega — ye ek "constant" ban jaata hai jise hum `GROUP BY` kar sakte hain!

**Solution Query:**
```sql
WITH numbered AS (
    SELECT user_id, login_date,
           ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) AS rn
    FROM Logins
),
grouped AS (
    SELECT user_id, login_date,
           DATE_SUB(login_date, INTERVAL rn DAY) AS island_group
    FROM numbered
)
SELECT user_id,
       MIN(login_date) AS streak_start,
       MAX(login_date) AS streak_end,
       COUNT(*) AS streak_length
FROM grouped
GROUP BY user_id, island_group
ORDER BY user_id, streak_start;
```

**Line-by-Line (ye interview mein sabse zyada explain karne wali cheez hai — dhyan se samjho):**
- `numbered` CTE — Har user ke logins ko date-order mein sequential number (1, 2, 3, ...) deta hai (`ROW_NUMBER()`, Q46 wala hi concept).
- `DATE_SUB(login_date, INTERVAL rn DAY)` — **Yehi asli trick hai.** Agar dates **consecutive** hain (jaise Jan1, Jan2, Jan3 — jinka `rn` 1,2,3 hai), to `date - rn` calculate karo:
  - Jan1 - 1 din = Dec31
  - Jan2 - 2 din = Dec31
  - Jan3 - 3 din = Dec31
  
  **Teeno ka result SAME hai (Dec31)!** Ye isliye hota hai kyunki jab dates aur unke row-numbers dono **ek-ek karke badhte** hain (consecutive), unka difference **constant** rehta hai. Lekin jaise hi ek **gap** aata hai (jaise Jan5 ke liye rn=4, kyunki Jan4 missing tha), to `Jan5 - 4 din = Jan1`, jo **alag** hai pichle group ke "Dec31" se — matlab ek **naya island_group** ban jaata hai.
- `GROUP BY user_id, island_group` — Ab jo bhi rows **same `island_group` value** share karti hain, wo automatically ek **consecutive streak** hain — chahe streak kitni bhi lambi ho, technique khud-ba-khud sahi tarike se group kar deti hai.
- `MIN(login_date)`, `MAX(login_date)`, `COUNT(*)` — Har streak-group ke liye start-date, end-date, aur streak-length nikaalte hain.

**Naya Concept — "Gaps and Islands" Technique (Row-Number Minus Date):**
"Ye ek **bahut famous, advanced SQL interview pattern** hai jo directly kisi built-in function se nahi hota — isse manually construct karna padta hai `ROW_NUMBER() - date` (ya date-equivalent) ke through. Ye pattern sirf login-streaks ke liye nahi, balki kisi bhi 'consecutive sequence detection' problem ke liye kaam karta hai — jaise 'consecutive winning days in stock market', 'consecutive months with sales growth', 'machine ke consecutive uptime periods', etc. Interview mein ye poochha jaana ek **senior/advanced-level signal** hai."

**Expected Output (is sample data pe):**
| user_id | streak_start | streak_end | streak_length |
|---|---|---|---|
| U1 | 2023-01-01 | 2023-01-03 | 3 |
| U1 | 2023-01-05 | 2023-01-07 | 3 |
| U2 | 2023-01-01 | 2023-01-01 | 1 |
| U2 | 2023-01-10 | 2023-01-10 | 1 |

**Follow-up Questions:**
- Ye "row-number minus date" trick specifically kyun kaam karta hai — thoda aur detail mein samjhao?
- Agar humein sirf **sabse lambi streak** (har user ke liye maximum) chahiye ho, na ki saari streaks, to query kaise badlegi?
- Ye technique `LAG`-based gap-detection (Q55) se zyada complex kyun hai, aur kab kaunsa use karoge?

**Follow-up Solutions:**
- **Trick detailed samjhao:** Socho ek sequence: dates = [1,2,3,5,6,7] (day-numbers), aur unke row-numbers (1-indexed, order mein) = [1,2,3,4,5,6]. Ab `date - row_number` nikaalo: (1-1)=0, (2-2)=0, (3-3)=0, (5-4)=1, (6-5)=1, (7-6)=1. Dekho — jab tak dates **ek-ek karke** badh rahi hain (gap nahi hai), `date - row_number` ka result **same** rehta hai (yaha 0). Jaise hi ek gap aata hai (4 missing hai, seedhe 3 se 5 pe jump), row_number to phir bhi sequentially badhta hai (4), lekin date usse zyada badh jaati hai (5) — is wajah se `date - row_number` ki value **badal jaati hai** (0 se 1 ho jaati hai) — aur yehi value-change hi naye group ka signal hai. Ye ek purely **arithmetic trick** hai jo consecutive-sequences ko automatically distinct "buckets" mein daal deta hai.
- **Sirf sabse lambi streak (per user):**
  ```sql
  WITH numbered AS (
      SELECT user_id, login_date,
             ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) AS rn
      FROM Logins
  ),
  grouped AS (
      SELECT user_id, login_date,
             DATE_SUB(login_date, INTERVAL rn DAY) AS island_group
      FROM numbered
  ),
  streaks AS (
      SELECT user_id, MIN(login_date) AS streak_start, MAX(login_date) AS streak_end,
             COUNT(*) AS streak_length
      FROM grouped
      GROUP BY user_id, island_group
  )
  SELECT user_id, streak_start, streak_end, streak_length
  FROM (
      SELECT *, ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY streak_length DESC) AS rn
      FROM streaks
  ) t
  WHERE rn = 1;
  ```
  Ye "streaks" CTE (jo already saari streaks calculate kar chuki hai) ke upar **ek aur layer** ki window function laga rahi hai — per-user, sabse lambi streak ko `rn=1` dekar filter kar rahe hain. Ye dikhata hai ki multiple advanced techniques **layer-by-layer** kaise combine hoti hain complex requirements ke liye.
- **`LAG`-based (Q55) vs Row-Number-Minus-Date — kab kaunsa:** `LAG`-based approach (Q55) **sirf "gap hai ya nahi" ye boolean-jaisa answer** deta hai — har row ke liye "pichle se kitna gap tha." Ye tab kaafi hai jab requirement simple ho ("gaps dikhao"). "Gaps-and-Islands" (Q63) approach **poore streaks ko groups mein todta hai** — jab requirement ho "har streak ka start/end/length chahiye" (ek summary per streak, na ki per-row gap-info), to ye zyada powerful/zaroori hai. Simple rule: agar sirf "kaha gap hai" jaanna hai, `LAG` kaafi hai; agar "streaks ko discrete units ki tarah treat karna hai" (jaise unko count karna, sort karna, ya unka summary nikaalna), Gaps-and-Islands zaroori hai.

---

### Q64. Employee_id sequence mein missing IDs dhundo

**Interview mein aise poocha jaayega:**
> "Suppose employee_ids are supposed to be a continuous sequence (e.g., 101 to 108), but some IDs are missing due to resignations where records were fully deleted. Write a query to find the missing IDs."

**Thought Process:**
- **Important note:** Humara real Employees data (101-108) mein koi gap nahi hai (sab consecutive hain) — is question ke liye ek **hypothetical/scratch scenario** use karenge taaki gap-detection technique properly demonstrate ho sake.
- Keyword pakdo: **"missing IDs in a sequence"** → classic "gap detection" — self-join ya numbers-sequence-generation technique.

**Scratch Example (sirf is question ke liye — maan lo `employee_id`s hain):**
101, 102, 103, 105, 106, 108 *(104 aur 107 missing hain — maan lo ye employees resign kar chuke the aur unka record poori tarah delete ho gaya)*

**Solution Query (self-join / "gap right after existing id" technique):**
```sql
SELECT (e1.employee_id + 1) AS missing_id_starts_at
FROM Employees e1
WHERE NOT EXISTS (
    SELECT 1 FROM Employees e2 WHERE e2.employee_id = e1.employee_id + 1
)
AND e1.employee_id + 1 < (SELECT MAX(employee_id) FROM Employees);
```

**Line-by-Line:**
- `e1.employee_id + 1` — Har existing employee_id ke "agle" ID (jo hona chahiye tha agar sequence continuous hoti) ko calculate karta hai.
- `WHERE NOT EXISTS (SELECT 1 FROM Employees e2 WHERE e2.employee_id = e1.employee_id + 1)` — Check karta hai ki wo "agla" ID **actually exist karta hai ya nahi** Employees table mein. Agar nahi karta, matlab ek gap hai.
- `AND e1.employee_id + 1 < (SELECT MAX(employee_id) FROM Employees)` — Ye condition zaroori hai taaki **sabse aakhri (max) ID ke baad** ka "non-existent agla ID" galti se "missing" na maana jaaye (kyunki sequence ka koi defined upper-bound nahi hai, sirf max jitna hi hai jo abhi tak insert hua) — matlab agar max ID 108 hai, to hum "109 missing hai" nahi bolna chahte, kyunki 109 kabhi assign hi nahi hua, delete nahi hua.

**Naya Concept — Self-Join Gap-Detection ("Next ID Doesn't Exist") Technique:**
"Ye ek simpler alternative hai poore 'sequence generation' se — hum **directly check** karte hain 'kya is existing ID ke turant baad wala ID exist karta hai?' Agar nahi, wahi humara gap hai. Ye tab tak kaam karta hai jab gaps chhote hon (ek-ek ID missing ho, consecutively multiple missing IDs ho to ye query sirf 'gap start' dikhayegi, poori range nahi — us case ke liye ek **numbers-table/recursive-CTE-based sequence generation** approach zyada robust hoti hai)."

**Expected Output (scratch example pe):** 104 (kyunki 103 ke baad 104 exist nahi karta), 107 (kyunki 106 ke baad 107 exist nahi karta).

**Follow-up Questions:**
- Agar **consecutively 2 ya zyada IDs** missing hon (jaise 104 aur 105 dono missing), to ye query kya dikhayegi — poori range ya sirf shuruaat?
- Recursive CTE (Q44 wala concept) use karke "expected range ke saare numbers generate karo, phir existing se compare karo" approach kaise likhoge?
- Ye technique bade tables (lakhon rows) pe kitni efficient hai?

**Follow-up Solutions:**
- **2+ consecutive missing IDs (jaise 104, 105 dono):** Ye query **sirf gap ki shuruaat** dikhayegi (104), poori missing range (104, 105 dono) nahi — kyunki hum sirf check kar rahe hain "kya agla ID missing hai," hum ye nahi track kar rahe "kitne consecutive IDs missing hain." Agar poori missing-range chahiye (jaise "104 se 105 tak missing"), to zyada sophisticated approach chahiye — jaise ek **numbers-generating recursive CTE** se poori expected sequence banakar, `LEFT JOIN + IS NULL` (Q29 wala anti-join pattern) se actual table ke against compare karna.
- **Recursive CTE se poori range generate karke approach:**
  ```sql
  WITH RECURSIVE expected_ids AS (
      SELECT MIN(employee_id) AS id FROM Employees
      UNION ALL
      SELECT id + 1 FROM expected_ids WHERE id + 1 <= (SELECT MAX(employee_id) FROM Employees)
  )
  SELECT ex.id AS missing_id
  FROM expected_ids ex
  LEFT JOIN Employees e ON ex.id = e.employee_id
  WHERE e.employee_id IS NULL;
  ```
  Ye recursive CTE (Q44 wala concept) `MIN` se `MAX` employee_id tak **saare possible numbers generate** karta hai (chahe wo exist karein ya nahi), phir `LEFT JOIN + IS NULL` (Q29 wala anti-join pattern) se sirf unhi ko dikhata hai jo actual `Employees` table mein exist nahi karte. Ye approach **poori missing-range** correctly dikhata hai, chahe kitne bhi consecutive IDs missing hon.
- **Bade tables pe efficiency:** Self-join wala approach (pehla solution) generally **efficient** hai chhote-medium gaps ke liye, kyunki ye sirf existing rows pe kaam karta hai. Recursive-CTE-se-poori-range-generate-karne wala approach, agar ID range **bahut bada** ho (jaise lakhon IDs), to **saari intermediate numbers generate** karna khud resource-intensive ho sakta hai — is case mein ek dedicated "numbers table" (pre-generated reference table 1 se N tak) rakhna aur usse join karna, real-production mein zyada efficient practice hai recursive CTE se har baar sequence generate karne ke bajaye.

---

### Q65. Do alag lists mein common IDs dhundo (`INTERSECT` logic)

**Interview mein aise poocha jaayega:**
> "You have two lists of IDs — List_A and List_B. Find the IDs that are common to both."

**Thought Process:**
- Ye ek generic set-operations question hai — humare business schema se directly related nahi, isliye ek **standalone scratch example** use karenge.
- Keyword pakdo: **"common to both"** → classic **intersection** — MySQL 8.0.31+ mein native `INTERSECT` support hai, lekin purane versions/interviewers ke liye portable alternative bhi jaanna zaroori hai.

**Scratch Example:**
```sql
List_A(id): 1, 2, 3, 4, 5
List_B(id): 3, 4, 5, 6, 7
```

**Solution Query (MySQL 8.0.31+ — native syntax):**
```sql
SELECT id FROM List_A
INTERSECT
SELECT id FROM List_B;
```

**Solution Query (Portable — kaam karta hai purane MySQL versions aur zyadatar databases mein):**
```sql
SELECT DISTINCT a.id
FROM List_A a
INNER JOIN List_B b ON a.id = b.id;
```

**Line-by-Line:**
- **Native version:** `INTERSECT` directly dono `SELECT` ke result-sets ka **common overlap** deta hai — bilkul `UNION` jaisi syntax, bas logic "common rows" hai "sab rows combine" ki jagah.
- **Portable version:** `INNER JOIN a.id = b.id` sirf wahi IDs match karega jo **dono tables mein exist** karte hain — jo effectively intersection jaisa hi result deta hai. `DISTINCT` zaroori hai agar List_A ya List_B mein khud duplicate IDs ho (taaki result mein bhi duplicate na aaye).

**Naya Concept — `INTERSECT` (MySQL 8.0.31+) aur Portable Alternative:**
"Sir, `INTERSECT` (aur `EXCEPT`) MySQL mein **version 8.0.31 (October 2022) mein add hua** — usse pehle MySQL mein ye set-operators available hi nahi the, sirf `UNION` tha. Isliye purane systems ya interviewers jo shayad is naye addition se familiar na hon, unke liye `INNER JOIN` (intersection ke liye) ya `LEFT JOIN + IS NULL` (Q66 mein, difference ke liye) jaanna **zaroori** hai — ye har database mein hamesha kaam karega, dialect-independent hai."

**Expected Output (is scratch example pe):** 3, 4, 5.

**Follow-up Questions:**
- MySQL ka `INTERSECT` PostgreSQL/Oracle ke `INTERSECT` se same hai kya?
- `INNER JOIN` wala approach `IN`/`EXISTS`-based approach se kaise compare karta hai?
- Agar List_A mein duplicate IDs hon (jaise `1,1,2,3`), to `INTERSECT` aur `INNER JOIN` (bina DISTINCT ke) ka behaviour kya hoga?

**Follow-up Solutions:**
- **Cross-database `INTERSECT` compatibility:** Haan, `INTERSECT` ka basic behaviour (common rows dono result-sets mein) **ANSI SQL standard** hai, aur PostgreSQL, Oracle, SQL Server, aur ab MySQL (8.0.31+) — sab isko essentially **same tarike se** implement karte hain. Minor syntax variations ho sakte hain (jaise kuch databases mein `INTERSECT ALL` bhi available hai jo duplicates ko bhi properly handle karta hai, na ki automatically distinct kar de).
- **`INNER JOIN` vs `IN`/`EXISTS`-based approach:**
  ```sql
  -- IN-based alternative
  SELECT DISTINCT id FROM List_A WHERE id IN (SELECT id FROM List_B);
  ```
  Ye bhi same result deta hai. `INNER JOIN` aur `IN`-subquery dono **functionally equivalent** hain is use-case ke liye — modern optimizers dono ko often similar execution plans mein convert kar dete hain. `EXISTS`-based version bhi kaam karega:
  ```sql
  SELECT DISTINCT a.id FROM List_A a WHERE EXISTS (SELECT 1 FROM List_B b WHERE b.id = a.id);
  ```
  Teeno approaches (JOIN, IN, EXISTS) valid hain — choice largely readability aur team-convention pe depend karti hai.
- **Duplicates List_A mein (jaise `1,1,2,3`):** `INTERSECT` (standard SQL behaviour) **automatically duplicates remove** kar deta hai result mein (jaise `UNION` karta hai) — isliye `1` sirf **ek baar** dikhega result mein, chahe List_A mein kitni baar ho. `INNER JOIN` **bina `DISTINCT`** ke, agar List_A mein `1` do baar hai, to result mein bhi `1` **do baar** dikhega (ek match per List_A row) — isliye `INNER JOIN` approach mein `DISTINCT` explicitly likhna **zaroori** hai agar exact `INTERSECT`-jaisa (duplicate-free) result chahiye.

---

### Q66. Table A mein present, Table B mein absent records dhundo (`EXCEPT`/Anti-Join)

**Interview mein aise poocha jaayega:**
> "Find the IDs that exist in List_A but not in List_B."

**Thought Process:**
- Ye Q65 ka hi "opposite" hai — intersection (common) ki jagah ab **difference** (A minus B) chahiye.
- Keyword pakdo: **"exist in A but not in B"** → `EXCEPT` (native, MySQL 8.0.31+) ya `LEFT JOIN + IS NULL` (portable anti-join, Q29/Q37 wala hi concept).

**Solution Query (MySQL 8.0.31+ — native syntax):**
```sql
SELECT id FROM List_A
EXCEPT
SELECT id FROM List_B;
```

**Solution Query (Portable — anti-join):**
```sql
SELECT a.id
FROM List_A a
LEFT JOIN List_B b ON a.id = b.id
WHERE b.id IS NULL;
```

**Line-by-Line:**
- **Native:** `EXCEPT` List_A ke saare results deta hai **minus** wo jo List_B mein bhi hain — Set-theory ka "A - B" (A minus B) operation.
- **Portable:** Bilkul Q29/Q37 wala hi anti-join pattern — `LEFT JOIN` se List_A ki saari rows guaranteed rakho, phir `WHERE b.id IS NULL` se sirf unhi ko filter karo jinka List_B mein koi match nahi mila.

**Naya Concept — `EXCEPT` (aur Oracle mein `MINUS`):**
"MySQL ka `EXCEPT` (8.0.31+) Oracle ke `MINUS` keyword jaisa hi kaam karta hai — dono 'first query minus second query' ka concept represent karte hain, bas keyword ka naam alag hai database ke hisaab se. Interview mein agar Oracle-context mein poochha jaaye, to `MINUS` bolna sahi hoga; MySQL-context mein `EXCEPT`."

**Expected Output (is scratch example pe):** 1, 2 (kyunki 3,4,5 dono lists mein hain, sirf 1,2 List_A mein exclusively hain).

**Follow-up Questions:**
- `EXCEPT`/`MINUS` aur `NOT IN` mein farak kya hai, aur `NOT IN` yaha use karne mein kya risk hai (Q37 wala NULL-trap yaad karo)?
- Agar humein **dono directions** ka difference chahiye ho (A-not-in-B, AND B-not-in-A dono ek saath, ek "symmetric difference"), to kaise karoge?
- List_B mein agar NULL id ho, to `LEFT JOIN + IS NULL` approach affect hoga kya?

**Follow-up Solutions:**
- **`EXCEPT`/`MINUS` vs `NOT IN`:** Dono conceptually same result de sakte hain, lekin `NOT IN` mein **wahi NULL-trap** hai jo Q37 mein dekha tha — agar `List_B.id` mein koi NULL ho, `WHERE id NOT IN (SELECT id FROM List_B)` **poori tarah break** ho jaayega (empty result dega, chahe genuine differences hon). `EXCEPT`/`MINUS` aur `LEFT JOIN + IS NULL` dono is NULL-trap se **immune** hain, isliye inhe generally **safer default** maana jaata hai `NOT IN` ke muqable jab column mein NULL possible ho.
- **Symmetric difference (dono directions):**
  ```sql
  (SELECT id FROM List_A EXCEPT SELECT id FROM List_B)
  UNION
  (SELECT id FROM List_B EXCEPT SELECT id FROM List_A);
  ```
  Ye "A mein hai B mein nahi" **aur** "B mein hai A mein nahi" dono results ko `UNION` se combine karta hai — final result un saari IDs ki hai jo **sirf ek** list mein hain (dono mein common wali exclude ho jaati hain) — set-theory mein isko "symmetric difference" kehte hain.
- **List_B mein NULL id hone par:** `LEFT JOIN + IS NULL` approach is case mein bhi **safe** rehta hai — kyunki `ON a.id = b.id` comparison mein agar `b.id` NULL hai, to wo row simply **match nahi karegi** (jaisa expected hai, NULL kisi se match nahi karta), aur List_A ki corresponding row (agar uska koi valid match nahi mila) `WHERE b.id IS NULL` mein sahi se pakdi jaayegi. `LEFT JOIN` approach ismein `NOT IN` jaisa vulnerable nahi hai.

---

### Q67. Pivot: har department ki total salary ko rows ki jagah columns mein dikhao

**Interview mein aise poocha jaayega:**
> "Display the total salary of Sales, Engineering, and HR departments as separate columns in a single row (a pivoted view)."

**Thought Process:**
- Ye Q22 (male/female conditional aggregation) ka hi **generalized application** hai, department-wise.
- Keyword pakdo: **"as separate columns in a single row"** → conditional aggregation (`SUM(CASE WHEN...)`) pattern, ek column per department.

**Solution Query:**
```sql
SELECT
    SUM(CASE WHEN department_id = 10 THEN salary ELSE 0 END) AS sales_total,
    SUM(CASE WHEN department_id = 20 THEN salary ELSE 0 END) AS engineering_total,
    SUM(CASE WHEN department_id = 30 THEN salary ELSE 0 END) AS hr_total
FROM Employees;
```

**Line-by-Line:**
- Har `SUM(CASE WHEN department_id = X THEN salary ELSE 0 END)` — Har row ke liye, agar department match kare to uski salary count hoti hai, warna 0 — phir sabko jod diya jaata hai. Ye Q22 wala hi exact pattern hai, bas boolean-count ki jagah **salary-sum** ho raha hai.
- Koi `GROUP BY` nahi — poori table ek hi implicit group hai, isliye ek hi row output mein aati hai.

**Naya Concept (recap) — Manual Pivot Pattern Generalize Hota Hai Kisi Bhi Aggregate Ke Liye:**
"Q22 mein humne conditional-aggregation se **count** nikaala tha (`SUM(CASE WHEN...THEN 1...)`), yaha humne **sum** nikaala (`SUM(CASE WHEN...THEN salary...)`) — pattern bilkul same hai, sirf `CASE WHEN` ke andar `1` ki jagah actual column (`salary`) daal diya. Ye pattern kisi bhi aggregate function (COUNT, SUM, AVG, MAX) ke saath is tarah generalize ho sakta hai."

**Expected Output (is sample data pe):** sales_total=177000, engineering_total=152000, hr_total=150000.

**Follow-up Questions:**
- Agar departments ki list bahut badi ho (jaise 50 departments), to ye manual-pivot approach practical rahega kya?
- `ELSE 0` na likhein to kya farak padega (`SUM` ke context mein)?
- Isi output ko normal rows-wise (pivot ke bina) bhi dikhana ho **saath mein**, to kaise karoge?

**Follow-up Solutions:**
- **50 departments hone par practicality:** Nahi, manual pivot (har department ke liye ek hardcoded `CASE WHEN` column) **50 departments ke liye impractical** ho jaata hai — query bahut lambi aur unmaintainable ban jaati (50 `SUM(CASE WHEN...)` lines!). Bade N ke liye, better approaches hain: (1) application-layer pivoting (query normal `GROUP BY` se rows return kare, application code — Python/Excel/BI-tool — usse columns mein convert kare), (2) dynamic SQL (Q64/PL-SQL guide wala `EXECUTE IMMEDIATE` concept) jo runtime pe departments ki list dekh kar query **generate** kare, ya (3) kuch databases mein built-in `PIVOT` operator hota hai (SQL Server mein hai, MySQL mein nahi) jo syntax ko cleaner banata hai bade N ke liye bhi.
- **`ELSE 0` na likhne ka farak:** Agar `ELSE 0` hata diya (sirf `CASE WHEN department_id = 10 THEN salary END`), to non-matching rows ke liye result **NULL** hoga (`ELSE` na hone par implicit `ELSE NULL` hota hai) — lekin `SUM()` **NULL ko automatically ignore** kar deta hai calculation mein (jaisa humne Q16/Q39 mein dekha) — isliye final result **same hi aayega** chahe `ELSE 0` ho ya na ho, `SUM` ke context mein ye farak nahi padta. (Note: agar `COUNT(CASE WHEN...)` use kar rahe hote instead of `SUM`, tab `ELSE 0` vs `ELSE NULL`/no-ELSE ka farak padta — Q22 ke follow-up mein ye already discuss kiya tha.)
- **Pivot aur normal rows-wise ek saath:** Technically ek hi query se dono formats simultaneously nahi de sakte (ya to pivoted-single-row chahiye, ya normal grouped-rows) — lekin do alag queries chala sakte ho (ek `GROUP BY department_id` wali normal version, ek ye pivoted version), ya application-layer mein ek hi normal `GROUP BY` result fetch karke, application code mein hi dono representations (rows-wise table, aur pivoted summary card) bana sakte ho — SQL query khud generally sirf ek format return karti hai per call.

---

### Q68. Har product ka revenue, total revenue ka kitna % hai

**Interview mein aise poocha jaayega:**
> "Find the percentage contribution of each product's revenue to the total revenue."

**Thought Process:**
- Ye Q56 (salary % of department total) jaisa hi pattern hai, bas product-revenue context mein, aur "total" yaha **poori company** hai (koi partition/grouping nahi, seedha overall total).
- Keyword pakdo: **"percentage contribution ... to the total"** → `SUM(amount)` per product, divide by overall total.

**Solution Query:**
```sql
SELECT p.product_name, SUM(o.amount) AS product_revenue,
       ROUND(SUM(o.amount) * 100.0 / (SELECT SUM(amount) FROM Orders), 2) AS pct_of_total
FROM Orders o
JOIN Products p ON o.product_id = p.product_id
GROUP BY p.product_name;
```

**Line-by-Line:**
- `JOIN Products p ON o.product_id = p.product_id` — Orders ko Products se jodkar product_name laate hain (Q20 wala hi pattern).
- `GROUP BY p.product_name` — Product-wise total revenue nikaalte hain.
- `(SELECT SUM(amount) FROM Orders)` — Ye ek **non-correlated subquery** hai (Q33 wala concept) jo **poori** Orders table ka total revenue ek baar nikalti hai, independently.
- `SUM(o.amount) * 100.0 / (...)` — Har product ka revenue, us grand-total se divide karke percentage.

**Naya Concept — Revenue-Contribution Percentage Pattern (Business Analytics ka Bread-and-Butter):**
"Ye ek **extremely common business-analytics query** hai — 'kaunsa product/customer/region sabse zyada revenue laata hai, percentage terms mein' — retail, e-commerce, SaaS sab jagah ye pattern use hota hai (jaise Pareto/80-20 analysis ka foundation)."

**Expected Output (is sample data pe):** Total orders amount = 1200+800+500+300+700 = 3500. Widget A(P1) = 1200+300=1500 (42.86%). Widget B(P2) = 800+700=1500 (42.86%). Gadget C(P3) = 500 (14.28%). *(Gadget D aur Gadget E kabhi order nahi hue, isliye INNER JOIN ki wajah se wo is list mein hain hi nahi.)*

**Follow-up Questions:**
- Products jinka kabhi order nahi hua (Gadget D, Gadget E), unhe bhi "0%" ke saath is list mein kaise dikhaoge?
- Isi query ko window function (`SUM() OVER ()`) se rewrite karo, subquery ke bina.
- Percentage ko highest-contributing product se sort karke dikhao.

**Follow-up Solutions:**
- **Never-ordered products ko 0% ke saath include karna:**
  ```sql
  SELECT p.product_name, COALESCE(SUM(o.amount), 0) AS product_revenue,
         ROUND(COALESCE(SUM(o.amount), 0) * 100.0 / (SELECT SUM(amount) FROM Orders), 2) AS pct_of_total
  FROM Products p
  LEFT JOIN Orders o ON p.product_id = o.product_id
  GROUP BY p.product_name;
  ```
  `FROM Products` se shuru kiya (na ki `Orders` se) aur `LEFT JOIN Orders` kiya — taaki Products ki saari rows guaranteed rahein (Q29 wala concept). `COALESCE(SUM(o.amount), 0)` — agar kisi product ka koi order nahi (SUM NULL aayega, kyunki koi rows hi nahi the sum karne ko), to use explicitly `0` bana rahe hain, taaki percentage bhi cleanly `0.00%` calculate ho (na ki NULL).
- **Window function se rewrite (subquery ke bina):**
  ```sql
  SELECT DISTINCT p.product_name,
         SUM(o.amount) OVER (PARTITION BY p.product_name) AS product_revenue,
         ROUND(
             SUM(o.amount) OVER (PARTITION BY p.product_name) * 100.0
             / SUM(o.amount) OVER (), 2
         ) AS pct_of_total
  FROM Orders o
  JOIN Products p ON o.product_id = p.product_id;
  ```
  `SUM(o.amount) OVER (PARTITION BY p.product_name)` — per-product total (jaisa Q56 mein dept-total tha). `SUM(o.amount) OVER ()` — **bina kisi `PARTITION BY` ke**, poori table ka grand-total deta hai (Q56 ke follow-up mein ye concept touch kiya tha). `DISTINCT` isliye zaroori hai kyunki window function rows collapse nahi karti, isliye har product multiple baar (ek per order) dikhega bina DISTINCT ke.
- **Highest-contributing se sort:**
  ```sql
  ... (same query as first solution) ...
  ORDER BY pct_of_total DESC;
  ```
  Bas ek `ORDER BY pct_of_total DESC` add karna hai — outer query ke end mein.

---

### Q69. Customers jinhone Products table ke saare products order kiye hon (Relational Division)

**Interview mein aise poocha jaayega:**
> "Find customers who have ordered every single product in the Products table."

**Thought Process:**
- **Important note:** Humare real Orders data mein koi customer aisa nahi hai jisne saare 5 products order kiye hon (C1 ne P1,P2; C2 ne P3,P2; C3 ne P1 order kiya) — is question ke liye ek **hypothetical extension** use karenge demonstration ke liye.
- Keyword pakdo: **"every single product"** → ye ek classic **"relational division"** problem hai — SQL mein iska koi direct operator nahi hai (kuch languages mein `DIVIDE` hota hai, SQL mein nahi), isliye humein **count-matching technique** se simulate karna padta hai.

**Scratch Extension (sirf demonstration ke liye):**
Maan lo ek naya customer **C5** hai jisne **saare 5 products** (P1, P2, P3, P4, P5) order kiye hain.

**Solution Query:**
```sql
SELECT o.customer_id
FROM Orders o
GROUP BY o.customer_id
HAVING COUNT(DISTINCT o.product_id) = (SELECT COUNT(*) FROM Products);
```

**Line-by-Line:**
- `GROUP BY o.customer_id` — Har customer ke orders ko group karte hain.
- `COUNT(DISTINCT o.product_id)` — Har customer ne **kitne alag-alag (distinct) products** order kiye hain — `DISTINCT` zaroori hai kyunki agar customer ne same product 2 baar order kiya ho, hume unique product-count chahiye, order-count nahi.
- `HAVING COUNT(DISTINCT o.product_id) = (SELECT COUNT(*) FROM Products)` — Sirf wo customers rakho jinka distinct-product-count **poori Products table ke total product-count ke exactly barabar** ho — matlab unhone **har product** order kiya hai, ek bhi miss nahi kiya.

**Naya Concept — Relational Division ("For All" Queries):**
"Ye pattern **'for all' semantics** represent karta hai — 'X ne **har** Y ko satisfy kiya', jabki zyadatar SQL queries **'exists'** semantics (kam se kam ek match) use karte hain. SQL mein koi direct 'for all' operator na hone ki wajah se, hum ise **count-matching trick** se simulate karte hain: 'agar customer ke distinct-matches ki count, total-required-items ki count ke barabar hai, to matlab usne sab cover kar liya.' Ye pattern thoda tricky hai isliye **advanced-level** interview question maana jaata hai."

**Expected Output (agar C5 wala scratch data include karein):** C5 (kyunki usne saare 5 products order kiye).

**Follow-up Questions:**
- Ye query kaam nahi karegi agar Products table mein koi **naya product add** ho jaaye jo abhi tak kisi ne order nahi kiya — kyun?
- Agar requirement ho "customers jinhone **Electronics category ke saare products** order kiye" (na ki poori Products table ke), to query kaise badlegi?
- `COUNT(DISTINCT o.product_id)` ki jagah `COUNT(o.product_id)` (bina DISTINCT) use karte to kya galat ho sakta tha?

**Follow-up Solutions:**
- **Naya product add hone se automatically outdated:** Haan, ye ek **inherent characteristic** hai is pattern ki, koi bug nahi — agar Products table mein 6th product add ho jaaye (jise abhi tak kisi ne order nahi kiya), to `(SELECT COUNT(*) FROM Products)` automatically 6 ho jaayega — aur ab **koi bhi customer** (chahe pehle "sab products" order kar chuka ho) is naye, higher threshold ko match nahi karega jab tak wo naya product bhi order na kare. Ye **correct, expected behaviour** hai — query dynamically "current total products" ke against check karti hai, hardcoded number ke against nahi.
- **Sirf Electronics category ke products ke liye:**
  ```sql
  SELECT o.customer_id
  FROM Orders o
  JOIN Products p ON o.product_id = p.product_id
  WHERE p.category = 'Electronics'
  GROUP BY o.customer_id
  HAVING COUNT(DISTINCT o.product_id) = (
      SELECT COUNT(*) FROM Products WHERE category = 'Electronics'
  );
  ```
  Do jagah `WHERE p.category = 'Electronics'` filter add karna padega — ek outer query mein (sirf Electronics orders consider karo), ek inner subquery mein (denominator bhi sirf Electronics products ka total ho, na ki poori Products table ka).
- **`COUNT(DISTINCT)` na use karne ka risk:** Agar `COUNT(o.product_id)` (bina DISTINCT) use karte, aur koi customer ne **same product multiple baar** order kiya ho (jaise ek product 3 baar order kiya, baaki kabhi nahi), to uski `COUNT` galti se **badi** dikh sakti thi (jaise agar usne ek hi product 5 baar order kiya ho aur total products bhi 5 hi hon, to `COUNT(o.product_id) = 5` **galat se match ho jaata** `HAVING` condition ko, jabki usne actually sirf **ek hi distinct product** order kiya tha, baaki 4 miss the) — ye ek **serious correctness bug** hota, isliye `DISTINCT` yaha absolutely mandatory hai.

---

### Q70. 3 consecutive months jaha revenue continuously badh raha tha

**Interview mein aise poocha jaayega:**
> "Find any 3 consecutive months where revenue showed continuous growth (each month higher than the previous)."

**Thought Process:**
- **Important note:** Humara real Orders data (Jan=1700, Feb=1500, Mar=300) mein revenue **ghat** raha hai, badh nahi raha — is pattern ko demonstrate karne ke liye ek **hypothetical extended monthly-revenue dataset** use karenge.
- Keyword pakdo: **"3 consecutive months ... continuous growth"** → ye "compare current with previous **2 steps**" wala pattern hai — `LAG(revenue, 1)` aur `LAG(revenue, 2)` dono chahiye, phir ek condition jo teeno ko compare kare.

**Scratch Example (hypothetical monthly revenue, demonstration ke liye):**
| month_no | revenue |
|---|---|
| 1 (Jan) | 1700 |
| 2 (Feb) | 1500 |
| 3 (Mar) | 300 |
| 4 (Apr) | 2000 |
| 5 (May) | 2500 |
| 6 (Jun) | 3200 |
| 7 (Jul) | 2800 |

**Solution Query:**
```sql
WITH monthly AS (
    SELECT month_no, revenue,
           LAG(revenue, 1) OVER (ORDER BY month_no) AS prev_1,
           LAG(revenue, 2) OVER (ORDER BY month_no) AS prev_2
    FROM Monthly_Revenue
)
SELECT month_no, revenue
FROM monthly
WHERE revenue > prev_1 AND prev_1 > prev_2;
```

**Line-by-Line:**
- `LAG(revenue, 1)` — Ek mahina pichhe ka revenue (seedha pichla mahina).
- `LAG(revenue, 2)` — **Do mahine** pichhe ka revenue — `LAG` ka second parameter batata hai "kitni rows peeche jaana hai" (Q52 ke follow-up mein ye concept dekha tha).
- `WHERE revenue > prev_1 AND prev_1 > prev_2` — Ye check karta hai ki **teeno consecutive mahine strictly increasing order** mein hain — current > pichla, aur pichla > uske bhi pichla. Agar dono conditions true hain, matlab humein ek "3-month upward streak" ka **aakhri mahina** mil gaya.

**Naya Concept — Multi-Step `LAG` for Multi-Period Trend Detection:**
"Ye pattern `LAG(column, N)` ko **multiple different N values** ke saath use karta hai (yaha 1 aur 2) ek saath, taaki ek **multi-period trend** (na ki sirf adjacent-pair comparison) detect ho sake. Isi pattern ko N=3,4,5... tak extend karke, "4 consecutive increasing months," "5 consecutive increasing months," etc. bhi detect kiye ja sakte hain — bas utne hi extra `LAG` calls aur `AND` conditions chahiye honge."

**Expected Output (scratch example pe):** Apr(2000) > Mar(300) > Feb(1500)? — Nahi, Mar(300) > Feb(1500) false hai, isliye Apr qualify nahi karta. May(2500) > Apr(2000) > Mar(300)? — Haan, dono true, **May qualifies**. Jun(3200) > May(2500) > Apr(2000)? — Haan, **Jun qualifies**. Jul(2800) > Jun(3200)? — Nahi (2800 < 3200), Jul qualify nahi karta. **Result: May aur Jun — ye batate hain ki Apr→May→Jun ek 3-month upward streak thi, aur Mar→Apr→May bhi ek streak thi jise May flagged karta hai।**

**Follow-up Questions:**
- Is result mein "May" aur "Jun" dikh rahe hain individual months ki tarah — agar humein poori streak ka **range** ("Mar se Jun tak") ek single row mein dikhana ho, to approach kaise badlegi?
- 4 consecutive increasing months detect karne ke liye query kaise extend karoge?
- Agar beech mein koi mahina **missing** ho data mein (jaise Apr ka data hi na ho), to `LAG(revenue, 2)` ka calculation kya galat kar sakta hai?

**Follow-up Solutions:**
- **Poori streak ko ek range ki tarah dikhana:** Ye Q63 wala **Gaps-and-Islands** pattern se combine karna padega — pehle "increasing/not-increasing" ka ek boolean flag banao har month ke liye, phir consecutive "increasing" months ko islands mein group karo (row-number-minus-technique se), phir har island ka `MIN(month)` aur `MAX(month)` nikaalo. Ye do advanced patterns (multi-step LAG + Gaps-and-Islands) ka **combination** hai — is level ka question genuinely senior/advanced interview mein hi expect hota hai.
- **4 consecutive increasing months:**
  ```sql
  WITH monthly AS (
      SELECT month_no, revenue,
             LAG(revenue, 1) OVER (ORDER BY month_no) AS prev_1,
             LAG(revenue, 2) OVER (ORDER BY month_no) AS prev_2,
             LAG(revenue, 3) OVER (ORDER BY month_no) AS prev_3
      FROM Monthly_Revenue
  )
  SELECT month_no, revenue
  FROM monthly
  WHERE revenue > prev_1 AND prev_1 > prev_2 AND prev_2 > prev_3;
  ```
  Bas ek aur `LAG(revenue, 3)` aur ek aur `AND` condition add karni hai — pattern linearly extend hota hai jitne bhi consecutive periods chahiye.
- **Missing month ka effect:** Agar beech mein koi mahina (jaise Apr) data mein hi missing ho (koi row hi na ho us mahine ki), to `LAG(revenue, 1) OVER (ORDER BY month_no)` **row-position** ke hisaab se kaam karta hai, calendar-date ke hisaab se nahi — matlab agar Apr missing hai, to May ke liye `LAG(revenue,1)` **Mar** ka revenue dega (kyunki Mar hi "ek row peeche" hai ab), na ki "Apr ka revenue" (jo exist hi nahi karta) — is se **galat trend-detection** ho sakta hai, kyunki humara code assume kar raha hai ki consecutive rows = consecutive calendar months, jo tab tak sahi hai jab tak data mein koi gap na ho। Isliye production mein aisi queries se pehle **data-completeness verify** karna (jaise Q64 wala gap-detection) ek important pre-step hai।

---

## Quick Revision Index (concept jaha pehli baar aaya — Q33-70)

| Concept | Question |
|---|---|
| Non-correlated subquery in `WHERE` | Q33 |
| Correlated subquery | Q34 |
| Nested `MAX`/`MIN` trick for Nth-highest | Q35, Q36 |
| `NOT IN` NULL-trap | Q37 |
| `NOT EXISTS` (NULL-safe alternative) | Q37, Q38 |
| NULL's dual effect (ignore in aggregate, exclude in comparison) | Q39 |
| NULL-safe equality operator (`<=>`) | Q40 |
| CTE (`WITH` clause) | Q41 |
| `CROSS JOIN` with single-row CTE (scalar broadcasting) | Q42 |
| CTE as pre-filter (filter-then-join) | Q43 |
| Recursive CTE (`WITH RECURSIVE`) | Q44 |
| Recursive CTE with custom anchor (subtree traversal) | Q45 |
| Window functions basics (`OVER`, `ROW_NUMBER`) | Q46 |
| `RANK()` vs `DENSE_RANK()` (skip vs no-skip) | Q47 |
| Window function can't be used directly in `WHERE` | Q48 |
| Running total (`SUM() OVER (ORDER BY...)`) | Q50 |
| `PARTITION BY` + `ORDER BY` (per-group running total) | Q51 |
| `LAG()` / `LEAD()` | Q52 |
| Top-1-per-group pattern | Q53 |
| Aggregate-then-window (CTE + LAG) | Q54 |
| `DATEDIFF` / gap detection | Q55 |
| Window function without `ORDER BY` (group-total broadcast) | Q56 |
| `ROW_NUMBER()` for deduplication | Q57 |
| Find-members-of-duplicate-groups pattern | Q58 |
| `LIMIT 1` tie-handling gotcha | Q59 |
| **Gaps-and-Islands technique** (row-number minus date) | Q63 |
| Self-join gap-detection (missing sequence IDs) | Q64 |
| `INTERSECT` (MySQL 8.0.31+) + portable `INNER JOIN` alternative | Q65 |
| `EXCEPT`/`MINUS` + portable anti-join alternative | Q66 |
| Manual pivot generalized (SUM instead of COUNT) | Q67 |
| Revenue-contribution percentage pattern | Q68 |
| **Relational Division** ("for all" queries) | Q69 |
| Multi-step `LAG` for multi-period trend detection | Q70 |

---

## Kaise practice karo isse

1. Pehle `SQL_Interview_Practice_Q1-Q32.md` revise karo — subqueries/joins/CTE ke liye foundational concepts wahi se aate hain.
2. Har question yaha bhi: pehle khud query likho (sirf Interview Question + Thought Process dekh kar), phir Solution Query se compare karo.
3. Jin questions mein **scratch/hypothetical data** use hua hai (Q57, Q64, Q65, Q66, Q69, Q70) — inka concept real business data se independent hai, isliye pattern yaad rakho, exact numbers nahi.
4. Category 7-8 (Window Functions, Advanced Patterns) sabse zyada senior/differentiator questions hain — agar interview mid-to-senior level ka hai, inhi pe sabse zyada time do.
5. **Gaps-and-Islands (Q63)** aur **Relational Division (Q69)** — ye dono patterns bahut advanced hain, agar pehli baar mein samajh na aaye to normal hai; inhe multiple baar dobara padhna.
