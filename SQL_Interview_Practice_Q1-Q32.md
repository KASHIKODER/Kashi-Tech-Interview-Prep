# SQL Interview Practice — Full Solutions (Category 1-4, Q1-Q32)
### Har question: interview-phrasing → thought process → query → line-by-line (interviewer explanation) → naya concept → follow-ups

> **Dialect:** MySQL 8.x. Jahan Oracle/SQL Server syntax alag hoga, wahan short note diya jaayega.
> **Common Schema + Sample Data** — poori file mein isi ko reuse karenge, taaki tumhe har question ke liye naya data yaad na rakhna pade.

```sql
Departments(department_id, department_name)
Employees(employee_id, employee_name, department_id, salary, manager_id, hire_date, gender)
Customers(customer_id, customer_name)
Products(product_id, product_name, category, price)
Orders(order_id, customer_id, order_date, amount, product_id)
Projects(project_id, project_name, employee_id)
```

**Departments**
| department_id | department_name |
|---|---|
| 10 | Sales |
| 20 | Engineering |
| 30 | HR |
| 40 | Marketing |

**Employees**
| employee_id | employee_name | department_id | salary | manager_id | hire_date | gender |
|---|---|---|---|---|---|---|
| 101 | Ravi Kumar | 10 | 75000 | NULL | 2019-03-14 | M |
| 102 | Priya Sharma | 10 | 62000 | 101 | 2020-06-01 | F |
| 103 | Amit Verma | 20 | 90000 | NULL | 2018-11-23 | M |
| 104 | Sneha Rao | 20 | 62000 | 103 | 2021-01-15 | F |
| 105 | Kunal Joshi | 30 | 55000 | 101 | 2022-07-19 | M |
| 106 | Neha Gupta | 30 | 95000 | 105 | 2023-02-10 | F |
| 107 | Arjun Singh | NULL | 48000 | NULL | 2023-05-05 | M |
| 108 | Ananya Iyer | 10 | 40000 | 101 | 2023-08-20 | F |

**Customers**
| customer_id | customer_name |
|---|---|
| C1 | Rakesh Sharma |
| C2 | Aisha Khan |
| C3 | Vikram Mehta |
| C4 | Aarushi Bose |

**Products**
| product_id | product_name | category | price |
|---|---|---|---|
| P1 | Widget A | Electronics | 600 |
| P2 | Widget B | Electronics | 400 |
| P3 | Gadget C | Home | 500 |
| P4 | Gadget D | Home | 250 |
| P5 | Gadget E | Electronics | NULL |

**Orders**
| order_id | customer_id | order_date | amount | product_id |
|---|---|---|---|---|
| O1 | C1 | 2023-01-10 | 1200 | P1 |
| O2 | C1 | 2023-02-15 | 800 | P2 |
| O3 | C2 | 2023-01-20 | 500 | P3 |
| O4 | C3 | 2023-03-05 | 300 | P1 |
| O5 | C2 | 2023-02-25 | 700 | P2 |

**Projects**
| project_id | project_name | employee_id |
|---|---|---|
| PR1 | Website Redesign | 101 |
| PR2 | Data Migration | 103 |
| PR3 | Mobile App | 101 |

*(Note: P4 aur P5 kabhi order nahi hue, C4 ne kabhi order nahi kiya, dept 40 Marketing mein koi employee nahi — ye sab jaan-boojhkar rakha hai kyunki LEFT JOIN/NULL/anti-join wale questions ke liye yehi cases test hoti hain.)*

---

## CATEGORY 1 — BASIC QUERY INTERPRETATION

### Q1. Saare employees ko salary descending order mein dikhao

**Interview mein aise poocha jaayega:**
> "Write a query to display all employee records sorted by salary in descending order."

**Thought Process:**
- Output: poori employee row (sab columns) chahiye — question mein "employees dikhao" bola hai, kisi specific column ka nahi.
- Source: sirf ek table — `Employees`. Koi join/filter/group ki zaroorat nahi.
- Keyword pakdo: **"descending order"** → seedha signal deta hai `ORDER BY column DESC` use karne ka.

**Solution Query:**
```sql
SELECT *
FROM Employees
ORDER BY salary DESC;
```

**Line-by-Line (interviewer ko bologe):**
- `SELECT *` — Saare columns chahiye, isliye star use kiya. (Production code mein hum specific columns select karte, lekin yaha requirement hi "sab kuch dikhao" hai.)
- `FROM Employees` — Single table, koi join nahi chahiye.
- `ORDER BY salary DESC` — Sorting **sabse aakhri** mein execute hoti hai (logical processing order), aur `DESC` keyword na likho to default `ASC` (ascending) hota hai — isliye descending ke liye explicitly likhna zaroori hai.

**Naya Concept — `ORDER BY` aur `ASC`/`DESC`:**
`ORDER BY` result set ko sort karta hai. Bina isके, database rows ko **kisi bhi order** mein return kar sakta hai (koi guarantee nahi) — isliye jab bhi order matter kare, `ORDER BY` explicitly likhna chahiye.

**Follow-up Questions:**
- Agar do employees ki salary same ho, to unka aapas mein order kya hoga? *(Answer: undefined/arbitrary — agar consistent order chahiye to secondary sort column add karo, jaise `ORDER BY salary DESC, employee_id ASC`.)*
- Agar `salary` column mein NULL ho to wo row kaha aayegi sorting mein? *(MySQL mein `DESC` order ke saath NULLs sabse pehle aate hain.)*
- Multiple columns pe sort karna ho to kaise likhoge?

---

### Q2. Employees table mein distinct department_ids nikaalo

**Interview mein aise poocha jaayega:**
> "Write a query to fetch the distinct department IDs present in the Employees table."

**Thought Process:**
- Output: sirf ek column — `department_id`, lekin **unique values**.
- Keyword pakdo: **"distinct"** → seedha `DISTINCT` keyword ki taraf point karta hai.
- Filter/join/group ki zaroorat nahi — bas duplicates hatane hain.

**Solution Query:**
```sql
SELECT DISTINCT department_id
FROM Employees;
```

**Line-by-Line:**
- `SELECT DISTINCT department_id` — `DISTINCT` result set se duplicate rows hata deta hai — yaha dept_id 10 teen baar hai (Ravi, Priya, Ananya) lekin output mein sirf ek baar aayega.
- `FROM Employees` — Single table read.

**Naya Concept — `DISTINCT`:**
`DISTINCT` **poori selected row** (yaha sirf ek column hai) par apply hota hai — agar multiple columns select karte to unka **combination** unique hona chahiye tha, individual column nahi.

**Expected Output (is sample data pe):** `10, 20, 30, NULL` — chaar distinct values, kyunki employee 107 ka dept_id NULL hai aur `DISTINCT` NULL ko bhi ek "value" ki tarah treat karta hai (sirf ek baar dikhayega, chahe kitne bhi employees ka dept NULL ho).

**Follow-up Questions:**
- `DISTINCT` aur `GROUP BY` mein farak kya hai jab dono duplicates remove karte lagte hain?
- Agar do columns pe `DISTINCT` lagao (jaise `department_id, gender`), to uniqueness kaise decide hogi?

---

### Q3. `salary` column ko output mein `monthly_salary` naam se dikhao (alias)

**Interview mein aise poocha jaayega:**
> "Display employee_name and salary, but rename the salary column as monthly_salary in the output."

**Thought Process:**
- Output: `employee_name`, aur `salary` column — lekin uska display-naam badalna hai.
- Keyword pakdo: **"rename in output"** → ye column-level naam change hai sirf **result set** mein, actual table structure nahi badal rahe — isliye `ALTER TABLE RENAME COLUMN` nahi, balki `AS` (alias) chahiye.

**Solution Query:**
```sql
SELECT employee_name, salary AS monthly_salary
FROM Employees;
```

**Line-by-Line:**
- `employee_name` — Jaisa hai waisa hi dikhega, koi rename nahi.
- `salary AS monthly_salary` — `AS` keyword se column ko ek **temporary display naam (alias)** de rahe hain — ye sirf is query ke output ke liye hai, actual table ka column naam `salary` hi rahega, database mein kuch permanently change nahi hota.

**Naya Concept — Column Alias (`AS`):**
Alias ka use readability ke liye hota hai — especially jab column naam technical ho (jaise `sal` ya `emp_nm`) aur report mein readable naam chahiye ho, ya jab calculated columns hon (jaise `salary * 12 AS annual_salary`) jinka koi naam hi nahi hota by default.

**Follow-up Questions:**
- `AS` likhna mandatory hai kya? *(Nahi — `salary monthly_salary` (bina AS ke) bhi kaam karta hai MySQL mein, lekin readability ke liye `AS` likhna best practice hai.)*
- Agar alias mein space chahiye (jaise "Monthly Salary"), to kaise likhoge? *(Backticks ya quotes mein: `` AS `Monthly Salary` ``.)*
- Kya `WHERE` clause mein is alias (`monthly_salary`) ko use kar sakte ho?

---

### Q4. Top 5 highest-paid employees dikhao

**Interview mein aise poocha jaayega:**
> "Write a query to display the top 5 highest-paid employees."

**Thought Process:**
- Output: employee records, lekin **sirf 5 rows**, aur wo bhi highest salary walo ki.
- Keyword pakdo: **"top 5"** → do cheezein chahiye: sabse pehle salary ke hisaab se **sort** karo (descending), phir sirf **5 rows tak limit** karo.
- Step order important hai: pehle ORDER BY, phir LIMIT — agar order galat hoga to "top 5" ka matlab hi nahi rahega.

**Solution Query:**
```sql
SELECT *
FROM Employees
ORDER BY salary DESC
LIMIT 5;
```

