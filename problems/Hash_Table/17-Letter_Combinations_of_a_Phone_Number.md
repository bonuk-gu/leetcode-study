# 17. Letter Combinations of a Phone Number

## 문제

https://leetcode.com/problems/letter-combinations-of-a-phone-number/description/

Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the number could represent. Return the answer in any order.

A mapping of digits to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

## 내 풀이(Py)

```py
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        output = []
        num_letters = [[],[],['a','b','c'],['d','e','f'],['g','h','i'],['j','k','l'],['m','n','o'],['p','q','r','s'], ['t','u','v'],['w','x','y','z']]

        def recursive_letter_comb(letter: str, i: int):
            num = ord(digits[i]) - ord('0')
            for new_letter in num_letters[num]:
                if i + 1 < len(digits):
                    recursive_letter_comb(letter + new_letter, i + 1)
                else:
                    output.append(letter + new_letter)

        recursive_letter_comb("", 0)

        return output
```

- 시간복잡도: O(n x 4^n)
- 공간복잡도: O(n x 4^n) (결과 포함)

### 개선점

1. 재귀함수의 형태

```py
def recursive_letter_comb(letter: str, i: int):
    ...
    if i + 1 < len(digits):
        recursive_letter_comb(...)
    else:
        output.append(...)
```

현재 위치가 마지막인지 이런 식으로 매번 확인하고 있음

```py
if i == len(digits):
    output.append(...)
    return
```

"i == len(digits)가 되었으면 하나의 조합이 완성되었다"

> 재귀함수의 역할이 훨씬 명확해짐

2. 기존 문자열에 계속해서 문자를 더하고 있음
3. num_letters를 굳이 2차원 배열로 만들 필요 X
4. num = ord(digits[i]) - ord('0') -> num = int(digits[i])

## 추천 풀이: 문자열 대신 path 리스트를 재사용하는 방식

```py
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        letters = [
            "", "",
            "abc", "def", "ghi",
            "jkl", "mno", "pqrs",
            "tuv", "wxyz"
        ]

        output = []
        path = []

        def backtrack(i: int):
            if i == len(digits):
                output.append("".join(path))
                return

            for letter in letters[int(digits[i])]:
                path.append(letter)
                backtrack(i + 1)
                path.pop()

        backtrack(0)

        return output
```

- path 리스트를 계속해서 재사용

```py
path.append(letter)
...
path.pop()
```

```text
path = []

a 선택
[a]
    d 선택
    [a, d] → "ad" 저장
    d 제거
    [a]

    e 선택
    [a, e] → "ae" 저장
    e 제거

    f 선택
    [a, f] → "af" 저장

a 제거

b 선택
[b]
...
```

> 전형적인 backtracking 패턴

## TypeScript 코드

```ts
function letterCombinations(digits: string): string[] {
  const output: string[] = [];

  const numLetters: string[] = [
    "",
    "",
    "abc",
    "def",
    "ghi",
    "jkl",
    "mno",
    "pqrs",
    "tuv",
    "wxyz",
  ];

  const path: string[] = [];

  function recursive_letter_comb(i: number): null {
    if (i === digits.length) {
      output.push(path.join(""));
      return;
    }

    for (const letter of numLetters[digits[i]]) {
      path.push(letter);
      recursive_letter_comb(i + 1);
      path.pop();
    }
  }

  recursive_letter_comb(0);

  return output;
}
```
