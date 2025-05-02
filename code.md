# mapping

```bash
# 스키마리스 방식으로 인덱스 생성 (매핑 없이 생성)
PUT /movie

# ID가 1인 영화 문서 추가
PUT /movie/_doc/1
{
    "movieNm": "살아남은 아이",
    "prdtYear": 2017
}

# ID가 2인 영화 문서 추가
PUT /movie/_doc/2
{
    "movieNm": "아이언맨",
    "prdtYear": 2010
}

# 인덱스에 저장된 모든 문서 조회
GET /movie/_search

# movie 인덱스의 매핑 및 설정 구조 확인
GET /movie

# movie 인덱스 삭제
DELETE /movie

# 명시적인 매핑 구조를 정의하여 인덱스 생성
PUT /movie
{
    "mappings": {
        "properties": {
            "movieNm": {
                "type": "text"       # 텍스트 검색이 가능한 분석 필드
            },
            "prdtYear": {
                "type": "integer"    # 숫자(정수) 필드
            }
        }
    }
}

# ID가 1인 영화 문서 다시 추가
PUT /movie/_doc/1
{
    "movieNm": "살아남은 아이",
    "prdtYear": 2017
}

# ID가 2인 영화 문서 다시 추가
PUT /movie/_doc/2
{
    "movieNm": "아이언맨",
    "prdtYear": 2010
}

# 명시된 매핑 구조 확인
GET /movie

# 인덱스에 저장된 모든 문서 다시 조회
GET /movie/_search

# ID가 2인 문서 삭제
DELETE /movie/_doc/2

# ID를 명시하지 않고 문서 추가 (Elasticsearch가 자동 생성)
POST /movie/_doc
{
    "movieNm": "토르",
    "prdtYear": 2020
}

# 인덱스 매핑 및 설정 구조 최종 확인
GET /movie

```

# 데이터 타입

```bash
# 인덱스 생성
PUT /movie_mapping
# "movie_mapping"이라는 이름의 인덱스를 생성합니다.

# 매핑 확인
GET /movie_mapping/_mapping
# 현재 "movie_mapping" 인덱스의 매핑 정보를 조회합니다.

# keyword 타입 필드 추가
PUT /movie_mapping/_mapping
{
  "properties": {
    "multiMovieYn": {
      "type": "keyword"
    }
  }
}
# "multiMovieYn" 필드를 keyword 타입으로 지정합니다.
# 분석하지 않고 전체 문자열을 기준으로 정렬, 필터 등에 사용됩니다.

# text 타입 필드 추가
PUT /movie_mapping/_mapping
{
  "properties": {
    "movieComment": {
      "type": "text"
    }
  }
}
# "movieComment" 필드를 text 타입으로 지정합니다.
# 분석을 거쳐 검색에 적합하게 처리됩니다.

# integer 타입 필드 추가
PUT /movie_mapping/_mapping
{
  "properties": {
    "Year": {
      "type": "integer"
    }
  }
}
# "Year" 필드를 정수 타입으로 지정합니다.
# 연도 등 정수형 데이터를 표현할 때 사용됩니다.

# date 타입 필드 추가
PUT /movie_mapping/_mapping
{
  "properties": {
    "date": {
      "type": "date",
      "format": "yyyy-MM-dd"
    }
  }
}
# "date" 필드를 날짜 타입으로 지정하고,
# "yyyy-MM-dd" 형식으로 입력받도록 설정합니다.

# date_range 타입 필드 추가
PUT /movie_mapping/_mapping
{
  "properties": {
    "showRange": {
      "type": "date_range"
    }
  }
}
# "showRange" 필드를 날짜 범위 타입으로 지정합니다.
# gte/lte 형식으로 날짜 구간을 표현할 수 있습니다.

# 날짜 범위 문서 삽입
POST /movie_mapping/_doc
{
  "showRange": {
    "gte": "2025-01-01",
    "lte": "2025-12-31"
  }
}
# "showRange" 필드에 2025년 한 해 동안의 날짜 범위를 저장합니다.

# geo_point 타입 필드 추가
PUT /movie_mapping/_mapping
{
  "properties": {
    "filmLocation": {
      "type": "geo_point"
    }
  }
}
# "filmLocation" 필드를 지리 좌표 정보용 geo_point 타입으로 지정합니다.

# 위치 정보 문서 삽입
POST /movie_mapping/_doc
{
  "filmLocation": {
    "lat": 55,
    "lon": -1 
  }
}
# "filmLocation" 필드에 위도 55, 경도 -1의 지리 좌표를 저장합니다.

```

