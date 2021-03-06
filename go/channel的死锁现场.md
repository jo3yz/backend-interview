# channel的死锁现场分析

## 无缓冲的channel

无缓冲的channel在单协程下，必死锁，因为无缓冲的channel的读写必须是同时进行，同时完成的，读写谁先到达就阻塞等待另外一个
现场1：

```
c1 := make(chan int)
<- c1
```

现场2：

```
c2 := make(chan int)
c2 <- 1
```

现场3：

```
c3 := make(chan int)
c3 <- 1
<- c3
```

总结，使用无缓冲的channel要注意死锁的问题，

## 有缓冲的channel

使用for range方式遍历channel之前，必须先close它，不然会死锁。但是使用三段式for不用

理由？

因为对channel的for range遍历会生成类似的代码

```go
ha := a
hv1, hb := <-ha
for ; hb != false; hv1, hb = <-ha {
    v1 := hv1
    hv1 = nil
    ...
}
```

可以看到，循环的结束条件是hb == false，而只有有缓冲channel被close之后，才会得到为false的布尔值。所以使用for range遍历一个没有close的channel，会一直阻塞在hv1, hb := <-ha上。