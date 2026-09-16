# Python `nonlocal`

`nonlocal`은 **중첩 함수에서 바깥 함수의 지역 변수를 수정할 때 사용하는 키워드**다.

## 기본 예시

```python
def outer():
    count = 0

    def inner():
        nonlocal count
        count += 1

    inner()
    print(count)  # 1
```

`inner()` 안에서 `count`를 새 지역 변수로 만들지 않고,
바깥 함수 `outer()`의 `count`를 사용한다.

---

## 왜 필요한가?

바깥 변수는 읽기만 할 때는 `nonlocal`이 필요 없다.

```python
def outer():
    count = 10

    def inner():
        print(count)

    inner()
```

하지만 값을 변경하려고 하면 문제가 생긴다.

```python
def outer():
    count = 10

    def inner():
        count += 1  # Error
```

Python은 함수 안에서 대입이 발생하면 `count`를 기본적으로 **현재 함수의 지역 변수**로 판단한다.

```python
count += 1
```

은 사실상:

```python
count = count + 1
```

이므로, 아직 만들어지지 않은 지역 변수 `count`를 읽으려고 해서 에러가 발생한다.

이때:

```python
nonlocal count
```

를 사용하면 바깥 함수의 `count`를 수정할 수 있다.

---

## `global`과의 차이

```python
x = 100

def outer():
    y = 10

    def inner():
        global x
        nonlocal y
```

- `global x`
  - 모듈 최상위의 전역 변수 `x`를 사용

- `nonlocal y`
  - 가장 가까운 바깥 함수의 지역 변수 `y`를 사용

```text
global   → 전역 변수
nonlocal → 바깥 함수의 지역 변수
```

---

## Mutable 객체는 조금 다르다

리스트처럼 내부 값을 수정하는 경우에는 `nonlocal`이 없어도 된다.

```python
def outer():
    nums = []

    def inner():
        nums.append(1)

    inner()
    print(nums)  # [1]
```

`nums` 자체에 새로운 값을 대입한 것이 아니라,
`nums`가 가리키는 리스트 내부를 수정했기 때문이다.

하지만 다음처럼 변수 자체를 다시 대입하면:

```python
def outer():
    nums = []

    def inner():
        nums = [1]
```

`inner()`의 새로운 지역 변수 `nums`가 만들어진다.

바깥 `nums`를 변경하려면:

```python
def outer():
    nums = []

    def inner():
        nonlocal nums
        nums = [1]
```

처럼 작성해야 한다.

---

## LeetCode에서 자주 보는 형태

```python
def solve():
    index = 0

    def dfs():
        nonlocal index

        value = arr[index]
        index += 1
```

여러 재귀 호출이 하나의 `index`를 공유하면서 입력을 순서대로 처리할 때 자주 사용된다.

예를 들어 preorder 순회 배열을 앞에서부터 하나씩 소비하는 트리 복원 문제에서 사용할 수 있다.

---

## 정리

```text
바깥 변수 읽기
→ nonlocal 필요 없음

바깥 변수에 새로운 값 대입
→ nonlocal 필요

바깥 mutable 객체 내부 수정
→ 보통 nonlocal 필요 없음
```

`nonlocal`은 **"이 변수는 현재 함수의 지역 변수가 아니라, 바깥 함수에 있는 변수다"**라고 Python에 알려주는 키워드라고 이해하면 된다.
