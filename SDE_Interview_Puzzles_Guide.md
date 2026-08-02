# 🧩 SDE Interview Puzzles — Zero se Interview-Ready Guide

Iss version mein har puzzle ek **mock interview** ki tarah likha hai — pehle interviewer sawaal puchta hai, fir "Tera Jawab" section mein wahi soch dikhayi hai jo ek achha candidate REAL TIME mein bolega — hesitate karte hue, chote cases try karte hue, pattern dhoondte hue, aakhir mein confident answer tak pahunchte hue. Ye padh ke tujhe pata chalega asli interview mein KAISE BOLNA hai, sirf answer nahi.

---

## PART 1: Kisi bhi Puzzle ko Attack Kaise Karein (ye pehle samajh)

Interviewer puzzle isliye nahi puchta ki tujhe answer pata hai ya nahi — wo dekhna chahta hai **tu pressure mein kaise sochta hai**. Isliye answer bolne se zyada important hai process bolna.

### 5-Step Framework (har puzzle pe use karo)

1. **Repeat the problem** — jo interviewer ne bola, apne words mein wapas bolo ("Toh basically aapko ye chahiye ki...") — isse galat assumption pe time waste nahi hota.
2. **Constraints likho** — kitne cuts allowed, kitna time, kya-kya diya hai, kya nahi pata.
3. **Chota case try karo** — agar 100 doors hai toh pehle 10 doors pe socho, pattern dikhega.
4. **Loud socho** — chup reh ke answer mat socho, jo bhi dimaag mein aa raha hai bolte jao. Interviewer wrong direction se turant sahi direction dikha dega agar tu bol raha hai.
5. **Answer ke baad verify** — chota example lekar apna answer check karo.

### Puzzle "Families" — Pattern Pehchano

| Type | Pehchan Kaise | Common Trick |
|---|---|---|
| **Weighing/Balance** | "balance scale", "find odd ball" | Groups mein baato (usually 3 parts), har weighing 3 outcomes deti hai |
| **Truth-teller/Liar** | "one always lies, one always speaks truth" | Aisa sawaal socho jiska jawab dono case mein SAME aaye |
| **Measurement (jugs/wires)** | "measure X liters/minutes" | Target se ulta (backward) socho, ya donon end jalao time half karne ke liye |
| **Invariant/Pattern** | "100 doors", "bulbs toggle", "balls remain" | Parity (odd/even) ya kisi property ko track karo jo har step mein same rehti hai |
| **Optimization/Logistics** | "bridge crossing", "minimize time/bananas" | Sabse slow/expensive resource ko minimum baar move karo |
| **Probability/Expected value** | "probability of...", "maximize chance" | Symmetry dhoondo, ya extreme case try karo (jaise saara ek jagah daal do) |

---

## PART 2: Tere 20 Puzzles — Mock Interview Style

### 1. Three Ants and Triangle

**🎤 Interviewer:** "Teen ants triangle ke teeno corners pe baithi hain. Har ant random ek direction (clockwise ya anticlockwise) choose karke edge ke saath chalna start karti hai. Kisi bhi do ants ke collide karne ki probability kya hai?"

**🧠 Tera Jawab:** "Okay, pehle main samajhta hoon — har ant ke paas sirf 2 choices hain: clockwise ya anticlockwise, aur teeno independently choose kar rahi hain. Toh total possible combinations = 2 × 2 × 2 = 8, aur ye sab equally likely hain.

Ab collision kab NAHI hoga, wo dekhna easier hai kyunki collision ke bohot saare cases hain but non-collision ke kam. Agar teeno SAME direction mein chalein — sab clockwise, ya sab anticlockwise — toh koi kisi se nahi takrayega, wo bas ek doosre ka peecha karte rahenge triangle ke around. Ye sirf 2 cases hain.

Baaki sab 6 combinations mein kam se kam do ants ek doosre ki taraf chal rahi hongi kisi edge pe, toh wo zaroor collide karengi.

Toh P(no collision) = 2/8 = 1/4, isliye P(collision) = 1 − 1/4 = 3/4."

**✅ Final Answer: 3/4**

---

### 2. Heaven and Hell

**🎤 Interviewer:** "Do gates hain — ek heaven ko, ek hell ko. Do gatekeepers hain, ek hamesha sach bolta hai, ek hamesha jhooth. Pata nahi kaun kaunse gate ka gatekeeper hai. Tumhe sirf EK sawaal puchna hai (kisi bhi ek gatekeeper se) aur heaven ka gate pata karna hai."

**🧠 Tera Jawab:** "Sabse pehle problem yeh hai ki mujhe nahi pata jis gatekeeper se pooch raha hoon wo truthful hai ya liar — toh mera sawaal aisa hona chahiye jo dono cases mein SAME answer de.

Agar main direct pucho 'ye heaven ka gate hai?' — truthful se sahi milega, liar se ulta. Problem ye hai mujhe pata nahi kaunsa hai, toh answer trust nahi kar sakta.

Idea ye hai — main ek 'nested' sawaal poochta hoon: 'Agar main doosre gatekeeper se poochun ki heaven ka gate kaunsa hai, toh wo kya bolega?'

