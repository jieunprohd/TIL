# 색인

```bash
PUT [인덱스 이름]/_doc
PUT [인덱스 이름]/_doc/[_id 값] # 같은 _id 값의 문서가 존재할 시 덮어씌움
PUT [인덱스 이름]/_create
PUT [인덱스 이름]/_create/[_id 값] # 같은 _id 값의 문서가 존재할 시 에러 발생
```

→ 라우팅 값 (`?routing=[_id값]`) 미지정 시 _id이 해시값을 기반으로 샤드 배정

색인 시 refresh 매개변수를 지정하여 즉시 검색 여부를 지정할 수 있음

- **true** → 색인 직후 문서가 색인된 샤드를 refresh하고 응답을 반환
- **wait_for** → refresh될 때까지 기다린 후 응답 반환, refresh_interval 크기 만큼 응답 지연 가능
- **false** → 기본값, refresh 관련 동작을 수행하지 않음

wait_for로 max_refresh_listeners를 넘어 refresh 대기 중인 경우에 강제 refresh 적용 가능

# 조회

조회의 경우 translog에서도 데이터를 읽어올 수 있어 refresh 되지 않아도 변경 내용 확인 가능

```bash
GET [인덱스 이름]/_doc/[_id 값]
GET [인덱스 이름]/_source/[_id 값] # 메타 데이터를 제외한 문서의 본문만 조회
```

_source_includes 또는 _source_excludes 옵션을 사용하여 제외할 필드 설정 가능 (와일드카드 *, 쉼표로 연결)

_source_includes는 _source로 줄여쓸 수 있으나, 문서의 _source를 포함 여부 옵션과 동일하여 true/false 입력 시 원하는 방향으로 동작하지 않을 수도 있음

라우팅을 사용한 경우 색인 시 사용한 라우팅과 동일하게 지정해야 의도하는 문서 조회가 가능함

# 업데이트

기본적으로 부분 업데이트로, 전체 업데이트가 필요한 경우에는 색인 문서를 사용해야 함

```bash
POST [인덱스 이름]/_update/[_id 값]
```

es의 세그먼트는 불변이기 때문에 업데이트 시 기존 문서 조회 후 부분 수정된 새 문서를 만들고 이 때 _source가 사용됨

→ _source 사용이 비활성화 (`_source: enabled`)된 경우 업데이트 불가

### doc에 내용을 기술하여 업데이트

```bash
POST [인덱스 이름]/_update/[_id 값]

{
"doc": {
"views": 36
},
"detect_noop": false, # noop 확인을 하지 않고 무조건 문서 업데이트 -> 불필요한 디스크 I/O 발생 가능
"doc_as_upsert": true, # 문서가 없는 경우에는 insert, 아니면 update로 동작하도록 설정
}
```

→ 응답 값의 result가 updated로 나오고, 이후 문서 조회 시 적용되어 조회됨

es의 경우 업데이트 전 문서 변경이 없는 요청인지(noop) 확인한 후 업데이트 → noop인 경우 응답의 result가 noop

### script를 이용하여 업데이트

```bash
POST [인덱스 이름]/_update/[_id 값]

{
"script": {
"source": "ctx._source.views += params.amount",
"lang": "painless", # es에서 자체 개발한 script 전용 언어
"params": {
"amoont": 1
}
}
}
```

→ 여러 줄의 스크립트인 경우 `“”” “””`로 묶을 수 있으나, 엄밀한 json 형태가 아니기 때문에 http로 보낼 경우 파싱 에러

**스크립트를 사용한 업데이트 시 접근 가능한 정보**

- **params** → params로 제공한 매개변수의 Map
- **ctx._source** → 문서의 _source를 Map 형태로 반환
- **ctx._op** → 작업 종류를 String으로 나타냄 (index, none, delete)
- **ctx._now** → 현재 타임스탬프값을 밀리세컨드로 반환
- **ctx._index** / **ctx._id** / **ctx._type** / **ctx._routing** / **ctx._version** → 문서의 메타데이터 각 값

색인과 동일하게 라우팅과 refresh 값을 설정할 수 있음

라우팅의 경우 색인과 동일한 routing id를 사용해야 하고, refresh도 성능을 고려해야 함

# 삭제

```bash
DELETE [인덱스 이름]/_delete/[_id 값]
DELETE [인덱스 이름] # 인덱스 전체 삭제이기 때문에 주의
```

색인과 동일하게 라우팅과 refresh 값을 설정할 수 있음

라우팅의 경우 색인과 동일한 routing id를 사용해야 하고, refresh도 성능을 고려해야 함