**Line-by-Line:**
- `ORDER BY salary DESC` — Pehle sabse zyada salary wale employees ko upar laate hain.
- `LIMIT 5` — Sorted result mein se **sirf pehli 5 rows** rakhte hain. `LIMIT` hamesha `ORDER BY` ke **baad** likha jaata hai, aur logical processing mein bhi sबसे last mein execute hota hai (sorting ke baad hi limit lagana meaningful hai).

**Naya Concept — `LIMIT`:**
`LIMIT n` result set ke sirf pehli `n` rows return karta hai. Oracle mein isके equivalent `FETCH FIRST 5 ROWS ONLY` (ya older syntax mein `ROWNUM <= 5`), SQL Server mein `TOP 5` hota hai — ye ek **dialect-specific** keyword hai, isliye interview mein bata dena chahiye "MySQL mein LIMIT hai, Oracle mein FETCH FIRST hai."

**Expected Output (is sample data pe):** Neha(95000), Amit(90000), Ravi(75000), Priya(62000)/Sneha(62000) — inme se koi 2 (tie hai 62000 pe, order undefined unless secondary sort ho), aur Kunal(55000). Top 5 = Neha, Amit, Ravi, aur dono 62000 walo mein se koi ek + Kunal (agar tie tie-break nahi kiya to result thoda inconsistent ho sakta hai — ye khud ek acha discussion point hai).

**Follow-up Questions:**
- Agar 4th aur 5th position pe salary tie ho (jaise yaha 62000 pe Priya aur Sneha), to "top 5" mein kaunsa aayega — kya ye guaranteed hai?
- 6th se 10th tak employees kaise nikaalo? *(Hint: `LIMIT 5 OFFSET 5`.)*
- Isi ko window function (`ROW_NUMBER`) se kaise likhoge? (Category 7 mein cover hoga.)

---

### Q5. Employees jo 2023 mein hire hue the

**Interview mein aise poocha jaayega:**
> "Write a query to find all employees who were hired in the year 2023."

**Thought Process:**
- Output: employee records — filter condition hire_date pe.
- Keyword pakdo: **"in the year 2023"** → date ke saath kaam karna hai, specifically sirf **year part** extract karna hai, poori date compare nahi karni.
- Do tarike hote hain: `YEAR(hire_date) = 2023` (simple, readable) ya `hire_date BETWEEN '2023-01-01' AND '2023-12-31'` (range-based, index-friendly).

**Solution Query:**
```sql
SELECT *
FROM Employees
WHERE YEAR(hire_date) = 2023;
```

**Line-by-Line:**
- `YEAR(hire_date)` — Ye ek **date function** hai jo `hire_date` column se sirf saal (year) nikaalti hai (jaise `2023-08-20` se `2023`).
- `= 2023` — Us extracted year ko `2023` se compare karte hain.

**Naya Concept — Function on Column in WHERE (aur performance ka trade-off):**
`WHERE YEAR(hire_date) = 2023` **readable** hai lekin agar `hire_date` pe index bana hua hai, to ye query **us index ko use nahi karegi** — kyunki database ko har row ke liye pehle `YEAR()` function calculate karni padegi, phir compare karna padega (isko "non-sargable query" kehte hain). Performance-critical jagah pe better approach:
```sql
SELECT *
FROM Employees
WHERE hire_date >= '2023-01-01' AND hire_date < '2024-01-01';
```
Ye range-based version index ko directly use kar sakti hai — **interview mein ye point bolna senior-level maturity dikhata hai.**

**Expected Output (is sample data pe):** Neha Gupta (2023-02-10), Arjun Singh (2023-05-05), Ananya Iyer (2023-08-20) — 3 rows.

**Follow-up Questions:**
- Function-based query aur range-based query mein performance ka farak kyun hota hai?
- Sirf **month** (koi bhi saal ka January) ke employees kaise nikaaloge?
- Kaunse employees **is saal ke pehle quarter** (Jan-Mar) mein hire hue?

---

### Q6. Employees jinka naam 'A' se start ho

**Interview mein aise poocha jaayega:**
> "Write a query to find employees whose name starts with the letter 'A'."

**Thought Process:**
- Output: employee records — filter condition naam ke pehle character pe.
- Keyword pakdo: **"starts with"** → pattern matching chahiye, `LIKE` operator ke saath wildcard.

**Solution Query:**
```sql
SELECT *
FROM Employees
WHERE employee_name LIKE 'A%';
```

**Line-by-Line:**
- `employee_name LIKE 'A%'` — `LIKE` pattern-matching operator hai. `%` ek wildcard hai jiska matlab hai "zero ya usse zyada koi bhi characters" — isliye `'A%'` ka matlab hai "A se start ho, uske baad kuch bhi ho sakta hai."

**Naya Concept — `LIKE` aur Wildcards:**
Do wildcards important hain:
- `%` — zero ya zyada koi bhi characters (jaise `'A%'` = A se start).
- `_` — **exactly ek** character (jaise `'A_i'` = A, koi ek character, phir i — match karega "Ari" ko lekin "Ai" ko nahi, kyunki beech mein exactly ek character chahiye).

**Note:** MySQL mein `LIKE` by default **case-insensitive** hota hai (depend karta hai collation settings pe), jabki Oracle mein `LIKE` by default **case-sensitive** hota hai — ye dialect difference interview mein bolna chahiye.

**Expected Output (is sample data pe):** Amit Verma, Arjun Singh, Ananya Iyer — 3 rows.

**Follow-up Questions:**
- Employees jinka naam 'a' se **end** ho, kaise nikaaloge? *(`LIKE '%a'`.)*
- Employees jinke naam mein 'an' kahin bhi ho, kaise nikaaloge? *(`LIKE '%an%'`.)*
- `LIKE 'A%'` aur `LIKE 'a%'` mein result same aayega ya alag — MySQL vs Oracle mein?

---

## CATEGORY 2 — FILTERING QUESTIONS

### Q7. Employees jinki salary 40000 aur 80000 ke beech ho

**Interview mein aise poocha jaayega:**
> "Find employees whose salary is between 40,000 and 80,000."

**Thought Process:**
- Output: employee records — range-based filter salary pe.
- Keyword pakdo: **"between"** → seedha `BETWEEN` operator ki taraf point karta hai (ya `>=` aur `<=` combine karke bhi likh sakte hain).

**Solution Query:**
```sql
SELECT *
FROM Employees
WHERE salary BETWEEN 40000 AND 80000;
```

**Line-by-Line:**
- `salary BETWEEN 40000 AND 80000` — Ye `salary >= 40000 AND salary <= 80000` ka shorthand hai. **Dono ends inclusive hain** — matlab exactly 40000 ya exactly 80000 wale bhi include honge.

**Naya Concept — `BETWEEN` (Inclusive Range):**
`BETWEEN` hamesha inclusive hota hai — ye common gotcha hai jo interviewer test karta hai: *"Kya BETWEEN exclusive hai ya inclusive?"* → Answer: **inclusive**, dono boundary values shamil hoti hain.

**Expected Output (is sample data pe):** Ravi(75000), Priya(62000), Sneha(62000), Kunal(55000), Arjun(48000), Ananya(40000) — 6 rows. (Amit-90000 aur Neha-95000 exclude ho jaate hain kyunki range se bahar hain.)

**Follow-up Questions:**
- `BETWEEN` ke bina isi query ko kaise likhoge?
- Agar humein **exclusive** range chahiye ho (40000 aur 80000 shamil na ho), to kya likhoge?
- `BETWEEN` dates ke saath bhi use ho sakta hai kya?

---

### Q8. Employees jo department 10, 20, ya 30 mein hon

**Interview mein aise poocha jaayega:**
> "Find all employees who belong to department 10, 20, or 30."

**Thought Process:**
- Output: employee records — multiple specific values match karne hain ek column mein.
- Keyword pakdo: list of specific values ("10, 20, ya 30") → `IN` operator, na ki teen `OR` conditions.

**Solution Query:**
```sql
SELECT *
FROM Employees
WHERE department_id IN (10, 20, 30);
```

**Line-by-Line:**
- `department_id IN (10, 20, 30)` — Internally ye `department_id = 10 OR department_id = 20 OR department_id = 30` jaisa hi hai, lekin `IN` likhna **zyada clean aur readable** hai, especially jab values ki list badi ho.

**Naya Concept — `IN` Operator:**
`IN` ek list ke against match karta hai. Important gotcha: agar list mein `NULL` ho (jaise `IN (10, 20, NULL)`), to `NULL` comparisons mein contribute nahi karta — sirf 10 aur 20 match honge (ye Category 5 mein `NOT IN` ke saath aur bhi critical ban jaata hai).

**Expected Output (is sample data pe):** Arjun Singh (dept NULL) ko chhodkar baaki saare 7 employees.

**Follow-up Questions:**
- `IN` aur multiple `OR` conditions mein performance ka koi farak hota hai?
- `NOT IN (10, 20, 30)` likhne se Arjun Singh (dept NULL) result mein aayega kya?
- Agar list bahut badi ho (jaise 500 department_ids), to `IN` ki jagah kya better approach hai? *(Hint: subquery ya join.)*

---

### Q9. Employees jinka manager_id NULL ho

**Interview mein aise poocha jaayega:**
> "Find all employees who do not have a manager (i.e., top-level employees)."

**Thought Process:**
- Output: employee records — filter jaha manager_id missing ho.
- Keyword pakdo: **"do not have"**, "missing" → `NULL` check chahiye, lekin `= NULL` nahi likh sakte (NULL ek "unknown" hai, koi value nahi jise `=` se compare karein) — `IS NULL` use karna padega.

**Solution Query:**
```sql
SELECT *
FROM Employees
WHERE manager_id IS NULL;
```

