# 1. Two Sum

https://leetcode.com/problems/two-sum/description/

## 문제

You are given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.

Example 1:

Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
Example 2:

Input: nums = [3,2,4], target = 6
Output: [1,2]
Example 3:

Input: nums = [3,3], target = 6
Output: [0,1]

## 내 풀이

```ts
function twoSum(nums: number[], target: number): number[] {
  for (let i: number = 0; i < nums.length; i++) {
    for (let j: number = 1; i + j < nums.length; j++) {
      if (nums[i] + nums[i + j] === target) {
        return [i, i + j];
      }
    }
  }
}
```

- 접근 방식: "brute force"
  앞에서부터 차례대로 더해보면서 찾음

- 시간복잡도: O(n^2)
- 공간복잡도: O(1)

* 개선 힌트
  (1) 현재 보고 있는 원소 nums[i] 뒤의 모든 원소를 순회하면서 덧셈을 하지 말고 target - nums[i]에 해당하는 숫자가 있는지만 확인
  (2) Map 자료구조를 활용
  (3) 배열을 왼쪽부터 한 번만 순회할 수 있는 방식 생각

## 2차 풀이

```ts
function twoSum(nums: number[], target: number): number[] {
  const numsMap = new Map<number, number>();
  for (let i: number = 0; i < nums.length; i++) {
    numsMap.set(nums[i], i);
  }
  for (let i: number = 0; i < nums.length; i++) {
    const findNum = target - nums[i];
    if (numsMap.get(findNum) && numsMap.get(findNum) !== i) {
      return [i, numsMap.get(findNum)];
    }
  }
}
```

- 시간복잡도: O(n)
- 공간복잡도: O(n)

- if (numsMap.get(findNum) && numsMap.get(findNum) !== i) 에서 numsMap.get(findNum)이 0일 수 있음. 그런데 JavaScript에서 0은 falsy라서 조건문이 실패할 가능성 존재. -> has() 사용

## 추천 풀이

```ts
function twoSum(nums: number[], target: number): number[] {
  const numsMap = new Map<number, number>();

  for (let i = 0; i < nums.length; i++) {
    const findNum = target - nums[i];

    if (numsMap.has(findNum)) {
      return [numsMap.get(findNum)!, i];
    }

    numsMap.set(nums[i], i);
  }

  return [];
}
```

- 시간복잡도: O(n)
- 공간복잡도: O(n)

- !는 TypeScript에게 "has()로 존재하는 걸 확인했으니 이 값은 undefined가 아니다"라고 알려줌
- 배열을 두 번 순회하지 않고 한 번 순회하도록 개선: findNum이 이전에 등장했는지만 체크

## Python

```py
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_dict = {}
        for i in range(len(nums)):
            new_target = target - nums[i]
            if new_target in num_dict:
                return [i, num_dict.get(new_target, 0)]
            else:
                num_dict[nums[i]] = i

```

- Feedback

1. range(len(nums))보다 enumerate(nums)를 쓰면 인덱스와 값을 동시에 얻을 수 있어서 더 자연스럽다.
2. 이미 if new_target in num_dict 로 key의 존재를 확인했기 때문에 num_dict.get(new_target, 0)를 쓸 필요 없이 그냥 num_dict[new_target]로 접근 가능
3. else 굳이 없어도 됨

## Python 추천 풀이

```py
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_dict = {}

        for i, num in enumerate(nums):
            complement = target - num

            if complement in num_dict:
                return [num_dict[complement], i]

            num_dict[num] = i
```
