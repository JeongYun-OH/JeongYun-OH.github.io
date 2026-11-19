---
title: "SQL 문제풀이 #2 - SELECT"
date: 2026-11-19
categories: [SQL 문제풀이]
tags: [SQL, 프로그래머스]
---


### 5. 잔챙이 잡은 수 구하기 (Lv.1)
---

### 🏷️ **주제** 
  - SELECT

### 🔗 **링크** 
  - https://school.programmers.co.kr/learn/courses/30/lessons/293258


### 💡 **Point**
  - COUNT 활용
---
<br>  

### ✅ 문제 풀이

```sql
SELECT 
    COUNT(*) AS FISH_COUNT
FROM FISH_INFO
WHERE LENGTH IS NULL;
```


### 📝 오답노트
- 'COUNT(*)' 했을 때 NULL도 셈 (행 개수를 셈)
- COUNT(LENGTH) 했으면 NULL 값은 세지 않아서 0을 반환
- NULL 비교는  ='NULL' 이 아닌 IS NULL, IS NOT NULL 로 처리

<br>

---