**Line-by-Line:**
- `manager_id IS NULL` — `IS NULL` ek special operator hai specifically NULL check karne ke liye. `manager_id = NULL` likhne se query **hamesha empty result** degi (kyunki `NULL = NULL` bhi `UNKNOWN` return karta hai, `TRUE` nahi) — ye ek bahut common beginner mistake hai.

**Naya Concept — `NULL` aur Three-Valued Logic:**
SQL mein comparison ka result sirf `TRUE`/`FALSE` nahi hota, teesra state bhi hai: `UNKNOWN`. Jab bhi `NULL` kisi comparison operator (`=`, `!=`, `>`, etc.) mein involve hota hai, result `UNKNOWN` aata hai — aur `WHERE` clause sirf `TRUE` wali rows rakhta hai, `UNKNOWN` ko discard kar deta hai. Isliye NULL check ke liye special operators `IS NULL` / `IS NOT NULL` hi use karne padte hain.

**Expected Output (is sample data pe):** Ravi Kumar, Amit Verma — 2 rows (ye company ke top-level/senior-most employees hain).

**Follow-up Questions:**
- `manager_id = NULL` likhne se kya hoga — error aayega ya khaali result?
- Employees jinka manager **hai** (NULL nahi), kaise nikaaloge?
- Ye query business-sense mein kya represent karti hai? *(CEO/top-level employees.)*

---

### Q10. Employees jo pichle 90 dinon mein hire hue

**Interview mein aise poocha jaayega:**
> "Find employees who were hired in the last 90 days."

**Thought Process:**
- Output: employee records — relative date filter, "aaj" ke reference se.
- Keyword pakdo: **"last 90 days"** → current date se **dynamically** calculate karna hai, hardcoded date nahi likhni.

**Solution Query:**
```sql
SELECT *
FROM Employees
WHERE hire_date >= CURDATE() - INTERVAL 90 DAY;
```

**Line-by-Line:**
- `CURDATE()` — Aaj ki date **dynamically** deta hai (query jab bhi chale, us din ki date use hogi — hardcode nahi hai).
- `CURDATE() - INTERVAL 90 DAY` — Current date se 90 din **peeche** ki date calculate karta hai.
- `hire_date >= ...` — Sirf wo employees jinka hire_date is calculated date ke baad (ya barabar) hai.

**Naya Concept — Relative Date Filtering (`CURDATE()`, `INTERVAL`):**
Ye query **dynamic** hai — result har din badalta rahega, kyunki `CURDATE()` roz nayi date deta hai. Isko hardcoded date range se compare karo: `WHERE hire_date >= '2026-05-05'` — ye static hai, ek din baad hi galat ho jaayegi. Interview mein bolo: *"Relative date conditions ke liye humesha date function use karta hoon, hardcoded date nahi, taaki query future mein bhi valid rahe."*

**Important Note:** Is static sample dataset (jisme sabse recent hire 2023 ka hai) pe agar ye query 2026 mein chalayenge, to koi row match nahi karegi — kyunki koi employee pichle 90 dinon mein hire nahi hua. Concept samajhna important hai, result specific date pe depend karta hai.

**Oracle equivalent:** `WHERE hire_date >= SYSDATE - 90` (Oracle mein date se seedha number subtract karke din ghata sakte ho).

**Follow-up Questions:**
- `CURDATE()` aur `NOW()` mein kya farak hai?
- Pichle 6 mahine ke employees kaise nikaaloge?
- Is quarter (last 3 months, calendar-aligned) ke employees kaise nikaaloge — kya ye "last 90 days" jaisa hi hai?

---

### Q11. Customers jinke naam mein 'sh' kahin bhi aata ho

**Interview mein aise poocha jaayega:**
> "Find customers whose name contains 'sh' anywhere in it."

**Thought Process:**
- Output: customer records — pattern match, beech mein kahin bhi ho sakta hai.
- Keyword pakdo: **"contains ... anywhere"** → wildcard **dono taraf** lagana padega.

**Solution Query:**
```sql
SELECT *
FROM Customers
WHERE customer_name LIKE '%sh%';
```

**Line-by-Line:**
- `LIKE '%sh%'` — `%` dono taraf lagane se matlab hai "sh" se pehle kuch bhi ho sakta hai aur baad mein bhi kuch bhi ho sakta hai — bas beech mein "sh" hona chahiye kahin bhi.

**Naya Concept — Wildcard dono taraf (`%value%`):**
`'A%'` (starts with) vs `'%A'` (ends with) vs `'%A%'` (contains anywhere) — teeno alag-alag patterns hain, position of `%` hi decide karta hai matching kaise hogi.

**Expected Output (is sample data pe):** Rakesh Sharma ("Rake**sh** Sharma"), Aisha Khan ("Ai**sh**a"), Aarushi Bose ("Aaru**sh**i") — 3 rows. Vikram Mehta match nahi karega ("Mehta" mein 'sh' nahi hai, sirf 'ht' hai).

**Follow-up Questions:**
- `LIKE '%sh%'` bade table pe slow kyun ho sakti hai — index use hota hai isme?
- `sh` ke case-sensitivity ka kya scene hai MySQL vs Oracle mein?
- Full-text search ke liye `LIKE '%...%'` best option hai, ya koi behtar tarika hai bade text data ke liye?

---

### Q12. Orders jo January ya February mein placed hue

**Interview mein aise poocha jaayega:**
> "Find all orders placed in the months of January or February."

**Thought Process:**
- Output: order records — date filter, do specific months.
- Keyword pakdo: **"January or February"** → `MONTH()` function se month extract karke `IN` ya `OR` se compare karo.

**Solution Query:**
```sql
SELECT *
FROM Orders
WHERE MONTH(order_date) IN (1, 2);
```

**Line-by-Line:**
- `MONTH(order_date)` — Date se sirf **month number** nikaalta hai (Jan=1, Feb=2, ...).
- `IN (1, 2)` — Sirf January(1) ya February(2) wale orders filter honge — `IN` yaha do `OR` conditions se better hai (Q8 wala concept).

**Important gotcha:** Is query mein **year specify nahi kiya** — agar tumhare paas multiple saalon ka data ho, to ye **har saal ke** Jan/Feb ko match karegi, sirf ek specific saal ke nahi. Agar ek specific year chahiye ho, to `AND YEAR(order_date) = 2023` add karna padega.

**Expected Output (is sample data pe):** O1(Jan 10), O2(Feb 15), O3(Jan 20), O5(Feb 25) — 4 rows. (O4 March mein hai, exclude.)

**Follow-up Questions:**
- Agar sirf **2023 ke** January/February orders chahiye hon, query kaise change hogi?
- `MONTH()` function use karne se index performance pe kya asar padta hai? (Q5 wala hi non-sargable concept yaha bhi apply hota hai.)
- Range-based (function-free) version kaise likhoge?

---

### Q13. Employees jinka koi department assign nahi hai

**Interview mein aise poocha jaayega:**
> "Find employees who are not assigned to any department."

**Thought Process:**
- Ye Q9 jaisa hi concept hai, bas column different hai — **quick recap**: "not assigned" → `IS NULL`.

**Solution Query:**
```sql
SELECT *
FROM Employees
WHERE department_id IS NULL;
```

**Line-by-Line:**
- `department_id IS NULL` — Sirf wo rows jinme department_id set hi nahi hua.

**Expected Output (is sample data pe):** Arjun Singh — 1 row.

**Follow-up Questions:**
- Ye query business mein kab useful hoti hai? *(Onboarding mein pending employees dhundhne ke liye, ya data-quality audit ke liye.)*
- Is result ko departments table se `LEFT JOIN` karke bhi nikaal sakte ho kya? (Category 4 mein cover hoga.)

---

### Q14. Products jinki price ek specific value ke equal nahi hai (NULL handling ke saath)

**Interview mein aise poocha jaayega:**
> "Find all products whose price is not equal to 400, making sure your query correctly handles any NULL prices."

**Thought Process:**
- Output: product records — `!=` filter, lekin **NULL wala trap** explicitly mention kiya gaya hai question mein.
- Keyword pakdo: **"not equal to"** → `!=` ya `<>`. Lekin **"NULL handling"** keyword ne clearly warn kiya hai ki simple `!=` kaafi nahi hoga.

**Solution Query (naive — pehle ye dikhao, phir problem explain karo):**
```sql
-- Ye query NULL price wale products ko SILENTLY chhod degi
SELECT *
FROM Products
WHERE price != 400;
```

**Problem:** Product P5 (Gadget E) ki price `NULL` hai. `NULL != 400` ka result `UNKNOWN` hota hai (Q9 wala three-valued logic), jo `WHERE` clause mein automatically discard ho jaata hai — matlab P5 result mein **nahi** aayega, chahe uski price 400 ho ya na ho, kyunki humein pata hi nahi hai.

**Correct Query (agar NULL wale bhi chahiye):**
```sql
SELECT *
FROM Products
WHERE price != 400 OR price IS NULL;
```

**Line-by-Line:**
- `price != 400` — Non-NULL prices jo 400 ke equal nahi hain.
- `OR price IS NULL` — Explicitly NULL prices ko bhi include kar rahe hain, kyunki wo `!=` comparison mein automatically nahi aa sakte.

**Naya Concept — NULL aur Negative Comparisons (`!=`, `NOT IN`):**
Ye ek **bahut common interview trap** hai — jab bhi negative comparison (`!=`, `NOT IN`, `NOT LIKE`) ho aur data mein NULL possible ho, hamesha socho: *"Kya NULL wali rows ko bhi include karna hai ya nahi?"* Agar haan, to explicitly `OR column IS NULL` add karna padega — silently NULL rows drop ho jaati hain warna.

**Expected Output (naive query se):** Widget A(600), Gadget C(500), Gadget D(250) — 3 rows, P5(NULL) missing.
**Expected Output (correct query se):** Upar wale 3 + Gadget E(NULL) — 4 rows.

