title: Round 1A 2021 - Code Jam 2021 -  Append Sort
author: Nico
tags:
  - Code Jam
categories:
  - Algorithm
date: 2021-04-14 21:37:00
---
原题地址: [传送门](https://codingcompetitions.withgoogle.com/codejam/round/000000000043585d/00000000007549e5)

### 分析
题目要求是对于输入的``X1,X2,…,XN``，求至少在每个数字后方增加多少个的``0-9``来保证``X1,X2,…,XN``的递增顺序。

举个例子，输入集为``98,9,8``，很明显当前顺序不是递增的，如果要保证顺序递增，需要在保证数组第二个元素大于第一个元素，第三个元素大于第二个元素。在不考虑题目中添加位数数量限制的情况下，``98,900,8000``这种通过保证每个元素之间总位数差为1的方案也是蛮不错的，但是如果要求增加尽可能少的位数来达到题目要求，最佳方案为``98,99,900``，也就是说我们要做到尽可能保证后一个元素的值尽可能接近前一个元素的值。

再举个例子，输入集为``1,1,1,1,1,1,1,1,1,1,1,1,1,1``，那么补全后的结果应该为``1 10 11 12 13 14 15 16 17 18 19 100 101 102``。

另外需要注意，此题有两个测试集，Test1还好，每次输入集的``X``数量以及范围都比较小，用``int``完全可以搞定，但是对于Test2来说，int不一定能够满足，当``X``的最大值为``10^9``，``N``为100时，在极端情况下（前一个元素总是比后一个元素大），最终补全后的结果的位数最大为``10 + 100 - 1 = 109``位，此时用int将会溢出导致结果错误，可以通过使用bigint或者自己将大数按位拆位数组来避免溢出的问题！

### Solve
```golang
package main

import (
	"bufio"
	"fmt"
	"math"
	"os"
	"strconv"
	"strings"
)

var res = make([]int, 0)
var in = bufio.NewReader(os.Stdin)

func solve(){
	inputs := readInts(in)
	count := 0
	nums := readInts(in)
	preMark := make([]int, 110)
	nextMark := make([]int, 110)
	for i := 0; i < inputs[0]; i ++{
		if i == 0{
			markFlush(preMark, nums[i])
			continue
		}
		preL := markLen(preMark)
		markFlush(nextMark, nums[i])
		nextL := markLen(nextMark)

		if preL > nextL {
			diff := preL - nextL
			for j := 0; j < nextL; j ++ {
				pv := preMark[j]
				nv := nextMark[j]
				if nv > pv {
					for k := 0; k < diff; k ++{
						nextMark[nextL + k] = 0
					}
					count += diff
					break
				}else if nv < pv {
					for k := 0; k < diff + 1; k ++{
						nextMark[nextL + k] = 0
					}
					count += diff + 1
					break
				}else if j == nextL - 1{
					for k := 0; k < diff; k ++{
						nextMark[nextL + k] = preMark[nextL + k]
					}
					count += diff
					for k := preL - 1; k > nextL - 1; k --{
						nextMark[k] ++
						if nextMark[k] < 10{
							break
						}
						nextMark[k] = 0
						if k == nextL {
							nextMark[preL] = 0
							count ++
						}
					}
					break
				}
			}
		}else if preL == nextL{
			for j := 0; j < preL; j ++ {
				pv := preMark[j]
				nv := nextMark[j]
				if nv > pv {
					break
				}
				if nv < pv || (nv == pv && j == preL - 1){
					count ++
					nextMark[nextL] = 0
					break
				}
			}
		}
		copy(preMark, nextMark)
	}
	res = append(res, count)
}

func markLen(mark []int) int{
	count := 0
	for ; count < len(mark); count++ {
		if mark[count] == -1 {
			break
		}
	}
	return count
}

func markFlush(mark []int, num int) {
	l := numLen(num)
	for i := 0; i < len(mark); i ++ {
		if i < l {
			mark[i] = num / pow(10, l - i - 1) % 10
		}else{
			mark[i] = -1
		}
	}
}

func pow(a, b int) int{
	return int(math.Pow(float64(a), float64(b)))
}

func numLen(num int) int{
	count := 0
	for{
		if num <= 0 {
			break
		}
		num = num / 10
		count ++
	}
	return count
}

func main()  {
	t := readInts(in)[0]
	for i := 0; i < t; i ++ {
		solve()
	}
	for i, v := range res {
		fmt.Printf("Case #%d: %d\n", i + 1, v)
	}
}

func readline(in *bufio.Reader) string{
	line, _ := in.ReadString('\n')
	return line[0:len(line) - 1]
}

func readInts(in *bufio.Reader) []int{
	line := readline(in)
	arr := strings.Split(line, " ")
	res := make([]int, 0)
	for _, str := range arr {
		v, _ := strconv.ParseInt(str, 10, 64)
		res = append(res, int(v))
	}
	return res
}

```