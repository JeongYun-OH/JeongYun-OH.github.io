---
title: GA4 데이터 빅쿼리에서 분석하기 (UNNEST 활용) - 연동부터 CSV 추출까지
description: GA4 데이터를 빅쿼리에서 분석하는 방법을 단계별로 설명합니다. BigQuery 연동 설정, UNNEST로 중첩 구조 풀기,
  실전 쿼리 템플릿 4가지, CSV·구글 시트 내보내기까지 한 흐름으로 정리했습니다.
date: 2026-05-08
categories:
- 데이터 분석
- 마케팅
tags:
- 빅쿼리
- GA4
- UNNEST
- SQL
- 데이터분석
- 구글애널리틱스
- 마케터
keywords:
- 빅쿼리 GA4 분석
- GA4 빅쿼리 연동
- 빅쿼리 unnest
- GA4 raw 데이터 추출
- 빅쿼리 nested 데이터
- GA4 빅쿼리 SQL
- 빅쿼리 GA4 쿼리
- GA4 이벤트 파라미터
layout: post
slug: ga4-빅쿼리-분석-unnest-하나면-풀린다-연동부터-csv-추출까지
---


빅쿼리를 처음 열고 GA4 데이터를 불러왔을 때, "이게 뭐지?"라는 반응이 나올 가능성이 큽니다. SELECT *를 쳐도 원하는 컬럼이 나오지 않습니다. 테이블의 '스키마' 탭을 확인해 보면 `event_params`라는 컬럼은 데이터 타입이 문자나 숫자가 아닌 `RECORD`라는 단어만 보입니다. SQL은 분명 알고 있는데, 이 데이터는 다른 언어로 쓰인 것처럼 낯설게 느껴질 수 있습니다.

그 이유는 GA4가 데이터를 '중첩(nested)' 구조로 저장하기 때문입니다. 일반 테이블이 행과 열로 납작하게 펼쳐져 있다면, GA4 데이터는 한 셀 안에 또 다른 표가 들어 있는 형태입니다. 이 데이터를 우리가 원하는 피벗 테이블 형태로 풀어서 보려면 `UNNEST`를 방식을 알아야 합니다.

이 글은 GA4와 빅쿼리를 연결하는 설정부터, 중첩 구조(NESTED)를 이해하고, 원하는 지표를 SQL로 꺼내고, 결과를 CSV나 구글 시트로 내보내는 것까지 한 흐름으로 다룹니다.

---

## 1. GA4 데이터, 빅쿼리에서 열면 왜 이렇게 생겼나요?

### 1.1 빅쿼리 × GA4 연동 3단계

GA4 데이터를 빅쿼리에서 분석하려면 먼저 두 서비스를 연결해야 합니다. 연결은 GA4 콘솔에서 시작합니다.

**① GA4 관리 콘솔에서 BigQuery 연결 설정**

1. GA4 속성(Property)에 접속한 뒤, 왼쪽 하단의 **관리(Admin)** 메뉴로 들어갑니다.
2. 속성(Property) 열에서 **BigQuery 연결(BigQuery Links)** 항목을 클릭합니다.
3. **연결(Link)** 버튼을 누르면 설정 화면이 열립니다.

**② 구글 클라우드 프로젝트 선택**

연결할 구글 클라우드(Google Cloud) 프로젝트를 지정합니다. 프로젝트가 없다면 [Google Cloud 콘솔](https://console.cloud.google.com)에서 먼저 프로젝트를 생성해야 합니다. 프로젝트 생성 시 결제 계정 연결이 필요하지만, 빅쿼리는 월 1TB 쿼리 처리까지 무료입니다. (프로젝트 생성은 다른 글에서도 많이 설명되어 제외했습니다.)

참고) 프로젝트를 생성할 때 프로젝트 ID는 나중에 변경할 수 없습니다. `my-analytics-project-2024`처럼 용도와 연도를 조합해 직관적으로 짓는 것이 좋습니다.

**③ 데이터 스트림 선택 및 빈도 설정 (내보내기 옵션 선택)**

프로젝트를 연결하고 나면 '어떤 데이터를 얼마나 자주 빅쿼리로 보낼지' 설정하게 됩니다.

