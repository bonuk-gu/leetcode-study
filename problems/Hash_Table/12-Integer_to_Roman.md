# 12. Integer to Roman

https://leetcode.com/problems/integer-to-roman/description/

## 문제

Seven different symbols represent Roman numerals with the following values:

Symbol Value
I 1
V 5
X 10
L 50
C 100
D 500
M 1000
Roman numerals are formed by appending the conversions of decimal place values from highest to lowest. Converting a decimal place value into a Roman numeral has the following rules:

If the value does not start with 4 or 9, select the symbol of the maximal value that can be subtracted from the input, append that symbol to the result, subtract its value, and convert the remainder to a Roman numeral.
If the value starts with 4 or 9 use the subtractive form representing one symbol subtracted from the following symbol, for example, 4 is 1 (I) less than 5 (V): IV and 9 is 1 (I) less than 10 (X): IX. Only the following subtractive forms are used: 4 (IV), 9 (IX), 40 (XL), 90 (XC), 400 (CD) and 900 (CM).
Only powers of 10 (I, X, C, M) can be appended consecutively at most 3 times to represent multiples of 10. You cannot append 5 (V), 50 (L), or 500 (D) multiple times. If you need to append a symbol 4 times use the subtractive form.
Given an integer, convert it to a Roman numeral.

Example 1:
Input: num = 3749
Output: "MMMDCCXLIX"
Explanation:
3000 = MMM as 1000 (M) + 1000 (M) + 1000 (M)
700 = DCC as 500 (D) + 100 (C) + 100 (C)
40 = XL as 10 (X) less of 50 (L)
9 = IX as 1 (I) less of 10 (X)
Note: 49 is not 1 (I) less of 50 (L) because the conversion is based on decimal places

Example 2:
Input: num = 58
Output: "LVIII"
Explanation:
50 = L
8 = VIII

Example 3:
Input: num = 1994
Output: "MCMXCIV"
Explanation:
1000 = M
900 = CM
90 = XC
4 = IV

## 내 풀이(TS)

```ts
function intToRoman(num: number): string {
  let roman: string = "";

  const romanMap = new Map<number, string>([
    [1, "I"],
    [5, "V"],
    [10, "X"],
    [50, "L"],
    [100, "C"],
    [500, "D"],
    [1000, "M"],
  ]);

  for (let i: number = 3; i >= 0; i--) {
    const decimal: number = 10 ** i;
    if (Math.floor(num / decimal) === 4) {
      roman += romanMap.get(decimal) + romanMap.get(5 * decimal);
      num -= 4 * decimal;
    } else if (Math.floor(num / decimal) === 9) {
      roman += romanMap.get(decimal) + romanMap.get(10 * decimal);
      num -= 9 * decimal;
    } else {
      if (num >= 5 * decimal) {
        roman += romanMap.get(5 * decimal);
        num -= 5 * decimal;
      }
      roman += romanMap.get(decimal).repeat(Math.floor(num / decimal));
      num -= decimal * Math.floor(num / decimal);
    }
  }

  return roman;
}
```

- 시간복잡도: O(1)
- 공간복잡도: O(1)

### 개선점

1. Map이 꼭 필요한가?

```ts
const romanMap = new Map<number, string>([
  [1, "I"],
  [5, "V"],
  [10, "X"],
  [50, "L"],
  [100, "C"],
  [500, "D"],
  [1000, "M"],
]);
```

key는 동적으로 추가되거나 삭제되지 않고 완전히 고정되어 있기 때문에, Map의 장점을 거의 사용하지 않음

2. TypeScript에서의 더 중요한 문제
   romanMap.get(decimal)의 타입은 string | undefined이기 때문에 romanMap.get(decimal).repeat(...)과 같은 부분이 undefined 에러에 걸릴 수 있음

3. i = 3 일 때 일반화가 약간 깨짐
   현재 문제는 조건이 num <= 3999이기 때문에 정확하지만, 추상화를 한 코드치고는 약간 애매한 부분

4. Math.floor(num / deciaml)과 같은 계산 반복

## 추천 풀이 1: 큰 것부터 greedy하게 빼기

```ts
function intToRoman(num: number): string {
  const values: number[] = [
    1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1,
  ];

  const symbols: string[] = [
    "M",
    "CM",
    "D",
    "CD",
    "C",
    "XC",
    "L",
    "XL",
    "X",
    "IX",
    "V",
    "IV",
    "I",
  ];
  // 순서대로 대응

  let roman = "";

  for (let i = 0; i < values.length; i++) {
    while (num >= values[i]) {
      roman += symbols[i];
      num -= values[i];
    }
  }

  return roman;
}
```

- 4, 9, 40, 90 같은 예외들을 아예 하나의 Roman numeral 단위로 취급하기
  (처음에 이 방법이 굉장히 비효율적일 것이라고 생각했는데, 시간복잡도/공간복잡도와 코드의 단순성을 비교했을 때 훨씬 좋은 것 같음)

## 추천 풀이 2: 자릿수별 Lookup Table 활용

```ts
function intToRoman(num: number): string {
  const thousands = ["", "M", "MM", "MMM"];
  const hundreds = ["", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"];
  const tens = ["", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"];
  const ones = ["", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"];

  return (
    thousands[Math.floor(num / 1000)] +
    hundreds[Math.floor((num % 1000) / 100)] +
    tens[Math.floor((num % 100) / 10)] +
    ones[num % 10]
  );
}
```

- 각 자릿수의 Roman 표현을 미리 만들어 두는 방식
- 하드코딩의 성격이 강함(면접 관점에서는 Greedy가 더 나을 수도?)

## Python 코드

### Greedy 방식

```py
class Solution:
    def intToRoman(self, num: int) -> str:
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

        roman = ""

        # for i in range(len(values)):
        #     while num >= values[i]:
        #         roman += symbols[i]
        #         num -= values[i]

        for value, symbol in zip(values, symbols):
            while num >= value:
                roman += symbol
                num -= value

        return roman
```

- [Python의 zip() 함수](<../../concepts/python/Python의 zip() 함수.md>)

### Lookup Table 방식

```py
class Solution:
    def intToRoman(self, num: int) -> str:
        thousands = ["", "M", "MM", "MMM"]

        hundreds = [
            "", "C", "CC", "CCC", "CD",
            "D", "DC", "DCC", "DCCC", "CM"
        ]

        tens = [
            "", "X", "XX", "XXX", "XL",
            "L", "LX", "LXX", "LXXX", "XC"
        ]

        ones = [
            "", "I", "II", "III", "IV",
            "V", "VI", "VII", "VIII", "IX"
        ]

        return (
            thousands[num // 1000]
            + hundreds[(num % 1000) // 100]
            + tens[(num % 100) // 10]
            + ones[num % 10]
        )
```
