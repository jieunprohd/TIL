# 동기 / 비동기

**동기**

- 태스크가 직렬적으로 동작, 요청이 들어온 순서를 지킴
- 요청을 보낸 후 응답을 받아야만 다음 동작 수행, 어떤 태스크 작업을 처리하는 동안 다른 태스크들은 대기

**비동기**

- 태스크가 병렬적으로 동작, 요청이 들어온 순서가 지켜지지 않을 수 있음
- 요청을 보낸 후 응답의 수락 여부와 상관없이 다음 동작 수행 → 다른 작업을 처리하면서 동시에 처리
- 비동기 요청에 대한 응답 후 처리할 콜백 함수를 알려줌 → 태스트 완료시 해당 콜백 함수 실행
- 비동기 처리를 위한 하나의 패턴으로 콜백 함수를 사용하지만 이는 **콜백 헬**에 빠질 위험이 있음

** 콜백 헬 : 여러 개의 콜백 함수가 중첩되어 복잡도가 높아지는 것


**Promise**

- Promise 생성자 함수를 인스턴스화 해 콜백 함수를 인자로 전달 resolve, reject (함수) 포함
- 비동기 처리 성공시 resolve 호출, 비동기 처리 실패시 reject 호출
- 후속 처리를 위해 then, catch 메소드 사용
- 비동기 처리 작업의 결과로 또다른 비동기 처리 작업시 생기는 콜벡 헬을 then, catch 체이닝을 통해 해결

- all() → 모든 프로미스가 성공해야만 전체 결과를 반환, 하나라도 실패하면 reject
- allSettled() → 성공/실패 상관없이, 모든 프로미스의 결과 상태를 확인하고 싶을 때 사용
- any() → 여러 프로미스 중, 하나라도 성공하면 성공(resolve), 모두 실패해야만 reject됨
- race() → 가장 먼저 완료된 프로미스를 반환
- reject() → 주어진 값을 무조건 실패한 프로미스로 감싸서 반환
- resolve() → 주어진 값을 무조건 성공한 프로미스로 감싸서 반환

**Async / Await**

- async는 항상 프로미스 반환
- await는 async 함수 안에서만 동작, 프로미스가 처리될 때까지 대기
- try - catch로 성공, 실패 관리

**Promise와 async/await의 내부 동작 차이**

→ async/await가 Promise를 감싸고 있는 Wrapper 형태

** generator 찾아보기

### Blocking / Non-Blocking

**Blocking**

- 특정 작업을 처리하기 위해 메인 흐름을 막음
- 만약 서버에서 특정 작업을 처리하기 위해 다른 작업들까지 멈춘다면 Blocking

**Non-Blocking**

- 특정 작업을 처리하기 위해 메인 흐름을 막지 않음
- 만약 서버에서 특정 작업을 처리하면서 다른 작업들도 그대로 처리한다면 Non-Blocking

**예시**

- Sync + Blocking → 다른 작업이 진행되면 자기 작업도 진행 X, 다른 작업의 완료 여부를 받아 순차 처리
- ~~Sync + Non-Blocking → 다른 작업이 진행돼도 자기 작업 진행, 다른 작업의 결과를 바로 처리해 순차 처리~~
- ~~Async + Blocking → 다른 작업이 진행되면 자기 작업도 진행 X, 다른 작업과의 순서가 지켜지지 않음~~
- Async + Non-Blocking → 다른 작업이 진행돼도 자기 작업 진행, 다른 작업과의 순서가 지켜지지 않음

**Async vs Non-Blocking**

Async는 작업 완료 여부를 신경 쓰는지에 대한 관점

Non-Blocking은 현재 작업이 다른 작업의 진행을 막는지 여부에 대한 관점

** **제어권** → 함수를 실행할 수 있는 권리

Blocking → 다른 작업이 진행되면 자신의 제어권을 넘김

Non-Blocking → 다른 작업을 호출할 때 잠시 자신의 제어권을 넘기고 돌려받음

# node.js는 싱글스레드 기반

**동시성** → 2개 이상의 스레드가 동시에 작업, 같은 시간에 실행 X (싱글 코어의 멀티스레드 환경)
**병렬성** → 2개 이상의 스레드가 동시에 작업, 같은 시간에 실행 O (멀티 코어의 멀티스레드 환경)

**프로세스 내에서 하나의 이벤트 루프라는 메인 스레드만 실행**

→ 이렇게 되면 병렬처리에 의미가 없지 않나?!

**메인 스레드 이벤트 루프에서는 오래 걸리는 동작(파일 시스템 접근, 데이터베이스 쿼리, HTTP 요청 등)을 처리하지 않는다!**

![image.png](/image/image.png)

> 이벤트 루프는  시간이 오래 걸리는 작업은 C 기반의 libuv 라이브러리로 I/O 작업을 넘긴다
libuv는 전달받은 라이브러리를 내부의 워커 스레드로 전달하여 작업을 처리한다
>

위의 과정을 통해 메인 스레드는 멈추지 않고 동작할 수 있음!

** 추가적으로 Node.js v12 이후로 worker_thread 모듈이 생겨 명시적으로 멀티스레드 구현 가능

