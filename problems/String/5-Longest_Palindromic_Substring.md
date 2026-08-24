# 5. Longest Palindromic Substring

https://leetcode.com/problems/longest-palindromic-substring/description/

## 문제

Given a string s, return the longest palindromic substring in s
(palindromic: 앞으로 읽어도, 뒤로 읽어도 똑같은 문자열)

- Example 1:
  Input: s = "babad"
  Output: "bab"
  Explanation: "aba" is also a valid answer.

- Example 2:
  Input: s = "cbbd"
  Output: "bb"

## 내 풀이

### 첫 번째 (틀린 풀이)

```py
class Solution:
    def longestPalindrome(self, s: str) -> str:
        longest = s[0]
        char_dict = {}

        for i, char in enumerate(s):
            if char in char_dict:
                length = i - char_dict[char] + 1
                if length > len(longest):
                    left = char_dict[char] + 1
                    right = i - 1
                    palindromic = True

                    while left <= right and palindromic:
                        if s[left] == s[right]:
                            left += 1
                            right -= 1
                        else:
                            palindromic = False

                    if palindromic:
                        longest = s[char_dict[char]:i + 1]

            char_dict[char] = i

        return longest
```

#### 접근방법

1. 딕셔너리를 만들고 s를 순회하면서 각 문자와 인덱스를 key, value로 저장
2. 현재 인덱스의 문자가 이전에 등장했으면 양끝에서부터 좁혀오며 문자가 동일한지 확인
3. palindrome이면서 기존의 palindrome보다 길이가 길면 갱신

#### 틀린 이유

- 각 문자에 대해 가장 최근 등장 위치 하나만 기억하기 때문
- 'ccc'와 같은 경우를 생각하면 잘못된 알고리즘

### 두 번째

```py
class Solution:
    def longestPalindrome(self, s: str) -> str:
        longest = s[0]

        for i, char in enumerate(s):
            subs = char
            left = i
            right = i

            while left - 1 >= 0 and s[left - 1] == char:
                subs = s[left] + subs
                left -= 1

            while right + 1 < len(s) and s[right + 1] == char:
                subs = subs + s[right]
                right += 1

            while left - 1 >= 0 and right + 1 < len(s) and s[left - 1] == s[right + 1]:
                subs = s[left] + subs + s[right]
                left -= 1
                right += 1

            if right - left + 1 > len(longest):
                longest = s[left:right + 1]

        return longest
```

#### 접근방법

1. s를 순회하면서 각 문자를 palindrome의 가운데 문자라고 가정
2. 왼쪽/오른쪽으로 확장하며 가운데에 동일한 문자가 있는 경우를 처리하며 substring에 문자를 더함
3. 양 옆으로 확장하면서 양 옆에 동일한 문자가 오는 경우를 처리하며 substring에 문자를 더함
4. 기존의 가장 긴 palindrome과 길이를 비교하며 갱신

- 시간복잡도: 최악의 경우 O(n^3) (python 문자열 연산 때문에)
- 공간복잡도: O(n)(최종적으로 만들어지는 subs, longest 문자열 때문)

#### 문제점/개선점

1. s[left] + subs
   subs = subs + s[right]
   subs = s[left] + subs + s[right]
   와 같이 매번 문자열을 만드는 것을 피해야 함(시간복잡도 면에서도 불리)

## 세 번째(Best)

```py
class Solution:
    def longestPalindrome(self, s: str) -> str:
        longest = s[0]
        i = 0

        while i < len(s):
            char = s[i]
            left = i
            right = i

            while right + 1 < len(s) and s[right + 1] == char:
                right += 1
                i += 1

            while left - 1 >= 0 and right + 1 < len(s) and s[left - 1] == s[right + 1]:
                left -= 1
                right += 1

            if right - left + 1 > len(longest):
                longest = s[left:right + 1]

            i += 1

        return longest
```

leetcode runtime에서 chatgpt의 추천 풀이보다 더 빠른 속도를 보임

#### 접근방법

1. s를 앞에서부터 순회하면서 각 문자를 palindrome의 가운데 문자라고 가정
2. 오른쪽으로만 확장하며 동일한 문자가 있는지 확인하며 right와 i를 동시에 증가시키며 동일한 palindrome를 반복해서 계산하는 경우를 방지
3. 양 옆으로 확장하면서 양 옆에 동일한 문자가 오는 경우를 처리 left와 rigth를 이동
4. 기존의 palindrome과 길이를 비교하여 갱신

- 시간복잡도: O(n^2)
- 공간복잡도: O(1)

#### 문제점/개선점

1. ````py
   if right - left + 1 > len(longest):
       longest = s[left:right + 1]
       ```
   ````
   이 부분은 여전히 불필요. 길이만 계산해뒀다가 문자열 slice는 마지막 한 번만 하면 됨.

## 추천 풀이: Expand Around Center

```py
class Solution:
    def longestPalindrome(self, s: str) -> str:
        start = 0
        max_length = 1

        def expand(left: int, right: int):
            nonlocal start, max_length

            while left >= 0 and right < len(s) and s[left] == s[right]:
                length = right - left + 1

                if length > max_length:
                    start = left
                    max_length = length

                left -= 1
                right += 1

        for i in range(len(s)):
            # 홀수 길이 palindrome
            expand(i, i)

            # 짝수 길이 palindrome
            expand(i, i + 1)

        return s[start:start + max_length]
```

- 각 문자를 pelindrome의 가운데 문자로 가정하는 것은 동일함
- 가운데에서 동일 문자가 반복되는 경우를 따로 생각하지 않고 홀수 길이 palindrome과 짝수길이 palindrome을 따로 찾아냄

## TypeScript 풀이

```ts
function longestPalindrome(s: string): string {
  let maxLength: number = 1;
  let start: number = 0;

  let i: number = 0;
  while (i < s.length) {
    const num = s[i];
    let left: number = i;
    let right: number = i;

    while (right < s.length - 1 && s[right + 1] == num) {
      right++;
      i++;
    }

    while (left >= 1 && right < s.length - 1 && s[left - 1] === s[right + 1]) {
      left--;
      right++;
    }

    if (right - left + 1 > maxLength) {
      start = left;
      maxLength = right - left + 1;
    }

    i++;
  }

  return s.slice(start, start + maxLength);
}
```
