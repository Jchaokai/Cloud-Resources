## 动态规划

DP问题思路图：

![DP问题思路图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/DP/DP%E9%97%AE%E9%A2%98%E6%80%9D%E8%B7%AF.JPG)

**一般分为:&emsp; `线性DP`&emsp; `区间DP`&emsp; `背包问题 `**

------



-  **[LeetCode 53](https://leetcode-cn.com/problems/maximum-subarray/)**

<img src="https://github.com/Jchaokai/Cloud-Resources/blob/master/images/DP/DP-LeetCode-53.JPG" style="zoom:80%;" />

```go
const (
    INT_MAX = int(^uint(0) >> 1)
    INT_MIN = ^INT_MAX
)
func maxSubArray(nums []int) int {
    res :=INT_MIN
    last :=0
    for i:=0;i<len(nums);i++{
        now :=  max(last,0) + nums[i]
        res = max(res,now)
        last = now
    }
    return res
}
func max(a int,b int) int{
    if a >=b {
        return a
    }
    return b
}
```

- **[leetcode 120](https://leetcode-cn.com/problems/maximum-subarray/)** 

![image](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/DP/DP-LeetCode-120.JPG) 

```go
const (
    INT_MAX = int(^uint(0) >> 1)
    INT_MIN = ^INT_MAX
)
func minimumTotal(nums  [][]int) int {
    n := len(nums)
    /*
    	新建二维数组
    	优化：
    		- 可以原数组上直接做
    		- 滚动数组
    */
    f := make([][]int,n)
    for i,v := range nums{
        f[i] = make([]int,len(v))
    }
    f[0][0] =nums[0][0]
    for i := 1; i < n; i++{
        for j := 0; j <= i; j++{
            f[i][j] = INT_MAX
            if j > 0 { f[i][j] = min(f[i-1][j-1] + nums[i][j] , f[i][j])}
            if j < i { f[i][j] = min(f[i-1][j] + nums[i][j] , f[i][j])}
        } 
    }
    res := INT_MAX
    for i := 0; i < n; i++{
        res = min(res,f[n-1][i])
    }
    return res
}
func min(a int,b int) int{
    if a <= b{
        return a
    }
    return b
}
```

**滚动数组优化**

```go
const (
    INT_MAX = int(^uint(0) >> 1)
    INT_MIN = ^INT_MAX
)
func minimumTotal(nums  [][]int) int {
    n := len(nums)
    /*
    	- 滚动数组	%2 和 &1 一个效果
    */
    f := make([][]int,2)
    for i :=0; i < 2; i++{
        f[i] = make([]int,n)
    }
    f[0][0] =nums[0][0]
    for i := 1; i < n; i++{
        for j := 0; j <= i; j++{
            f[i & 1][j] = INT_MAX
            if j > 0 { f[i & 1][j] = min(f[(i-1)&1][j-1] + nums[i][j] , f[i&1][j])}
            if j < i { f[i & 1][j] = min(f[(i-1)&1][j] + nums[i][j] , f[i&1][j])}
        } 
    }
    res := INT_MAX
    for i := 0; i < n; i++{
        res = min(res,f[(n-1)&1][i])
    }
    return res
}
func min(a int,b int) int{
    if a <= b{
        return a
    }
    return b
}
```

- **[LeetCode 63](https://leetcode-cn.com/problems/unique-paths-ii/)**

```go
func uniquePathsWithObstacles(nums [][]int) int {
    n,m := len(nums),len(nums[0])
    f := make([][]int,n)
    for i :=0; i < n; i++{
        f[i] = make([]int,m)
    }
    for i :=0; i < n; i++{
        for j := 0; j < m; j++{
            //判断障碍物,边界
            if nums[i][j] == 1 { continue }
            if i ==0 && j ==0 { f[i][j] = 1}
            if i > 0 { f[i][j] += f[i-1][j] }
            if j > 0 { f[i][j] += f[i][j-1] }
        }
    }
    return f[n-1][m-1]
}
```

- **[LeetCode 91](https://leetcode-cn.com/problems/decode-ways/)**

![image](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/DP/DP-LeetCode-91.JPG)

```go
func numDecodings(s string) int {
    n := len(s)
    f := make([]int,n+1)
    f[0] = 1
    for i := 1; i <= n;i++{
        //最后一个是一位数，且不 ==0
        if s[i-1] != '0' { f[i] += f[i-1]}
        //最后一个是二位数
        if i >= 2{
            tmp := (s[i-2] - '0') * 10 + s[i-1] -'0'
            if tmp >= 10 && tmp <= 26 { f[i] += f[i-2] }
        }
    }
    return f[n]
}

```

- **[LeetCode 198](https://leetcode-cn.com/problems/house-robber/)**

![image](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/DP/DP-LeetCode-198.JPG)

```go
func rob(nums []int) int {
    n := len(nums)
    f := make([]int,n+1)
    g := make([]int,n+1)
    for i := 1; i <= n; i++{
        f[i] = max(f[i-1],g[i-1])
        g[i] = f[i-1] + nums[i-1]
    }
    return max(f[n],g[n])
}

func max(a int,b int) int{
    if a >= b{
        return a
    }
    return b
}

```

