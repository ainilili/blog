title: Kick Start Round A 2021 -  K-Goodness String
author: Nico
tags:
  - Kick Start
categories:
  - Algorithm
date: 2021-03-22 02:37:00
---
原题地址: [传送门](https://codingcompetitions.withgoogle.com/kickstart/round/0000000000436140/000000000068cca3#problem)

### 分析
题意大概是当字符串对称位置的字母如果不相同则score加一分，输入字符串以及期望的score分数，求将当前字符串变动任意位置的字母使之score等于期望的socre。

其实就是求字符串对称位置字符不相同的数量，然后和期望score做差再取abs。
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
	n := inputs[0]
	k := inputs[1]
	str := readline(in)
	s := 0

	for i := 0; i < len(str); i++ {
		r := n - 1 - i
		if r <= i {
			break
		}
		if str[i] != str[r] {
			s ++
		}
	}
	result := k - s
	if result < 0{
		result = - result
	}
	res = append(res, result)
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