- **Daily(일별)**: 전날 데이터를 매일 새벽에 적재합니다. 분석용으로는 이 옵션이 일반적입니다.
- **Streaming(스트리밍)**: 실시간으로 데이터가 쌓입니다. 비용이 발생하므로 실시간 모니터링이 필요한 경우에만 선택합니다.

설정을 저장하면 24~48시간 이내에 지정한 클라우드 프로젝트 안에 `analytics_XXXXXXXXX` 형태의 데이터셋이 생성됩니다. `XXXXXXXXX`는 GA4 속성 ID입니다.

---

### 1.2 테이블 구조 훑어보기 — `events_*` 파티션 테이블이란?

빅쿼리에서 해당 데이터셋을 열면 `events_20240601`, `events_20240602`처럼 날짜가 붙은 테이블들이 나열되어 있습니다. 이것이 **파티션 테이블(partitioned table)**입니다.

쉽게 말해, 하루치 이벤트 데이터가 날짜별 파일 하나에 담겨 있는 구조입니다. 엑셀로 치면 날짜별 시트가 따로 만들어진 것과 비슷합니다.

날짜를 지정해 특정 하루 데이터를 보려면 정확한 테이블 이름을 씁니다.

```sql
SELECT *
FROM `프로젝트ID.analytics_XXXXXXXXX.events_20240601`
LIMIT 10
```

여러 날의 데이터를 한 번에 보려면 와일드카드(`*`)와 `_TABLE_SUFFIX`를 활용합니다.

```sql
SELECT *
FROM `프로젝트ID.analytics_XXXXXXXXX.events_*`
WHERE _TABLE_SUFFIX BETWEEN '20240601' AND '20240630'
LIMIT 100
```

참고) `events_intraday_*` 테이블도 함께 생성되는 경우가 있습니다. 이는 당일 실시간 데이터로, 자정이 지나면 `events_*`로 병합되고 삭제됩니다. 분석 목적으로는 `events_*`만 사용하면 됩니다.

---

### 1.3 `event_params`가 일반 컬럼처럼 안 보이는 이유

`events_*` 테이블의 스키마를 보면 대부분의 컬럼은 익숙한 형태입니다. `event_date`는 날짜 문자열, `event_name`은 이벤트 이름, `user_pseudo_id`는 사용자 식별자입니다.

그런데 `event_params`와 `user_properties`는 타입이 `RECORD`로 표시됩니다. 옆의 화살표를 펼치면 또 다른 컬럼들이 나옵니다. 이것이 **ARRAY(배열)와 STRUCT(구조체)**입니다.

일반 테이블의 셀에는 숫자 하나, 문자열 하나처럼 단일 값이 들어갑니다. 그런데 GA4의 `event_params`는 셀 안에 작은 표 하나가 들어 있는 구조입니다.

예를 들어 `page_view` 이벤트 하나가 발생하면, 그 이벤트에는 여러 개의 파라미터가 함께 기록됩니다.

- `page_location`: 현재 페이지 URL
- `page_title`: 페이지 제목
- `ga_session_id`: 세션 ID

이 파라미터들이 `event_params`라는 하나의 컬럼 안에 **배열(ARRAY)** 로 담깁니다. 각 파라미터는 `key`(파라미터 이름)와 `value`(파라미터 값)를 가진 **STRUCT 구조**입니다.

실제 데이터를 머릿속으로 그려보면 이렇습니다.

```
event_params (ARRAY of STRUCT)
├── {key: "page_location", value: {string_value: "https://example.com/home"}}
├── {key: "page_title",    value: {string_value: "홈페이지"}}
└── {key: "ga_session_id", value: {int_value: 1234567890}}
```

`SELECT *`를 해도 이 안의 값이 평면으로 나오지 않는 이유가 여기 있습니다. 배열 안에 든 값을 꺼내려면 배열을 펼쳐주는 작업이 필요합니다. 그 작업이 `UNNEST`입니다.

---

## 2. UNNEST: 중첩 데이터를 테이블로 펼치기

### 2.1 UNNEST로 데이터 펼치기(FROM 절에서 활용)