**Follow-up Questions:**
- `<>` aur `!=` mein koi farak hai? *(Nahi, dono same hain, `<>` ANSI-standard hai, `!=` zyada commonly likha jaata hai.)*
- Yehi NULL trap `NOT IN` ke saath aur bhi dangerous kyun ban jaata hai? (Category 5 mein detail mein cover hoga.)
- Agar requirement ho "sirf wo products jinki price known hai aur 400 nahi hai", to query kaise likhoge? *(Sirf `WHERE price != 400` hi sahi hoga tab, bina OR ke.)*

---

## CATEGORY 3 — AGGREGATE & GROUPING

### Q15. Har department mein kitne employees hain

**Interview mein aise poocha jaayega:**
> "Write a query to find the number of employees in each department."

**Thought Process:**
- Output: `department_id` aur uska employee count — ek row **per department**, na ki per employee.
- Keyword pakdo: **"in each department"** → result department-wise chahiye → `GROUP BY department_id`. Aur **"number of employees"** → `COUNT(*)`.
- Order of thinking: pehle GROUP BY decide karo (kis column pe groups banane hain), phir uske andar konsa aggregate chahiye.

**Solution Query:**
```sql
SELECT department_id, COUNT(*) AS total_employees
FROM Employees
GROUP BY department_id;
```

**Line-by-Line:**
- `SELECT department_id, COUNT(*)` — Har group ka `department_id` aur us group mein kitni rows hain, dono dikha rahe hain.
- `FROM Employees` — Source table.
- `GROUP BY department_id` — Saari rows ko `department_id` ke basis par **groups** mein todta hai — same dept_id wali saari rows ek hi group mein aa jaati hain. Iske baad `COUNT(*)` **har group ke liye alag se** calculate hota hai, poori table pe ek saath nahi.

**Naya Concept — `GROUP BY`:**
`GROUP BY` rows ko categories mein todta hai based on ek ya zyada columns ki value — phir aggregate functions (`COUNT`, `SUM`, `AVG`, etc.) **har group ke andar** calculate hote hain, independently. Bina `GROUP BY` ke, `COUNT(*)` **poori table** ka ek hi number dega.

**Expected Output (is sample data pe):**
| department_id | total_employees |
|---|---|
| 10 | 3 |
| 20 | 2 |
| 30 | 2 |
| NULL | 1 |

**Follow-up Questions:**
- NULL department wali row ko group mein kaise treat kiya (Q15 output mein)? *(MySQL NULL ko bhi ek separate group maanta hai.)*
- Department **name** (na ki sirf ID) ke saath ye result kaise doge? *(Category 4 join ki zaroorat padegi.)*
- `GROUP BY` mein multiple columns (jaise `department_id, gender`) daalo to kya hoga?

---

### Q16. Har department ki average salary

**Interview mein aise poocha jaayega:**
> "Find the average salary for each department."

**Thought Process:**
- Same pattern jaisa Q15 — group department-wise, bas aggregate function `COUNT` ki jagah `AVG` hoga.
- Keyword pakdo: **"average"** → `AVG()`.

**Solution Query:**
```sql
SELECT department_id, AVG(salary) AS avg_salary
FROM Employees
GROUP BY department_id;
```

**Line-by-Line:**
- `AVG(salary)` — Har group ke andar saari salaries ka average nikalta hai. Agar kisi employee ki salary `NULL` hoti, to `AVG` use automatically ignore kar deta (yaha koi NULL salary nahi hai, isliye ye edge case abhi trigger nahi hoga, lekin concept yaad rakho).

**Expected Output (is sample data pe):**
| department_id | avg_salary |
|---|---|
| 10 | 59000.00 |
| 20 | 76000.00 |
| 30 | 75000.00 |
| NULL | 48000.00 |

**Follow-up Questions:**
- Agar kisi employee ki salary NULL hoti, to `AVG` uska kya karta? *(Ignore karta — na numerator mein count hota, na denominator mein.)*
- `AVG` ko round karke 2 decimal places tak kaise dikhaoge? *(`ROUND(AVG(salary), 2)`.)*
- Company ki overall average salary se compare karke sirf "above average" departments kaise nikaaloge? (Q21 mein aage cover hoga.)

---

### Q17. Wo departments jinme 5 se zyada employees hain

**Interview mein aise poocha jaayega:**
> "Find departments that have more than 2 employees."
> *(Note: is sample dataset mein max 3 employees ek department mein hain, isliye demonstration ke liye threshold "> 2" use kar rahe hain — asli interview mein wo koi bhi N de sakta hai, jaise 5, 10, waghera — pattern bilkul same rahega.)*

**Thought Process:**
- Output: sirf wo departments jo condition satisfy karte hain — lekin condition ek **aggregate value** (count) pe hai, individual row pe nahi.
- Keyword pakdo: **grouped result ko filter karna hai** → `HAVING`, na ki `WHERE` (kyunki `WHERE` aggregate ke saath kaam nahi karta — Category 3 ka sabse important interview point).

**Solution Query:**
```sql
SELECT department_id, COUNT(*) AS total_employees
FROM Employees
GROUP BY department_id
HAVING COUNT(*) > 2;
```

**Line-by-Line:**
- `GROUP BY department_id` — Pehle groups banao.
- `HAVING COUNT(*) > 2` — Groups **banne ke baad**, sirf unhi ko rakho jinka count 2 se zyada ho. `HAVING` mein aggregate function (`COUNT(*)`) direct use kar sakte hain, kyunki ye grouping ke **baad** evaluate hoti hai.

**Naya Concept — `HAVING` (aur `WHERE` se farak):**
"Sir, `WHERE` row-level filter hai jo grouping se **pehle** chalta hai — isliye usme aggregate function use nahi kar sakte, kyunki jab WHERE chal raha hota hai tab groups bane hi nahi hote. `HAVING` group-level filter hai jo grouping ke **baad** chalta hai, isliye HAVING mein aggregate functions allowed hain."

**Expected Output (is sample data pe):**
| department_id | total_employees |
|---|---|
| 10 | 3 |

**Follow-up Questions:**
- `WHERE` mein `COUNT(*) > 2` likhne ki koshish karo — kya error aayega?
- Kya ek hi query mein `WHERE` aur `HAVING` dono use ho sakte hain? *(Haan — WHERE row-filter pehle, HAVING group-filter baad mein, dono alag purpose serve karte hain, ek saath bhi chal sakte hain.)*
- Departments jinme **exactly** 2 employees hain (na kam, na zyada), kaise nikaaloge?

---

### Q18. Har department ki highest salary, department name ke saath

**Interview mein aise poocha jaayega:**
> "Find the highest salary in each department, along with the department name."

**Thought Process:**
- Output: `department_name` (na ki sirf ID) aur us department ki `MAX(salary)`.
- Keyword pakdo: **"highest salary"** → `MAX()`. Lekin **"department name"** — Employees table mein sirf `department_id` hai, naam `Departments` table mein hai — isliye ek **chhota JOIN** bhi chahiye (poori JOIN detail Category 4 mein cover hogi, yaha bas iska use dikha rahe hain).
- Order of operations: pehle tables join karo, phir group karo, phir aggregate.

**Solution Query:**
```sql
SELECT d.department_name, MAX(e.salary) AS highest_salary
FROM Employees e
JOIN Departments d ON e.department_id = d.department_id
GROUP BY d.department_name;
```

**Line-by-Line:**
- `FROM Employees e JOIN Departments d ON e.department_id = d.department_id` — Employees aur Departments ko combine kar rahe hain taaki department ka **naam** mil sake (sirf ID nahi). Ye ek `INNER JOIN` hai — matlab sirf wo employees consider honge jinka koi valid department match ho.
- `GROUP BY d.department_name` — Ab groups department **naam** ke basis par ban rahe hain (ID ke basis par bhi bana sakte the, dono equivalent hain yaha kyunki ek naam se ek hi ID hai).
- `MAX(e.salary)` — Har group (department) ke andar sabse zyada salary.

**Naya Concept (preview) — Aggregate + Join Together:**
"Ye query dikhati hai ki aggregate functions aur joins ek saath kaise kaam karte hain — pehle join se poora combined data set banta hai (employee + uske department ka naam), phir us combined data pe GROUP BY aur MAX apply hote hain. Note: kyunki ye INNER JOIN hai, Arjun Singh (jiska department hi NULL hai) is result mein bilkul nahi aayega, aur Marketing department (jisme koi employee nahi) bhi nahi aayega."

**Expected Output (is sample data pe):**
| department_name | highest_salary |
|---|---|
| Sales | 75000 |
| Engineering | 90000 |
| HR | 95000 |

**Follow-up Questions:**
- Arjun Singh (NULL department) is result mein kyun nahi dikh raha?
- Marketing department (0 employees) is result mein kyun nahi hai — kaise include karoge? *(Hint: LEFT JOIN — Category 4.)*
- Har department ka **highest-paid employee ka naam bhi** chahiye ho (na sirf salary number), to query kaise badlegi? *(Ye simple GROUP BY se possible nahi — subquery/window function chahiye, Category 5/7 mein cover hoga.)*

---

### Q19. Har customer ne kitne orders place kiye

**Interview mein aise poocha jaayega:**
> "Find the total number of orders placed by each customer."

**Thought Process:**
- Same GROUP BY + COUNT pattern (Q15 jaisa), bas `Orders` table pe, `customer_id` ke basis par.

**Solution Query:**
```sql
SELECT customer_id, COUNT(*) AS total_orders
FROM Orders
GROUP BY customer_id;
```

**Line-by-Line:**
- `GROUP BY customer_id` — Har customer ke orders ko ek group mein todta hai.
- `COUNT(*)` — Us group mein kitni rows (orders) hain.

**Expected Output (is sample data pe):**
| customer_id | total_orders |
|---|---|
| C1 | 2 |
| C2 | 2 |
| C3 | 1 |

