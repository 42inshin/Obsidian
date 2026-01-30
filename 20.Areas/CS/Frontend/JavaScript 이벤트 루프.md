## ✅ 자바스크립트는 단일 스레드 기반

- JavaScript는 하나의 **Main Thread(메인 스레드)** 위에서 실행됨
- 코드 실행, UI 업데이트, 이벤트 처리 모두 이 스레드에서 수행됨

---

## 🔄 이벤트 루프 구조

1. **Call Stack**: 동기 코드 실행
2. **Microtask Queue**
   - `Promise.then`, `await`, `queueMicrotask()` 등
   - Call Stack이 비면 **즉시 처리**
3. **Macrotask Queue (Task Queue)**
   - `setTimeout`, `setInterval`, `setImmediate` 등
   - **Microtask가 모두 처리된 후** 처리됨

---

## 📌 중요한 개념 정리

| 구분        | Microtask                                   | Macrotask (Task Queue)                             |
| --------- | ------------------------------------------- | -------------------------------------------------- |
| **예시**    | `Promise.then`, `await`, `queueMicrotask()` | `setTimeout`, `setInterval`, `I/O`, `UI Rendering` |
| **처리 방식** | **큐가 빌 때까지** 전부 쏟아부어 실행                     | **한 번에 하나씩**만 실행 후 루프 재점검                          |
| **우선순위**  | 🥇 1순위                                      | 🥉 2순위 (Microtask 없을 때만)                           |

---

## 🔍 예시 코드 및 출력 순서

### 예제 1: Microtask vs Macrotask

```js
console.log("start");

setTimeout(() => {
  console.log("timeout");
}, 0);

Promise.resolve().then(() => {
  console.log("promise");
});

(async () => {
  await Promise.resolve();
  console.log("async done");
})();

console.log("end");
```

### 출력 순서:
```
start
end
promise
async done
timeout
```

### 이유:
1. 동기 코드 → `start`, `end`
2. `Promise.then` → Microtask Queue에 등록
3. `await`도 내부적으로 Microtask 처리됨
4. 마지막으로 Macrotask인 `setTimeout` 실행

---

## 🧠 queueMicrotask()란?

- Microtask Queue에 직접 작업을 등록하는 함수
- `Promise.then()`처럼 빠른 비동기 작업이 필요할 때 사용
- 렌더링 전에 작업을 마치고 싶을 때 유용

### 예제 2: queueMicrotask vs setTimeout

```js
console.log("start");

// 1. Macrotask를 먼저 등록
setTimeout(() => {
  console.log("macrotask (setTimeout)");
}, 0);

// 2. Microtask를 나중에 등록
queueMicrotask(() => {
  console.log("microtask (queueMicrotask)");
});

console.log("end");
```

### 출력 순서:
```
start
end
microtask (queueMicrotask)
macrotask (setTimeout)
```

---

## 📌 정리

- `Task Queue`는 보통 **Macrotask Queue**를 의미함
- Microtask는 항상 Macrotask보다 먼저 처리됨
- Microtask를 명시적으로 등록하고 싶을 때 `queueMicrotask()` 사용

---

## 📚 관련 용어

| 용어 | 의미 |
|------|------|
| Call Stack | 현재 실행 중인 함수 목록 |
| Event Loop | Call Stack이 비면 Microtask → Macrotask 순으로 처리 |
| Microtask Queue | 빠르게 처리할 Promise 기반 작업들 |
| Macrotask Queue | Timer나 I/O와 관련된 작업들 |