`UNNEST`는 배열 안의 항목들을 각각 별도의 행으로 만들어줍니다. 행이 '늘어난다'고 표현하지만, 더 정확히는 배열의 각 원소가 독립적인 행으로 '펼쳐진다'고 이해하는 것이 좋습니다.

예를 들어 `event_params` 배열에 파라미터가 5개 들어 있는 이벤트 하나가 있다면, `UNNEST`를 적용하면 그 이벤트의 행이 5개로 펼쳐집니다.

```
UNNEST 이전:
| event_name | event_params (ARRAY)           |
|------------|-------------------------------|
| page_view  | [{key:page_location, value:...},
               {key:page_title, value:...},
               {key:ga_session_id, value:...}]  ← 배열 1개

UNNEST 이후:
| event_name | ep.key          | ep.value          |
|------------|-----------------|-------------------|
| page_view  | page_location   | https://...       |
| page_view  | page_title      | 홈페이지           |
| page_view  | ga_session_id   | 1234567890        |
```

배열 하나가 3개의 행으로 펼쳐졌습니다. `event_name` 값은 세 행 모두에 동일하게 붙어 나옵니다.

이 원리를 이해하면 `UNNEST`가 FROM 절에 들어가는 이유도 자연스럽게 이해됩니다. `UNNEST`는 새로운 '행 집합'을 만들어내는 작업이기 때문에, 테이블처럼 FROM 절에 위치합니다.

---

### 2.2 기본 패턴 한 줄 외우기

`event_params`를 다루는 가장 기본적인 패턴은 이렇습니다.

```sql
SELECT
  event_name,
  a.key,
  a.value.string_value
FROM `프로젝트ID.analytics_XXXXXXXXX.events_20240601`,
UNNEST(event_params) AS a
LIMIT 10
```

`UNNEST(event_params) AS a`는 배열을 펼치면서 각 원소에 `a`라는 별칭을 붙이는 코드입니다. 이후 `a.key`, `a.value.string_value`처럼 점(`.`)으로 내부 필드에 접근합니다.

`value` 안에는 타입에 따라 여러 하위 필드가 있습니다.

| 하위 필드 | 담기는 값의 타입 |
|---|---|
| `value.string_value` | 문자열 (URL, 이름 등) |
| `value.int_value` | 정수 (세션 ID, 수량 등) |
| `value.float_value` | 부동소수점 (금액, 비율 등) |
| `value.double_value` | 더 높은 정밀도의 부동소수점 |

어떤 파라미터가 어떤 타입을 쓰는지는 GA4 문서에서 확인하거나, 위 쿼리로 직접 훑어보면 금방 파악할 수 있습니다.

---

### 2.3 LEFT JOIN UNNEST vs CROSS JOIN UNNEST — 언제 어떤 걸 쓰나

`UNNEST`를 FROM 절에 쉼표로 연결하는 것은 사실 `CROSS JOIN UNNEST`와 동일합니다. 그리고 이 방식에는 한 가지 특성이 있습니다. **배열이 비어 있는 행은 결과에서 사라집니다.**

대부분의 GA4 이벤트는 `event_params`에 값이 있기 때문에 이 방식으로 충분합니다. 그런데 배열이 비어 있어도 원래 행을 유지하고 싶다면 `LEFT JOIN UNNEST`를 씁니다.

```sql
-- CROSS JOIN 방식 (쉼표 = 기본값, 배열이 빈 행은 제거됨)
SELECT event_name, a.key, a.value.string_value
FROM `프로젝트ID.analytics_XXXXXXXXX.events_20240601`,
UNNEST(event_params) AS a

-- LEFT JOIN 방식 (배열이 비어 있어도 원래 행 유지)
SELECT event_name, a.key, a.value.string_value
FROM `프로젝트ID.analytics_XXXXXXXXX.events_20240601`
LEFT JOIN UNNEST(event_params) AS a
```

마케터가 실제로 분석하는 상황에서는 대부분 CROSS JOIN(쉼표) 방식이면 충분합니다. 특정 파라미터가 없는 이벤트까지 전부 포함해서 세고 싶을 때 LEFT JOIN을 선택하면 됩니다.

