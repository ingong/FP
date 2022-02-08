## go

함수의 순서를 뒤집어서 논리적인 순서와 일치시킨다.

```jsx
const go = (...args) => reduce((a, f) => f(a), args);
```

**111 을 출력하는 예제**

```jsx
go(
  0,
  a => a + 1,
  a => a + 10,
  a => a + 100,
  log
);
```

## pipe

함수를 리턴하는 함수이다.

```jsx
const pipe =
  (f, ...fs) =>
  (...as) =>
    go(f(...as), ...fs);
```

## curry

간결한 표현을 만든다. 함수를 부분적으로 실행하며, 원하는 인자를 모두 받기 전까지는 함수를 return 한다.

```jsx
const curry =
  f =>
  (a, ..._) =>
    _.length ? f(a, ..._) : (..._) => f(a, ..._);
```

## map, filter, reduce + curry

```jsx
# map
const map = curry((f, iter) => {
  const res = [];
  for (const a of iter) res.push(f(a));
  return res;
});

# filter
const filter = curry((f, iter) => {
  const res = [];
  for (const a of iter) {
    if (f(a)) res.push(a);
  }
  return res;
});

# reduce
const reduce = curry((f, acc, iter) => {
  if (!iter) {
    iter = acc[Symbol.iterator]();
    acc = iter.next().value;
  }
  for (const a of iter) {
    acc = f(acc, a);
  }
  return acc;
});

```

## 예제

```jsx
const products = [
  { name: '반팔티', price: 15000 },
  { name: '긴팔티', price: 20000 },
  { name: '핸드폰케이스', price: 15000 },
  { name: '후드티', price: 30000 },
  { name: '바지', price: 25000 },
];

// 20000 원 미만인 가격의 총합을 산출하기
1. go 활용
go(
  products,
  (products) => filter((p) => p.price < 20000)(products),
  (products) => map((p) => p.price)(products),
  (prices) => reduce(add)(prices),
  log,
);

2. go, curry 활용
go(
  products,
  filter((p) => p.price < 20000),
  map((p) => p.price),
  reduce(add),
);

3. go, curry, pipe 활용
const total_price = pipe(
  map((p) => p.price),
  reduce(add),
);

const base_total_price = (predi) =>
	pipe(filter(predi), total_price);

go(
  products,
  base_total_price((p) => p.price < 20000),
  log,
);
```
