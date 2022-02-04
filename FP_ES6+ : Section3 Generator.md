### Generator 함수

제네레이터 함수를 통해서 제네레이터를 만들 수 있다. 이 때 특별한 문법 구조인 `function*` 이 필요하다.

```jsx
function* generateSequence() {
  yield 1;
  yield 2;
  return 3;
}
```

제네레이터 함수를 호출하면 코드가 실행되는 것이 아닌, 실행을 처리하는 특별 객체인 제네레이터 객체가 반환된다.

```jsx
let gen = generateSequence();
console.dir(gen);
```

콘솔 창에서 해당 코드를 실행해보자.

`next()` 는 제네레이터의 주요 메서드이다. `next()` 를 호출하면 가장 가까운 `yield` 문을 만날 때까지 실행이 지속된다. 해당 `yield` 문을 만나면 실행을 멈추고, 산출하고자 하는 값인 `value` 가 반환된다.

제네레이터 객체 또한 이터레이터이기 때문에 `next()` 메서드가 반환해야하는 값은 `value` 와 `done` 프로퍼티를 가진 객체이다.

- `value`: 산출 값
- `done`: 함수 코드 실행이 끝났으면 `true`, 아니라면 `false`

### 제네레이터의 특징

1. `yield` 를 통해 몇 번의 next 를 통해 값을 꺼낼 지 정할 수 있다.
2. `return` 값을 만들 수 있다. `done = true` 일 때 value 에 return 값이 전달된다. 하지만 `for ... of` 로 순회할 때 return 값은 포함되지 않는다.
3. `yield` 를 통해 결과를 외부로 전달할 수도, 제네레이터 안으로 값을 전달할 수 도 있다.

첫 번째 특징은 위에서 살펴봤기 때문에 생략하고 2번째 특징부터 살펴보자.

```jsx
function* generateSequence() {
  yield 1;
  yield 2;
  return 3;
}

let generator = generateSequence();

for (let value of generator) {
  alert(value); // 1, 2가 출력됨
}
```

세 번째 특징에 대해서 살펴보자. `next` 메서드의 인자로 제네레이터 내부로 값을 전달할 수 있다.

```jsx
function* gen() {
  // 질문을 제너레이터 밖 코드에 던지고 답을 기다립니다.
  let result = yield '2 + 2 = ?'; // (*)

  alert(result);
}

let generator = gen();

// 1. generator 객체가 반환하는 값을 question 에 담고,
// 2. generator 내부에 4라는 값을 전달해보자.
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2d1927fb-f983-467d-90aa-84a2a14fe371/Untitled.png)

### 제네레이터 예제

특정 숫자를 입력받았을 때, 해당 숫자까지의 홀수를 출력하는 코드를 만들어보자.

```jsx
function createOdds(number) {
  let i = 0;
  while (i <= number) {
    if (i % 2) console.log(i);
    i += 1;
  }
}
```

문제는 없다. 하지만 기획에서 홀수가 아니라 짝수를 출력하는 코드로 변경해야한다고 한다면, 우리는 `if` 문 안에 조건을 변경시켜야만 한다. 함수형 프로그래밍을 통해서 좀 더 조합성이 높은 코드로 작성해보자.

이번 글에서 학습했던 `Generator` 개념을 생각해보고 코드를 읽어보자.

```jsx
function* infinity(i = 0) {
  while (true) yield i++;
}

function* limit(l, iter) {
  for (const a of iter) {
    yield a;
    if (a == l) return;
  }
}

function* odds(l) {
  for (const a of limit(l, infinity(1))) {
    if (a % 2) yield a;
  }
}

for (const a of odds(40)) console.log(a);
```

만약 짝수의 경우를 출력해야한다면 다음과 같은 코드를 추가할 수 있다.

```jsx
function* even(l) {
  for (const a of limit(l, infinity(1))) {
    if (!(a % 2)) yield a;
  }
}

for (const a of even(40)) console.log(a);
```

이렇게 작성한다면 다시 홀수로 변경하는 경우에도 기존 코드가 남아있기 때문에 손 쉽게 사용할 수 있다. 최근 면접 준비를 할 때 좋은 소프트웨어란 변화에 유연한 소프트웨어이고, 변화에 유연하기 위해서는 책임이 명확한 모듈화가 되어야한다는 것을 학습했다. 이러한 맥락에서 함수형 프로그래밍은 좋은 소프트웨어를 위해 사용될 수 있는 패러다임이라는 것을 체감할 수 있었다.
