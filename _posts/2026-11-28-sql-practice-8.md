---
title: "SQL 문제풀이 #8 - JOIN, GROUP BY"
date: 2026-11-28
categories: [SQL 문제풀이]
tags: [SQL, 프로그래머스]
---

### 32. 분기별 분화된 대장균의 개체 수 구하기 (Lv.2)

---

### 🏷️ **주제**
- GROUP BY

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/299308

### 💡 **Point**
- GROUP BY

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    CONCAT(QUARTER(DIFFERENTIATION_DATE), 'Q') AS QUARTER,
    COUNT(*) AS ECOLI_COUNT
FROM ECOLI_DATA
GROUP BY QUARTER
ORDER BY QUARTER ASC;

```

### 📝 오답노트
- GROUP BY 행을 묶어서 계산. 행 수가 줄어듬. 
- EX) 카테고리별 합산
- WITH 쿼리를 단계로 나누고 이름표를붙임. 행수 변화없이 "정리"하는 도구 

<br>

---

<br>

### 33. 특정 물고기를 잡은 총 수 구하기 (Lv.2)

---

### 🏷️ **주제**
-  JOIN

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/298518

### 💡 **Point**
- JOIN 함수. 

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    COUNT(A.ID) AS FISH_COUNT
FROM FISH_INFO AS A
    JOIN FISH_NAME_INFO AS B ON A.FISH_TYPE = B.FISH_TYPE
WHERE B.FISH_NAME IN ('BASS','SNAPPER')
```

### 📝 오답노트
- WHERE 조건의 컬럼 값 실수 안하게 확인하기
