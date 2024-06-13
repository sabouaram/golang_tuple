# golang_tuple
A Golang tuple type definition

```
package main

import (
	"fmt"
	"reflect"
)

type tuple struct {
	elements []any
}

type tupleElement struct {
	index int
	value any
	typ   reflect.Type
}

func Tuple(elements ...any) *tuple {
	return &tuple{elements: elements}
}

func (t *tuple) len() int {
	return len(t.elements)
}

func (t *tuple) get(index int) (any, reflect.Type) {
	if index < 0 || index >= len(t.elements) {
		panic("index out of range")
	}
	return t.elements[index], reflect.TypeOf(t.elements[index])
}

func (t *tuple) valueAt(index int) any {
	value, _ := t.get(index)
	return value
}

func (t *tuple) typeAt(index int) reflect.Type {
	_, typ := t.get(index)
	return typ
}

func (t *tuple) Iterate() <-chan tupleElement {
	ch := make(chan tupleElement)
	go func() {
		for i, elem := range t.elements {
			ch <- tupleElement{i, elem, reflect.TypeOf(elem)}
		}
		close(ch)
	}()
	return ch
}

func (t *tuple) String() string {
	result := "tuple{"
	for i, elem := range t.elements {
		result += fmt.Sprintf("%v (type: %s)", elem, reflect.TypeOf(elem))
		if i < len(t.elements)-1 {
			result += ", "
		}
	}
	result += "}"
	return result
}
////////////////////////////////////////////////Example////////////////////////////////////////////////////////////////
func main() {

	t := Tuple("Alice", 30, "Engineer", true, 42.0)

	fmt.Printf("t[0] = %v (type: %s)\n", t.valueAt(0), t.typeAt(0))
	fmt.Printf("t[1] = %v (type: %s)\n", t.valueAt(1), t.typeAt(1))

	for elem := range t.Iterate() {
		fmt.Printf("t[%d] = %v (type: %s)\n", elem.index, elem.value, elem.typ)
	}
	
	fmt.Println(t)

}
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////

```

### To be done:  
1- Compare(tuple1, tuple2 tuple) => to compare   
2 tuples to be supported to compare with order or without order  
2- Delete(index) ,  DeleteLast()  
3- Append()  
4- AddAfter(index)  
5- AddBefore(index)

