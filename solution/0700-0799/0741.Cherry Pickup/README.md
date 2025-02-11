# [741. 摘樱桃](https://leetcode-cn.com/problems/cherry-pickup)

[English Version](/solution/0700-0799/0741.Cherry%20Pickup/README_EN.md)

## 题目描述

<!-- 这里写题目描述 -->

<p>一个N x N的网格<code>(grid)</code>&nbsp;代表了一块樱桃地，每个格子由以下三种数字的一种来表示：</p>

<ul>
	<li>0 表示这个格子是空的，所以你可以穿过它。</li>
	<li>1 表示这个格子里装着一个樱桃，你可以摘到樱桃然后穿过它。</li>
	<li>-1 表示这个格子里有荆棘，挡着你的路。</li>
</ul>

<p>你的任务是在遵守下列规则的情况下，尽可能的摘到最多樱桃：</p>

<ul>
	<li>从位置&nbsp;(0, 0) 出发，最后到达 (N-1, N-1) ，只能向下或向右走，并且只能穿越有效的格子（即只可以穿过值为0或者1的格子）；</li>
	<li>当到达 (N-1, N-1) 后，你要继续走，直到返回到 (0, 0) ，只能向上或向左走，并且只能穿越有效的格子；</li>
	<li>当你经过一个格子且这个格子包含一个樱桃时，你将摘到樱桃并且这个格子会变成空的（值变为0）；</li>
	<li>如果在 (0, 0) 和 (N-1, N-1) 之间不存在一条可经过的路径，则没有任何一个樱桃能被摘到。</li>
</ul>

<p><strong>示例 1:</strong></p>

<pre>
<strong>输入:</strong> grid =
[[0, 1, -1],
 [1, 0, -1],
 [1, 1,  1]]
<strong>输出:</strong> 5
<strong>解释：</strong> 
玩家从（0,0）点出发，经过了向下走，向下走，向右走，向右走，到达了点(2, 2)。
在这趟单程中，总共摘到了4颗樱桃，矩阵变成了[[0,1,-1],[0,0,-1],[0,0,0]]。
接着，这名玩家向左走，向上走，向上走，向左走，返回了起始点，又摘到了1颗樱桃。
在旅程中，总共摘到了5颗樱桃，这是可以摘到的最大值了。
</pre>

<p><strong>说明:</strong></p>

<ul>
	<li><code>grid</code> 是一个&nbsp;<code>N</code> * <code>N</code> 的二维数组，N的取值范围是<code>1 &lt;= N &lt;= 50</code>。</li>
	<li>每一个&nbsp;<code>grid[i][j]</code> 都是集合&nbsp;<code>{-1, 0, 1}</code>其中的一个数。</li>
	<li>可以保证起点&nbsp;<code>grid[0][0]</code>&nbsp;和终点&nbsp;<code>grid[N-1][N-1]</code>&nbsp;的值都不会是 -1。</li>
</ul>

## 解法

<!-- 这里可写通用的实现逻辑 -->

**方法一：动态规划**

线性 DP。

类似题型：方格取数、传纸条。

<!-- tabs:start -->

### **Python3**

<!-- 这里可写当前语言的特殊实现逻辑 -->

```python
class Solution:
    def cherryPickup(self, grid: List[List[int]]) -> int:
        n = len(grid)
        dp = [[[float('-inf')] * n for _ in range(n)]
              for _ in range((n << 1) - 1)]
        dp[0][0][0] = grid[0][0]
        for k in range(1, (n << 1) - 1):
            for i1 in range(n):
                for i2 in range(n):
                    j1, j2 = k - i1, k - i2
                    if not 0 <= j1 < n or not 0 <= j2 < n or grid[i1][j1] == -1 or grid[i2][j2] == -1:
                        continue
                    t = grid[i1][j1]
                    if i1 != i2:
                        t += grid[i2][j2]
                    for x1 in range(i1 - 1, i1 + 1):
                        for x2 in range(i2 - 1, i2 + 1):
                            if x1 >= 0 and x2 >= 0:
                                dp[k][i1][i2] = max(
                                    dp[k][i1][i2], dp[k - 1][x1][x2] + t)
        return max(0, dp[-1][-1][-1])
```

### **Java**

<!-- 这里可写当前语言的特殊实现逻辑 -->

