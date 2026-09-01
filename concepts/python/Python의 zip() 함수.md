# Python `zip()`

Python의 `zip()`은 **여러 iterable의 같은 위치에 있는 값들을 하나씩 묶어서 순회할 수 있게 해주는 내장 함수**이다.

## 기본 사용법

```python
values = [1000, 900, 500]
symbols = ["M", "CM", "D"]

for value, symbol in zip(values, symbols):
    print(value, symbol)
```

출력:

```text
1000 M
900 CM
500 D
```

개념적으로는 다음과 같이 같은 인덱스의 원소들이 묶인다.

```text
values:   1000   900   500
symbols:  "M"   "CM"  "D"

            ↓      ↓     ↓

         (1000, "M")
         (900, "CM")
         (500, "D")
```

따라서

```python
for value, symbol in zip(values, symbols):
```

는 반복할 때마다 각각의 튜플을 unpacking하여 `value`, `symbol`에 할당하는 것과 같다.

---

## 인덱스를 사용하는 방식과 비교

`zip()`을 사용하지 않으면 다음과 같이 작성할 수 있다.

```python
for i in range(len(values)):
    value = values[i]
    symbol = symbols[i]
```

`zip()`을 사용하면:

```python
for value, symbol in zip(values, symbols):
```

처럼 작성할 수 있다.

인덱스 `i` 자체가 필요하지 않고, **서로 대응되는 두 값을 함께 사용하려는 목적이라면 `zip()`이 코드의 의도를 더 명확하게 표현한다.**

---

## `zip()`이 반환하는 값

Python 3의 `zip()`은 결과 리스트를 즉시 생성하지 않는다.

```python
a = [1, 2, 3]
b = ["a", "b", "c"]

z = zip(a, b)

print(z)
```

출력은 대략 다음과 같다.

```text
<zip object at ...>
```

즉, `zip()`은 **iterator**를 반환하며 필요한 값을 하나씩 생성한다.

결과 전체를 확인하려면 `list()`로 변환할 수 있다.

```python
print(list(zip(a, b)))
```

```python
[(1, "a"), (2, "b"), (3, "c")]
```

---

## iterable의 길이가 다르면?

기본 `zip()`은 **가장 짧은 iterable이 끝나는 순간 종료된다.**

```python
a = [1, 2, 3]
b = ["a", "b"]

print(list(zip(a, b)))
```

결과:

```python
[(1, "a"), (2, "b")]
```

`a`의 마지막 값인 `3`은 사용되지 않는다.

```text
a:  1   2   3
    ↓   ↓
b:  a   b

   (1, "a")
   (2, "b")
```

따라서 서로 길이가 반드시 같아야 하는 데이터라면 주의해야 한다.

---

## `strict=True`

Python 3.10 이상에서는 `strict=True` 옵션을 사용할 수 있다.

```python
a = [1, 2, 3]
b = ["a", "b"]

list(zip(a, b, strict=True))
```

두 iterable의 길이가 다르므로 `ValueError`가 발생한다.

```text
ValueError
```

따라서 **두 iterable의 길이가 반드시 동일해야 한다는 것을 보장하고 싶다면 `strict=True`를 사용할 수 있다.**

```python
for value, symbol in zip(values, symbols, strict=True):
    ...
```

---

## 3개 이상의 iterable도 가능

`zip()`은 두 개뿐만 아니라 여러 iterable을 동시에 묶을 수도 있다.

```python
names = ["Alice", "Bob"]
ages = [25, 30]
cities = ["Seoul", "Busan"]

for name, age, city in zip(names, ages, cities):
    print(name, age, city)
```

출력:

```text
Alice 25 Seoul
Bob 30 Busan
```

개념적으로:

```text
("Alice", 25, "Seoul")
("Bob", 30, "Busan")
```

형태로 묶인다.

---

## 활용 예시: Integer to Roman

- [12. Integer to Roman](../../problems/Hash_Table/12-Integer_to_Roman.md)

```python
values = [
    1000, 900, 500, 400,
    100, 90, 50, 40,
    10, 9, 5, 4, 1
]

symbols = [
    "M", "CM", "D", "CD",
    "C", "XC", "L", "XL",
    "X", "IX", "V", "IV", "I"
]

for value, symbol in zip(values, symbols):
    while num >= value:
        roman += symbol
        num -= value
```

`values`와 `symbols`는 서로 같은 위치의 값이 한 쌍을 이룬다.

```text
1000 ↔ "M"
900  ↔ "CM"
500  ↔ "D"
400  ↔ "CD"
...
```

따라서 인덱스를 사용할 필요 없이 `zip()`으로 두 값을 직접 함께 순회하는 것이 자연스럽다.

---

## JavaScript / TypeScript와 비교

JavaScript와 TypeScript에는 Python의 `zip()`에 직접 대응하는 기본 함수가 없다.

Python:

```python
for value, symbol in zip(values, symbols):
    ...
```

TypeScript에서는 일반적으로 인덱스를 사용한다.

```typescript
for (let i = 0; i < values.length; i++) {
  const value = values[i];
  const symbol = symbols[i];
}
```

따라서 여러 배열의 같은 위치에 있는 값을 함께 순회하는 경우 Python의 `zip()`이 특히 편리하다.

---

## 정리

`zip()`은 다음과 같은 경우 유용하다.

- 두 개 이상의 iterable을 동시에 순회할 때
- 같은 인덱스에 있는 값들이 서로 대응 관계를 가질 때
- 반복문에서 인덱스 자체는 필요하지 않을 때
- 여러 배열에서 대응되는 데이터를 깔끔하게 unpacking하고 싶을 때

핵심은 다음과 같이 기억하면 된다.

> `zip()` = 여러 iterable의 같은 위치에 있는 값들을 묶어서 순회하는 함수
