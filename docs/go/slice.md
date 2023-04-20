# Go Slice

Slice（切片）可以理解为不固定长度的数组。它的类型和操作比数组更加灵活。Slice 是数组的一个引用。

## 1 结构组成

`src/runtime/slice.go` 中 slice 的结构如下：

```go
type slice struct {
	array unsafe.Pointer
	len   int
	cap   int
}
```

其中有三个元素：

- `array` 指向一个数组的指针。
- `len` 长度，是 slice 的可见元素的个数。使用 `len()` 函数可以获取切边的长度。
- `cap` 容量，表示 slice 指向的内存空间的最大容量（对应元素的个数，而不是字节数）。使用 `cap()` 函数可以获取 slice 的容量。

访问 slice 中元素时，如果访问的元素超过 slice 长度程序会报错。

向 slice 追加元素时，如果 slice 总元素数超过 slice 容量，slice 会自动扩容。

数组的长度和容量是一样的。

## 2 定义 slice

```go
// 声明一个 slice，但没有分配空间。此时 s 等于 nil
var s []type

// 使用 make() 创建 slice，指定了 slice 的 length（capacity 为可选参数）
var s []type = make([]type, length)
s           := make([]type, length)
s           := make([]type, length, capacity)

// 声明一个 slice 并初始化
s := []int{1, 2, 3}

// 从数组 arr 初始化 slice
s := arr[:]
s := arr[2:4]

// 从 slice s 初始化 slice
s2 := s[2:4]
```

## 3 向 slice 追加元素

向 slice 追加元素使用内置的 `append()` 函数。如果追加后总元素数超过 slice 的容量，slice 会自动扩容。

用法如下：

```go
s = append(s, 1)                 // 向 slice s 追加一个元素
s = append(s, 1, 2)              // 向 slice s 追加两个元素
s = append(s, []int{1, 2, 3}...) // 追加一个 slice，slice 需要使用...运算符
s = append([]int{1, 2, 3}, s...) // 向 slice 前面追加元素
```

Slice 后使用 `...` 运算符会把 slice 打散作为参数传递。

需要注意的是，在 slice 容量不足的情况下，append 的操作会导致重新分配内存和数据复制。

> https://golang.org/ref/spec#Appending_and_copying_slices

## 4 遍历 slice 元素

使用内置的 `range()` 函数可以遍历 slice 元素。用法如下：

```go
for _, item := range s {
	fmt.Println(item)
}
```
