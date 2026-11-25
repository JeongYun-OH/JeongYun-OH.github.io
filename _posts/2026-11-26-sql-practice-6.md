title: "SQL 문제풀이 #6 - SELECT"
date: 2026-11-26
categories: [SQL 문제풀이]
tags: [SQL, 프로그래머스]
---

### 21. 이름이 있는 동물의 아이디 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/59407

### 💡 **Point**
- IS NULL

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    ANIMAL_ID
FROM ANIMAL_INS
WHERE NAME IS NOT NULL
ORDER BY ANIMAL_ID ASC;

```

### 📝 오답노트
- NULL 제외 = IN NOT NULL

<br>

---

<br>

### 22. 상위 n개 레코드 (Lv.1)

---

### 🏷️ **주제**
-  SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/59405

### 💡 **Point**
- LIMIT

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    NAME
FROM ANIMAL_INS
ORDER BY DATETIME ASC
LIMIT 1
```

### 📝 오답노트
- 가장 먼저, 최근, TOP, 최하위 등을 선별할 때는 ORDER BY 와 LIMIT을 활용.


<br>

---

<br>

### 23. 여러 기준으로 정렬하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/59404

### 💡 **Point**
- ORDER BY

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    ANIMAL_ID,
    NAME,
    DATETIME
FROM ANIMAL_INS
ORDER BY NAME ASC, DATETIME DESC

```

### 📝 오답노트
- 이름 순 - ASC, 최신 순 또는 나중에 시작 - DESC

<br>

---


<br>

### 24. 동물의 아이디와 이름 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/59403

### 💡 **Point**
- SELECT

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    ANIMAL_ID,
    NAME
FROM ANIMAL_INS
ORDER BY ANIMAL_ID
```

### 📝 오답노트
- 정렬은 ORDER BY 활용

<br>

---

<br>

### 25. 이름이 없는 동물의 아이디 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/59039

### 💡 **Point**
- IS NULL

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    ANIMAL_ID
FROM ANIMAL_INS
WHERE NAME IS NULL
ORDER BY ANIMAL_ID ASC
```

### 📝 오답노트
- NULL 인 값 IS NULL

<br>

---

<br>

### 26. 어린 동물 찾기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/59037

### 💡 **Point**
- <>, !=

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    ANIMAL_ID,
    NAME
FROM ANIMAL_INS 
WHERE INTAKE_CONDITION <> 'Aged'
ORDER BY ANIMAL_ID ASC;
```

### 📝 오답노트
- 아니다 연산자 <>, !=. 
<> 를 활용하면 NULL 값은 제외되는 점 참고
- 여러 값이 모두 아니다 NOT IN. 
- 특정 글자가 포함되어 있지 않 NOT LIKE
- 값이 비어있지 않다 IS NOT NULL


<br>

---

<br>

### 27. 아픈 동물 찾기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/59036

### 💡 **Point**
- WHERE

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    ANIMAL_ID,
    NAME
FROM ANIMAL_INS
WHERE INTAkE_CONDITION = 'Sick'
ORDER BY ANIMAL_ID ASC;
```

### 📝 오답노트
- WHERE 같다는 '='

<br>

---

<br>

### 28. 역순 정렬하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/59035

### 💡 **Point**
- ORDER BY DESC

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    NAME,
    DATETIME
FROM ANIMAL_INS
ORDER BY ANIMAL_ID DESC;
```

### 📝 오답노트
- 역순은 DESC

<br>

---

<br>

### 29. 모든 레코드 조회하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/59034

### 💡 **Point**
- * 애스터리스크(asterisk)

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    *
FROM ANIMAL_INS
ORDER BY ANIMAL_ID 
```

### 📝 오답노트
- 모든 값 추출 * 애스터리스크(asterisk)
