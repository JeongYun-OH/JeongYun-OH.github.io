---
title: "SQL 문제풀이 #1 - SELECT"
date: 2026-11-17
categories: [SQL 문제풀이]
tags: [SQL, 프로그래머스]
---


### 1. 가장 큰 물고기 10마리 구하기 (Lv.1)

---

### 🏷️ **주제** 
  - SELECT

### 🔗 **링크** 
  - https://school.programmers.co.kr/learn/courses/30/lessons/298517


### 💡 **Point**
  - ORDER BY와 LIMIT 활용
    
---
<br>  

### ✅ 문제 풀이

```sql
SELECT 
    ID,
    LENGTH
FROM FISH_INFO
ORDER BY LENGTH DESC , ID
LIMIT 10
```


### 📝 오답노트
- ORDER BY 내림차순은 DESC, 오름차순은 ASC 로 작성 또는 생략, ORDER BY 다음 LIMIT 작성

<br>

---

<br>

### 2. 한 해에 잡은 물고기 수 구하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/298516

### 💡 **Point**
- DATE Type에서 필요한 조건만 뽑아서 계산하기. YEAR 

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    COUNT(ID) AS FISH_COUNT
FROM FISH_INFO
WHERE YEAR(TIME) = 2021
```

### 📝 오답노트
- FROM 작성을 까먹지 말자. 
- 시간 타입에서 BETWEEN 외에 YEAR, DAY, MONTH 이런 걸 잘 활용하자
- NULL 이 포함된 컬럼에서는 COUNT 는 NULL을 세지 않는다.  
- BETWEEN 활용하고 싶으면 WHERE TIME BETWEEN '2021-01-01' AND '2021-12-31' 처럼 작성
- COUNT(*)가 행 갯수를 셀 때 관행적
<br>

---

<br>

### 3. 잡은 물고기 중 가장 큰 물고기의 길이 구하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/298515

### 💡 **Point**
- CONCAT, MAX 함수 제대로 활용. 문자는 '' 활용

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    CONCAT(MAX(LENGTH), 'cm') AS MAX_LENGTH
FROM FISH_INFO
```

### 📝 오답노트
- MAX는 ()를 추가해서 작성. 문자 타입은 '' 사이에 추가

<br>

---

<br>

### 4. 잡은 물고기의 평균 길이 구하기(Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/293259

### 💡 **Point**
- AVG 함수, ROUND 함수, IF 활용

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    ROUND(AVG(IF(LENGTH IS NULL, 10, LENGTH)), 2) AS AVERAGE_LENGTH
FROM FISH_INFO;
```

### 📝 오답노트
- IF는 WHERE 처럼 절이 아닌 함수이다. SELECT 부분에 활용 가능. (단, MYSQL만 가능)
- AVG 계산은 NULL을 제외하고 계산하게 됨. 그래서 평균 값이 NULL을 제외하면 차이가 발생
- ROUND(X,2)는 둘째자리까지 남김(셋째자리에서 반올림 하는 것)
- IF 대체 문법 IFNULL(LENGTH, 10), COALESCE(LENGTH, 10)
<br>