# analyze

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
# C
PUT movie_mapping/_doc/1
{
    "movieNm": "아이언맨"
}

# R
GET movie_mapping/_doc/1

# U
PUT movie_mapping/_doc/1
{
    "movieNm": "아이언맨2"
}

# D
DELETE movie_mapping/_doc/1

GET movie/_search
```

# 검색

```bash
# URL 파라미터를 이용한 검색 (prdtYear이 2018인 문서 검색)
GET /movie/_search?q=prdtYear:2018

# URL 파라미터를 이용한 검색 (movieNm에 "star"가 포함된 문서 검색)
GET /movie/_search?q=movieNm:star


# Request Body 방식으로 term 쿼리 실행 (prdtYear이 정확히 2018인 문서 검색)
GET /movie/_search
{
  "query": {
    "term": {"prdtYear": 2018}
  }
}

# bool + filter를 사용한 term 쿼리 (정확한 값 조건 검색, 분석되지 않은 필드에 적합)
GET /movie/_search
{
  "query": {
    "bool": {
      "filter": {
        "term": {
          "prdtYear": 2018
        }
      }
    }
  }
}


# 결과 페이징 처리 (2번째 결과부터 10개 검색)
GET /movie/_search
{
  "query": {
    "term": {"prdtYear": 2018}
  },
  "from": 1,
  "size": 10
}


# 정렬 조건 추가 (movieNm이 star인 문서 중 prdtYear 기준 내림차순 정렬)
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


# 특정 필드만 응답에 포함 (movieNm 필드만 반환)
GET /movie/_search
{
  "query": {
    "term": {
      "movieNm": "star"
    }
  },
  "_source": ["movieNm"]
}


# range 쿼리 (prdtYear이 2010 이상, 2020 이하인 문서 검색)
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


# match 쿼리 + operator "and" 사용 (movieNm에 "listen"과 "Catch" 둘 다 포함된 문서 검색)
GET /movie/_search
{
  "query": {
    "match": {
      "movieNm": {
        "query": "listen Catch",
        "operator": "and"
      }
    }
  }
}


# match 쿼리 + fuzziness 사용 (오타 허용 검색, "stat"와 유사한 단어 검색)
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
# fuzziness 1은 한 글자 차이까지 허용하는 퍼지 검색입니다.

```

# 검색 2
```bash
# kibana_sample_data_ecommerce 인덱스 전체 데이터 검색
GET /kibana_sample_data_ecommerce/_search


# kibana_sample_data_ecommerce 인덱스에 ecommerce라는 별칭(alias)을 부여
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "kibana_sample_data_ecommerce",
        "alias": "ecommerce"
      }
    }
  ]
}


# ecommerce alias를 통해 전체 문서 검색 (match_all은 모든 문서를 검색)
GET /ecommerce/_search
{
  "query": {
    "match_all": {}
  }
}


# ecommerce alias에 해당하는 인덱스의 매핑 정보 확인
GET /ecommerce/_mapping


# match 쿼리: customer_full_name 필드에서 "Mary Bailey"가 포함된 문서 검색 (분석기 적용됨)
GET /ecommerce/_search
{
  "query": {
    "match": {
      "customer_full_name": "Mary Bailey"
    }
  }
}


# multi_match 쿼리: 여러 필드(category, products.product_name)에 대해 "dark" 포함된 문서 검색
GET /ecommerce/_search
{
  "query": {
    "multi_match": {
      "query": "dark",
      "fields": ["category", "products.product_name"]
    }
  }
}


# term 쿼리: 분석되지 않은 정확한 값 "Monday"와 일치하는 day_of_week 필드 검색
GET /ecommerce/_search
{
  "query": {
    "term": {
      "day_of_week": {
        "value": "Monday"
      }
    }
  }
}


# bool 쿼리: 복합 조건 검색
# - must: category가 "clothing"이어야 함
# - must_not: day_of_week이 "Monday"이면 안 됨
# - filter: taxful_total_price가 1~50 사이여야 함 (score에 영향 없음)
GET /ecommerce/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "category": "clothing"
          }
        }
      ],
      "must_not": [
        {
          "term": {
            "day_of_week": {
              "value": "Monday"
            }
          }
        }
      ],
      "should": [],
      "filter": [
        {
          "range": {
            "taxful_total_price": {
              "gte": 1,
              "lte": 50
            }
          }
        }
      ]
    }
  }
}


