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

