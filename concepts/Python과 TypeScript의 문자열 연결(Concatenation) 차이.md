## Python과 TypeScript의 문자열 연결(Concatenation) 차이

문자열을 반복적으로 이어 붙일 때 Python과 TypeScript(JavaScript)는 권장되는 방식이 조금 다르다.

### Python

Python의 문자열(`str`)은 **immutable**이므로 문자열을 수정할 수 없다.

```python
result = ""

for char in s:
    result += char
```

`result += char`가 실행될 때 기존 문자열을 수정하는 것이 아니라 새로운 문자열을 생성한다.

따라서 문자열이 계속 길어지는 상황에서는 반복적인 복사가 발생할 수 있으며, 일반적으로는 다음과 같이 **리스트에 문자를 저장한 뒤 마지막에 `join()`하는 방식**이 권장된다.

```python
result = []

for char in s:
    result.append(char)

return ''.join(result)
```

`list.append()`는 amortized `O(1)`이고, 마지막 `join()`은 전체 문자열 길이가 `n`일 때 `O(n)`이다.

따라서 반복적인 문자열 생성에서는 다음 패턴을 사용하는 것이 안전하다.

```python
# 권장
chars = []
chars.append(char)
result = ''.join(chars)
```

---

### TypeScript / JavaScript

JavaScript의 문자열 역시 **immutable**이다.

따라서 겉으로 보면 다음 코드 역시 매번 새로운 문자열을 만들기 때문에 비효율적으로 보인다.

```typescript
let result = "";

for (const char of s) {
  result += char;
}
```

하지만 V8과 같은 현대 JavaScript 엔진은 문자열 concatenation을 적극적으로 최적화한다.

`a + b`를 수행할 때 항상 즉시 두 문자열 전체를 복사해서 새로운 연속된 문자열을 만드는 것이 아니라, 내부적으로 기존 문자열들을 참조하는 **rope / cons string과 유사한 구조**를 사용할 수 있다.

개념적으로는 다음과 같다.

```text
"hello" + "!"

       concat
       /    \
 "hello"    "!"
```

필요한 시점에 문자열을 실제 연속된 형태로 만드는(flatten) 방식으로 처리할 수 있기 때문에:

```typescript
result += char;
```

패턴이 예상보다 매우 효율적으로 동작하는 경우가 많다.

따라서 JavaScript/TypeScript에서는 단순한 문자열 누적이라면 다음 코드도 일반적으로 충분히 좋은 방식이다.

```typescript
let result = "";

for (const char of s) {
  result += char;
}
```

오히려 다음처럼 배열을 사용하는 경우:

```typescript
const result: string[] = [];

for (const char of s) {
  result.push(char);
}

return result.join("");
```

배열의 element 관리, backing storage, `push()`, 마지막 `join()` 등의 추가 작업 때문에 실제 실행 속도가 더 느릴 수도 있다.

---

### promlems - String - 15. Zigzag Conversion에서의 예

Row Simulation을 구현할 때 Python에서는 각 row를 문자 배열로 관리하는 것이 좋다.

```python
rows = [[] for _ in range(numRows)]

for char in s:
    rows[row].append(char)

return ''.join(''.join(row) for row in rows)
```

반면 TypeScript에서는 각 row를 문자열로 관리하는 구현도 충분히 효율적이며, 실제로 더 빠르게 동작할 수도 있다. (실제로 leetcode runtime 상에서 아주 미세하지만 더 빠르게 동작함)

```typescript
const rows: string[] = Array(numRows).fill("");

for (const char of s) {
  rows[row] += char;
}

return rows.join("");
```

### 정리

| Language                | 일반적으로 권장되는 문자열 누적 방식               |
| ----------------------- | -------------------------------------------------- |
| Python                  | `list.append()` → `''.join()`                      |
| TypeScript / JavaScript | `+=`도 충분히 효율적이며 일반적으로 사용 가능      |
| TypeScript / JavaScript | `Array.push()` → `join()`이 항상 더 빠른 것은 아님 |

중요한 점은 **문자열이 immutable이라는 언어 수준의 특성만 보고 실제 성능을 판단하면 안 된다는 것**이다.

동일한 `string += char` 코드라도 런타임과 엔진의 최적화 방식에 따라 실제 비용이 달라질 수 있다.

특히 JavaScript에서는 V8 등의 엔진이 문자열 concatenation을 적극적으로 최적화하기 때문에, Python에서 사용하는 성능 최적화 패턴을 그대로 적용할 필요는 없다.

> **핵심:**
> Python에서는 반복적인 문자열 연결 시 `list + join`을 우선 고려하고,
> TypeScript/JavaScript에서는 단순한 문자열 누적이라면 `+=`도 충분히 좋은 선택이다.
