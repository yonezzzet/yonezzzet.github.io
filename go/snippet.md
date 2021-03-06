
# Go Code Snippet


## 標準入力から１行読み込み

```go
var sc = bufio.NewScanner(os.Stdin)

func nextLine() string {
	sc.Scan()
	return sc.Text()
}
```

## スタック/Stack

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

## スタック(interface{})

```go
type Stack []interface{}

func (s *Stack) push(val interface{}) {
	*s = append(*s, val)
}

func (s *Stack) peek() (val interface{}, err error) {
	l := len(*s)
	if l > 0 {
		val = (*s)[l-1]
		return
	} else {
		err = fmt.Errorf("stack is empty")
		return
	}
}

func (s *Stack) pop() (val interface{}, err error) {
	l := len(*s)
	if l > 0 {
		*s, val = (*s)[:l-1], (*s)[l-1]
		return
	} else {
		err = fmt.Errorf("stack is empty")
		return
	}
}
```

## キュー/Queue

```go
package main

import "fmt"

func main() {
	q := make(Queue, 0)
	fmt.Println(q)
	q.enq(10)
	q.enq(20)
	fmt.Println(q)
	for {
		v, err := q.deq()
		if err != nil {
			fmt.Println("end")
			break
		}
		fmt.Println(v)
	}
}

type Queue []int

func (q *Queue) enq(val int) {
	*q = append(*q, val)
}

func (q *Queue) deq() (val int, err error) {
	l := len(*q)
	if l > 0 {
		val, *q = (*q)[0], (*q)[1:]
		return
	} else {
		err = fmt.Errorf("queue is empty")
		return
	}
}
```