# prefix 쿼리: category 필드가 "me"로 시작하는 값을 가진 문서 검색 (예: "mens", "meat" 등)
GET /ecommerce/_search
{
  "query": {
    "prefix": {
      "category": {
        "value": "me"
      }
    }
  }
}


# exists 쿼리: currency 필드가 존재하는 문서만 검색
GET /ecommerce/_search
{
  "query": {
    "exists": {
      "field": "currency"
    }
  }
}


# wildcard 쿼리: customer_first_name 필드에서 "E"로 시작하고 총 7자리인 값을 검색
# "E?????"는 대소문자 구분 없이(E로 시작 + 임의의 6글자)
GET /ecommerce/_search
{
  "query": {
    "wildcard": {
      "customer_first_name": {
        "value": "E?????",
        "case_insensitive": true
      }
    }
  }
}


```

# 분석기 커스텀

```bash
# 기본 분석기(standard analyzer): 소문자 변환 + 불용어 제거 + 일반적인 토크나이징
POST _analyze
{
  "analyzer": "standard",
  "text": "Hello world!!!!"
}
# 결과: ["hello", "world"]


# 공백 기반 분석기(whitespace analyzer): 단순히 공백 기준으로만 토큰 분리, 문장 부호 그대로 유지
POST _analyze
{
  "analyzer": "whitespace",
  "text": "Hello world!!!!"
}
# 결과: ["Hello", "world!!!!"]


# Déjà 같은 특수 문자 포함된 문장을 standard analyzer로 분석
POST _analyze
{
  "analyzer": "standard",
  "text": "Is this Déjà vu?"
}
# 결과: ["is", "this", "déjà", "vu"] (소문자 처리됨, déj`à` 그대로 유지됨)


# 사용자 정의 분석기 구성 없이 analyzer 구성 요소(tokenizer, filter)를 직접 지정해서 분석
POST _analyze
{
  "tokenizer": "standard",
  "filter": ["lowercase", "asciifolding"],
  "text": "Is this Déjà vu?",
  "explain": true
}
# Déjà → deja로 변환 (asciifolding 필터가 비ASCII 문자 제거)
# 결과: ["is", "this", "deja", "vu"]


# 사용자 정의 인덱스 생성: 영어 전용 커스텀 분석기(my_analyzer)를 적용
PUT /article
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": [
            "lowercase",         # 소문자 변환
            "asciifolding"       # 비ASCII 문자 제거 (예: é → e)
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "content": {
        "type": "text",
        "analyzer": "my_analyzer"
      }
    }
  }
}


# my_analyzer를 사용하여 텍스트 분석 테스트
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "Is this Déjà vu?"
}
# 결과: ["is", "this", "deja", "vu"]


# article 인덱스 삭제
DELETE /article


# 개선된 버전(ver.2): HTML 제거 + stemming 추가
PUT /article
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "type": "custom",
          "char_filter": ["html_strip"],    # HTML 태그 제거
          "tokenizer": "standard",
          "filter": [
            "lowercase",
            "asciifolding",
            "stemmer"                       # 동사 원형 처리(jumping → jump)
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "content": {
        "type": "text",
        "analyzer": "my_analyzer"
      }
    }
  }
}


# my_analyzer 분석기 동작 확인 (HTML 제거, 소문자화, ASCII folding, stemming 모두 적용)
POST /article/_analyze
{
  "field": "content",
  "text": "<b>Is this Déjà vu?<b>. foxes are jumping",
  "explain": true
}
# 결과 예시: ["is", "this", "deja", "vu", "fox", "are", "jump"]
# foxes → fox, jumping → jump (stemming 적용)


# 문서 색인: HTML 포함된 문서 저장됨
POST /article/_doc
{
  "content": "<b>Is this Déjà vu?<b>. foxes are jumping"
}

POST /article/_doc
{
  "content": "<b>Is this Déjà vu?<b>. foxes are jumped"
}
# 두 문서 모두 'jump'로 색인됨(stemmer 덕분에)


