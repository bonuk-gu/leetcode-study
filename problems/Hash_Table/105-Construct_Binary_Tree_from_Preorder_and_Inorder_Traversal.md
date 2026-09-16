# 105. Construct Binary Tree from Preorder and Inorder Traversal

## 문제

https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description/

## 내 풀이(TS)

```ts
/**
 * Definition for a binary tree node.
 * class TreeNode {
 *     val: number
 *     left: TreeNode | null
 *     right: TreeNode | null
 *     constructor(val?: number, left?: TreeNode | null, right?: TreeNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.left = (left===undefined ? null : left)
 *         this.right = (right===undefined ? null : right)
 *     }
 * }
 */

function buildTree(preorder: number[], inorder: number[]): TreeNode | null {
  const inorderMap = new Map<number, number>();

  inorder.forEach((num, index) => {
    inorderMap.set(num, index);
  });

  const head: TreeNode = new TreeNode(preorder[0]);

  function findLocation(preorderNum: number, currentNode: TreeNode) {
    if (inorderMap.get(preorderNum)! < inorderMap.get(currentNode.val)!) {
      if (currentNode.left === null) {
        currentNode.left = new TreeNode(preorderNum);
      } else {
        findLocation(preorderNum, currentNode.left);
      }
    } else {
      if (currentNode.right === null) {
        currentNode.right = new TreeNode(preorderNum);
      } else {
        findLocation(preorderNum, currentNode.right);
      }
    }
  }

  for (let i: number = 1; i < preorder.length; i++) {
    let current: TreeNode = head;

    findLocation(preorder[i], current);
  }

  return head;
}
```

- 시간복잡도: O(n^2) worst case / 균형 트리라면 약 O(n log g)
- 공간복잡도: O(n)

### 접근방법

- preorder 는 root -> left -> right 순이고 inorder는 left -> root -> right 순을 활용
- inorder의 value와 인덱스를 Map 자료구조를 활용하여 매핑
- preorder를 순회하며 각 value의 위치를 inorder의 맵을 활용하여 탐색(root로 부터 왼쪽/오른쪽으로 재귀적으로 탐색)

### 개선점

> > 위치를 탐색하는 구조 자체의 개선

- 현재 매 노드마다 root -> child(left/right) -> child(left/right)를 다시 탐색
- 이로 인해 최악의 경우 시간 복잡도가 O(n^2)이 됨
- 최적의 풀이를 통해 핵심 알고리즘의 시간 복잡도를 O(n)으로 바꿀 수 있음

## 추천 풀이

```ts
function buildTree(preorder: number[], inorder: number[]): TreeNode | null {
  const inorderMap = new Map<number, number>();

  for (let i = 0; i < inorder.length; i++) {
    inorderMap.set(inorder[i], i);
  }

  let preorderIndex = 0;

  function build(left: number, right: number): TreeNode | null {
    if (left > right) {
      return null;
    }

    const rootValue = preorder[preorderIndex++];
    const root = new TreeNode(rootValue);

    const inorderIndex = inorderMap.get(rootValue)!;

    root.left = build(left, inorderIndex - 1);
    root.right = build(inorderIndex + 1, right);

    return root;
  }

  return build(0, inorder.length - 1);
}
```

> > inorder 배열에서 left ~ right 구간에 해당하는 서브트리를 만들어서 반환하는 함수(build)를 사용

- build의 함수의 left, rigth는 inorder의 인덱스
- preorder의 첫 번째 원소가 root이므로 inorder에서 root의 위치를 기준으로 left/right subtree 구간을 나누고
- 각 구간에서 다시 root를 갱신하며 left/right subtree를 찾아가는 방식
- left를 먼저 만드는 것이 중요!
  ```ts
  root.left = build(left, inorderIndex - 1);
  root.right = build(inorderIndex + 1, right);
  ```
  그래야 preorderIndex가 증가하면서 root가 알맞게 갱신됨
- 중요 코드

```ts
const rootValue = preorder[preorderIndex++];
===
const rootValue = preorder[preorderIndex];
preorderIndex++;
```

## Python 코드

```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def buildTree(self, preorder: list[int], inorder: list[int]) -> TreeNode | None:
        inorder_map = {}
        for index, value in enumerate(inorder):
            inorder_map[value] = index

        preorder_index = 0

        def build(left: int, right: int) -> TreeNode | None:
            if left > right:
                return None

            nonlocal preorder_index

            root_value = preorder[preorder_index]
            preorder_index += 1
            root = TreeNode(root_value)

            inorder_index = inorder_map[root_value]

            root.left = build(left, inorder_index - 1)
            root.right = build(inorder_index + 1, right)

            return root

        return build(0, len(inorder) - 1)
```

- [Python의 nonocal](<../../concepts/python/Python의 nonlocal.md>)
