# 18. 4Sum

https://leetcode.com/problems/string-to-integer-atoi/description/

## 문제

Implement the myAtoi(string s) function, which converts a string to a 32-bit signed integer.

The algorithm for myAtoi(string s) is as follows:

- Whitespace: Ignore any leading whitespace (" ").
- Signedness: Determine the sign by checking if the next character is '-' or '+', assuming positivity if neither present.
- Conversion: Read the integer by skipping leading zeros until a non-digit character is encountered or the end of the string is reached. If no digits were read, then the result is 0.
- Rounding: If the integer is out of the 32-bit signed integer range [-231, 231 - 1], then round the integer to remain in the range. Specifically, integers less than -231 should be rounded to -231, and integers greater than 231 - 1 should be rounded to 231 - 1.

Return the integer as the final result.

## 내 풀이(Py)

```py
class Solution:
    def myAtoi(self, s: str) -> int:
        output = []
        pointer = 0
        maximum = pow(2, 31) - 1
        minimum = -pow(2, 31)
        whitespace_check = False
        signedness_check = False
        positive = True
        leading_zero_check = False

        while pointer < len(s):
            if not whitespace_check:
                if s[pointer] == " ":
                    pointer += 1
                    continue
                else:
                    whitespace_check = True

            if not signedness_check:
                signedness_check = True
                if s[pointer] == '-':
                    positive = False
                    pointer += 1
                    continue
                elif s[pointer] == '+':
                    pointer += 1
                    continue

            if not leading_zero_check:
                if s[pointer] == '0':
                    pointer += 1
                    continue
                else:
                    leading_zero_check = True

            if s[pointer].isdigit():
                output.append(s[pointer])
            else:
                break

            pointer += 1

        if len(output) == 0:
            return 0

        if len(output) > 10:
            if positive:
                return maximum
            else:
                return minimum

        integer = 0
        index = 0
        while len(output) > 0:
            integer += int(output.pop()) * pow(10, index)
            index += 1

        if not positive:
            integer = -1 * integer

        if integer > maximum:
            return maximum

        if integer < minimum:
            return minimum

        return integer
```

- 시간복잡도: O(n)
- 공간복잡도: O(n) output = []

### 개선점

1. 상태 변수가 너무 많음
   whitespace_check = False
   signedness_check = False
   positive = True
   leading_zero_check = False ...
   -> 상태를 boolean으로 기억할 필요 없이 그냥 각 단계를 순서대로 처리

2. output = []을 만들었다가 다시 숫자로 변환
   -> 숫자를 읽는 즉시 정수로 전환할 수 있음

3. overflow도 누적 중 검사 가능

## 추천 풀이 코드

```py
class Solution:
    def myAtoi(self, s: str) -> int:
        INT_MAX = 2**31 - 1
        INT_MIN = -2**31

        i = 0
        n = len(s)

        # 1. leading whitespace
        while i < n and s[i] == ' ':
            i += 1

        # 2. sign
        sign = 1

        if i < n and s[i] in '+-':
            if s[i] == '-':
                sign = -1
            i += 1

        # 3. digits
        num = 0
        limit = INT_MAX if sign == 1 else -INT_MIN
        # 양수: 2147483647, 음수: 2147483648

        while i < n and '0' <= s[i] <= '9':
            digit = ord(s[i]) - ord('0')
        # ASCII 코드 비교로 인해 숫자인이 아닌지 바로 확인 및 변환 가능

            # num * 10 + digit > limit 인지 미리 검사
            if num > (limit - digit) // 10:
                return INT_MAX if sign == 1 else INT_MIN

            num = num * 10 + digit
            # 기존 num에 10을 곱하면 자릿수가 하나씩 올라감
            i += 1

        return sign * num
```

## TypeScript 코드

```ts
function myAtoi(s: string): number {
  const INT_MAX = 2 ** 31 - 1;
  const INT_MIN = -(2 ** 31);

  let i = 0;
  let sign = 1;
  let num = 0;

  // 1. Skip leading whitespace
  while (i < s.length && s[i] === " ") {
    i++;
  }

  // 2. Check sign
  if (i < s.length && (s[i] === "+" || s[i] === "-")) {
    sign = s[i] === "-" ? -1 : 1;
    i++;
  }

  // 3. Parse digits
  while (i < s.length && s[i] >= "0" && s[i] <= "9") {
    const digit = s.charCodeAt(i) - 48;

    num = num * 10 + digit;

    if (sign === 1 && num > INT_MAX) {
      return INT_MAX;
    }

    if (sign === -1 && num > -INT_MIN) {
      return INT_MIN;
    }

    i++;
  }

  return sign * num;
}
```
