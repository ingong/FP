## 개요

보조 함수와 이터러블 프로토콜을 따르는 객체를 인자로 받아서 다형성을 확보한다.

## map

```jsx
const map = (f, iter) => {
  const res = [];
  for (const a of iter) res.push(f(a));
  return res;
};
```

## filter

```jsx
const filter = (f, iter) => {
  const res = [];
  for (const a of iter) {
    if (f(a)) res.push(a);
  }
  return res;
};
```

## reduce

인자를 2개 받는 경우, acc 에 첫 번째 인자 값을 저장하고 iter 에 iterator 를 대입한다. 이를 통해 초기 값을 받지 않아도 reduce 함수가 정상적으로 동작한다. JS 의 reduce 함수도 동일하게 동작한다.

```jsx
const reduce = (f, acc, iter) => {
  if (!iter) {
    iter = acc[Symbol.iterator]();
    acc = iter.next().value;
  }
  for (const a of iter) {
    acc = f(acc, a);
  }
  return acc;
};
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

// 20000 원 미만인 가격의 총합을 산출하시오
log(
  reduce(
    add,
    map(
      p => p.price,
      filter(p => p.price < 20000, products)
    )
  )
);
```
