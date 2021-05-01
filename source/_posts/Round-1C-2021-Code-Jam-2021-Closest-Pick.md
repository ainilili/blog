title: 'Round 1C 2021 - Code Jam 2021 -  Closest Pick '
author: Nico
tags:
  - Code Jam
categories:
  - Algorithm
date: 2021-05-01 19:59:00
---
原题地址: [传送门](https://codingcompetitions.withgoogle.com/codejam/round/00000000004362d7/00000000007c0f00#problem)

### 题解
从题目中可以得知，要想赢，必须满足以下规则：
 - 1.不能与已选号码重复
 - 2.所选号码周围的其他号码尽可能未被选中

第一个规则很好理解，第二个规则这里解释一下，也是思路的核心。如果要想赢，需要离中奖号码最近，要想使胜率最大化，那么就要尽可能保证所选号码离所有其余未选号码的距离尽可能大于它们到已选号码，故而，当所选号码周围未选号码最多时，就是最大的胜率。

所以，这题的本质为寻找已选号码有序情况下的前两个最大的间隔，再计算分别计算两个间隔中能保证赢的号码数，再除以号码可选范围即为胜率，分情况考虑，记排序后的已选号码集合为arr，号码可选范围为k，已选号码总数为n：
 - 开头间隔1 ~ arr[0]和结尾间隔k - arr[n - 1]可赢号码数为当前间隔长度
 - 两个已选号码之间的间隔如果为i，此时又要分两种情况考虑：
  - 如果同一个间隔中同时选择两个号码，那么可赢号码数量为当前间隔长度
  - 如果同一个间隔中只选择一个号码，那么可盈号码数量为interval / 2 + interval % 2

### Golang解决
```go
func solve() float64{
	inputs := readInts()
	n, k, arr := inputs[0], inputs[1], readInts()
	sort.Ints(arr)
	max1, max2 := arr[0] - 1, k - arr[n - 1]
	if max2 > max1 {
		max2, max1 = max1, max2
	}
	maxInterval := 0
	for i := 1; i < n; i ++ {
		interval := arr[i] - arr[i - 1] - 1
		if interval > 0{
			if interval > maxInterval {
				maxInterval = interval
			}
			interval = interval / 2 + interval % 2
			if interval > max1 {
				max2, max1 = max1, interval
			}else if interval > max2{
				max2 = interval
			}
		}
	}
	return float64(max(max1 + max2, maxInterval)) / float64(k)
}
```