# jump 단어로 검색 (jumping, jumped 모두 검색됨)
GET /article/_search?q=content:jump


# deja 단어로 검색 (Déjà → deja로 분석되었기 때문에 검색 가능)
GET /article/_search?q=content:deja

```

# 집계
```bash
# 인덱스 데이터 검색 (기본 샘플 로그 데이터 확인)
GET /kibana_sample_data_logs/_search

# kibana_sample_data_logs 인덱스에 logs 라는 alias(별칭) 추가
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "kibana_sample_data_logs",
        "alias": "logs"
      }
    }
  ]
}

# alias를 통해 logs 인덱스 매핑 정보 조회
GET /logs/_mapping

# alias를 통해 logs 인덱스 데이터 검색
GET /logs/_search

# IP 기준으로 집계 (IP별 문서 수 집계)
GET /logs/_search?size=0
{
  "aggs": {
    "region_count": {
      "terms": {
        "field": "ip"
      }
    }
  }
}

# geo.dest 필드(도착 국가 코드) 기준으로 집계 (국가별 문서 수 집계)
GET /logs/_search?size=0
{
  "aggs": {
    "region_count": {
      "terms": {
        "field": "geo.dest"
      }
    }
  }
}

# 응답 코드(response.keyword) 기준으로 집계
GET /logs/_search?size=0
{
  "aggs": {
    "status_count": {
      "terms": {
        "field": "response.keyword"
      }
    }
  }
}

# geo.dest가 CN(중국)인 로그의 bytes 합계
GET /logs/_search?size=0
{ 
  "query": {
    "match": {
      "geo.dest": "CN"
    }
  },
  "aggs": {
    "total_bytes": {
      "sum": {
        "field": "bytes"
      }
    }
  }
}

# geo.dest가 CN인 로그의 ip 필드 개수(count)
GET /logs/_search?size=0
{
  "query": {
    "match": {
      "geo.dest": "CN"
    }
  },
  "aggs": {
    "count": {
      "value_count": {
        "field": "ip"
      }
    }
  }
}

# bytes 필드에 대한 확장 통계 집계 (합계, 평균, 분산, 표준편차 등)
GET /logs/_search?size=0
{
  "aggs": {
    "stats": {
      "extended_stats": {
        "field": "bytes"
      }
    }
  }
}

# geo.dest 필드의 고유 값 개수 (cardinality 집계)
GET /logs/_search?size=0
{
  "aggs": {
    "card": {
      "cardinality": {
        "field": "geo.dest"
      }
    }
  }
}

# bytes 필드의 백분위수(전체 중 상위 몇 %인지)
GET /logs/_search?size=0
{
  "aggs": {
    "percent": {
      "percentiles": {
        "field": "bytes"
      }
    }
  }
}

# bytes 필드의 특정 백분위수(10%, 50%, 90%) 값 추출
GET /logs/_search?size=0
{
  "aggs": {
    "percent": {
      "percentiles": {
        "field": "bytes",
        "percents": [10, 50, 90]
      }
    }
  }
}

# 특정 값(100, 9999)이 bytes 필드 전체 값 중 몇 %에 위치하는지
GET /logs/_search?size=0
{
  "aggs": {
    "percent": {
      "percentile_ranks": {
        "field": "bytes",
        "values": [100, 9999]
      }
    }
  }
}

# geo.coordinates 필드의 지리적 범위(좌상단/우하단) 계산
GET /logs/_search?size=0
{
  "aggs": {
    "viewport": {
      "geo_bounds": {
        "field": "geo.coordinates"
      }
    }
  }
}

# bytes 필드 값의 범위에 따른 집계 (range bucket aggregation)
GET /logs/_search?size=0
{
  "aggs": {
    "byte_range": {
      "range": {
        "field": "bytes",
        "ranges": [
          {
            "from": 1000,
            "to": 2000
          },
          {
            "from": 2000,
            "to": 3000
          }
        ]
      }
    }
  }
}

# 특정 날짜 범위의 문서 수 집계 (date_range 집계)
GET /logs/_search?size=0
{
  "aggs": {
    "date-count": {
      "date_range": {
        "field": "@timestamp",
        "ranges": [
          {
            "from": "2025-06-09T11:12:29.904Z",
            "to": "2025-06-09T12:12:29.904Z"
          }
        ]
      }
    }
  }
}

