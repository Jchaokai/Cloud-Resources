## 动态规划

DP问题思路图：

![DP问题思路图](https://github.com/Jchaokai/Cloud-Resources/blob/master/images/DP%E9%97%AE%E9%A2%98%E6%80%9D%E8%B7%AF.JPG)

-  **[LeetCode 53](https://leetcode-cn.com/problems/maximum-subarray/)**

<img src="https://github.com/Jchaokai/Cloud-Resources/blob/master/images/DP-LeetCode-53.JPG" style="zoom:80%;" />

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

<img src="https://github.com/Jchaokai/Cloud-Resources/blob/master/images/DP-LeetCode-120.JPG" style="zoom:80%;" />

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