그런데 실제 분석에서는 배열 전체를 펼치는 것보다, **특정 key 하나의 값만 꺼내는 패턴**이 훨씬 자주 쓰입니다. 이 패턴이 다음 섹션의 핵심입니다.

---

## 3. 복붙 가능한 쿼리 템플릿 4가지

마케터가 실제로 꺼내고 싶은 지표는 명확합니다. 페이지별 조회 수, 캠페인별 유입, 특정 이벤트 발생 수, 유저별 행동 등입니다. 이 네 가지를 바로 쓸 수 있는 템플릿으로 정리합니다.

모든 쿼리에서 반복되는 핵심 패턴은 아래 참고하면 됩니다.

```sql
(SELECT value.string_value FROM UNNEST(event_params) WHERE key = 'key_이름')
```

이것은 서브쿼리 방식으로 특정 파라미터 값 하나를 꺼내는 패턴입니다. UNNEST로 배열을 펼친 뒤, `WHERE key = '...'`로 원하는 파라미터만 필터해서 값을 가져옵니다. 이 패턴을 SELECT 절 안에서 컬럼처럼 쓰면, 배열 전체를 펼치지 않고도 원하는 값 하나만 추출할 수 있습니다.

---

### 3.1 page_location(페이지별 조회 수) 추출

마케터가 가장 자주 확인하는 지표 중 하나는 "어떤 페이지가 몇 번 조회됐는가"입니다. GA4에서는 `page_view` 이벤트가 발생할 때 `page_location` 파라미터로 URL이 기록됩니다.

```sql
SELECT
  (SELECT value.string_value
   FROM UNNEST(event_params)
   WHERE key = 'page_location') AS page_location,
  COUNT(*) AS page_views
FROM `프로젝트ID.analytics_XXXXXXXXX.events_*`
WHERE
  _TABLE_SUFFIX BETWEEN '20240601' AND '20240630'
  AND event_name = 'page_view'
GROUP BY page_location
ORDER BY page_views DESC
LIMIT 20
```

`WHERE event_name = 'page_view'`로 페이지뷰 이벤트만 필터하고, `page_location`을 GROUP BY 기준으로 삼아 URL별 집계를 냅니다.

참고) URL 쿼리스트링(`?utm_source=...` 이하 부분)이 포함된 채로 집계되기 때문에, 같은 페이지가 여러 URL로 나뉘어 보일 수 있습니다. 이 경우 `REGEXP_REPLACE` 함수로 쿼리스트링을 제거하거나, URL 경로만 추출할 수 있습니다.

---

### 3.2 캠페인·소스·미디엄(UTM 파라미터) 추출

광고나 이메일로 유입된 사용자를 분석하려면 UTM 파라미터 데이터가 필요합니다. GA4는 세션 시작 시점의 UTM 값을 `traffic_source.source`, `traffic_source.medium`, `traffic_source.name`에 저장하기도 하고, 이벤트 파라미터로도 기록합니다.

빅쿼리에서 이벤트 파라미터 기준으로 캠페인 소스·미디엄·캠페인 이름을 추출하는 쿼리입니다.

```sql
SELECT
  (SELECT value.string_value
   FROM UNNEST(event_params)
   WHERE key = 'source') AS source,
  (SELECT value.string_value
   FROM UNNEST(event_params)
   WHERE key = 'medium') AS medium,
  (SELECT value.string_value
   FROM UNNEST(event_params)
   WHERE key = 'campaign') AS campaign,
  COUNT(DISTINCT user_pseudo_id) AS users,
  COUNT(*) AS sessions
FROM `프로젝트ID.analytics_XXXXXXXXX.events_*`
WHERE
  _TABLE_SUFFIX BETWEEN '20240601' AND '20240630'
  AND event_name = 'session_start'
GROUP BY source, medium, campaign
ORDER BY sessions DESC
LIMIT 30
```

`session_start` 이벤트를 기준으로 삼으면 세션 단위로 유입 소스를 집계할 수 있습니다. `user_pseudo_id`로 중복을 제거(DISTINCT)하면 유저 수도 함께 볼 수 있습니다.

