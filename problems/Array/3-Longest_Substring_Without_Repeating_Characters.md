# 3. Longest Substring Without Repeating Characters

https://leetcode.com/problems/longest-substring-without-repeating-characters/description

## 문제

Given a string s, find the length of the longest substring without duplicate characters.

- Example 1:
  Input: s = "abcabcbb"
  Output: 3
  Explanation: The answer is "abc", with the length of 3. Note that "bca" and "cab" are also correct answers.

- Example 2
  Input: s = "bbbbb"
  Output: 1
  Explanation: The answer is "b", with the length of 1.

- Example 3:
  Input: s = "pwwkew"
  Output: 3
  Explanation: The answer is "wke", with the length of 3.
  Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.

## 내 풀이

### 첫 번째

```py
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        subs_length = 0
        substring = ""

        for char in s:
            if char in substring:
                subs_length = max(subs_length, len(substring))
                substring = substring[substring.index(char) + 1:]
                substring += char
            else:
                substring += char

        subs_length = max(subs_length, len(substring))

        return subs_length
```

- 시간복잡도: O(n^2) 최악
- 공간복잡도: O(n)

- if char in substring 에서 매번 문자열을 순회해야 하기 때문에 시간복잡도 면에서 매우 불리

### 두 번째

```py
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        max_length = 0
        subs_length = 0
        subs_start_index = 0
        char_dict = {}

        for i, char in enumerate(s):
            if char in char_dict and char_dict[char] >= subs_start_index:
                max_length = max(max_length, subs_length)
                subs_length = i - char_dict[char]
                subs_start_index = char_dict[char] + 1
                char_dict[char] = i
            else:
                char_dict[char] = i
                subs_length += 1

        max_length = max(max_length, subs_length)

        return max_length
```

- 시간복잡도: O(n)
- 공간복잡도: O(k) (k는 substring의 서로 다른 문자의 수), O(n) 최악

- 개선점

1. subs_length 변수는 없앨 수 있음 (substring의 길이는 언제나 1 - subs_start_index + 1)

## 개선 풀이

```py
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        max_length = 0
        start = 0
        char_dict = {}

        for i, char in enumerate(s):
            if char in char_dict and char_dict[char] >= start:
                start = char_dict[char] + 1

            char_dict[char] = i
            max_length = max(max_length, i - start + 1)

        return max_length
```

## TypeScript 풀이

```ts
function lengthOfLongestSubstring(s: string): number {
  let maxLength: number = 0;
  let startIndex: number = 0;
  const charMap = new Map<string, number>();

  for (let i: number = 0; i < s.length; i++) {
    const char = s[i];

    if (charMap.has(char) && charMap.get(char) >= startIndex) {
      startIndex = charMap.get(char) + 1;
    }

    maxLength = Math.max(maxLength, i - startIndex + 1);
    charMap.set(char, i);
  }

  return maxLength;
}
```