**Note:** C4 (Aarushi Bose) is result mein **bilkul nahi dikhega**, kyunki uska koi order hi nahi hai `Orders` table mein — `GROUP BY` sirf existing rows pe group banata hai, "0 orders wale customers" ko dikhane ke liye `Customers` table se `LEFT JOIN` chahiye hoga (Q27 mein cover hoga).

**Follow-up Questions:**
- C4 (jisne kabhi order nahi kiya) is output mein kyun missing hai?
- Customers jinhone **sirf ek** order kiya hai, kaise filter karoge? *(`HAVING COUNT(*) = 1`.)*
- Har customer ka total order **amount** (count nahi, sum) kaise nikaaloge?

---

### Q20. Har product category ka total revenue

**Interview mein aise poocha jaayega:**
> "Find the total revenue generated by each product category."

**Thought Process:**
- Output: `category` aur uska total revenue — lekin revenue `Orders.amount` mein hai, category `Products.category` mein hai — **do tables join karni padengi**, phir category-wise group karke sum lena hoga.
- Order of thinking: pehle join (data connect karo), phir group (category-wise todo), phir aggregate (sum karo).

**Solution Query:**
```sql
SELECT p.category, SUM(o.amount) AS total_revenue
FROM Orders o
JOIN Products p ON o.product_id = p.product_id
GROUP BY p.category;
```

**Line-by-Line:**
- `FROM Orders o JOIN Products p ON o.product_id = p.product_id` — Har order ko uske product se jodkar, us order ki **category** pata karte hain (kyunki `Orders` table mein khud category nahi hai, sirf `product_id` hai).
- `GROUP BY p.category` — Ab combined data ko category ke basis par group karte hain.
- `SUM(o.amount)` — Har category ke andar saare orders ka amount jod dete hain.

**Naya Concept — Join + Group By ek saath (revenue aggregation pattern):**
Ye ek **bahut common real-world pattern** hai — jab bhi "revenue by X" jaisa koi requirement ho aur revenue ka data ek table mein ho, category jaisi grouping-attribute doosri table mein ho, to pehle join karke ek single combined view banate hain, phir usi par group-by/aggregate apply karte hain.

**Expected Output (is sample data pe):**
| category | total_revenue |
|---|---|
| Electronics | 3000 |
| Home | 500 |

*(Calculation: Electronics = O1(1200, P1) + O2(800, P2) + O4(300, P1) + O5(700, P2) = 3000. Home = O3(500, P3) = 500. Product P4 aur P5 kabhi order nahi hue, isliye unka contribution 0 hai aur INNER JOIN ki wajah se unka category-total mein directly koi asar nahi — lekin Electronics/Home dono categories already kisi order se represent ho rahi hain isliye missing nahi hongi.)*

**Follow-up Questions:**
- Agar koi poori category hi kabhi order na hui ho, to wo is INNER JOIN wale result mein dikhegi ya nahi?
- Revenue ko highest se lowest order mein kaise sort karoge?
- Har category ka **average order value** (na ki total) kaise nikaaloge?

---

### Q21. Wo departments jinki average salary company-wide average se zyada hai

**Interview mein aise poocha jaayega:**
> "Find departments whose average salary is higher than the company's overall average salary."

**Thought Process:**
- Output: departments jo ek **comparison condition** satisfy karte hain — lekin comparison value khud bhi ek **calculated aggregate** hai (company ka overall average), koi fixed number nahi diya gaya.
- Keyword pakdo: **"higher than the company's overall average"** — ye ek **do-level aggregation** hai: pehle company ka ek overall average chahiye (poori table ka), phir har department ka apna average us se compare karna hai. Ye seedha `HAVING AVG(salary) > <fixed number>` se nahi ho sakta, kyunki number khud hi ek subquery se aana hai.
- Isliye `HAVING` clause ke andar hi ek **subquery** daalni padegi.

**Solution Query:**
```sql
SELECT department_id, AVG(salary) AS dept_avg_salary
FROM Employees
GROUP BY department_id
HAVING AVG(salary) > (SELECT AVG(salary) FROM Employees);
```

**Line-by-Line:**
- `GROUP BY department_id` — Department-wise groups banao.
- `HAVING AVG(salary) > (SELECT AVG(salary) FROM Employees)` — Har department ka average salary compare kar rahe hain ek **inner query** ke result se, jo **poori** Employees table ka overall average nikalti hai (bina kisi GROUP BY ke — ye ek independent, single-value query hai).

**Naya Concept — Subquery Inside `HAVING`:**
"Sir, yaha maine ek **non-correlated subquery** use ki hai `HAVING` ke andar — ye inner query (`SELECT AVG(salary) FROM Employees`) sirf **ek baar** independently execute hoti hai, ek single number return karti hai, aur phir wo number outer query ke har group ke against compare hota hai. Ye subquery outer query ke kisi column pe depend nahi karti, isliye ise non-correlated kehte hain (Category 5 mein correlated subquery ka farak detail mein cover karenge)."

**Expected Output (is sample data pe):** Company overall avg = 65875. Dept 20 (Engineering, avg 76000) aur Dept 30 (HR, avg 75000) — dono is threshold se zyada hain, isliye ye 2 rows result mein aayengi. Dept 10 (59000) aur NULL-dept group (48000) — dono kam hain, exclude ho jaayenge.

**Follow-up Questions:**
- Isi query ko CTE use karke kaise rewrite karoge (readability ke liye)? (Category 6 mein cover hoga.)
- Kya ye ek correlated subquery hai ya non-correlated? Farak samjhao.
- Agar "company average" ki jagah "company median" chahiye ho, to approach kaise badlegi?

---

### Q22. Ek hi row mein male vs female employees ka count (conditional aggregation)

**Interview mein aise poocha jaayega:**
> "Display the count of male and female employees in a single row (two separate columns)."

**Thought Process:**
- Output: **ek hi row**, do columns — `male_count` aur `female_count`. Ye normal `GROUP BY gender` se **alag** hai, kyunki GROUP BY do alag rows dega (ek Male ke liye, ek Female ke liye), lekin humein **ek row mein side-by-side** chahiye.
- Keyword pakdo: **"in a single row"** → ye "pivot"-jaisa pattern hai — `CASE WHEN` ke andar condition daalke, phir `SUM`/`COUNT` se aggregate karna padega. Isko **conditional aggregation** kehte hain.

**Solution Query:**
```sql
SELECT
    SUM(CASE WHEN gender = 'M' THEN 1 ELSE 0 END) AS male_count,
    SUM(CASE WHEN gender = 'F' THEN 1 ELSE 0 END) AS female_count
FROM Employees;
```

**Line-by-Line:**
- `CASE WHEN gender = 'M' THEN 1 ELSE 0 END` — Har row ke liye, agar gender 'M' hai to `1`, warna `0` — ye ek **per-row calculated value** hai, aggregate nahi (abhi tak).
- `SUM(...)` — Ab in saare 1s aur 0s ko **jod** rahe hain poori table mein — jitne bhi 1 mile (matlab jitne Male employees the), unka total hi male_count ban jaata hai. Ye trick hai: `CASE WHEN` se condition ko **number** mein convert karo, phir `SUM` se count nikaalo.
- Doosra `SUM(CASE WHEN gender = 'F' ...)` — Same logic, Female ke liye.
- Koi `GROUP BY` nahi hai — poori table **ek hi group** maani ja rahi hai (implicit), isliye result bhi ek hi row aayega.

**Naya Concept — Conditional Aggregation (`SUM(CASE WHEN ...)`):**
"Ye pattern tab use hota hai jab humein GROUP BY ke categories ko **rows ki jagah columns** mein dikhana ho — jise 'pivoting' bhi kehte hain. `COUNT(CASE WHEN condition THEN 1 END)` bhi isi kaam ke liye use ho sakta hai (SUM ki jagah COUNT, aur ELSE 0 ki zaroorat nahi kyunki COUNT NULLs ko already ignore karta hai)."

**Expected Output (is sample data pe):**
| male_count | female_count |
|---|---|
| 4 | 4 |

**Follow-up Questions:**
- `SUM(CASE WHEN...)` ki jagah `COUNT(CASE WHEN...)` use karo to query kaise likhogे, aur `ELSE 0` ki zaroorat kyun nahi padegi?
- Isi pattern se **department-wise** male/female count (multiple rows, ek per department) kaise nikaaloge?
- Ye "pivot" pattern MySQL mein manually karna padta hai kyun — kya koi built-in `PIVOT` keyword hai? *(SQL Server mein PIVOT hota hai, MySQL mein nahi, isliye CASE WHEN approach standard hai.)*

---

## CATEGORY 4 — JOINS

### Q23. Employee name + department name (`INNER JOIN`)

**Interview mein aise poocha jaayega:**
> "Write a query to display each employee's name along with their department name."

**Thought Process:**
- Output: `employee_name` (Employees se) aur `department_name` (Departments se) — **do tables se data chahiye**, ek hi row mein.
- Keyword pakdo: "employee **with** department name" → ye classic **join** keyword pattern hai — dono tables ke beech ek common column (`department_id`) se connect karna hai.
- Decide karo: sirf wo employees chahiye jinka **valid match** ho dono taraf, ya bina-match wale bhi? Question mein koi indication nahi hai ki unmatched bhi chahiye, isliye default **INNER JOIN** use karenge.

**Solution Query:**
```sql
SELECT e.employee_name, d.department_name
FROM Employees e
INNER JOIN Departments d ON e.department_id = d.department_id;
```

