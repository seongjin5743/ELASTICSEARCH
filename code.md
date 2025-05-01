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

# 검색 2
```bash
GET /kibana_sample_data_ecommerce/_search

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


GET /ecommerce/_search
{
    "query": {
        "match_all": {}
    }
}

GET /ecommerce/_mapping

# match query
GET /ecommerce/_search
{
    "query": {
        "match": {
          "customer_full_name": "Mary Bailey"
        }
    }
}

# multi match
GET /ecommerce/_search
{
    "query": {
        "multi_match": {
          "query": "dark",
          "fields": ["category", "products.product_name"]
        }
    }
}


# term query
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

# bool query
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


# prefix 
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


# exists
GET /ecommerce/_search
{
    "query": {
        "exists": {
            "field": "currency"
        }
    }
}

# wildcard
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
POST _analyze
{
    "analyzer": "standard",
    "text": "Hello world!!!!"
}

POST _analyze
{
    "analyzer": "whitespace",
    "text": "Hello world!!!!"
}

POST _analyze
{
    "analyzer": "standard",
    "text": "Is this Déjà vu?"
}


POST _analyze
{
    "tokenizer": "standard",
    "filter": ["lowercase", "asciifolding"],
    "text": "Is this Déjà vu?",
    "explain": true
}


# for english
PUT /article
{
    "settings": {
        "analysis": {
            "analyzer": {
                "my_analyzer": {
                    "type": "custom",
                    "tokenizer": "standard",
                    "filter": [
                        "lowercase", 
                        "asciifolding"
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

GET /article/_analyze
{
    "analyzer": "my_analyzer",
    "text": "Is this Déjà vu?"
}

DELETE /article


# ver.2
PUT /article
{
    "settings": {
        "analysis": {
            "analyzer": {
                "my_analyzer": {
                    "type": "custom",
                    "char_filter": ["html_strip"],
                    "tokenizer": "standard",
                    "filter": [
                        "lowercase",
                        "asciifolding",
                        "stemmer"
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

POST /article/_analyze
{
    "field": "content",
    "text": "<b>Is this Déjà vu?<b>. foxes are jumping",
    "explain": true
}

POST /article/_doc
{
    "content": "<b>Is this Déjà vu?<b>. foxes are jumping"
}

POST /article/_doc
{
    "content": "<b>Is this Déjà vu?<b>. foxes are jumped"
}

GET /article/_search?q=content:jump
GET /article/_search?q=content:deja
​
```

# 집계
```bash
GET /kibana_sample_data_logs/_search

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

GET /logs/_mapping
GET /logs/_search



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


# 합산 / 평균 / 최대 / 최소
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



# value_count
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


# state / extended_stats
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


# cardinality
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


# percentiles
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


# 지형 집계
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



# 버킷 집계
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



# pipeline 
# 형제 집계
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


# 부모자식 집계
# 날짜별 데이터 증가폭 출력
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