# bytes 필드에 대한 구간별 히스토그램 (5000 간격)
GET /logs/_search?size=0
{
  "aggs": {
    "byte_histo": {
      "histogram": {
        "field": "bytes",
        "interval": 5000
      }
    }
  }
}

# 날짜 기준 히스토그램 (하루 단위로 집계)
GET /logs/_search?size=0
{
  "aggs": {
    "date_histo": {
      "date_histogram": {
        "field": "@timestamp",
        "calendar_interval": "1d"
      }
    }
  }
}

# pipeline aggregation - 형제 집계
# 일자별 bytes 총합과 그 중 최소합계 일자 찾기
GET /logs/_search?size=0
{
  "aggs": {
    "date_histo": {
      "date_histogram": {
        "field": "@timestamp",
        "calendar_interval": "1d"
      },
      "aggs": {
        "bytes_sum": {
          "sum": {
            "field": "bytes"
          }
        }
      }
    },
    "min_bytes": {
      "min_bucket": {
        "buckets_path": "date_histo>bytes_sum"
      }
    }
  }
}

# pipeline aggregation - 부모-자식 집계
# 일자별 최대 bytes 값의 변화량(증가 폭) 계산
GET /logs/_search?size=0
{
  "aggs": {
    "date_histo": {
      "date_histogram": {
        "field": "@timestamp",
        "calendar_interval": "1d"
      },
      "aggs": {
        "bytes_max": {
          "max": {
            "field": "bytes"
          }
        },
        "max_deriv": {
          "derivative": {
            "buckets_path": "bytes_max"
          }
        }
      }
    }
  }
}


```

# TEMP

```bash
# 기본 사용 예시: Nori Analyzer 설정 및 분석
DELETE /article

PUT /article
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "type": "custom",  # 사용자 정의 분석기
          "tokenizer": "nori_tokenizer",  # Nori 토크나이저 사용
          "filter": [
            "nori_part_of_speech",  # 품사 필터
            "nori_readingform"  # 읽기 형태 필터
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "content": {
        "type": "text",  # 텍스트 필드
        "analyzer": "my_analyzer"  # 사용자 정의 분석기 적용
      }
    }
  }
}

# 분석 요청: '안녕하세요' 텍스트에 대해 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "안녕하세요"
}

# 분석 요청: '잠실역에서 롯데타워가 보여요' 텍스트에 대해 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "잠실역에서 롯데타워가 보여요"
}

# 분석 요청: '세종시' 텍스트에 대해 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "세종시"
}

# 분석 요청: '알잘딱깔센' 텍스트에 대해 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "알잘딱깔센"
}


# 커스텀 사용 예시: 사용자 정의 토크나이저 및 필터 설정
DELETE /article

PUT /article
{
  "settings": {
    "analysis": {
      "tokenizer": {
        "my_tokenizer": {
          "type": "nori_tokenizer",  # Nori 토크나이저 사용
          "decompound_mode": "mixed",  # 복합어 분해 모드
          "user_dictionary_rules": [  # 사용자 정의 단어 추가
            "c++",
            "씨쁠쁠"
          ]
        }
      },

      "filter": {
        "my_filter": {
          "type": "nori_part_of_speech",  # 품사 필터 사용
          "stoptags": [
            "IC"  # 불필요한 품사 태그 제외
          ]
        }
      },
      
      "analyzer": {
        "my_analyzer": {
          "type": "custom",  # 사용자 정의 분석기
          "tokenizer": "my_tokenizer",  # 사용자 정의 토크나이저 사용
          "filter": [
            "my_filter",  # 사용자 정의 품사 필터
            "nori_readingform",  # 읽기 형태 필터
            "nori_number"  # 숫자 처리 필터
          ]
        }
      }
    }
  }
}

# 분석 요청: '우와! 잠실역에서 롯데타워가 보여요' 텍스트 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "우와! 잠실역에서 롯데타워가 보여요"
}

# 분석 요청: '大韓民國 세종시 일이삼번지' 텍스트 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "大韓民國 세종시 일이삼번지"
}

# 분석 요청: '알잘딱깔센' 텍스트 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "알잘딱깔센"
}

# 분석 요청: 'c++이 파이썬보다 더 어려워요' 텍스트 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "c++이 파이썬보다 더 어려워요"
}

# 분석 요청: '아이패드 샀어요' 텍스트 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "아이패드 샀어요"
}

