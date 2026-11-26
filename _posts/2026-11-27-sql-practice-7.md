---
title: "SQL 문제풀이 #7 - JOIN,WITH"
date: 2026-11-27
categories: [SQL 문제풀이]
tags: [SQL, 프로그래머스]
---

### 30. 부모의 형질을 모두 가지는 대장균 찾기 (Lv.2)

---

### 🏷️ **주제**
- JOIN

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/301647

### 💡 **Point**
- JOIN, SELF JOIN, 문제 이해(비트 연산자)

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    C.ID,
    C.GENOTYPE,
    P.GENOTYPE AS PARENT_GENOTYPE
FROM ECOLI_DATA AS C
JOIN ECOLI_DATA AS P
    ON C.PARENT_ID = P.ID
WHERE (C.GENOTYPE & P.GENOTYPE) = P.GENOTYPE
ORDER BY C.ID ASC;

```

### 📝 오답노트
- 문제 이해.(PARENT.ID와 ID를 묶는 것)
- 비트 연산자 & (2진수에 자주 사용)
- 비트 플래그 방식에 활용. (여러 개의 예/아니오 정보를 숫자 하나에 담아둔 데이터)
-  두 숫자가 "공통으로 가진 것"을표 

<br>

---

<br>

### 31. 연도별 대장균 크기의 편차 구하기 (Lv.2)

---

### 🏷️ **주제**
-  WINDOW

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/299310

### 💡 **Point**
- WINDOW 함수. 다시 풀어보기

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    YEAR(DIFFERENTIATION_DATE) AS YEAR,
    MAX(SIZE_OF_COLONY) AS YEAR_DEV,
    ID
FROM ECOLI_DATA
GROUP BY YEAR(DIFFERENTIATION_DATE)
ORDER BY DIFFERENTIATION_DATE ASC, SIZE_OF_COLONY ASC;
```

### 📝 오답노트
- GROUP BY는 행이 줄어둠. (연도별 1행씩만 남음)
- GROUP BY 상태에서 ID를 설정하면 어떤 ID를 보여줘야 할지 구별이 안됨
- MAX에서 최대값만 추출하는 것이 아니라 값 - MAX 를 해야하는 상황
- 정렬 기준이 YEAR, YEAR_DEV여야 함.
=> 연도별 값을 따로 만든 뒤, 원래 테이블과 합쳐서 계산 해야함. 이때 WITH 과 JOIN 활용.

```sql
WITH YEAR_MAX AS (                                   -- 1단계: 연도별 최대 크기
    SELECT
        YEAR(DIFFERENTIATION_DATE) AS YEAR,
        MAX(SIZE_OF_COLONY)        AS MAX_SIZE
    FROM ECOLI_DATA
    GROUP BY YEAR(DIFFERENTIATION_DATE)
)
SELECT                                                -- 2단계: 각 대장균에 붙여서 편차 계산
    M.YEAR,
    M.MAX_SIZE - E.SIZE_OF_COLONY AS YEAR_DEV,
    E.ID
FROM ECOLI_DATA AS E
JOIN YEAR_MAX AS M
    ON YEAR(E.DIFFERENTIATION_DATE) = M.YEAR
ORDER BY M.YEAR ASC,
         YEAR_DEV ASC;
```