Dekho ye kaise kaam karta hai: Agar main truthful guard se poochu — wo sach-sach bata dega ki doosra (liar) heaven ke baare mein JHOOTH bolega, matlab hell wala gate bolega. Toh truthful guard 'hell wala gate' bolega.

Agar main liar guard se poochu — wo JHOOTH bolega ki doosra (truthful) kya bolega. Truthful sahi (heaven) bolta, but liar isko ulta bata dega — wo bhi 'hell wala gate' bolega.

Dono case mein jawab HELL ka gate aata hai! Toh jo bhi gate wo bataye, main uske OPPOSITE wale gate se chala jaunga."

**✅ Final Answer:** Pucho "doosra guard kya kahega heaven ke baare mein" → jawab ka **opposite** lo.

---

### 3. 10 Coins Puzzle

**🎤 Interviewer:** "Tum blindfolded ho, table pe 10 coins hain. Pata hai 5 heads-up hain, 5 tails-up, but nahi pata kaunse kaunse. Coins ko touch/flip kar sakte ho but dekh/feel karke heads-tails distinguish nahi kar sakte. Do piles banao jisme heads ki count barabar ho."

**🧠 Tera Jawab:** "Main coins ko dekh nahi sakta, toh solution kisi aisi manipulation se aana chahiye jo GUARANTEED kaam kare, chahe arrangement kuch bhi ho.

Chalo chota example try karta hoon — 4 coins, 2 heads 2 tails. Agar random 2 ka ek pile banau, doosra 2 ka — pehle pile mein heads 0,1,ya 2 ho sakti hain, control nahi hai.

Lekin agar pehle pile ko poora FLIP kar doon? Maan lo pehle pile (2 coins) mein k heads the — flip ke baad wo (2-k) heads reh jaate hain (k heads tails ban gaye, (2-k) tails heads ban gaye).

Doosre pile mein kitne heads honge? Total heads 2 the, pehle mein original k the, toh doosre mein bacha (2-k).

Dekho — flip ke baad dono piles mein heads count (2-k) hai — BARABAR, chahe k kuch bhi ho!

General solution: 10 coins mein random 5 ka pile banao, doosra pile baaki 5 ka. Pehle pile ke saare 5 coins FLIP kar do — dono piles mein heads count guaranteed barabar ho jayegi."

**✅ Final Answer:** Random 5 ka pile banao, use poora flip karo → heads equal ho jaate hain.

---

### 4. Mislabeled Jars

**🎤 Interviewer:** "3 jars hain — A('Candies'), B('Sweets'), C('Candies and Sweets' mixed). TEENO labels galat hain. Ek time pe sirf ek item nikal sakte ho. Minimum kitne items nikalne padenge sab jars correctly label karne ke liye?"

**🧠 Tera Jawab:** "Sabse pehle — teeno labels galat hain, ye bohot strong info hai, yahin se start karta hoon.

Jar C pe likha 'mixed' hai — but ye galat hai. Iska matlab jar C mixed NAHI ho sakta, wo PURE candies hoga ya PURE sweets. Toh agar main isse sirf EK item nikalu, mujhe with certainty pata chal jayega jar C asal mein kya hai.

Maan lo nikla item 'Candy' hai — toh jar C = pure Candies.

Ab jar B pe likha 'Sweets' hai, jo galat hai — toh B Sweets nahi hai. B ya toh Candies hai ya Mixed. Chunki C already Candies nikla (duplicate nahi ho sakta), B = **Mixed** hoga.

Bacha jar A — uska label 'Candies' bhi galat hai, aur Candies (C ke paas) + Mixed (B ke paas) already assign ho chuke, toh A = **Sweets** by elimination."

**✅ Final Answer:** Sirf **1 pick**, jar labeled "Mixed" se — baaki elimination se.

---

### 5. 50 Red + 50 Blue Marbles

**🎤 Interviewer:** "2 boxes hain, total 50 red + 50 blue marbles, jaise chaho baant sakte ho. Ek random box choose hoga (50-50), fir us box se random ball. Probability of RED maximize karo."

**🧠 Tera Jawab:** "Pehle equal split try karta hoon dimaag mein — 25-25 dono box mein, toh P(red) = 0.5. Ye baseline hai.

Extreme case try karta hoon — ek box mein SIRF red daal doon (guaranteed red), doosre mein baaki sab. Box 1 mein sirf **1 red marble**. Box 2 mein 49 red + 50 blue = 99 marbles.

Calculate karta hoon: 50% chance Box 1 → red guaranteed (100%). 50% chance Box 2 → red chance = 49/99 ≈ 49.5%.

Total = 0.5×1 + 0.5×(49/99) = 0.5 + 0.2475 ≈ 0.7475 (~74.7%).

Verify karta hoon ye optimal hai ya nahi — agar Box 1 mein 2 red daalu instead of 1, Box 1 se guaranteed red hi milta rahega (koi fayda nahi), lekin Box 2 mein red kam ho jayega (48/98 ≈ 48.97%, jo worse hai). Toh Box 1 mein **exactly 1 red** rakhna optimal hai."