참고) GA4에서 UTM 파라미터는 세션 첫 번째 이벤트에만 붙기 때문에, 모든 이벤트에서 source·medium·campaign이 채워지는 것은 아닙니다. `session_start` 또는 `first_visit` 이벤트를 기준으로 집계하는 것이 정확합니다.

---

### 3.3 특정 이벤트 필터 + 사용자 수 집계

전환(purchase, sign_up 등) 이벤트나 특정 버튼 클릭 이벤트가 몇 명의 사용자에게 발생했는지 집계하는 패턴입니다.

예시로 `purchase` 이벤트의 일별 발생 수와 유니크 사용자 수를 뽑겠습니다.

```sql
SELECT
  event_date,
  COUNT(*) AS purchase_count,
  COUNT(DISTINCT user_pseudo_id) AS unique_purchasers,
  SUM((SELECT value.int_value
       FROM UNNEST(event_params)
       WHERE key = 'value')) AS total_revenue
FROM `프로젝트ID.analytics_XXXXXXXXX.events_*`
WHERE
  _TABLE_SUFFIX BETWEEN '20240601' AND '20240630'
  AND event_name = 'purchase'
GROUP BY event_date
ORDER BY event_date
```

`total_revenue`는 `value`라는 파라미터를 합산한 값입니다. GA4의 전자상거래 이벤트에서 `value`는 구매 금액을 기록하는 파라미터입니다. 다만 금액은 `float_value` 또는 `int_value`로 저장될 수 있으므로, 실제 데이터를 먼저 확인한 뒤 적절한 하위 필드를 선택하는 것이 좋습니다.

자신의 GA4에 커스텀 이벤트를 설정해뒀다면 `event_name`에 해당 이벤트 이름을 넣으면 됩니다.

---

### 3.4 user_pseudo_id 기반 유저 단위 분석

GA4에서 개별 사용자를 추적하는 식별자는 `user_pseudo_id`입니다. 이 값을 기준으로 유저별 행동을 집계할 수 있습니다.

예시로 특정 기간 동안 유저별 총 이벤트 수, 세션 수, 마지막 방문일을 뽑는 쿼리입니다.

```sql
SELECT
  user_pseudo_id,
  COUNT(*) AS total_events,
  COUNT(DISTINCT
    (SELECT value.int_value
     FROM UNNEST(event_params)
     WHERE key = 'ga_session_id')) AS session_count,
  MAX(event_date) AS last_visit_date
FROM `프로젝트ID.analytics_XXXXXXXXX.events_*`
WHERE _TABLE_SUFFIX BETWEEN '20240601' AND '20240630'
GROUP BY user_pseudo_id
ORDER BY total_events DESC
LIMIT 50
```

`ga_session_id`는 세션마다 부여되는 정수 값입니다. `user_pseudo_id`와 `ga_session_id`의 조합으로 세션을 고유하게 식별할 수 있습니다.

이 쿼리 결과를 CRM 데이터나 별도 유저 테이블과 JOIN하면 실제 사용자 행동 분석으로 확장할 수 있습니다.

---

**재사용 핵심 패턴 요약**

위 네 가지 템플릿에서 반복되는 핵심 패턴을 다시 정리하면 이렇습니다.

```sql
-- 문자열 파라미터 꺼내기
(SELECT value.string_value FROM UNNEST(event_params) WHERE key = 'key_이름')

-- 정수 파라미터 꺼내기
(SELECT value.int_value FROM UNNEST(event_params) WHERE key = 'key_이름')

-- 부동소수점 파라미터 꺼내기
(SELECT value.float_value FROM UNNEST(event_params) WHERE key = 'key_이름')
```

이 세 줄을 복사해서 `key_이름`과 `value.타입`만 바꾸면, GA4의 모든 이벤트 파라미터를 꺼낼 수 있습니다.

---

## 4. 결과를 CSV·스프레드시트로 내보내기

쿼리 결과가 나왔습니다. 이제 조회한 데이터를 엑셀이나 구글 시트로 내보내는 방법을 알아보겠습니다.

### 4.1 빅쿼리 콘솔에서 바로 다운로드하는 법

가장 간단한 방법은 쿼리 결과를 콘솔에서 직접 내려받는 것입니다.

