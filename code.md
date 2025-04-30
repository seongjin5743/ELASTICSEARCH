# mapping

```bash
# 스키마리스 방식으로 인덱스 생성
PUT /movie

# 데이터 추가 1
PUT /movie/_doc/1
{
    "movieNm": "살아남은 아이",
    "prdtYear": 2017
}

# 데이터 추가 2
PUT /movie/_doc/2
{
    "movieNm": "아이언맨",
    "prdtYear": 2010
}

# 저장된 데이터 확인
GET /movie/_search

# movie인덱스 구조 확인
GET /movie

# 인덱스 삭제
DELETE /movie

# 스키마 구조를 잡아서 인덱스 생성
PUT /movie
{
    "mappings": {
        "properties": {
            "movieNm": {
                "type": "text"
            },
            "prdtYear": {
                "type": "integer"
            }
        }
    }
}

# 데이터 추가 1
PUT /movie/_doc/1
{
    "movieNm": "살아남은 아이",
    "prdtYear": 2017
}

# 데이터 추가 2
PUT /movie/_doc/2
{
    "movieNm": "아이언맨",
    "prdtYear": 2010
}

# movie인덱스 구조 확인
GET /movie

# 데이터 확인
GET /movie/_search

# 2번 문서 삭제
DELETE /movie/_doc/2

# id 없이 데이터 생성
POST /movie/_doc
{
    "movieNm": "토르",
    "prdtYear": 2020
}

GET /movie
```

# 데이터 타입

```bash
# 인덱스 생성
PUT /movie_mapping

# mapping 확인
GET /movie_mapping/_mapping

#keyword
PUT /movie_mapping/_mapping
{
    "properties": {
        "multiMovieYn": {
            "type": "keyword"
        }
    }
}

# text
PUT /movie_mapping/_mapping
{
    "properties": {
        "movieComment": {
            "type": "text"
        }
    }
}

# integer
PUT /movie_mapping/_mapping
{
    "properties": {
        "Year": {
            "type": "integer"
        }
    }
}

# date
PUT movie_mapping/_mapping
{
    "properties": {
        "date": {
            "type": "date",
            "format": "yyyy-MM-dd"
        }
    }
}

# range
PUT movie_mapping/_mapping
{
    "properties": {
        "showRange": {
            "type": "date_range"
        }
    }
}

POST movie_mapping/_doc
{
    "showRange": {
        "gte": "2025-01-01",
        "lte": "2025-12-31"
    }
}

# GEO
PUT movie_mapping/_mapping
{
    "properties": {
        "filmLocation": {
            "type": "geo_point"
        }
    }
}

POST movie_mapping/_doc
{
    "filmLocation": {
        "lat": 55,
        "lon": -1 
    }
}
```

- analyze

```bash
# 분석기
POST _analyze
{
    "analyzer": "standard",
    "text": "우리나라가 좋은나라, 대한민국 화이팅"
}
```

# CRUD

```bash
# CRUD
PUT movie_mapping/_doc/1
{
    "movieNm": "아이언맨"
}

GET movie_mapping/_doc/1

PUT movie_mapping/_doc/1
{
    "movieNm": "아이언맨2"
}

DELETE movie_mapping/_doc/1

GET movie/_search
```

# 검색

```bash
# URL 검색
GET /movie/_search?q=prdtYear:2018
GET /movie/_search?q=movieNm:star

# Request Body 검색
GET /movie/_search
{
    "query": {
        "term": {"prdtYear": 2018}
    }
}

GET /movie/_search
{
    "query": {
        "bool":{
            "filter ": {
                "term": {
                    "prdtYear": 2018
                }
            }
        }
    }
}

# from, size
GET /movie/_search
{
    "query": {
        "term": {"prdtYear": 2018}
    },
    "from": 1,
    "size": 10
}

# sort
GET /movie/_search
{
    "query": {
        "term": {"movieNm": "star"}
    },
    "sort": {
        "prdtYear": {
            "order": "desc"
        }
    }
}

# _source 
GET /movie/_search
{
    "query": {
        "term": {
            "movieNm": "star"
        }
    },
    "_source": ["movieNm"]
}

# range
GET /movie/_search
{
    "query": {
        "range": {
            "prdtYear": {
              "gte": 2010,
              "lte": 2020
            }
        }
    }
}

# operator
GET movie/_search
{
    "query":{
        "match": {
          "movieNm": {
            "query": "listen Catch",
            "operator": "and"
          }
        }
    }
}

# fuzziness
GET /movie/_search
{
    "query": {
        "match": {
          "movieNm": {
            "query": "stat",
            "fuzziness": 1
          }
        }
    }
}
```