**Line-by-Line:**
- `Employees e` / `Departments d` — Table aliases diye taaki query short aur readable rahe, especially jab dono tables mein `department_id` jaisa **same-naam column** ho — alias na do to ambiguity error aayega.
- `INNER JOIN Departments d` — Dono tables ko combine kar rahe hain.
- `ON e.department_id = d.department_id` — Ye **join condition** hai — batati hai ki kaunsi rows "match" maani jaayengi (jahan dono taraf ka department_id barabar ho).
- **Important:** `INNER JOIN` sirf wo rows return karta hai jinka **dono taraf** match mile — agar kisi employee ka `department_id` `Departments` table mein exist hi nahi karta (ya NULL hai), wo row result mein **nahi** aayegi.

**Naya Concept — `INNER JOIN`:**
`INNER JOIN` do tables ka **intersection** deta hai — sirf matching rows. Ye sabse common join hai jab humein "sirf complete/valid data" chahiye ho, missing/orphan records nahi.

**Expected Output (is sample data pe):** 7 rows — sab employees except Arjun Singh (jiska department_id NULL hai, isliye kisi department se match nahi hoga).

**Follow-up Questions:**
- Arjun Singh is result mein kyun missing hai?
- Agar Marketing department (jisme koi employee nahi) ka naam bhi dikhana ho result mein, to join type badalna padega ya nahi?
- `INNER JOIN` likhne ki jagah sirf `JOIN` likh sakte ho kya? *(Haan, `JOIN` by default `INNER JOIN` hi maana jaata hai MySQL mein.)*

---

### Q24. Sab employees, chahe department assigned ho ya na ho (`LEFT JOIN`)

**Interview mein aise poocha jaayega:**
> "Write a query to display all employees along with their department name, including employees who don't have a department assigned."

**Thought Process:**
- Ye Q23 jaisa hi hai, lekin ab explicitly bola gaya hai **"including employees who don't have a department"** — matlab Employees table ki **saari rows guaranteed** chahiye, chahe Departments mein match mile ya na mile.
- Keyword pakdo: **"including ... who don't have"** → `INNER JOIN` yaha kaam nahi karega (kyunki wo unmatched rows drop kar deta), `LEFT JOIN` chahiye.

**Solution Query:**
```sql
SELECT e.employee_name, d.department_name
FROM Employees e
LEFT JOIN Departments d ON e.department_id = d.department_id;
```

**Line-by-Line:**
- `FROM Employees e LEFT JOIN Departments d` — `LEFT JOIN` mein **left side wali table (Employees) ki saari rows guaranteed** aayengi — chahe match mile ya na mile.
- `ON e.department_id = d.department_id` — Match hone pe department_name mil jaayega; match na hone pe (jaise Arjun Singh ke case mein), `department_name` column **NULL** aa jaayega, lekin employee ki row **drop nahi hogi**.

**Naya Concept — `LEFT JOIN` (aur `INNER JOIN` se farak):**
"Sir, `LEFT JOIN` ka use tab karta hoon jab main **left table ki har row** guarantee karni ho result mein, chahe doosri table mein uska koi match ho ya na ho. Unmatched rows ke liye doosri table ke columns automatically NULL ho jaate hain — jaise yaha Arjun Singh ka department_name NULL aayega, lekin uski employee_name phir bhi dikhegi."

**Expected Output (is sample data pe):** Saare 8 employees — Arjun Singh ka `department_name = NULL`, baaki sabka actual department name.

**Follow-up Questions:**
- `LEFT JOIN` mein "left" kis table ko bolte hain — query mein order kaise decide karta hai?
- Agar sirf wo employees chahiye jinka department **NULL** ho (matlab unmatched wale hi), to `LEFT JOIN` ke upar aur kya add karna padega? *(`WHERE d.department_id IS NULL`.)*
- `LEFT JOIN` aur `LEFT OUTER JOIN` mein koi farak hai? *(Nahi, `OUTER` keyword optional hai, dono same hain.)*

---

### Q25. Sab departments, chahe employee ho ya na ho (`RIGHT JOIN`)

**Interview mein aise poocha jaayega:**
> "Write a query to display all departments along with their employees, including departments that currently have no employees."

**Thought Process:**
- Ye Q24 ka **reverse** hai — ab "guaranteed rows" Departments table ki chahiye, na ki Employees ki.
- Keyword pakdo: **"including departments that have no employees"** → do tarike se likh sakte ho: (a) `RIGHT JOIN` use karo table order same rakhte hue, ya (b) table order **swap** karke `LEFT JOIN` hi use karo (zyada common practice).

**Solution Query (Approach A — RIGHT JOIN):**
```sql
SELECT d.department_name, e.employee_name
FROM Employees e
RIGHT JOIN Departments d ON e.department_id = d.department_id;
```

**Solution Query (Approach B — LEFT JOIN, table order swap — zyada preferred):**
```sql
SELECT d.department_name, e.employee_name
FROM Departments d
LEFT JOIN Employees e ON e.department_id = d.department_id;
```

**Line-by-Line (Approach A):**
- `Employees e RIGHT JOIN Departments d` — `RIGHT JOIN` mein **right side wali table (Departments) ki saari rows guaranteed** hongi, chahe unme koi employee ho ya na ho. Marketing department (jisme 0 employees hain) bhi dikhegi, `employee_name` column NULL ke saath.

**Naya Concept — `RIGHT JOIN` (aur ye kam kyun use hota hai):**
"Sir, `RIGHT JOIN` practically `LEFT JOIN` ka hi mirror-image hai — jo kaam `A RIGHT JOIN B` karta hai, wahi kaam `B LEFT JOIN A` (table order swap karke) bhi kar deta hai. Isi wajah se, zyadatar developers aur companies ke style-guides `RIGHT JOIN` avoid karte hain — sirf `LEFT JOIN` use karte hain (table order ko convenient tarike se rearrange karke), taaki team ki saari queries ek hi consistent pattern follow karein aur padhne mein aasan rahe."

**Expected Output (dono approaches se same result):** Saare 4 departments — Marketing ke saath `employee_name = NULL`, baaki teen departments apne-apne employees ke saath (multiple rows agar department mein multiple employees hain).

**Follow-up Questions:**
- `RIGHT JOIN` ko `LEFT JOIN` mein convert karne ke liye kya karna padta hai?
- Sirf Marketing wali row (0-employee department) isolate karke kaise dikhaoge?
- `FULL OUTER JOIN` in dono se kaise alag hai? *(MySQL mein FULL OUTER JOIN directly support nahi hai — LEFT JOIN UNION RIGHT JOIN se simulate karna padta hai; ye ek acha follow-up discussion point hai.)*

---

### Q26. Employee aur unke manager ka naam side-by-side (`SELF JOIN`)

**Interview mein aise poocha jaayega:**
> "Write a query to display each employee's name along with their manager's name."

**Thought Process:**
- Output: `employee_name` aur `manager_name` — dono **Employees table se hi** aa rahe hain (koi doosri table involved nahi), bas ek row ka `manager_id` doosri row ke `employee_id` se match karna hai.
- Keyword pakdo: **"employee and manager"** (same table ke andar ka relationship) → `SELF JOIN` — same table ko do baar, alag aliases ke saath, reference karna padega.

**Solution Query:**
```sql
SELECT e.employee_name AS employee, m.employee_name AS manager
FROM Employees e
LEFT JOIN Employees m ON e.manager_id = m.employee_id;
```

**Line-by-Line:**
- `Employees e` aur `Employees m` — **Same table** ko do baar alias kiya — `e` employee ke roop mein, `m` uske manager ke roop mein. Isi wajah se ise **self join** kehte hain.
- `ON e.manager_id = m.employee_id` — Har employee row (`e`) ke `manager_id` ko match kar rahe hain kisi doosri row (`m`) ke `employee_id` se — matlab "us employee ka boss dhundo jiska ID is manager_id ke barabar hai."
- `LEFT JOIN` (na ki INNER) use kiya — kyunki Ravi aur Amit (top-level employees) ka `manager_id` `NULL` hai. Agar `INNER JOIN` use karte, to unki rows hi result se **gayab** ho jaati (kyunki NULL kisi bhi `employee_id` se match nahi karta) — `LEFT JOIN` unko bhi rakhta hai, bas unka `manager` column `NULL` aa jaata hai.

**Naya Concept — `SELF JOIN`:**
"Sir, self join tab use hota hai jab ek table ke andar hi kisi row ka relationship kisi **doosri row** se ho — jaise yaha employee ka manager bhi ek employee hi hota hai. Hum table ko do baar alag naam (alias) dekar treat karte hain jaise wo do alag tables hon."

**Expected Output (is sample data pe):**
| employee | manager |
|---|---|
| Ravi Kumar | NULL |
| Priya Sharma | Ravi Kumar |
| Amit Verma | NULL |
| Sneha Rao | Amit Verma |
| Kunal Joshi | Ravi Kumar |
| Neha Gupta | Kunal Joshi |
| Arjun Singh | NULL |
| Ananya Iyer | Ravi Kumar |

**Follow-up Questions:**
- `LEFT JOIN` ki jagah `INNER JOIN` use karte to kaunsi rows result se gayab ho jaati?
- Kya self join mein bhi index ka same fayda milta hai jaise normal join mein?
- 2-level hierarchy (employee → manager → manager ka manager) chahiye ho to kya karna padega? (Recursive CTE — Category 6.)

---

### Q27. Customers aur unke orders — including wo customers jinhone kabhi order nahi kiya

**Interview mein aise poocha jaayega:**
> "Write a query to display all customers along with their orders, including customers who have never placed an order."

**Thought Process:**
- Same pattern jaisa Q24 — "including ... who have never" → `Customers` table ki saari rows guaranteed chahiye → `LEFT JOIN`.

**Solution Query:**
```sql
SELECT c.customer_name, o.order_id, o.amount
FROM Customers c
LEFT JOIN Orders o ON c.customer_id = o.customer_id;
```

