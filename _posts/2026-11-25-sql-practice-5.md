---
title: "SQL 문제풀이 #5 - SELECT"
date: 2026-11-25
categories: [SQL 문제풀이]
tags: [SQL, 프로그래머스]
---

### 15. 가장 비싼 상품 구하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/131697

### 💡 **Point**
- SELECT, MAX

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    MAX(PRICE) AS MAX_PRICE
FROM PRODUCT

```

### 📝 오답노트
- 가장 높은, 가장 최근 등은 MAX 활용

<br>

---

<br>

### 16. 조건에 맞는 회원수 구하기 (Lv.1)

---

### 🏷️ **주제**
-  SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/131535

### 💡 **Point**
- YEAR, WHERE

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    COUNT(USER_ID) AS USERS
FROM USER_INFO
WHERE YEAR(JOINED) = '2021'
    AND AGE BETWEEN '20' AND '29'
```

### 📝 오답노트
- 부등호 =, >=, <= 인지 잘 생각해서 작성하기


<br>

---

<br>

### 17. 나이 정보가 없는 회원 수 구하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/131528

### 💡 **Point**
- IS NULL

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    COUNT(USER_ID) AS USERS
FROM USER_INFO
WHERE AGE IS NULL

```

### 📝 오답노트
- NULL 값 연산은 '='가 아닌 IS NULL, IS NOT NULL

<br>

---


<br>

### 18. 경기도에 위치한 식품창고 목록 출력하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/131114

### 💡 **Point**
- IFNULL, LIKE

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    WAREHOUSE_ID,
    WAREHOUSE_NAME,
    ADDRESS,
    IFNULL(FREEZER_YN,'N') AS FREEZER_YN
FROM FOOD_WAREHOUSE
WHERE ADDRESS LIKE '%경기%'
ORDER BY WAREHOUSE_ID ASC;
```

### 📝 오답노트
- IFNULL로 NULL일 때 조건 활용
- LIKE 활용



<br>

---

<br>

### 19. 강원도에 위치한 생산공장 목록 출력하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/131112

### 💡 **Point**
- LIKE

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    FACTORY_ID,
    FACTORY_NAME,
    ADDRESS
FROM FOOD_FACTORY
WHERE ADDRESS LIKE '%강원도%'
ORDER BY FACTORY_ID ASC;
```

### 📝 오답노트
- LIKE 활용은 와일드카드(%)와 함께 이용

<br>

---

<br>

### 20. 최댓값 구하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/59415

### 💡 **Point**
- MAX

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    MAX(DATETIME) AS 시간
FROM ANIMAL_INS
```

### 📝 오답노트
- 시간 DATETIME은 HH:MM:SS 스타일까지 함께 가지고 있다. 이걸 날짜로만 변환하려면 DATE_FORMAT( 컬럼, '%Y-%m-%d') 형식으로 활용
