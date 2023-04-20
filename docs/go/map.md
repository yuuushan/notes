# Go Map

Map 是一种无序的键值对的集合。通过哈希表实现。

## 1 定义 Map

```go
// 声明一个 map，但没有分配空间。此时 m 等于 nil
var m map[key_type]value_type

// 使用 make() 创建 map
var m map[key_type]value_type := make(map[key_type]value_type)
m                             := make(map[key_type]value_type)

// 声明一个 map 并初始化
m := map[string]int {
    "item1": 1,
    "item2": 2,
}
```

key 的 type 通常是数字、string（不可以是 slice、map、function）

value 的 type 可以是数字、string、map、struct。

## 2 判断 map 中 key 是否存在

```go
_, ok := m["key"]
// ok 为 true 则 key 存在，为 false 则 key 不存在
```

## 3 删除 map 中的元素

使用`delete()`函数可以通过 key 来删除集合中的元素。如果 key 操作，删除该键值对；如果 key 不存在，不操作也不报错。

用法如下：

```go
delete(m, "key")
```

## 4 遍历 map 中的内容

```go
for key, value := range m {
    fmt.Println(key, value)
}
```