**Line-by-Line:**
- `Customers c LEFT JOIN Orders o` — Customers table left side pe hai, isliye uski saari rows guaranteed aayengi.
- `ON c.customer_id = o.customer_id` — Match hone pe order details milenge; Aarushi Bose (C4) ke liye `order_id` aur `amount` dono `NULL` aayenge, lekin uska naam phir bhi dikhega.

**Important Note:** Notice karo — C1 aur C2 ke **2-2 orders** hain, isliye unki row **do baar** repeat hogi result mein (ek row per order) — ye "one-to-many" join ka natural behaviour hai, koi bug nahi hai. Isi concept ko Q32 mein "duplication trap" ke roop mein detail se discuss karenge.

**Expected Output (is sample data pe):** 6 rows total — C1(2 rows, O1 & O2), C2(2 rows, O3 & O5), C3(1 row, O4), C4(1 row, order columns NULL).

**Follow-up Questions:**
- C1 aur C2 ki rows result mein do-do baar kyun aa rahi hain — ye galat hai kya?
- Sirf wo customers dikhao jinhone **kabhi order nahi kiya** (sirf C4 jaise cases) — is query mein kya add karna padega? *(`WHERE o.order_id IS NULL`.)*
- Har customer ka total order count bhi chahiye ho isi query mein, to kya karna padega? (GROUP BY + COUNT combine karna padega.)

---

### Q28. Customers jinhone January aur February dono mein order kiya ho

**Interview mein aise poocha jaayega:**
> "Find customers who placed orders in both January and February."

**Thought Process:**
- Ye pichle "OR" wale pattern (Q12) se **alag** hai — wahan "Jan OR Feb ka koi bhi order" chahiye tha, yaha specifically **dono** months mein kam se kam ek-ek order hona chahiye — matlab per-customer condition check karni hai.
- Keyword pakdo: **"both January and February"** → is per-customer condition ko verify karne ke liye humein pehle customer-wise **group** banana padega, phir group ke andar check karna padega ki dono months represent hain ya nahi — ye ek **conditional aggregation + HAVING** pattern hai (Q22 wala concept yaha bhi apply ho raha hai).
- **Gotcha jo bahut common hai:** `HAVING COUNT(DISTINCT MONTH(order_date)) >= 2` likhna **galat** hoga agar koi customer ne sirf January aur March mein order kiya ho — wo bhi "2 distinct months" count ho jaayega, lekin humein specifically **Jan aur Feb** chahiye, koi bhi 2 months nahi. Isliye explicit conditional check karna padega.

**Solution Query:**
```sql
SELECT customer_id
FROM Orders
GROUP BY customer_id
HAVING SUM(CASE WHEN MONTH(order_date) = 1 THEN 1 ELSE 0 END) > 0
   AND SUM(CASE WHEN MONTH(order_date) = 2 THEN 1 ELSE 0 END) > 0;
```

**Line-by-Line:**
- `GROUP BY customer_id` — Har customer ke saare orders ek group mein.
- `SUM(CASE WHEN MONTH(order_date) = 1 THEN 1 ELSE 0 END) > 0` — Ye check kar raha hai ki is customer ke group mein **kam se kam ek** January order hai (Q22 wala conditional-count pattern).
- `AND SUM(CASE WHEN MONTH(order_date) = 2 THEN 1 ELSE 0 END) > 0` — Aur ye check kar raha hai ki February ka bhi kam se kam ek order hai.
- Dono conditions `AND` se joined hain — matlab **dono** true honi chahiye, tabhi customer result mein aayega.

**Naya Concept — "Existence of Multiple Specific Categories" Pattern:**
"Ye pattern tab use hota hai jab requirement ho 'X aur Y dono condition satisfy honi chahiye ek hi group ke andar' — jaise 'dono months mein order kiya ho'. Naive approach (`COUNT(DISTINCT ...)  >= 2`) galat hai kyunki wo sirf 'kitne alag values hain' check karta hai, 'specifically kaunse values hain' nahi. Isliye har specific condition ke liye alag `SUM(CASE WHEN...)` likhna zyada correct aur safe approach hai."

**Expected Output (is sample data pe):** C1 (Jan: O1, Feb: O2 — dono present), C2 (Jan: O3, Feb: O5 — dono present) — 2 rows. C3 sirf March mein hai, isliye exclude.

**Follow-up Questions:**
- `COUNT(DISTINCT MONTH(order_date)) >= 2` kyun galat approach hoti agar koi customer ne Jan aur March mein order kiya ho (Feb mein nahi)?
- Isi pattern ko 3 specific months ("Jan, Feb, aur March teeno") ke liye kaise extend karoge?
- Customer names bhi chahiye is result mein, to Customers table ko kaise involve karoge?

---

### Q29. Products jo kabhi bike hi nahi (`LEFT JOIN` + `IS NULL`)

**Interview mein aise poocha jaayega:**
> "Find all products that have never been sold (i.e., never appear in any order)."

**Thought Process:**
- Output: Products jinka `Orders` table mein **koi match hi nahi** hai — ye ek "anti-join" pattern hai (jo cheez doosri table mein exist hi nahi karti, use dhundo).
- Keyword pakdo: **"never been sold"** → sabse pehle `LEFT JOIN` karo (Products ki saari rows guaranteed rakhne ke liye), phir `WHERE` mein check karo ki match wala side `NULL` hai ya nahi — ye "unmatched rows filter karo" ka standard tarika hai.

**Solution Query:**
```sql
SELECT p.product_id, p.product_name
FROM Products p
LEFT JOIN Orders o ON p.product_id = o.product_id
WHERE o.order_id IS NULL;
```

**Line-by-Line:**
- `Products p LEFT JOIN Orders o` — Saare products guaranteed aayenge, chahe unke orders ho ya na ho.
- `ON p.product_id = o.product_id` — Match milne pe order details aayenge; na milne pe `o.*` columns NULL honge.
- `WHERE o.order_id IS NULL` — Ye **filter** hai jo sirf unhi rows ko rakhta hai jinke liye match **nahi mila tha** (matlab `LEFT JOIN` ke baad jo NULL aaya). Ye poori query ko effectively ek "anti-join" bana deta hai — "sirf wahi products dikhao jinka Orders table mein koi trace hi nahi hai."

**Naya Concept — `LEFT JOIN ... WHERE ... IS NULL` (Anti-Join Pattern):**
"Ye ek bahut standard SQL pattern hai jab bhi requirement ho 'X jo Y mein exist nahi karta' — jaise 'customers jinhone order nahi kiya', 'products jo bike nahi', 'employees jinka department nahi hai (already Q13 mein dekha, wahan single-table NULL check tha, yaha do-table anti-join hai)'. LEFT JOIN se pehle saari rows le lo (match ho ya na ho), phir WHERE mein specifically unhi ko rakho jinka match column NULL aaya."

**Expected Output (is sample data pe):** Gadget D (P4), Gadget E (P5) — 2 rows.

**Follow-up Questions:**
- Isi kaam ke liye `NOT EXISTS` ka use karke query kaise likhoge? (Category 5 mein detail cover hoga — aur performance ka comparison bhi.)
- `NOT IN` se ye kaam karne ki koshish karo — kya NULL wala trap yaha bhi apply hoga? (Category 5.)
- Products jo **kam se kam ek baar** bike hain, unhe kaise nikaaloge? *(Simple INNER JOIN, ya `WHERE o.order_id IS NOT NULL`.)*

---

### Q30. Employees jo apne manager se zyada kamate hain

**Interview mein aise poocha jaayega:**
> "Find employees who earn more than their manager."

**Thought Process:**
- Ye Q26 (self join) ka hi extension hai — dono employee aur manager ki salary ek row mein laane ke baad, ek **comparison filter** add karna hai.
- Keyword pakdo: **"more than their manager"** — self join zaroori hai (employee aur manager same table mein hain), aur uske upar ek `WHERE` condition jo dono salaries compare kare.

**Solution Query:**
```sql
SELECT e.employee_name AS employee, e.salary AS employee_salary,
       m.employee_name AS manager, m.salary AS manager_salary
FROM Employees e
JOIN Employees m ON e.manager_id = m.employee_id
WHERE e.salary > m.salary;
```

**Line-by-Line:**
- `Employees e JOIN Employees m ON e.manager_id = m.employee_id` — Q26 wala hi self join, bas yaha `INNER JOIN` use kiya (na ki LEFT), kyunki humein sirf wo employees chahiye jinka manager **hai** (NULL manager wale, jaise Ravi/Amit, is comparison mein automatically exclude ho jaayenge kyunki unka koi manager hi match nahi karega).
- `WHERE e.salary > m.salary` — Ye final filter hai — sirf wo rows rakho jaha employee ki salary uske manager se zyada ho.

**Naya Concept — Self Join + Filter Combine (Comparison-Between-Related-Rows Pattern):**
"Ye pattern batata hai ki self join sirf hierarchy dikhane ke liye nahi, balki **do related rows ko compare** karne ke liye bhi use hota hai — bina isके, humein manually har employee ki salary uske manager ki salary se compare karne ke liye application-side loop likhna padta; SQL mein self join se ye ek hi query mein ho jaata hai."

**Expected Output (is sample data pe):** Neha Gupta (95000) apne manager Kunal Joshi (55000) se zyada kamati hai — 1 row.

**Follow-up Questions:**
- INNER JOIN ki jagah LEFT JOIN use karte to result mein kya farak padta?
- Employees jo apne manager se **kam** kamate hain, kaise nikaaloge?
- Salary ka difference (employee_salary - manager_salary) bhi column mein dikhana ho, to query kaise badlegi?

---

### Q31. Employees, Departments, aur ek teesri table (Projects) ko join karo

**Interview mein aise poocha jaayega:**
> "Write a query to display each employee's name, their department name, and the project(s) they are working on."

