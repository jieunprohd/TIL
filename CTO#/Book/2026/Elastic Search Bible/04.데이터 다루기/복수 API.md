# bulk API

여러 색인, 업데이트, 삭제를 한 번의 요청에 담아서 보내는 api

```bash
POST _bulk # POST [인덱스 이름]/_bulk로도 사용 가능

{"index":("_index": "bulk_test","_id": "1"}}
{"field1": "value1"} # bulk_test 인덱스에 _id 값이 1인 문서 색인
{"delete": {"_index": "bulk_test","_id":"2"}} # _id 값이 2인 문서 삭제
{"create": {"_index": "bulk_test","_id":"3"}
{"field1" :"value3"} # _id 값이 3인 문서 색인
{"update": {"_id": "1", "_index": "bulk_test"}}
{"doc": {"field2" : "value2"}} # _id 값이 1인 문서 수정
{"index": {"_index": "bulk_test","_id":"4" ,"routing":"a"}}
{"field1": "value4" } # _id 값이 4인 문서를 a를 기반으로 라우팅하여 색인
```

_bulk 요청에서 index 미설정 시 기본 인덱스에 작업됨

### 작업 순서

bullk api의 작업은 그 순서대로가 아닌, 여러 개의 주 샤드로 넘어가고 각 요청이 독자적으로 수정됨

→ 완전히 동일한 인덱스, _id 값, 라우팅 조합은 반드시 동일한 주 샤드로 넘어가 순서가 보장됨

# multi get API

_id를 여러개 지정하여 문서를 한 번에 조회하는 api

```bash
GET _mget

{
    "docs": [
        {
            "_index": [인덱스 이름],
            "_id": [_id 값],
            "routing": [routing 값],
            "_source": {
                "includes": ["p*"],
                "excludes": ["views"]
            }
        }
    ]
}

GET [인덱스 이름]/_mget

{
  "ids": [_id 값들]
}
```

# update by query

조건에 만족하는 문서 전체 업데이트

```bash
POST [인덱스 이름]/_update_by_query

{
    "script": {
      "source": "ctx._source.field1 = ctx._source.field1 + '-' + ctx._id"
    },
    "query": {
        "exists": {
          "field": "field1"
        }
    }
    # field1이라는 필드가 존재하는 문서를 대상으로 field1을 **[field1 값]-[_id 값]**으로 바꾸는 작업
    
    # "doc": {
    # 	"필드": [변경할 값] # doc을 이용한 업데이트는 미지원
    # }
}
```

doc 필드를 통한 업데이트는 지원하지 않고 script를 통한 업데이트만 지원

update by query의 경우 조건에 일치하는 문서를 스냅샷을 찍고, 변화가 생긴 경우 업데이트하지 않음

→ conflicts 매개변수를 통해 지정 가능 (abort → 충돌 시 중단, proceed → 다음 작업을 넘어감)

abort로 인해 작업이 중단되더라도 롤백되는 것이 아니기 때문에 고려 필요

### 스로틀링

대량 업데이트 시 서비스 작업 환경에 영향이 가지 않도록 하는 기능

**매개변수 정리**

- **scroll_size** → scroll_size에 따라서 문서를 조회하고 업데이트 작업
- **scroll** → 스냅샷을 찍은 후 문맥 보존 시간, scroll_size만큼 작업 처리가 가능해야 하기 때문에 너무 짧으면 안 됨
- **requests_per_second** → 평균적으로 초당 몇 개까지의 작업을 수행할지 결정

**스로틀링의 핵심은 requests_per_second와 scroll_size 값을 조절하는 것**

대형 서비스의 경우 스로틀링 시간을 넉넉하게 잡아 클러스터의 부담과 서비스 리스크를 줄이고 안전하게 작업 수행

문서 업데이트 중에 서비스 장애가 발생하여 스로틀링을 다시 해야하는 경우 rethrottle를 사용하여 변경 할 수 있음

```bash
POST _update_by_query/[task id]/_rethrottle?requests_per_second=&scroll_size=&scroll=
```

### 비동기 요청과 tasks API

스로틀링의 경우 수십시간 이상 걸릴 수 있어 https 타임아웃으로 응답 확인이 어려워짐

wait_for_completion 매개변수를 false로 처리해 비동기적으로 처리할 수 있음 → 응답으로 task의 id 반환

**tasks 조회**

```bash
GET .tasks/_doc/[task id 값]
GET .tasks/[task id 값]
```

**tasks 취소**

```bash
POST _tasks/[task id 값]/_cancel
```

**tasks 결과 삭제**

```bash
DELETE .tasks/_doc/[task id 값]
```

### 슬라이싱

slices 매개변수를 통해 검색과 업데이트를 지정한 개수로 쪼개 병렬 수행 가능

```bash
POST [인덱스 이름]/_update_by_query?slices=auto

{
  # 처리할 script, query
}
```

**slice 설정 가능 값**

- **1** → 기본값으로 병렬로 쪼개지 않음
- **auto** → es가 적절한 개수를 지정하여 작업 병렬 수행

슬라이드 수가 샤드 수보다 많은 경우 한 샤드 내의 데이터를 여러 슬라이스에 분배하면서 시간과 메모리가 많이 소요됨

requests_per_second 매개변수는 각 슬라이스에 쪼개져서 적용됨 (slices가 4, requests_per_second가 1000 → 각 슬라이스는 250씩 분배)

# delete by query

update by query와 같이 검색 쿼리로 삭제할 대상을 지정한 후에 삭제 수행

```bash
POST [인덱스 이름]/_delete_by_query

{
    "query": {
      # 조건 쿼리 작성
    }
}
```