# 분석 요청: 'ipad 판매합니다.' 텍스트 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "ipad 판매합니다."
}


# 불용어 및 동의어 필터 설정 예시
DELETE article

PUT /article
{
  "settings": {
    "analysis": {
      "tokenizer": {
        "my_tokenizer": {
          "type": "nori_tokenizer",  # Nori 토크나이저 사용
          "decompound_mode": "mixed",  # 복합어 분해 모드
          "user_dictionary_rules": [  # 사용자 정의 단어 추가
            "c++",
            "씨쁠쁠"
          ]
        }
      },

      "filter": {
        "my_filter": {
          "type": "nori_part_of_speech",  # 품사 필터 사용
          "stoptags": [
            "IC"  # 불필요한 품사 태그 제외
          ]
        },
        "my_stop_filter": {
          "type": "stop",  # 불용어 필터 사용
          "stopwords_path": "custom/stop.txt"  # 불용어 파일 경로
        },
        "my_synonym_filter": {
          "type": "synonym",  # 동의어 필터 사용
          "synonyms_path": "custom/synonym.txt"  # 동의어 파일 경로
        }
      },
      
      "analyzer": {
        "my_analyzer": {
          "type": "custom",  # 사용자 정의 분석기
          "tokenizer": "my_tokenizer",  # 사용자 정의 토크나이저 사용
          "filter": [
            "my_filter",  # 사용자 정의 품사 필터
            "nori_readingform",  # 읽기 형태 필터
            "nori_number",  # 숫자 처리 필터
            "my_stop_filter",  # 불용어 필터
            "my_synonym_filter"  # 동의어 필터
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "content": {
        "type": "text",  # 텍스트 필드
        "analyzer": "my_analyzer"  # 사용자 정의 분석기 적용
      }
    }
  }
}

# 분석 요청: 'python good' 텍스트 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "python good"
}

# 분석 요청: '바보야 python이 c++보다 더 쉬워!' 텍스트 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "바보야 python이 c++보다 더 쉬워!"
}

# 분석 요청: 'ipad를 구매했습니다. 너무 비싸요 ㅠㅠ' 텍스트 분석
GET /article/_analyze
{
  "analyzer": "my_analyzer",
  "text": "ipad를 구매했습니다. 너무 비싸요 ㅠㅠ"
}

# 문서 추가 예시
POST /article/_doc
{
  "content": "바보야 c++이 파이썬 보다 더 어렵잖아"
}

POST /article/_doc
{
  "content": "python 오늘 처음배웠어요"
}

POST /article/_doc
{
  "content": "pythonnnnnn 오늘 처음배웠어요"
}

POST /article/_doc
{
  "content": "pythonist 오늘 처음배웠어요"
}

POST /article/_doc
{
  "content": "pythony 오늘 처음배웠어요"
}

# 검색 요청: 'python' 단어 검색
GET /article/_search?q=content:python


# 하이라이트 기능 예시
GET /article/_search
{
  "query": {
    "match": {
      "content": "python"  # 'python' 단어를 포함하는 문서 찾기
    }
  },
  "highlight": {
    "fields": {
      "content": {}  # content 필드에 하이라이트 적용
    }
  }
}


# 자동완성 기능 예시
GET /article/_search
{
  "suggest": {
    "my-suggest": {
      "text": "ptthon",  # 오타 수정 제안
      "term": {
        "field": "content"  # content 필드를 대상으로 자동완성 제안
      }
    }
  }
}


# 자동완성 (Completion) 예시
PUT /product
{
  "mappings": {
    "properties": {
      "name": {
        "type": "completion"  # 자동완성 필드 설정
      }
    }
  }
}

POST /product/_doc
{
  "name": "ipad"
}

POST /product/_doc
{
  "name": "ipad air"
}

POST /product/_doc
{
  "name": "ipad pro"
}

POST /product/_doc
{
  "name": "아이폰 16 프로"
}

POST /product/_doc
{
  "name": "아이폰 16 프로 맥스"
}

# 자동완성 검색 요청: '아이'로 시작하는 추천 검색어
POST /product/_search
{
  "suggest": {
    "my_suggest": {
      "prefix": "아이",  # '아이'로 시작하는 검색어 추천
      "completion": {
        "field": "name"  # 'name' 필드를 대상으로 자동완성
      }
    }
  }
}

```