## ES2015(ES6) 에서의 순회

### ES2015 전

순회를 통해 값을 얻기 위해서는 해당 index 에 접근한 후에, 해당 인덱스에 매핑되는 값에 접근해야했다. 어떻게 접근하는지 위주로 기술되었기 때문에 명령적이다. 값에 접근하기 위해서는 `length` 라는 프로퍼티에 의존해야했고, `index` 로 접근해야했기 때문이다. 다음 예제에서 `list` 와 `str` 의 각 인자에 접근하기 위해서는 index 에 우선적으로 접근한 것을 확인할 수 있다.

```jsx
const list = [1, 2, 3];
for (var i = 0; i < list.length; i++) {
  log(list[i]);
}

const str = 'abc';
for (var i = 0; i < str.length; i++) {
  log(str[i]);
}
```

### ES2015 후

ES2015 에서는 선언적으로 해당 값에 접근할 수 있는 `for ... of` 문법이 등장했다. 이는 간결하게 만드는 것 이상의 역할을 했다. 따라서 어떠한 규약이 등장했으며, 순회에 대한 추상화를 어떻게 이뤄냈는지 알아보는 것이 중요하다. 이전에 `ES2015` 에 등장한 `Set` 과 `Map` 이라는 객체를 `for ... of` 을 통해 순회해보자.

```jsx
const log = console.log;

const set = new Set([1, 2, 3]);
for (const a of set) log(a);
// 1, 2, 3

const map = new Map([
  ['a', 1],
  ['b', 2],
  ['c', 3],
]);
for (const a of map) log(a);
// ['a', 1], ['b', 2], ['c', 3]
```

해당 객체들은 어떻게 순회가 구현되었을까? 만약 `for ... in` 문의 확장이라면 index 를 통해서 접근하는 것이 가능할 것이다.

`set[0]` 으로 set 이라는 변수에 접근한다면 `undefined` 가 나온다. index 로 접근하는 건 아니라는 사실을 알 수 있다. 정답을 말하자면 이터러클/이터레이터 프로토콜이 순회를 가능하게 했으며, 이에 대해서 알아보는 것이 중요하다.

## 이터러블/이터레이터 프로토콜

### 이터러블

이터레이터를 리턴하는 `[Symbol.iterator]` 메서드를 가진 값이다.

Symbol 에 대한 설명은 이번 글에서 다룰 범위를 넘어가기 때문에 따로 학습하기를 추천한다. Symbol 에 대해서 간략하게 설명하면 ES2015 에서 추가된 JS 의 7번째 자료형으로, 변하지 않는 원시 자료형이다.

### 이터레이터

`{ value, done }` 객체를 리턴하는 next 메서드를 가진 값이다.

### 이터러블/이터레이터 프로토콜

**이터러블**을 `for ... of`, `전개 연산자` 등과 함께 동작하도록한 규약이다. 예를 들면 `for ... of` 을 순회할 때 이터레이터의 done 의 값이 false 일 때는 해당 value 를 반환하고, done 이 true 인 경우에는 해당 for ... of 문을 빠져나오게 하는 것도 해당 프로토콜로 인해 가능한 것이다.

이터러블과 이터레이터의 정의 그리고 이터러블/이터레이터 프로토콜에 기반해서 이터러블을 구현해보자. `3, 2, 1` 순으로 숫자를 출력하는 것을 목표로 작성해보자.

```jsx
const iterable = {
  [Symbol.iterator]() {
    let i = 3;
    return {
      next() {
        return i === 0 ? { done: true } : { value: i--, done: false };
      },
    };
  },
};

for (const a of iterable) console.log(a);
// 3, 2, 1
```

JS 의 배열을 순회할 수 있는 것도 JS 의 배열이 이터러블/이터레이터 프로토콜을 순회할 수 있기 때문에 가능한 것이다.

### JS 의 배열

```jsx
const arr = [1, 2, 3];
console.log(arr[Symbol.iterator]);
// ƒ values() { [native code] }
for (const a of arr) console.log(a);
// 1, 2, 3
```

위 예시처럼 `arr[Symbol.iterator]` 에 접근해보면 함수가 출력되고, 순회하면 결과값이 출력된다. `arr[Symbol.iterator]` 에 `null` 을 대입하면 어떻게 될까?

```jsx
const arr = [1, 2, 3];
arr[Symbol.iterator] = null;
for (const a of arr) console.log(a);
```

다음과 같이 순회할 수 없다는 에러가 출력된다. `Set` 과 `Map` 객체도 동일하게 동작하는 것을 확인할 수 있었다.

## Well-formed iterator

