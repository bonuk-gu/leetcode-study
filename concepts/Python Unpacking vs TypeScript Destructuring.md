# Python Unpacking vs TypeScript Destructuring

Python과 TypeScript에서는 여러 값을 한 번에 변수에 할당할 수 있다.

겉보기에는 비슷하지만 사용하는 개념은 조금 다르다.

---

## 1. Python - Unpacking

```python
a, b = [1, 2]
```

Iterable 내부의 값을 여러 target에 순서대로 나누어 할당한다.

```text
[1, 2]
 ↓  ↓
 a  b
```

리스트뿐 아니라 다양한 iterable에서 사용할 수 있다.

```python
a, b = (1, 2)
a, b = "AB"
a, b = range(2)
```

따라서 단순히 `Tuple Unpacking`보다는 **Iterable Unpacking**이라고 이해하는 것이 더 정확하다.

### Swap

```python
a, b = b, a
```

### 나머지 값 받기

```python
first, *rest = [1, 2, 3, 4]
```

결과:

```python
first  # 1
rest   # [2, 3, 4]
```

Python은 기본적으로 target 개수와 값 개수가 맞지 않으면 에러가 발생한다.

```python
a, b = [1, 2, 3]
# ValueError
```

---

## 2. TypeScript - Destructuring Assignment

```ts
const [a, b] = [1, 2];
```

Array나 Object의 구조를 **패턴에 맞춰 분해해서 값을 추출**한다.

### Array Destructuring

```ts
const [first, second] = [10, 20];
```

### Swap

```ts
[a, b] = [b, a];
```

### 특정 값 건너뛰기

```ts
const [first, , third] = [10, 20, 30];
```

### 나머지 값 받기

```ts
const [first, ...rest] = [1, 2, 3, 4];
```

### Object Destructuring

```ts
const user = {
  name: "Kim",
  age: 30,
};

const { name, age } = user;
```

객체의 property를 직접 분해할 수 있다는 것이 Python unpacking과의 큰 차이점이다.

---

## 3. 주요 차이

| 특징                 | Python Unpacking              | TypeScript Destructuring |
| -------------------- | ----------------------------- | ------------------------ |
| 기본 개념            | Iterable의 값을 target에 분배 | 구조를 pattern으로 분해  |
| List / Array         | 가능                          | 가능                     |
| String               | 가능                          | 가능                     |
| Object property 추출 | 직접 대응 문법 없음           | 가능                     |
| 값이 너무 많음       | Error                         | 남는 값 무시             |
| 값이 부족함          | Error                         | `undefined`              |
| 나머지 값            | `*rest`                       | `...rest`                |
| 특정 값 무시         | `_` 관례                      | 빈 자리 사용             |
| Swap                 | `a, b = b, a`                 | `[a, b] = [b, a]`        |

---

## 핵심 정리

Python:

```text
Iterable
→ 내부 값을 꺼내 여러 target에 분배
```

TypeScript:

```text
Array / Object
→ 원하는 구조의 pattern으로 분해
```

그래서 둘 다 swap에 사용할 수 있지만, Python은 **Iterable Unpacking**, TypeScript는 **Destructuring Assignment**라는 서로 다른 언어 기능을 사용하는 것이다.
