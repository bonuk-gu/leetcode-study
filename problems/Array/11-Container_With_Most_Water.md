# 11. Container With Most Water

https://leetcode.com/problems/container-with-most-water/description/

## 문제

You are given an integer array height of length n. There are n vertical lines drawn such that the two endpoints of the ith line are (i, 0) and (i, height[i]).

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return the maximum amount of water a container can store.

Notice that you may not slant the container.

## 내 풀이

```ts
function maxArea(height: number[]): number {
  let x1: number = 0;
  let x2: number = height.length - 1;
  let y1: number = height[x1];
  let y2: number = height[x2];
  let maxWater: number = (x2 - x1) * Math.min(y1, y2);

  while (x2 - x1 > 1) {
    if (y1 < y2) {
      // x1++;
      // y1 = height[x1];
      // if((x2 - x1) * Math.min(y1, y2) > maxWater) {
      //     maxWater = (x2 - x1) * Math.min(y1, y2);
      // };
      if (
        height[x1 + 1] > y1 &&
        (x2 - (x1 + 1)) * Math.min(height[x1 + 1], y2) > maxWater
      ) {
        maxWater = (x2 - (x1 + 1)) * Math.min(height[x1 + 1], y2);
      }
      x1++;
      y1 = height[x1];
    } else {
      // x2--;
      // y2 = height[x2];
      // if((x2 - x1) * Math.min(y1, y2) > maxWater) {
      //     maxWater = (x2 - x1) * Math.min(y1, y2);
      // };
      if (
        height[x2 - 1] > y2 &&
        (x2 - 1 - x1) * Math.min(height[x2 - 1], y1) > maxWater
      ) {
        maxWater = (x2 - 1 - x1) * Math.min(height[x2 - 1], y1);
      }
      x2--;
      y2 = height[x2];
    }
  }

  return maxWater;
}
```

- 접근 방식

1. 처음에는 "brute force"로 시도했으나 Time Limit Exceeded
2. 양 끝에서 높이가 더 낮은 쪽의 포인트를 안쪽으로 이동시키며 넓이 비교

- 시간복잡도: O(n)

- 공간복잡도: O(1)

## Feedback

1. 코드가 불필요하게 복잡한 조건들을 가지고 있음: if(height[x1 + 1] > y1 && ...) 같은 코드는 성능에 큰 영향을 주지는 않음 (코드 복잡도 대비 얻는 이득이 크지 않음)

## 추천 풀이

```ts
while (left < right) {
  maxWater = Math.max(
    maxWater,
    (right - left) * Math.min(height[left], height[right]),
  );

  if (height[left] < height[right]) {
    left++;
  } else {
    right--;
  }
}
```

- 시간복잡도: O(n)
- 공간복잡도: O(n)

- 넓이를 먼저 계산하고 포인터 이동

## python 풀이

```py
class Solution:
    def maxArea(self, height: List[int]) -> int:
        left = 0
        right = len(height) - 1
        maxWater = 0

        while left < right:
            maxWater = max(maxWater, (right-left) * min(height[left], height[right]))
            if(height[left] < height[right]):
                left += 1
            else:
                right -= 1

        return maxWater
```