```java
class Solution {
    public int cherryPickup(int[][] grid) {
        int n = grid.length;
        int[][][] dp = new int[n * 2][n][n];
        dp[0][0][0] = grid[0][0];
        for (int k = 1; k < n * 2 - 1; ++k) {
            for (int i1 = 0; i1 < n; ++i1) {
                for (int i2 = 0; i2 < n; ++i2) {
                    int j1 = k - i1, j2 = k - i2;
                    dp[k][i1][i2] = Integer.MIN_VALUE;
                    if (j1 < 0 || j1 >= n || j2 < 0 || j2 >= n || grid[i1][j1] == -1 || grid[i2][j2] == -1) {
                        continue;
                    }
                    int t = grid[i1][j1];
                    if (i1 != i2) {
                        t += grid[i2][j2];
                    }
                    for (int x1 = i1 - 1; x1 <= i1; ++x1) {
                        for (int x2 = i2 - 1; x2 <= i2; ++x2) {
                            if (x1 >= 0 && x2 >= 0) {
                                dp[k][i1][i2] = Math.max(dp[k][i1][i2], dp[k - 1][x1][x2] + t);
                            }
                        }
                    }
                }
            }
        }
        return Math.max(0, dp[n * 2 - 2][n - 1][n - 1]);
    }
}
```

### **C++**

```cpp
class Solution {
public:
    int cherryPickup(vector<vector<int>>& grid) {
        int n = grid.size();
        vector<vector<vector<int>>> dp(n << 1, vector<vector<int>>(n, vector<int>(n, -1e9)));
        dp[0][0][0] = grid[0][0];
        for (int k = 1; k < n * 2 - 1; ++k)
        {
            for (int i1 = 0; i1 < n; ++i1)
            {
                for (int i2 = 0; i2 < n; ++i2)
                {
                    int j1 = k - i1, j2 = k - i2;
                    if (j1 < 0 || j1 >= n || j2 < 0 || j2 >= n || grid[i1][j1] == -1 || grid[i2][j2] == -1) continue;
                    int t = grid[i1][j1];
                    if (i1 != i2) t += grid[i2][j2];
                    for (int x1 = i1 - 1; x1 <= i1; ++x1)
                        for (int x2 = i2 - 1; x2 <= i2; ++x2)
                            if (x1 >= 0 && x2 >= 0)
                                dp[k][i1][i2] = max(dp[k][i1][i2], dp[k - 1][x1][x2] + t);
                }
            }
        }
        return max(0, dp[n * 2 - 2][n - 1][n - 1]);
    }
};
```

### **Go**

```go
func cherryPickup(grid [][]int) int {
	n := len(grid)
	dp := make([][][]int, (n<<1)-1)
	for i := range dp {
		dp[i] = make([][]int, n)
		for j := range dp[i] {
			dp[i][j] = make([]int, n)
		}
	}
	dp[0][0][0] = grid[0][0]
	for k := 1; k < (n<<1)-1; k++ {
		for i1 := 0; i1 < n; i1++ {
			for i2 := 0; i2 < n; i2++ {
				dp[k][i1][i2] = int(-1e9)
				j1, j2 := k-i1, k-i2
				if j1 < 0 || j1 >= n || j2 < 0 || j2 >= n || grid[i1][j1] == -1 || grid[i2][j2] == -1 {
					continue
				}
				t := grid[i1][j1]
				if i1 != i2 {
					t += grid[i2][j2]
				}
				for x1 := i1 - 1; x1 <= i1; x1++ {
					for x2 := i2 - 1; x2 <= i2; x2++ {
						if x1 >= 0 && x2 >= 0 {
							dp[k][i1][i2] = max(dp[k][i1][i2], dp[k-1][x1][x2]+t)
						}
					}
				}
			}
		}
	}
	return max(0, dp[n*2-2][n-1][n-1])
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}
```

### **JavaScript**

```js
/**
 * @param {number[][]} grid
 * @return {number}
 */
var cherryPickup = function (grid) {
    const n = grid.length;
    let dp = new Array(n * 2 - 1);
    for (let k = 0; k < dp.length; ++k) {
        dp[k] = new Array(n);
        for (let i = 0; i < n; ++i) {
            dp[k][i] = new Array(n).fill(-1e9);
        }
    }
    dp[0][0][0] = grid[0][0];
    for (let k = 1; k < n * 2 - 1; ++k) {
        for (let i1 = 0; i1 < n; ++i1) {
            for (let i2 = 0; i2 < n; ++i2) {
                const j1 = k - i1,
                    j2 = k - i2;
                if (
                    j1 < 0 ||
                    j1 >= n ||
                    j2 < 0 ||
                    j2 >= n ||
                    grid[i1][j1] == -1 ||
                    grid[i2][j2] == -1
                ) {
                    continue;
                }
                let t = grid[i1][j1];
                if (i1 != i2) {
                    t += grid[i2][j2];
                }
                for (let x1 = i1 - 1; x1 <= i1; ++x1) {
                    for (let x2 = i2 - 1; x2 <= i2; ++x2) {
                        if (x1 >= 0 && x2 >= 0) {
                            dp[k][i1][i2] = Math.max(
                                dp[k][i1][i2],
                                dp[k - 1][x1][x2] + t,
                            );
                        }
                    }
                }
            }
        }
    }
    return Math.max(0, dp[n * 2 - 2][n - 1][n - 1]);
};
```

### **...**

```

```

<!-- tabs:end -->
