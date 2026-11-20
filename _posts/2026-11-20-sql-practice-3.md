---
title: "SQL 문제풀이 #3 - SELECT,JOIN,LIKE,CASE WHEN"
date: 2026-11-20
categories: [SQL 문제풀이]
tags: [SQL, 프로그래머스]
---

### 6. Python 개발자 찾기(Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/276013

### 💡 **Point**
- WHERE 과 OR 을 통한 여러 조건 걸기

---

<br>

### ✅ 문제 풀이

```sql
SELECT 
    ID,
    EMAIL,
    FIRST_NAME,
    LAST_NAME
FROM DEVELOPER_INFOS
WHERE SKILL_1 = 'Python' OR SKILL_2 = 'Python' OR SKILL_3 ='Python'
ORDER BY ID
```

### 📝 오답노트
- 더 간단하게 쓰는 방법
- WHERE 'Python' IN (SKILL_1, SKILL_2, SKILL_3)   -- 값 1개를 여러 컬럼과 비교
보통 컬럼 IN (값1, 값2) 이렇게 사용. 반대로 값 IN (컬럼1, 컬럼2) 이런 형태로도 활용 가능

<br>

---

<br>

### 7. 조건에 부합하는 중고거래 댓글 조회하기 (Lv.1)

---

### 🏷️ **주제**
-  SELECT,  JOIN

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/164673

### 💡 **Point**
- JOIN 활용과 각 테이블에서 필요로 하는 컬럼을 꺼내는 것.
- 테이블에서 필요로 하는 컬럼을 조건으로 활용하는 것

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    A.TITLE,
    A.BOARD_ID,
    B.REPLY_ID,
    B.WRITER_ID,
    B.CONTENTS,
    DATE_FORMAT(B.CREATED_DATE, '%Y-%m-%d') AS CREATED_DATE
FROM USED_GOODS_BOARD AS A 
JOIN USED_GOODS_REPLY AS B 
    ON A.BOARD_ID = B.BOARD_ID
WHERE A.CREATED_DATE BETWEEN '2022-10-01' AND '2022-10-31'
ORDER BY B.CREATED_DATE ASC,                  
         A.TITLE ASC;  
```

### 📝 오답노트
- 날짜에 '' 활용이 없어서 날짜가 아닌 숫자의 뺄셈으로 계산됨
- WEHER B.CREATED_DATE  댓글 작성일로 거름 -> 문제는 게시글 작성일 기준
- ORDER BY 정렬 조건 2개 추가
- 추가로 날짜 포맷을 형식에 맞게 출력하기 위해선 DATE_FORMAT(B.CREATED_DATE, '%Y-%m-%d') AS CREATED_DATE
로 추가해주는 것이 좋음 DATE_FORMAT(컬럼, '%Y-%m-%d') 기억하기


<br>

---

<br>

### 8. 특정 옵션이 포함된 자동차 리스트 구하기 (Lv.1)

---

### 🏷️ **주제**
- SELECT

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/157343

### 💡 **Point**
- 문자열 조건 LIKE '%%' 활용하기

---

<br>

### ✅ 문제 풀이

```sql
SELECT 
    CAR_ID,
    CAR_TYPE,
    DAILY_FEE,
    OPTIONS
FROM CAR_RENTAL_COMPANY_CAR
WHERE OPTIONS LIKE '%네비게이션%'
ORDER BY CAR_ID DESC;
```

### 📝 오답노트
- 문자열에 LIKE '%' 와일드카드 활용
- '_'는 정확히 1개 값만 활용

<br>

---


<br>

### 9. 자동차 대여 기록에서 장기/단기 대여 구분하기 (Lv.1)

---

### 🏷️ **주제**
- STRING, DATE

### 🔗 **링크**
- https://school.programmers.co.kr/learn/courses/30/lessons/151138

### 💡 **Point**
- 날짜 조건에 맞는 값 불러오기
- CASE 문 활용

---

<br>

### ✅ 문제 풀이

```sql
SELECT
    WHEN 
        END_DATE - START_DATE >= 30 THEN '장기 대여'
        ELSE '단기 대여'
        END
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
WHERE START_DATE >= '2022-09-01'
    AND END_DATE <'2022-10-01'
```

### 📝 오답노트
- 1) CASE 문 활용 시 CASE WHEN ELSE 문법 실수, END 뒤에 컬럼명 추가
- 2) 날짜 뺄셈 문법 DATEDIFF 활용 => DATEDIFF(END_DATE, START_DATE_ + 1
- 3) WHERE에서 대여 시작일로 설정해야 할 것을 종료일로 설정함 
- 4) SELECT 절 문제 누락
- 5) ORDER BY 기준 잘 살펴보기

### ✅ 수정 후

```sql
SELECT
	HISTORT_ID,
	CAR_ID,
	DATE_FORMAT(START_DATE, '%Y-%m-%d' ) AS START_DATE,
	DATE_FORMAT(END_DATE, '%Y-%m-%d') AS END_DATE,
CASE 
    WHEN DATEDIFF(END_DATE, START_DATE) + 1 >= 30 THEN '장기 대여'
    ELSE '단기 대여'
    END AS RENT_TYPE
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
WHERE START_DATE >= '2022-09-01'
    AND START_DATETE <'2022-10-01'
ORDER BY HISTORY_ID DESC;
```

<br>

---

<br>
