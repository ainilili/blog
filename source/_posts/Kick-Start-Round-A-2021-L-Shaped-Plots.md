title: 'Kick Start Round A 2021 -  L Shaped Plots '
author: Nico
tags: []
categories:
  - Algorithm
date: 2021-03-22 04:01:00
---
原题地址: [传送门](https://codingcompetitions.withgoogle.com/kickstart/round/0000000000436140/000000000068c509)

### 分析
在二维坐标系中由很多坐标点组成段，段的最小长度为2，且段只有x轴（横轴）和y轴（纵轴）之分，求由两个段组成，垂直有共同顶点，且长边是短边两倍的组合数量。

输入集是一个二维数组，要求中的两个线段互相垂直，这里可以分析出组合中的两个线段一个在x轴一个在y轴。一种思路是通过遍历二维数组得到x和y轴所有的段，再对每个段的端点做索引，再做dfs求组合数，不过这种简单的思路在Test2会出现超时或内存溢出的错误，所以要换一种更简单的思路。

个人解决思路是先对x或y任意维度做head的索引（y轴由上到下，head为上顶点，x轴head为左顶点），例如先对y轴所有段的head做索引，这里直接使用二维数组，假设y轴段的索引定义为二维数组``indexs = [1000][1000]int``, indexs第一层下标为x轴坐标，第二层下标为y轴坐标，值为段的长度。之后再遍历获取所有的x轴段，对于x轴的每个段，取其对应的head和tail坐标以及长度len，通过枚举所有满足的y轴坐标值：
 - y1 (head.x, head.y - （len/2） + 1)
 - y2 (head.x, head.y)
 - y3 (head.x, head.y - （len*2） + 1)
 - y4 (tail.x, tail.y - （len/2） + 1)
 - y5 (tail.x, tail.y)
 - y6 (tail.x, tail.y - （len*2） + 1)

(y1和y4只有当len为偶数且大于等于4的时候才成立)
之后通过得到的y轴坐标去检索indexs，如果存在则判断对应段的长度是否满足与当前x轴段长的规则，如果满足则count ++，所有x轴段匹配完毕，输出count为结果值。
### Slove
```golang
package main

import (
	"bufio"
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
	cols := [1000][1000]int{}
	for i := 0; i < c; i ++ {
		y := -1
		for j := 0; j < r;	j ++  {
			if grid[j][i] == 1{
				if y == -1 {
					y = j
				}
			}
			if y != -1 && (grid[j][i] != 1  || j == r - 1){
				d := j - y
				if j == r - 1 && grid[j][i] == 1{
					d ++
				}
				if d > 1 {
					for di := 0; di < d; di ++ {
						for dj := di + 1; dj < d;  dj ++{
							cols[i][y + di] = dj - di + 1
						}
					}
				}
				y = -1
			}
		}
	}
	count := 0
	for i := 0; i < r; i ++ {
		x := -1
		for j := 0; j < c;	j ++  {
			if grid[i][j] == 1{
				if x == -1 {
					x = j
				}
			}
			if x != -1 &&(grid[i][j] != 1  || j == c - 1){
				d := j - x
				if j == c - 1 && grid[i][j] == 1{
					d ++
				}
				if d > 1 {
					for di := 0; di < d; di ++ {
						for dj := di + 1; dj < d;  dj ++{
							hx := x + di
							hy := i
							tx := x + dj
							ty := i
							l := dj - di + 1

							if l % 2 == 0 && l >= 4{
								chy := hy - l / 2 + 1
								cty := ty - l / 2 + 1
								if chy >= 0 {
									if n := cols[hx][chy]; n >= l / 2{
										count ++
									}
								}
								if n := cols[hx][hy]; n >= l / 2 {
									count ++
								}
								if cty >= 0 {
									if n := cols[tx][cty]; n >= l / 2{
										count ++
									}
								}
								if n := cols[tx][ty]; n >= l / 2 {
									count ++
								}
							}
							chy := hy - l * 2 + 1
							cty := ty - l * 2 + 1
							if chy >= 0 {
								if n := cols[hx][chy]; n >= l * 2 {
									count ++
								}
							}
							if n := cols[hx][hy]; n >= l * 2 {
								count ++
							}
							if cty >= 0 {
								if n := cols[tx][cty]; n >= l * 2 {
									count ++
								}
							}
							if n := cols[tx][ty]; n >= l * 2 {
								count ++
							}
						}
					}
				}
				x = -1
			}
		}
	}

	res = append(res, count)
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