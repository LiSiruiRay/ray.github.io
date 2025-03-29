---
title: 'An interesting bug: function argument defined as a function'
date: 2025-03-29 14:57:18
tags:
---

Yesterday I was having an interview with a company and they wanted me to write a tree structure and do a traverse on the tree. At first I wrote the following code:
```python
class TreeNode:
    val: str
    children: list
    def __init__(self, val = "", children = list()):
        self.children = children
        self.val = val

def bfs(root: TreeNode):
    if not root: return []
    q = deque([root])
    res = []
    while q:
        cur = q.popleft()
        res.append(cur.val)
        for c in cur.children:
            q.append(c)
    return res

root = TreeNode(val="A")
B = TreeNode(val="B")
C = TreeNode(val="C")
D = TreeNode(val="D")
E = TreeNode(val="E")
F = TreeNode(val="F")
G = TreeNode(val="G")
H = TreeNode(val="H")
I = TreeNode(val="I")
J = TreeNode(val="J")
root.children.append(B)
root.children.append(C)
root.children.append(D)
# B.children.append(E)
# B.children.append(F)
# F.children.append(I)
# C.children.append(G)
# G.children.append(J)
# C.children.append(H)
print(bfs(root=root))
```

Yet this code gives me a bug, more specifically the `dfs` function run into a infinite loop. Can you tell why?

- Hint: Problem is in the structure of `TreeNode`.

When I chagned the `TreeNode` to the following, the bug is fixed:
```python

class TreeNode:
    val: str
    children: list
    def __init__(self, val = "", children = None):
        if children is None:
            self.children = []
        else:
            self.children = children
        self.val = val
```

---

Answer:
The issue is using a mutable default argument in the first implementation. When we defind a function (constructor `__init__` here) with a mutable value like `list()`, there is a list created when the function is defined, not each time when the function is called. Thus, with the first implementation, throughout the whole implementation, though called `TreeNode()` several time, we actually only have one `children` across all `A`, `B`, ...