**단계별 안내**

1. 쿼리를 실행하면 결과 테이블이 하단에 나타납니다.
2. 결과 테이블 상단에 있는 **저장(Save results)** 버튼을 클릭합니다.
3. 드롭다운 메뉴에서 **CSV(쉼표로 구분됨)** 또는 **JSON** 형식을 선택합니다.
4. **로컬 파일로 다운로드(Download local file)**를 선택하면 파일이 바로 다운로드됩니다.

참고) 빅쿼리 콘솔에서 직접 다운로드할 수 있는 결과는 최대 **16,000행**까지입니다. 그 이상이라면 아래에서 설명하는 구글 드라이브나 클라우드 스토리지로 내보내는 방법을 사용해야 합니다.

**대용량 결과물 내보내기 — 구글 클라우드 스토리지(GCS) 경유**

행이 많아 직접 다운로드가 안 될 때는 먼저 결과를 구글 클라우드 스토리지 버킷에 저장한 뒤, 버킷에서 파일을 내려받습니다.

```sql
-- 결과를 GCS 버킷에 CSV로 저장하는 방법 (빅쿼리 콘솔 > 저장 > Cloud Storage에 저장)
-- 또는 아래처럼 쿼리 결과를 임시 테이블에 저장한 뒤 내보내기 가능
```

빅쿼리 콘솔에서 **저장(Save results) → Google Cloud Storage에 저장**을 선택하면 버킷 경로를 지정하는 화면이 나옵니다. 저장 후 GCS 콘솔에서 파일을 직접 다운로드합니다.

---

### 4.2 구글 시트로 내보내기

구글 시트로 바로 연동하는 방법도 있습니다. 팀과 실시간으로 공유하거나, 시트에서 차트를 만들어야 할 때 유용합니다.

**방법 1: 빅쿼리 콘솔에서 직접 내보내기**

1. 쿼리 실행 후 결과 테이블 상단의 **저장(Save results)** 클릭
2. **Google 스프레드시트** 선택
3. 새 스프레드시트가 열리며 결과가 자동으로 붙여넣어집니다.

이 방법 역시 최대 16,000행 제한이 있습니다.

**방법 2: 구글 시트에서 빅쿼리 연결 (Connected Sheets)**

구글 시트에는 **Connected Sheets(연결된 시트)** 기능이 있습니다. 빅쿼리 테이블 또는 쿼리 결과를 구글 시트에 직접 연결해 최대 수백만 행을 시트에서 다룰 수 있습니다.

1. 구글 시트에서 **데이터(Data) → 데이터 커넥터(Data connectors) → BigQuery에 연결**을 선택합니다.
2. 프로젝트와 테이블을 선택하거나, 직접 SQL 쿼리를 입력합니다.
3. **새로 고침(Refresh)** 버튼을 누를 때마다 최신 데이터를 불러옵니다.

참고) Connected Sheets는 Google Workspace Business Standard 이상 요금제에서 사용할 수 있습니다. 개인 계정에서는 제한이 있을 수 있으니 확인이 필요합니다.

---

**어떤 방법을 선택할까 — 상황별 요약**

| 상황 | 권장 방법 |
|---|---|
| 결과가 16,000행 이하, 바로 파일로 저장 | 콘솔 Save results → CSV 다운로드 |
| 구글 시트에서 팀과 공유 | 콘솔 Save results → Google 스프레드시트 |
| 16,000행 초과, 대용량 파일 | GCS(Google Cloud Storage) 내보내기 |
| 주기적인 데이터 업데이트와 협업 필요 시 | 구글 시트 Connected Sheets 연동 |

### 5. 마무리
빅쿼리에서 GA4 데이터를 처음 마주하면 막막함과 당황스러움이 먼저 들 수 있습니다. 하지만 UNNEST 문법을 알고 몇 가지 패턴만 학습한다면 GA4 데이터를 빅쿼리에서 쉽게 다룰 수 있게 됩니다.
이 글에서 소개한 기본 쿼리 템플릿을 활용하여 저와 같은 비전공자들, 마케터도 빅쿼리에서 더 인사이트 있는 GA4 분석을 시작할 수 있으면 좋겠습니다.