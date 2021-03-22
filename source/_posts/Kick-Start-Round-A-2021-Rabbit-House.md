title: Kick Start Round A 2021 -  Rabbit House
author: Nico
tags:
  - Kick Start
categories:
  - Algorithm
date: 2021-03-22 12:33:00
---
原题地址: [传送门](https://codingcompetitions.withgoogle.com/kickstart/round/0000000000436140/000000000068cb14)

### 分析
对于英文弱鸡的笔者，这道题着实看了很久才看懂题意，同样在一个二维平面内存在着``R * C``的格子，且每个格子都会有``0≤ Gi,j ≤2*10^6``各盒子，例如：
```
0 0 0
0 2 0
0 0 0
```
上述表示R为3，C为3的格子，中间格子的盒子数量为2，其余格子盒子数量为0。

描述到此为止，最终问题是求输入的R*C的格子中，如果要保证每个格子与相邻格子的盒子数量差在1以内，需要在当前规格的基础上，至少增加多少个盒子？

因为最终要保证整个平面内，所有的格子高度差绝对值 <= 1，所以应该从最高的格子开始，让其相邻格子条件，之后除去当前格子，再取剩下格子中最高的格子循环处理，直到当前最高的格子高度为1时结束循环，将新增的盒子数作为答案输出。

分析后的问题转化为将格子从高到低排列，并优先取出最高的格子做处理，并且格子的高度排列是会随着处理过程发生变化，所以使用优先队列处理之！

### Solve
```golang
package main

import (
	"bufio"
	"container/heap"
	"fmt"
	"os"
	"strconv"
	"strings"
)

var res = make([]int, 0)

var in = bufio.NewReader(os.Stdin)

func solve(){
	inputs := readInts(in)
	r := inputs[0]
	c := inputs[1]
	grid := make([][]int, r)
	for i := 0; i < r; i ++ {
		grid[i] = readInts(in)
	}

	nodes := &nodes{}
	for i := 0; i < r; i ++ {
		for j := 0; j < c; j++ {
			heap.Push(nodes, node{
				val: grid[i][j],
				x: j,
				y: i,
			})
		}
	}
	m := 0
	for ;;{
		if nodes.Len() == 0{
			break
		}
		n := heap.Pop(nodes).(node)
		if n.val != grid[n.y][n.x] {
			continue
		}
		if n.x > 0 {
			if n.val - grid[n.y][n.x - 1] > 1{
				m += n.val - grid[n.y][n.x - 1] - 1
				grid[n.y][n.x - 1] = n.val - 1
				heap.Push(nodes, node{
					val: grid[n.y][n.x - 1],
					x: n.x - 1,
					y: n.y,
				})
			}
		}
		if n.x < c -1 {
			if n.val - grid[n.y][n.x + 1] > 1{
				m += n.val - grid[n.y][n.x + 1] - 1
				grid[n.y][n.x + 1] = n.val - 1
				heap.Push(nodes, node{
					val: grid[n.y][n.x + 1],
					x: n.x + 1,
					y: n.y,
				})
			}
		}
		if n.y > 0 {
			if n.val - grid[n.y - 1][n.x] > 1{
				m += n.val - grid[n.y - 1][n.x] - 1
				grid[n.y - 1][n.x] = n.val - 1
				heap.Push(nodes, node{
					val: grid[n.y - 1][n.x],
					x: n.x,
					y: n.y - 1,
				})
			}
		}
		if n.y < r - 1 {
			if n.val - grid[n.y + 1][n.x] > 1{
				m += n.val - grid[n.y + 1][n.x] - 1
				grid[n.y + 1][n.x] = n.val - 1
				heap.Push(nodes, node{
					val: grid[n.y + 1][n.x],
					x: n.x,
					y: n.y + 1,
				})
			}
		}
	}
	res = append(res, m)
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

type node struct {
	val int
	x int
	y int
}

type nodes []node

func (t *nodes) Len() int {
	return len(*t) //
}

func (t *nodes) Less(i, j int) bool {
	return (*t)[i].val >= (*t)[j].val
}

func (t *nodes) Swap(i, j int) {
	(*t)[i], (*t)[j] = (*t)[j], (*t)[i]
}

func (t *nodes) Push(x interface{}) {
	*t = append(*t, x.(node))
}

func (t *nodes) Pop() interface{} {
	n := len(*t)
	if n > 0 {
		x := (*t)[n-1]
		*t = (*t)[:n-1]
		return x
	}
	return -1
}

func (t *nodes) Peek() interface{} {
	n := len(*t)
	if n > 0 {
		return (*t)[0]
	}
	return -1
}

```