** 기본적으로 4개의 스레드로 구성, UV_THREADPOOL_SIZE를 통해 128개까지 늘릴 수 있음

**그럼 위의 관점에서 보았을 때!**

병렬 처리가 가능한 이유 → 이벤트루프가 워커 스레드로 작업을 위임하면서 메인 스레드는 안 멈추기 때문!

**프로세스 정리**

1. 클라이언트에서 n번의 chunk 업로드 요청을 보냄
2. I/O 작업은 libuv에서 알아서 백그라운드로 Thread Pool로 위임
3. 어떤건 OS 커널에게 맡김, 어떤 건 바로 실행
4. 어떤 건 Thread Pool에서 요청을 처리 → 최소 4개, 최대 128개의 스레드풀을 돌면서 병렬적으로 처리
5. 작업이 끝나면 완료 콜백을 이벤트 큐에 등록
6. 이벤트 루프가 해당 콜백을 ****이벤트 큐에서 꺼내 실행
7. Thread Pool에서 처리된 응답들은 이벤트 루프를 통해 클라이언트로 전달

** OS 커널 → 프로세스 관리, 파일 시스템 관리 등을 전담

파일 시스템 작업, 네트워크 I/O, 타이머, 입출력 장치 관련 작업은 이벤트 루프가 OS 커널에 위임


**I/O 작업이 아닌 CPU가 동작해야 하는 작업은 메인 스레드 하나에서 처리해야 한다!**

→ Java Spring의 경우 스레드풀에서 남는 스레드에 요청을 전달해 처리한다

→ Node.js는 무거운 CPU 작업이 들어오면 멈춘다 → **scale-out 방식**으로 해결 가능

→ 즉, 멀티 스레드가 아니라도 여러 개의 Node.js 서버를 띄움으로써 무거운 CPU 작업 처리가 가능하다

** **워커스레드 사용시 가능**

**이벤트 큐**

- Macro Task Queue - setTimeout, setImmediate, I/O callbacks
- Micro Task Queue - Promise.then, queueMicrotask

Micro Task Queue의 우선순위가 Macro Task Queue보다 높음

### 워커스레드로 멀티코어 구현 테스트

상황 : 1부터 100억까지 더할 때 1코어 테스트와 5코어 테스트, 10코어 소요 시간테스트 진행

**1코어**

- 코드

```jsx
const {Worker} = require('worker_threads');

const workerPath = './multicore.js';

function main() {
const range = 10000000000;
const startTime = new Date().getTime();

const worker = new Worker(workerPath, {
workerData: {
startRange: 1,
endRange: range
},
});

worker.on('message', (result) => {
const endTime = new Date().getTime();
console.log(`합계: ${result}`);
console.log(`실행시간: ${endTime - startTime}ms`);
});
}

main();
```


```sql
합계: 50000000000067860000
실행시간: 14536ms
```

**5코어**

- 코드

```jsx
const {Worker} = require('worker_threads');

const workerPath = './multicore.js';

function main() {
const range = 10000000000;
const startPoints = [1, 2000000000, 4000000000, 6000000000, 8000000000];
const workers = [];
let completed = 0;
let sum = 0;
const startTime = new Date().getTime();

for (let i = 0; i < 5; i++) {
const worker = new Worker(workerPath, {
workerData: {
startRange: startPoints[i],
endRange: i === 4 ? range : startPoints[i + 1],
},
});

worker.on('message', (result) => {
console.log(`worker${i + 1}: ${result}`);
sum += result;
completed++;

if (completed === 5) {
const endTime = new Date().getTime();
console.log(`합계: ${sum}`);
console.log(`실행시간: ${endTime - startTime}ms`);
}
});

workers.push(worker);
}
}

main();
```


```sql
합계: 50000000000072040000
실행시간: 3322ms
```

**10코어**

- 코드

```jsx
const {Worker} = require('worker_threads');

const workerPath = './multicore.js';

function main() {
const range = 10000000000;
const startPoints = [1, 1000000000, 2000000000, 3000000000, 4000000000, 5000000000, 6000000000, 7000000000, 8000000000, 9000000000];
const workers = [];
let completed = 0;
let sum = 0;
const startTime = new Date().getTime();

for (let i = 0; i < 10; i++) {
const worker = new Worker(workerPath, {
workerData: {
startRange: startPoints[i],
endRange: i === 9 ? range : startPoints[i + 1],
},
});

worker.on('message', (result) => {
console.log(`worker${i + 1}: ${result}`);
sum += result;
completed++;

if (completed === 10) {
const endTime = new Date().getTime();
console.log(`합계: ${sum}`);
console.log(`실행시간: ${endTime - startTime}ms`);
}
});

workers.push(worker);
}
}

main();
```


```sql
합계: 50000000045079730000
실행시간: 2330ms
```

### 추가

- promise.try() → 반환값에 관계 없이 모든 종류의 콜백을 Promise 내부에서 감싸는 메서드
- promise.withResolvers() → 새로운 Promise 객체를 만들고, resolve와 reject 함수 반환