잘 구현된 **`iterable`** 은 **`iterator`** 를 만들었을 때, **`iterator`** 를 for ... of 문에 넣었을 때도 모든 값을 순회하도록 되어있으며, **`iterator`** 를 진행하다가도 순회를 할 수 있다. 첫 번째 예시부터 살펴보자.

```jsx
const arr = [1, 2, 3];
let iter = arr[Symbol.iterator]();
for (const a of iter) console.log(a);
// 1, 2, 3
```

두 번째 예시를 살펴보자.

```jsx
const arr = [1, 2, 3];
let iter = arr[Symbol.iterator]();
iter.next();
for (const a of iter) console.log(a);
// 2, 3
```

이전에 우리가 구현했던 iterable 객체도 동일하게 동작하는지 살펴보자.

```jsx
const iterable = {
  [Symbol.iterator]() {
    let i = 3;
    return {
      next() {
        return i === 0 ? { done: true } : { value: i--, done: false };
      },
    };
  },
};
const iterator = iterable[Symbol.iterator]();

for (const a of iterator) console.log(a);
```

안타깝게도 우리가 구현한 **`iterable`** 이 반환한 **`iterator`** 는 **`iterable`** 하지 않다고 출력된다. 무엇이 문제일까? iterator 가 순회되기 위해서는 **`Symbol.iterator`** 메서드를 가져야하기 때문이다. **`iterator`** 가 **`Symbol.iterator`** 메서드를 갖도록 추가해주고, this 로 본인을 반환하도록 작성해주자. 즉, 본인도 next 메서드를 갖는 **`iterator`** 이며 **`Symbol.iterator`** 메서드를 갖는 **`iterable`** 이 되게한다는 의미이다.

```jsx
const iterable = {
  [Symbol.iterator]() {
    let i = 3;
    return {
      next() {
        return i === 0 ? { done: true } : { value: i--, done: false };
      },
      [Symbol.iterator]() {
        return this;
      },
    };
  },
};
const iterator = iterable[Symbol.iterator]();

for (const a of iterator) console.log(a);
```

이렇게 iterable 의 **`Symbol.iterator`** 메서드가 반환하는 **`iterator`** 가 자기 자신을 반환하는 **`Symbol.iterator`** 메서드를 가지고 있을 때 이를 **`well-formed iterable`** 이라고 부른다.

**`well-formed iterable`** 을 정리하면 다음과 같다.

- **`iterable`** 과 **`iterable`** 이 반환하는 **`iterator`** 모두 **`for ... of`** 로 순회할 수 있다.
- **`iterator`** 을 일정 부분 순회한 후에 다시 순회를 해도 순회가 가능하다.

두 번째 특징은 함수 내부의 실행 흐름을 제어할 수 있다는 generator 의 핵심원리와 연관이 있을 것같다는 생각이 든다. 이전까지 확실하게 Generator 를 이해했다는 느낌을 받지 못했었는데, 이러한 모호한 이해를 확실하게 이해할 수 있게 된 것 같다.

## 이터러블/이터레이터 프로토콜은 어디까지

지금까지는 **`Array, Set, Map`** 에 대해서만 살펴봤지만 사실 이터러블/이터레이터 프로토콜은 우리가 편하게 사용하는 대부분의 오픈소스라이브러리나 DOM APIs 에도 구현이 되어있다. 한 가지 예시를 알아보자.

```jsx
const all = documnet.querySelectorAll('*');
let iterator = all[Symbol.iterator]();
cnsole.log(all);
// NodeList(9)
// [html, head, meta, meta, meta, title, script, body, div#root]

for (const a of iterator) console.log(a);
// 순회가 가능하다!
```

우리가 JavaScript 로 DOM 에 접근할 때 사용했던 **`document.querySelectorAll`** 의 반환값도`NodeList` 다. 즉, 배열을 순회한다는 표현보다는 앞으로도 이터러블/이터레이터 프로토콜을 만족하도록 내부적으로 구현되어 있다는 표현이 더 맞는 표현인 것이다. **`immutbale js`** 에서도 객체를 **`for ... of`** 문으로 순회할 수 있도록 **`Symbol.iterator`** 메서드가 구현되어 있다. 지금까지 당연스럽게 순회했던 객체들이 어떻게 내부적으로 순회가능을 구현했는지 알게 된 학습이였다.

## 기타

- **`Array`, `Set`, `Map`**

이터러블/이터레이터 프로토콜을 따르는 내장 객체이다.

- **`Symbol`**

ES2015 에서 추가된 7번째 자료형으로, 변경되지 않는 원시값이다. 이는 유일하다는 특성을 갖고 있기 때문에 객체의 key 로 사용될 수 있다.
