---
title: "SQL 문제풀이 #4 - SELECT"
date: 2026-11-24
categories: [SQL 문제풀이]
tags: [SQL, 프로그래머스]
---

### 10. 평균 일일 대여 요금 구하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/151136

### 💡 **Point**
- ROUND, LIKE

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    ROUND(AVG(DAILY_FEE),0) AS AVERAGE_FEE
FROM CAR_RENTAL_COMPANY_CAR
WHERE CAR_TYPE LIKE 'SUV'
```

### 📝 오답노트
- ROUND는 어디까지 보일 것인가 기준으로 생각하면 편함

<br>

---

<br>

### 11. 조건에 맞는 도서 리스트 출력하기 (Lv.1)

---

### 🏷️ **주제**
-  SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/144853

### 💡 **Point**
- YEAR, WHERE

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    BOOK_ID,
    PUBLISHED_DATE
FROM BOOK
WHERE YEAR(PUBLISHED_DATE) = '2021'
    AND CATEGORY LIKE '인문'
ORDER BY PUBLISHED_DATE ASC; 
```

### 📝 오답노트
- 2021년 한정한다면 연도만 추출하는 YEAR로 WHERE 문 활용하는 것이 편함


<br>

---

<br>

### 12. 과일로 만든 아이스크림 고르기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/133025

### 💡 **Point**
- JOIN, WHERE

---

<br>

### ✅ 문제 풀이

```sql
SELECT 
    A.FLAVOR
FROM FIRST_HALF AS A JOIN ICECREAM_INFO AS B ON A.FLAVOR = B.FLAVOR
WHERE A.TOTAL_ORDER > 3000
    AND B.INGREDIENT_TYPE LIKE 'fruit_based'
ORDER BY A.TOTAL_ORDER DESC
```

### 📝 오답노트
- JOIN 시 별칭을 썼다면 WHERE, ORDER BY에서도 별칭을 활용

<br>

---


<br>

### 13. 인기있는 아이스크림 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/133024

### 💡 **Point**
- ORDER BY 두 번 활용

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    FLAVOR
FROM FIRST_HALF
ORDER BY TOTAL_ORDER DESC, SHIPMENT_ID ASC;
```

### 📝 오답노트
- ORDER BY 문 활용 시 두 개 이상의 정렬을 하려면 ',' 을 활용해서 나 



<br>

---

<br>

### 14. 흉부외과 또는 일반외과 의사 목록 출력하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/132203

### 💡 **Point**
- DATE_FORMAT

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    DR_NAME,
    DR_ID,
    MCDP_CD,
    DATE_FORMAT(HIRE_YMD, '%Y-%m-%d') AS HIRE_YMD
FROM DOCTOR
WHERE MCDP_CD ='CS' OR MCDP_CD = 'GS'
ORDER BY HIRE_YMD DESC, DR_NAME ASC
```

### 📝 오답노트
- '이거나' 조 연산자 OR 또는 IN을 활용

<br>

---

<br>

### 14. 12세 이하인 여자 환자 목록 출력하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/132201

### 💡 **Point**
- IF 문 활용

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    PT_NAME,
    PT_NO,
    GEND_CD,
    AGE,
    IF (TLNO IS NULL ,'NONE',TLNO) AS TLNO
FROM PATIENT
WHERE AGE <= 12
    AND GEND_CD = 'W'
ORDER BY AGE DESC,PT_NAME ASC;
```

### 📝 오답노트
- NULL을 찾는 조건문을 활용할 때 IFNULL (MYSQL전용) 또는 COALESCE 활용
- IFNULL(TLNO,'NONE'), COALESCE(TLNO,'NONE')
