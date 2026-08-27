# 6. Zigzag Conversion

https://leetcode.com/problems/zigzag-conversion/description/?envType=problem-list-v2&envId=string

## 문제

The string "PAYPALISHIRING" is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

P A H N
A P L S I I G
Y I R
And then read line by line: "PAHNAPLSIIGYIR"

Write the code that will take a string and make this conversion given a number of rows:

string convert(string s, int numRows);

- Example 1:
  Input: s = "PAYPALISHIRING", numRows = 3
  Output: "PAHNAPLSIIGYIR"

- Example 2:
  Input: s = "PAYPALISHIRING", numRows = 4
  Output: "PINALSIGYAHRPI"
  Explanation:
  P I N
  A L S I G
  Y A H R
  P I

- Example 3:
  Input: s = "A", numRows = 1
  Output: "A"

## 내 풀이(Py)

```py
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1 or numRows >= len(s):
            return s

        jump = 2 * (numRows - 1)
        # output = ""
        output = []

        for i in range(numRows):
            if i == 0:
                step = i
                while step < len(s):
                    # output += s[step]
                    output.append(s[step])
                    step += jump
            elif i == (numRows - 1):
                step = i
                while step < len(s):
                    # output += s[step]
                    output.append(s[step])
                    step += jump
            else:
                step = i
                round = 1

                while step < len(s):
                    # output += s[step]
                    output.append(s[step])
                    round += 1
                    if(round % 2 == 0):
                        step = (round // 2) * jump - i
                    else:
                        step = (round // 2) * jump + i

        return ''.join(output)
```

- 시간복잡도: O(n) (s의 각 문자가 output에 정확히 한 번씩 들어감)
- 공간복잡도: O(n) / 보조공간 O(1)

### 접근방법 (인덱스 계산)

1. numRows에 따라 zigzag의 반복 패턴이 몇번째마다 나타나는지 계산
2. 각 row의 순서대로 row에 들어갈 문자가 있는 인덱스를 계산하며 삽입

### 개선점

1. 첫 번째 row와 마지막 row의 인덱스 계산 및 삽입 코드가 완전히 동일
2. 중간 row의 계산식이 수학적으로는 맞지만, 가동성이 떨어짐

```py
round += 1
if(round % 2 == 0):
    step = (round // 2) * jump - i
else:
    step = (round // 2) * jump + i
```

## 추천 풀이 1(내 코드 개선)

```py
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1 or numRows >= len(s):
            return s

        cycle = 2 * (numRows - 1)
        result = []

        for row in range(numRows):
            for j in range(row, len(s), cycle):
                result.append(s[j])

                diagonal = j + cycle - 2 * row

                if row != 0 and row != numRows - 1 and diagonal < len(s):
                    result.append(s[diagonal])

        return ''.join(result)
```

- 중복 제거 및 가동성 개선
- 시간복잡도 및 공간복잡도 동일

## 추천 풀이 2(Row Simulattion 방식)

```py
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1 or numRows >= len(s):
            return s

        rows = [[] for _ in range(numRows)]
        row = 0
        direction = 1

        for char in s:
            rows[row].append(char)

            if row == 0:
                direction = 1
            elif row == numRows - 1:
                direction = -1

            row += direction

        string_list = []
        for row in rows:
            string_list.append(''.join(row))

        return ''.join(string_list)
```

- 실제로 지그재그로 위 → 아래 → 위 방향을 이동하면서 각 문자를 해당 row에 삽입
- === direction 변수를 통해 방향 통제 ===
- 직관적이고 구현하기 쉬움

- 시간복잡도: O(n) / 공간복잡도: O(n)

## TypeScript 풀이코드

```ts
function convert(s: string, numRows: number): string {
  if (numRows == 1 || numRows >= s.length) {
    return s;
  }

  const rows: string[] = [];
  for (let i: number = 0; i < numRows; i++) {
    rows.push("");
  }

  let row: number = 0;
  let direction: number = 1;

  for (const char of s) {
    rows[row] += char;

    if (row === numRows - 1) {
      direction = -1;
    }

    if (row === 0) {
      direction = 1;
    }

    row += direction;
  }

  return rows.join("");
}
```

- [Python과 TypeScript의 문자열 연결(Concatenation) 차이](<../../concepts/Python과%20TypeScript의%20문자열%20연결(Concatenation)%20차이.md>)
