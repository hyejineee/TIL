---
title: Elasticsearch 입문 — Docker로 띄우고 curl로 넣기
date: 2019-12-16
category: devops
tags: [elasticsearch, docker, search]
summary: JSON 기반 검색·분석 엔진. Docker로 띄운 뒤 _doc로 데이터 추가, _search로 QueryDSL 검색. 응답 hits 섹션 상위 10개를 반환.
---

## Elasticsearch란?

JSON 기반의 검색 & 분석 엔진. 모든 데이터 타입에 대해 실시간 검색과 분석 기능을 제공한다.

## Docker로 띄우기

```bash
docker run -d --name elasticsearch \
  -p 9200:9200 -p 9300:9300 \
  -e "discovery.type=single-node" \
  elasticsearch:<버전>
```

## 데이터 추가 — `_doc` endpoint

인덱스가 없으면 자동 생성, 있으면 갱신.

```bash
curl -X PUT "localhost:9200/customer/_doc/1?pretty" \
  -H 'Content-Type: application/json' \
  -d '{ "field": "value" }'
```

> 참고: This request automatically creates the customer index if it doesn't already exist, adds a new document that has an ID of 1, and stores and indexes the name field.

## 데이터 검색 — `_search` endpoint

QueryDSL로 조건부 검색. 결과의 `hits` 섹션엔 상위 10개가 담긴다.

```bash
curl -X GET "localhost:9200/bank/_search?pretty" \
  -H 'Content-Type: application/json' \
  -d '{
    "query": { "match_all": {} },
    "sort": [ { "account_number": "asc" } ]
  }'
```

응답 주요 필드:

| 필드 | 의미 |
| --- | --- |
| `took` | 쿼리 수행 시간(ms) |
| `timed_out` | 타임아웃 여부 |
| `hits.total.value` | 전체 매칭 수 |
| `hits.max_score` | 가장 연관도 높은 데이터 점수 |
| `hits.hits[]._source` | 실제 문서 내용 |

## 참고

- [Elasticsearch Getting Started — Search](https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started-search.html)