**Thought Process:**
- Output: `employee_name`, `department_name`, `project_name` — **teen tables** se data chahiye, ek hi row mein.
- Keyword pakdo: koi naya keyword nahi, bas ye samajhna hai ki joins ko **chain** kiya ja sakta hai — pehla join ek intermediate combined result banata hai, uske upar doosra join lagta hai.

**Solution Query:**
```sql
SELECT e.employee_name, d.department_name, pr.project_name
FROM Employees e
JOIN Departments d ON e.department_id = d.department_id
JOIN Projects pr ON e.employee_id = pr.employee_id;
```

**Line-by-Line:**
- `Employees e JOIN Departments d ON e.department_id = d.department_id` — Pehla join: employee ke saath uska department jodta hai.
- `JOIN Projects pr ON e.employee_id = pr.employee_id` — Doosra join: **pehle join ke result** ke upar, ab Projects table ko bhi jodte hain employee_id se match karke. Effectively database pehle Employees+Departments ka combined set banata hai, phir us poore set ko Projects ke saath match karta hai.
- Dono joins `INNER JOIN` hain (default) — matlab sirf wahi employees dikhenge jinka valid department **ho** aur kam se kam ek project **bhi ho**.

**Naya Concept — Chaining Multiple Joins:**
"Sir, joins ko sequentially chain kiya ja sakta hai jitni bhi tables involve karni ho — database internally pehle do tables ko combine karta hai ek intermediate result set banakar, phir us poore set ko agli table ke saath match karta hai. Order thoda matter karta hai readability aur kabhi-kabhi performance ke liye, lekin logically result same rahega chahe kisi bhi order mein tables join karo (jab tak sab INNER JOIN hain)."

**Expected Output (is sample data pe):**
| employee_name | department_name | project_name |
|---|---|---|
| Ravi Kumar | Sales | Website Redesign |
| Ravi Kumar | Sales | Mobile App |
| Amit Verma | Engineering | Data Migration |

**Important note:** Notice karo Ravi Kumar ki row **do baar** aa rahi hai — kyunki uske 2 projects hain. Ye bilkul expected hai (one-to-many join), lekin agar isi result pe aage koi aggregate (jaise "har department mein kitne employees hain") calculate karne ki koshish karoge, to ye duplication ek **trap** ban jaayegi — yehi Q32 ka topic hai.

**Follow-up Questions:**
- Ravi Kumar ki row do baar kyun aa rahi hai — kya ye ek bug hai?
- Employees jinka **koi project nahi hai**, unhe bhi is result mein kaise include karoge? *(LEFT JOIN Projects ke saath.)*
- Agar Kunal Joshi ka department NULL hota aur wo kisi project pe kaam kar raha hota, to kya wo is INNER-JOIN wale result mein dikhta?

---

### Q32. Join ke baad duplicate rows kaise ban jaati hain (many-to-many/one-to-many join pitfall)

**Interview mein aise poocha jaayega:**
> "You wrote a query to count the number of employees per department by joining Employees, Departments, and Projects. The Sales department shows 2 employees, but you know it actually has 3. What went wrong, and how do you fix it?"

**Thought Process:**
- Ye ek **debugging/conceptual** question hai, naya query likhne se zyada — interviewer tumhara **depth of understanding** test kar raha hai joins ke internals ka.
- Keyword pakdo: **"count shows wrong number after joining"** → classic signal hai ki kahin **row multiplication (fan-out)** ho rahi hai kisi one-to-many relationship ki wajah se.

**Problem Query (jo galat count deti hai):**
```sql
-- YE QUERY GALAT HAI — dept-wise employee count inflate ho jaata hai
SELECT d.department_name, COUNT(*) AS employee_count
FROM Employees e
JOIN Departments d ON e.department_id = d.department_id
JOIN Projects pr ON e.employee_id = pr.employee_id
GROUP BY d.department_name;
```

**Kya ho raha hai (dry-run karke dikhao):**
Q31 ka result yaad karo — Ravi Kumar (Sales) ki row **2 baar** aayi thi (2 projects ki wajah se), Amit Verma (Engineering) ki row **1 baar**. Ab agar is 3-row combined result pe `COUNT(*) GROUP BY department_name` chalate ho, to:
- Sales → Ravi ki 2 rows count ho jaayengi → **`employee_count = 2`**, jabki Sales mein actually 3 employees hain (Ravi, Priya, Ananya) — lekin Priya aur Ananya ka to koi project hi nahi hai Projects table mein, isliye INNER JOIN ne unhe already drop kar diya, aur bacha hua Ravi 2 baar count ho gaya.
- Engineering → Amit ki 1 row → `employee_count = 1`, jabki Engineering mein actually 2 employees hain (Amit, Sneha) — Sneha ke paas project nahi hai isliye wo bhi drop ho gayi.
- **Result poori tarah misleading hai** — dono galat directions mein (kabhi undercounting employees bina projects wale, kabhi overcounting employees multiple-projects wale).

**Fix (Approach 1 — Aggregate Projects pehle, phir join karo):**
```sql
SELECT d.department_name, COUNT(DISTINCT e.employee_id) AS employee_count
FROM Employees e
JOIN Departments d ON e.department_id = d.department_id
LEFT JOIN Projects pr ON e.employee_id = pr.employee_id
GROUP BY d.department_name;
```

**Line-by-Line (fix ka):**
- `LEFT JOIN Projects` — Ab employees jinka koi project nahi hai (Priya, Sneha, Kunal, Neha, Ananya), wo bhi drop nahi honge.
- `COUNT(DISTINCT e.employee_id)` — Ye **duplicate rows ko count karte waqt ignore** kar deta hai — chahe Ravi ki row 2 baar aaye (2 projects ki wajah se), `DISTINCT` uske `employee_id` ko sirf **ek baar** count karega.

**Naya Concept — Join Fan-Out / Row Duplication Trap:**
"Sir, jab bhi ek query mein **ek-se-zyada 'many' relationship** ek saath join ki jaati hai (jaise yaha ek employee ke multiple projects ho sakte hain), to result set mein rows **multiply** ho jaati hain — ye koi bug nahi, ye join ka **correct, expected behaviour** hai. Lekin agar iske baad hum `COUNT(*)` jaisa simple aggregate laga dein bina soche, to numbers **inflate** ho jaate hain. Isse bachne ke do standard tarike hain: ya to `COUNT(DISTINCT primary_key)` use karo (jaisa upar dikhaya), ya phir doosri table (Projects) ko **pehle khud alag se aggregate** kar lo (jaise 'har employee ke kitne projects hain' ek subquery/CTE mein nikaal lo), phir usko join karo — taaki row-multiplication hi na ho."

**Follow-up Questions:**
- `COUNT(*)` aur `COUNT(DISTINCT e.employee_id)` mein yaha specifically kya farak aaya?
- Agar humein sirf employee count nahi, balki **"har employee ke kitne projects hain"** bhi chahiye ho sath mein, to `COUNT(DISTINCT)` wala approach kaam karega ya nahi?
- Ye fan-out problem sirf 3-table joins mein hoti hai, ya 2-table `LEFT JOIN` (jaise Q27 ka Customers-Orders) mein bhi ho sakti hai? *(Haan, Q27 mein bhi ho rahi thi — C1/C2 ki rows 2-2 baar aayi thi — wahi concept hai.)*

---

## Quick Revision Index (concept jaha pehli baar aaya)

| Concept | Question |
|---|---|
| `ORDER BY` / `ASC` / `DESC` | Q1 |
| `DISTINCT` | Q2 |
| Column Alias (`AS`) | Q3 |
| `LIMIT` | Q4 |
| Function on column in WHERE (`YEAR()`) + non-sargable performance issue | Q5 |
| `LIKE` + wildcards (`%`, `_`) | Q6 |
| `BETWEEN` (inclusive) | Q7 |
| `IN` operator | Q8 |
| `IS NULL` / Three-valued logic | Q9 |
| Relative date filtering (`CURDATE()`, `INTERVAL`) | Q10 |
| Wildcard dono taraf (`%value%`) | Q11 |
| Month-based filtering, missing-year gotcha | Q12 |
| NULL + negative comparison trap (`!=`, `NOT IN`) | Q14 |
| `GROUP BY` | Q15 |
| `HAVING` vs `WHERE` | Q17 |
| Aggregate + Join together | Q18 |
| Join + Group By revenue pattern | Q20 |
| Subquery inside `HAVING` (non-correlated) | Q21 |
| Conditional Aggregation (`SUM(CASE WHEN...)`) | Q22 |
| `INNER JOIN` | Q23 |
| `LEFT JOIN` | Q24 |
| `RIGHT JOIN` (aur kyun avoid karte hain) | Q25 |
| `SELF JOIN` | Q26 |
| `LEFT JOIN ... WHERE ... IS NULL` (Anti-Join) | Q29 |
| Self Join + Filter (row comparison pattern) | Q30 |
| Chaining multiple joins | Q31 |
| **Join Fan-Out / Row Duplication Trap** | Q32 |

---

## Kaise practice karo isse

1. File ko top se padho ek baar — sirf **Interview Question** aur **Thought Process** dekho, khud query likhne ki koshish karo (bina scroll kiye).
2. Apni query ko yaha di gayi **Solution Query** se compare karo.
3. **Line-by-Line** section ko zor se bolke practice karo — jaise interviewer saamne baitha ho.
4. Har question ke **Follow-up Questions** khud se answer karne ki koshish karo — yehi asli interview mein poochhe jaate hain.
5. Jo bhi concept weak lage, **Quick Revision Index** se seedha us question pe jump karo.

*Category 5 (Subqueries), 6 (CTE), 7 (Window Functions), aur 8 (Advanced Patterns) ke liye isi format mein alag notes bana denge jab tum wahan tak pahunchoge.*
