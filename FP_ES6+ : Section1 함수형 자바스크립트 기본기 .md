## 일급

- 값으로 다룰 수 있어 변수에 담을 수 있다.
- 함수의 인자와 결과로 사용될 수 있다.

## 일급함수

- 함수를 값으로 다룰 수 있다.
- 조합성과 추상화의 도구가 된다.

## 고차함수

함수를 값으로 다루는 함수이며, 크게 2가지로 구분된다.

1. 함수를 인자로 받아서 실행하는 함수이다.

   ex) `apply1`, `times`

   ```jsx
   const log = console.log;
   // apply1
   const apply1 = f => f(1);
   const add2 = a => a + 2;
   log(apply1(add2)); // 3

   // times
   const times = (f, n) => {
     let i = -1;
     while (++i < n) f(i);
   };
   times(log, 3); // 0, 1, 2
   ```

1. 함수를 만들어서 리턴하는 함수이다.

   클로저를 만들어 리턴하는 함수이다. `addMaker` 함수에서는 무엇을 클로저라고 하는가? 아래의 예제를 살펴보면 `addMaker` 함수는 a 를 첫 인자로 받고, 이때 만들어진 환경인 a 를 기억하며, 이 a 와 함수 자체의 객체를 반환한다. 따라서 선언된 당시 환경인 `a` 와 `(b) ⇒ a + b` 를 통칭해서 클로저라고 지칭한다.

   ex) `addMaker`

   ```jsx
   const addMaker = a => b => a + b;
   const add10 = addMaker(10);
   log(add10(5));
   ```

## 궁금한 내용

- 추상화와 분리의 개념적 차이
