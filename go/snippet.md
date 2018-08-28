
# Go Code Snippet

## スタック/Stack

<script src="https://gist.github.com/yonezzzet/4ebc9cedb91be0b9a6f84f353a85667e.js"></script>

```go
package main

import "fmt"

func main() {
	stack := make(Stack, 0)
	fmt.Println(stack)
	stack.push(10)
	stack.push(11)
	stack.push(12)
	fmt.Println(stack)
	for {
		v, err := stack.pop()
		if err != nil {
			fmt.Println("end")
			break
		}
		fmt.Println(v)
	}
	fmt.Println(stack)
}

type Stack []int

func (s *Stack) push(val int) {
	*s = append(*s, val)
}

func (s *Stack) pop() (val int, err error) {
	l := len(*s)
	if l > 0 {
		*s, val = (*s)[:l-1], (*s)[l-1]
		return
	} else {
		return -1, fmt.Errorf("stack is empty")
	}
}
```