**✅ Final Answer:** Box 1 mein sirf 1 red, Box 2 mein baaki sab → **74/99 ≈ 74.7%**

---

### 6. Minimum Cuts — Gold Bar, 5 Days

**🎤 Interviewer:** "Worker 5 din kaam karega. Gold bar hai. Har din end mein use gold ka piece dena hai jo total payment ka 1/5th ho. Minimum kitne cuts?"

**🧠 Tera Jawab:** "Pehle clarify karna chahunga — kya piece wapas leke exchange kar sakta hoon, ya sirf directly dena hai bina wapas liye? [Standard reading yahi hai ki koi exchange mention nahi hai, matlab jo diya wapas nahi lena.]

Agar exchange allowed NAHI hai, mujhe literally 5 distinct equal pieces chahiye bina kisi ko wapas liye. n equal pieces banane ke liye (n-1) cuts lagte hain — jaise rassi ko 2 pieces mein todne ke liye 1 cut, 3 ke liye 2 cuts.

Toh 5 pieces ke liye 4 cuts."

**✅ Final Answer:** **4 cuts**
*(Note: iska cousin puzzle — 7 din, sirf 2 cuts, agar exchange allowed ho — alag answer deta hai, wo #17 mein hai neeche.)*

---

### 7. 100 Doors

**🎤 Interviewer:** "100 doors row mein, sab closed. Person 100 baar walk karta hai — walk N mein har N-th door toggle karta hai. End mein kaunse doors open rahenge?"

**🧠 Tera Jawab:** "Chota case try karta hoon — 10 doors. Door 6 kab-kab toggle hoga? Jab walk number, 6 ka divisor ho — walks 1,2,3,6 sab door 6 ko touch karenge. Divisors of 6 = {1,2,3,6}, count = 4 (even). 4 toggles = wapas closed (start closed tha).

Door 9 dekhta hoon — divisors of 9 = {1,3,9}, count = 3 (odd). 3 toggles: closed→open→closed→open. End mein OPEN.

Pattern dikh raha hai — jo door OPEN reh raha hai uske divisors ODD count mein hain. Normally divisors PAIRS mein aate hain (d aur n/d), jo count ko even bana deta hai — SIVAY jab number PERFECT SQUARE ho (jaise 9=3×3, 3 khud se pair hota hai, sirf ek baar count hota hai) — tabhi count odd hota hai.

Toh sirf perfect squares wale doors open rahenge: 1,4,9,16,25,36,49,64,81,100."

**✅ Final Answer:** **10 doors open** (perfect squares)

---

### 8. Find Fastest 3 Horses (25 horses, races of 5)

**🎤 Interviewer:** "25 horses, top 3 fastest dhoondhne hain. Ek race mein max 5 horses, sirf relative order milta hai (koi stopwatch nahi). Minimum kitni races?"

**🧠 Tera Jawab:** "Pehla step obvious hai — 25 horses ko 5-5 ke 5 groups mein baato aur race karao. 5 races hui, ab har group ka internal order pata hai.

Ab har group ke winner ko race karau — 6th race — winner overall FASTEST hai, confirm ho gaya.

Ab #2 aur #3 kaun, ye tricky hai. Naive lagega ki 6th race ke 2nd-3rd le lo, lekin galat hai — kyunki ho sakta hai kisi group mein 2nd aur 3rd fastest horse dono the (uska group ka winner overall fastest tha), wo apne group mein hi 2nd-3rd reh gaye bina doosre groups ke fast horses se race kiye.

Sahi candidates: 6th race ka 2nd place, 6th race ka 3rd place, AUR overall winner ke apne group ka 2nd-3rd place horse (kyunki winner sabse fast tha, uske group ke 2nd-3rd bhi contenders hain).

In candidates ko ek final race (7th) mein daalo — top 2 wahi #2 aur #3 honge."

**✅ Final Answer:** **7 races**

---

### 9. Bee Distance Between Trains

**🎤 Interviewer:** "2 trains ek doosre ki taraf aa rahi hain — 50 km/h aur 70 km/h. Jab 100 km door hain, ek bee (80 km/h) train A se B ki taraf udna start karti hai, wapas A ki taraf, aise repeat jab tak trains collide na ho jayen. Bee ki total distance?"

**🧠 Tera Jawab:** "Dekhne mein complicated lagta hai kyunki bee baar-baar direction change kar rahi hai. Lekin main path track nahi karunga — sirf ye chahiye ki bee kitni DER tak udd rahi hai, kyunki distance = speed × time.

Bee tab tak udegi jab tak trains collide na ho jaye. Trains ek doosre ki taraf aa rahi hain, combined closing speed = 50+70 = 120 km/h. Distance 100 km. Time to collide = 100/120 = 5/6 hour.

Bee is poore time (5/6 hour) tak apni constant 80 km/h speed se udd rahi hai — chahe kitni baar direction change kare, total distance = speed × time.

Distance = 80 × 5/6 = 400/6 = 200/3 ≈ 66.67 km."

**✅ Final Answer:** **200/3 ≈ 66.67 km**

---

### 10. Cut Cake into 8 Equal Pieces, 3 Cuts

**🎤 Interviewer:** "Round cake hai, 3 cuts mein 8 equal pieces mein kaato."

**🧠 Tera Jawab:** "Pehla instinct — top se dekh ke 2D mein socho. 3 straight cuts se top-view mein max 7 pieces ban sakte hain, aur equal bhi nahi honge easily 3 cuts mein 8 ke liye. Toh 3D mein sochna padega — HEIGHT bhi use karni hai.

Pehle 2 cuts: cake ke center se guzarte hue, ek doosre ke perpendicular — jaise '+' sign top se. Isse cake 4 equal quarters mein bat jaata hai.

Teesra cut: cake ko HORIZONTALLY kaato — jaise burger bun ko beech se slice karte hain, top aur bottom half alag karke, height ke beech se.

Har quarter-piece ab do hisso (top+bottom) mein bat jaata hai — 4 × 2 = 8 equal pieces."

**✅ Final Answer:** 2 vertical perpendicular cuts (center se) + 1 horizontal cut

---

### 11. Last Ball Remaining (20 Red, 16 Blue)

**🎤 Interviewer:** "Bag mein 20 red, 16 blue balls. Har step: 2 balls nikalo — same color → 1 BLUE daal do; different color → 1 RED daal do (nikale hue wapas nahi jaate). Ye chalta hai jab tak 1 ball na bache. Uska color?"

**🧠 Tera Jawab:** "Har step mein 2 nikalte, 1 daalte — total 1 se kam hota rehta hai, guaranteed terminate hoga 1 ball pe.

Color predict karne ke liye ek INVARIANT dhoondta hoon — kuch property jo har step mein SAME rahe.

Red balls ki COUNT ki PARITY (odd/even) track karta hoon, case by case:
- 2 RED nikale → replace 1 blue → red count −2 (even change, parity same)
- 2 BLUE nikale → replace 1 blue → red unchanged (parity same)
- 1 red + 1 blue nikale → replace 1 red → red: −1(nikla) +1(add hua) = 0 net change (parity same)

Teeno cases mein red ki PARITY kabhi change nahi hoti — ye invariant hai!

Shuru mein red = 20 (EVEN). Toh hamesha red count EVEN rahega.

Jab 1 ball bachegi, red count 0 ya 1 hoga. Parity even honi chahiye, toh red = 0 (0 even, 1 odd) — last ball RED nahi ho sakti."

**✅ Final Answer:** **Blue**

---

### 12. Two Water Jug Problem (m, n liters → d liters)

**🎤 Interviewer:** "m-liter aur n-liter jug hain (0<m<n), dono khali, koi markings nahi. Exactly d liters measure karo (d<n) using fill/empty/pour operations."

**🧠 Tera Jawab:** "Pehle check karta hoon ye possible bhi hai ya nahi — fill/empty/pour se jo quantity bane, wo hamesha m aur n ka linear combination hoti hai (Bezout's identity se). Ye tabhi d de sakti hai jab d, gcd(m,n) ka multiple ho.

Agar possible hai, toh main states systematically explore karunga (BFS jaisa) — (jug1, jug2) ek state hai, operations se naye states bante hain.

Example se dikhata hoon — 4L aur 3L jug se 2L nikalna hai. gcd(4,3)=1, 2 iska multiple hai, possible hai.
1. 4L fill karo → (4,0)
2. 4L se 3L mein pour karo (3L full ho jaye) → (1,3)
3. 3L empty karo → (1,0)
4. Bacha 1L, 3L mein pour karo → (0,1)
5. 4L phir fill karo → (4,1)
6. 4L se 3L mein pour karo (3L ko sirf 2L aur chahiye full hone ke liye) → (2,3)

Ab 4L jug mein exactly 2L hai."

**✅ Final Answer:** Method — gcd(m,n) divide d karta ho tabhi possible, fir fill/pour/empty states simulate karo.

---

### 13. 3 Bulbs and 3 Switches

**🎤 Interviewer:** "Room mein 3 bulbs, bahar 3 switches (pata nahi kaunsa kaunse se connect hai). Switches jaise chaho operate karo, lekin ek baar room khola toh switches change nahi kar sakte. Har switch ka bulb identify karo."

**🧠 Tera Jawab:** "Challenge ye hai ki sirf ek baar dekh sakta hoon — matlab sirf ON/OFF se 3 bulbs distinguish karna hai, but 2 states hain 3 cheezon ke liye — direct approach kaam nahi karega.

Mujhe ek THIRD signal chahiye jo visually na dikhe lekin detectable ho — HEAT! Bulb jalne pe garam hota hai, off karne ke baad bhi kuch der garam rehta hai.

Strategy: Switch 1 ON karo, 5 min wait karo, fir OFF kar do. Switch 2 ON karo, wahi chhod do. Switch 3 touch mat karo. Room mein jao.

Jo bulb ON hai → Switch 2. Jo OFF hai lekin GARAM hai → Switch 1 (pehle jal chuka tha). Jo OFF hai aur THANDA hai → Switch 3 (kabhi jala hi nahi)."

**✅ Final Answer:** ON = Switch2, OFF+garam = Switch1, OFF+thanda = Switch3

---

### 14. Measure 45 Minutes Using 2 Wires (each burns in 1 hour, non-uniform)

**🎤 Interviewer:** "2 wires, dono ek end se jalao toh exactly 60 min mein khatam hoti hain, but NON-UNIFORMLY jalti hain (length aur time proportional nahi). Matchsticks hain. Exactly 45 minutes measure karo."

**🧠 Tera Jawab:** "Non-uniform hai toh LENGTH pe bharosa nahi kar sakta. Lekin TOTAL TIME fixed hai — poori wire hamesha 60 min leti hai, kisi bhi end se.

Trick yaad aati hai — agar wire ko DONO ends se ek saath jalao, toh wo exactly ADHE TIME (30 min) mein khatam ho jaati hai, chahe burning pattern kuch bhi ho.

Plan: t=0 pe Wire A ko DONO ends se jalao, Wire B ko sirf EK end se. Wire A 30 min mein khatam hogi. Jaise hi A khatam ho (t=30), turant Wire B ka DOOSRA end bhi jala do.

Wire B ek end se 30 min jal chuki hai. Total 60 min leti hai ek end se, toh bache hue hisse ko usi end se jalane mein 30 min AUR lagte (kyunki total hamesha 60, 30 already ho chuke).

Ab doosra end bhi jal raha hai — jo bacha hai wo dono taraf se jal raha hai, jo adhe time mein khatam hota hai. Toh 30 min ki jagah sirf 15 min lagenge.

Total = 30 + 15 = 45 minutes."

**✅ Final Answer:** **45 minutes** — dono end A + ek end B jalao, A khatam hote hi B ka doosra end jalao.

---

### 15. Find Ages of Daughters (product = 72)

**🎤 Interviewer:** "Alok ki 3 betiyan. Hint 1: ages ka product 72. Shyam bolta hai 'kaafi nahi'. Hint 2: sum = ghar ka number. Shyam ghar dekh ke aata hai, phir bhi bolta hai 'pata nahi chal raha'. Hint 3: sabse badi beti ko strawberry ice cream pasand hai. Ab bata deta hai. Ages kya hain?"

**🧠 Tera Jawab:** "Pehla hint — product 72. Saare positive integer triplets (a,b,c) list karta hoon: (1,1,72), (1,2,36), (1,3,24), (1,4,18), (1,6,12), (1,8,9), (2,2,18), (2,3,12), (2,4,9), (2,6,6), (3,3,8), (3,4,6).

Doosra hint — Shyam sum jaan ke bhi confuse hai. Iska matlab us SUM VALUE ke MULTIPLE triplets hain.

Sums nikalta hoon: 74, 39, 28, 23, 19, 18, 22, 17, 15, 14, 14, 13.

Dekho — 14 do baar aa raha hai: (2,6,6) aur (3,3,8). Ye ambiguous case hai, ghar ka number 14 hai.

Teesra hint — 'sabse badi beti' (singular, unique oldest). (2,6,6) mein do betiyan 6-6 ki hain, tie hai, koi unique oldest nahi. (3,3,8) mein sirf ek 8-saal ki hai jo clearly sabse badi hai."

**✅ Final Answer:** **3, 3, 8**

---

### 16. 10 Balls in 5 Lines (4 balls each line)

**🎤 Interviewer:** "10 balls ko 5 lines mein arrange karo aise ki har line mein exactly 4 balls ho."

**🧠 Tera Jawab:** "Simple grid try karta hoon dimaag mein — normal independent rows mein 10 balls, 5 rows, har row mein sirf 2 balls fit hongi. Mujhe kuch chahiye jaha LINES EK DOOSRE KO CROSS karein, taaki ek ball multiple lines mein count ho sake.

Ye mujhe STAR shape yaad dilata hai — 5-pointed star (pentagram), jo bina pen uthaye continuous banate hain.

5-pointed star mein: 5 outer tips + 5 inner points (jaha lines cross karti hain) = total 10 points.

Har line (star ka ek edge) apne 2 outer tips ko connect karta hai, aur beech mein 2 aur lines ko cross karta hai (2 inner points) — total 4 points per line."

**✅ Final Answer:** **5-pointed star (pentagram)** arrangement

---

### 17. Gold Rod 7 Units, 2 Cuts, Pay Daily (exchange allowed)

**🎤 Interviewer:** "Employee 7 din kaam karega, 7-unit gold rod hai. Har din end mein uske paas total gold 1 unit badhna chahiye. Max 2 cuts allowed. Kaise?"

**🧠 Tera Jawab:** "7 din, payment 1,2,3,...,7 tak sequentially badhega. Agar one-way dena hota (wapas nahi lena), 7 pieces (6 cuts) chahiye hote — but sirf 2 cuts hain, matlab EXCHANGE allowed honi chahiye.

2 cuts se 3 pieces milte hain. Agar pieces BINARY progression mein banau — 1, 2, 4 — inko combine/exchange karke 1 se 7 tak koi bhi sum ban sakta hai. Verify: 1+2+4=7 ✓.

Daily exchange:
Day1: 1 do (1). Day2: 1 wapas, 2 do (2). Day3: 1 do (3). Day4: 1+2 wapas, 4 do (4). Day5: 1 do (5). Day6: 1 wapas, 2 do (6). Day7: 1 do (7).

Har din exact N units uske paas hain."

**✅ Final Answer:** Pieces = **1, 2, 4** (2 cuts), binary exchange

---

### 18. Torch and Bridge (A=1, B=2, C=5, D=8 min)

**🎤 Interviewer:** "4 log — A(1), B(2), C(5), D(8 min) — bridge cross karni hai raat mein. Ek torch, max 2 log saath, torch hamesha chahiye, do log saath chalein toh slower ki speed lagti hai. Minimum total time?"

**🧠 Tera Jawab:** "Torch WAPAS bhi laani padti hai har baar. Main sochta hoon slowest do (C=5, D=8) ko minimum impact ke saath kaise cross karau — unka combined time (max(5,8)=8) sirf EK BAAR count hona chahiye.

Strategy: 
1. A+B cross (max(1,2)=2) — total 2
2. A wapas (1) — total 3
3. C+D cross (max(5,8)=8) — total 11
4. B wapas (2) — total 13
5. A+B cross (2) — total 15

Compare karta hoon alternative se — agar A har baar wapas jaye: A+C(5), A wapas(1), A+D(8), A wapas(1), A+B(2) = 17, ye worse hai.

Best strategy: slow logon ko SAATH bhejo (combined max time ek hi baar), torch wapas laane ke liye fastest do logon (A,B) ko use karo."

**✅ Final Answer:** **15 minutes**

---

### 19. Poison and Rat (1000 bottles, find in 1 hour)

**🎤 Interviewer:** "1000 wine bottles, ek poisoned hai. Rat poison piye toh 1 hour mein marta hai. 1 hour mein find out karna hai kaunsi bottle poisoned hai. Minimum kitne rats?"

**🧠 Tera Jawab:** "Sequential test (1 rat, 1 bottle) 1000 tests lega, time sirf 1 hour hai. Mujhe PARALLEL information encoding chahiye.

Har rat YES/NO signal de sakta hai. N rats se total outcomes = 2^N. Har bottle ko unique combination assign kar sakta hoon (binary number jaisa) — jis bottle poisoned hai, uska binary pattern hi ye batayega ki KAUN SE rats marenge.

Bottles ko 1-1000 number do, binary mein likho. Rat i ko wahi bottles piladunga jinke binary mein i-th bit set hai.

Ek ghante baad jo rats mare, unke bit positions milake poisoned bottle ka number ban jayega.

2^N ≥ 1000 chahiye: 2^9=512 (kam), 2^10=1024 (kaafi)."

**✅ Final Answer:** **10 rats**

---

### 20. Camel and Banana Puzzle (3000 bananas, 1000 km, capacity 1000)

**🎤 Interviewer:** "3000 bananas, camel max 1000 carry kar sakta hai, har km 1 banana khaata hai. Destination 1000 km door. Max kitne bananas pahunch sakte hain?"

**🧠 Tera Jawab:** "3000 bananas but camel sirf 1000 carry karta hai — 3 'loads' hain, camel ko baar-baar wapas aana padega.

3 loads ko 1 km move karne ke liye: forward-back-forward-back-forward = 5 crossings, rate = 5 bananas/km, jab tak stock 2000 (2 loads) na ho jaye. Budget = 3000-2000=1000. Distance = 1000/5 = 200 km.

2 loads ke liye rate = 3 bananas/km (forward-back-forward), jab tak 1000 (1 load) na bache. Budget=1000. Distance=1000/3≈333.33 km.

Total covered = 200+333.33=533.33 km. Baaki = 466.67 km.

1 load bacha, sirf FORWARD, rate=1 banana/km. 466.67 km mein 466.67 bananas kharch.

Destination = 1000 − 466.67 ≈ 533.33"

**✅ Final Answer:** **≈533 bananas**

---

## PART 3: 7 Extra Commonly-Asked Puzzles — Mock Interview Style

### 21. Egg Drop Puzzle (2 eggs, 100-floor building)

**🎤 Interviewer:** "2 eggs, 100-floor building. Egg gira ke pata karna hai kaunse floor se upar egg tootne lagta hai (critical floor). Worst-case minimum drops chahiye."

**🧠 Tera Jawab:** "Naive linear search — floor 1 se ek-ek upar, worst case 100 drops. Bohot zyada.

Better — pehle egg se bade jumps loon (risk), agar toota toh doosre se linear search chote range mein. Trade-off — bada jump loon shuru mein toh agar TOOT gaya, doosre egg ke liye kam drops bachenge (kyunki total drops FIXED rakhna hai).

Isliye jump size HAR BAAR chhota hona chahiye jaise aage badhta hoon — taaki total drops (kahin bhi break ho) hamesha same rahe.

Maan lo total = n. Pehla drop floor n se, agla n+(n-1), phir n+(n-1)+(n-2)... Total floors = n(n+1)/2 ≥ 100.

n=13: 91 (kam). n=14: 105 (kaafi)."

**✅ Final Answer:** **14 drops** (worst case)

---

### 22. 9 Balls Weighing Puzzle

**🎤 Interviewer:** "9 balls, ek heavier hai baaki same weight. Balance scale (left/right/balanced) se minimum weighings mein heavy ball dhoondo."

**🧠 Tera Jawab:** "Balance scale ek weighing mein 3 outcomes deta hai — left heavy, right heavy, equal. Isliye balls ko 2 nahi, 3 groups mein baatunga.

9 balls → 3-3-3.
Weighing 1: Group A vs Group B. Balance → heavy ball Group C mein. Unbalanced → heavy side wale group mein.

Ab 3 candidates bache.
Weighing 2: un 3 mein se 2 ko scale pe rakho (1v1), teesra bahar. Equal → bahar wala heavy. Unequal → bhari side heavy."

**✅ Final Answer:** **2 weighings**

---

### 23. 100 Prisoners and Light Bulb

**🎤 Interviewer:** "100 prisoners, ek room mein ek bulb. Roz warden random ek prisoner bhejta hai (repeats ho sakte hain), wo bulb ON/OFF kar sakta hai ya kuch nahi. Jab kisi ko confidence ho ki sab 100 log room mein at least ek baar aa chuke hain, declare karega. Galat declare = punishment. Strategy design karo."

**🧠 Tera Jawab:** "Individual prisoner ko pata nahi baaki kitne aa chuke — sirf bulb state dikhti hai, limited info. Mujhe system chahiye jaha information ACCUMULATE ho sake.

Agar sab ko same rule doon (symmetric), info accumulate nahi hogi. Isliye ek SPECIAL role chahiye — ek Counter, baaki sab sirf ek SIGNAL de sakein.

Strategy: Ek prisoner 'Counter'. Baaki 99 ka rule — room mein jao, bulb OFF mile, AUR pehle kabhi ON nahi kiya (sirf pehli baar), toh ON kar do. Counter ka rule — bulb ON mile toh OFF karke count +1.

Har non-counter sirf EK BAAR contribute karta hai (signal permanently register hota hai jab tak counter consume na kare). Jab count 99 ho jaaye, matlab 99 different prisoners signal de chuke — plus counter khud already gaya hai — sab 100 confirmed."

**✅ Final Answer:** Counter strategy — count = 99 → declare

---

### 24. Monty Hall Problem

**🎤 Interviewer:** "3 doors — ek car, do bakri. Ek door pick karte ho. Host (jise pata hai) ek AUR door kholta hai jisme bakri hai. Switch karoge ya nahi, aur kyun?"

**🧠 Tera Jawab:** "Instinct kehta hai 2 doors bache, 50-50 honi chahiye. Lekin host ki ACTION mein info hai — wo random nahi khol raha, jaan-boojh kar bakri wala khol raha hai.

Original probabilities dekhta hoon — pehli baar pick kiya, car milne ka chance 1/3, 'car doosre 2 mein se ek mein hai' ka chance 2/3.

Host ek galat option elimination kar deta hai us 2/3 wale group se. Poora 2/3 probability ab bache hue doosre door pe CONCENTRATE ho jaati hai.

Mera original pick abhi bhi 1/3, doosra bacha door ab 2/3 — wo poora 2/3 group ka 'survivor' hai."

**✅ Final Answer:** **Switch karo** → 2/3 win chance (vs 1/3 stick)

---

### 25. Wolf, Goat, and Cabbage River Crossing

**🎤 Interviewer:** "Farmer ko wolf, goat, cabbage lekar river cross karni hai. Boat mein farmer + max 1 cheez. Farmer na ho toh: wolf goat ko khaata hai, goat cabbage khaati hai. Sabko paar karo."

**🧠 Tera Jawab:** "GOAT sabse risky hai — dono directions mein conflict mein hai. Wolf-cabbage saath safe hain.

1. GOAT le jao doosre kinare (wolf+cabbage safe akele).
2. Farmer akela wapas.
3. WOLF le jao — lekin agar chhod ke aaun, doosre kinare wolf+goat akele reh jayenge, wolf khaayega goat ko. Toh GOAT ko WAPAS le aata hoon.
4. Goat chhod ke, CABBAGE le jao (jaha wolf hai, wolf cabbage nahi khaata).
5. Farmer akela wapas.
6. GOAT le jao doosre kinare."

**✅ Final Answer:** **7 crossings** — goat 3 baar boat mein

---

### 26. 5 Pirates and 100 Gold Coins (Game Theory)

**🎤 Interviewer:** "5 pirates, seniority fixed. Senior-most propose karta hai coins baatne ka. Sab vote karte hain (proposer included), ≥50% approve nahi toh proposer overboard, agla propose karta hai. Rational: 1=zinda rehna, 2=max gold, 3=kisi ko feikwana. Senior-most kya propose karega?"

**🧠 Tera Jawab:** "Game theory — BACKWARD sochta hoon, smallest case se.

Pirates 1(junior) se 5(senior).

**2 pirates:** Pirate 2 ko sirf apna vote chahiye (50%) → (2:100, 1:0).

**3 pirates:** Pirate 3 ko 2 votes chahiye. Pirate 1 ko pata hai fail hone pe 2-pirate case mein 0 milega. Toh Pirate 3 use 1 coin de, accept karega → (3:99, 2:0, 1:1).

**4 pirates:** Pirate 4 ko 2 votes chahiye. Pirate 2 ko 3-pirate case mein 0 milta tha, use 1 coin do → (4:99, 3:0, 2:1, 1:0).

**5 pirates:** Pirate 5 ko 3 votes chahiye. 4-pirate case mein Pirate 3 aur Pirate 1 dono ko 0 milta tha — unhe 1-1 coin do, dono accept karenge."

**✅ Final Answer:** **(5:98, 4:0, 3:1, 2:0, 1:1)** — votes 5,3,1 = 3/5 majority, pass ho jaata hai

---

### 27. Fermi / Estimation Questions

**🎤 Interviewer:** "Estimate karo — Delhi mein kitne petrol pumps honge?"

**🧠 Tera Jawab:** "Exact answer nahi chahiye, APPROACH test ho raha hai. Chote layers mein todunga, har layer pe round-number assumption lunga, clearly bolta jaunga.

Delhi population ≈ 2 crore.
Vehicles — maan lo har 4 logon mein 1 ke paas vehicle → ~50 lakh vehicles.
Refuel frequency — hafte mein ek baar → daily demand ≈ 7 lakh/day.
Ek pump ki capacity — 4-5 nozzles, per nozzle ~200/day (peak hours account karke), 4 nozzles ≈ 500-800/day, conservative 500 leta hoon.
Pumps needed = 7,00,000 / 500 ≈ 1400.

Interviewer ko clearly bolta hoon ye estimate hai — real number 500-1500 range mein kahin ho sakta hai, but approach structured hai."

**✅ Final Answer:** ~1400 (estimate) — **approach hi evaluate hota hai, exact number nahi**

---

## Quick Revision Table (interview se 10 min pehle ye dekh lena)

| # | Puzzle | Final Answer |
|---|---|---|
| 1 | 3 Ants Triangle | 3/4 collision probability |
| 2 | Heaven and Hell | Pucho "doosra guard kya kahega" → opposite lo |
| 3 | 10 Coins | 5 ka pile banao, flip karo → equal heads |
| 4 | Mislabeled Jars | 1 pick, "mixed" label wale jar se |
| 5 | 50 Red 50 Blue Marbles | 1 red akela box mein → 74/99 ≈ 74.7% |
| 6 | Gold Bar 5 days | 4 cuts |
| 7 | 100 Doors | 10 open (perfect squares) |
| 8 | 25 Horses Top 3 | 7 races |
| 9 | Bee Distance | 200/3 ≈ 66.67 km |
| 10 | Cake 8 pieces 3 cuts | 2 vertical + 1 horizontal |
| 11 | Last Ball (20R,16B) | Blue |
| 12 | Two Water Jug | gcd(m,n) divide d karta ho tabhi possible |
| 13 | 3 Bulbs 3 Switches | ON+warm+cold trick |
| 14 | 45 min, 2 wires | Dono end A + ek end B, 30 min pe B ka doosra end jalao |
| 15 | Daughters' Ages | 3, 3, 8 |
| 16 | 10 Balls 5 Lines | Pentagram (5-point star) |
| 17 | Gold Rod 7 units | 1,2,4 pieces (2 cuts) |
| 18 | Bridge Torch (1,2,5,8) | 15 minutes |
| 19 | Poison Rat (1000) | 10 rats |
| 20 | Camel Banana (3000,1000km) | ≈533 bananas |
| 21 | Egg Drop (2 eggs,100 floors) | 14 drops |
| 22 | 9 Balls Weighing | 2 weighings |
| 23 | 100 Prisoners Bulb | Counter strategy |
| 24 | Monty Hall | Switch → 2/3 win chance |
| 25 | Wolf Goat Cabbage | 7 crossings |
| 26 | 5 Pirates 100 coins | 98,0,1,0,1 |
| 27 | Fermi Estimation | Approach matter karta hai, answer nahi |

---

## Interview Se Pehle 3 Last Tips

1. **Chup mat raho.** Jitna zyada bologe utna interviewer ko pata chalega tu soch raha hai, guess nahi kar raha.
2. **Agar atak jao, chota case try karo.** 100 doors atka? 10 doors pe try karo, pattern turant dikhega.
3. **Answer ke baad ek line mein verify karo.** "Toh agar main ye check karu chote example se..." — ye tumhe confident dikhata hai aur galti bhi pakad leta hai.

Bas — ab har puzzle ko bina isse padhe, bol ke practice kar khud se, exactly isi tarah "soch bol ke" — phir ready ho tu.
