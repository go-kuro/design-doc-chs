# mod设计文档
`github.com/go-kuro/kuro/mod` 

## 目标
在`kuro build`的过程中，解析go.mod和go.sum文件，获取所有涉及到的包，并加载到内存中

## 流程
大致流程如下(这里后面找一个图工具)：
Step 1.命令
	`kuro build`

Step 2.绑架`go build`，在`go build`之前，先使用`go mod`流程把所涉及的相关包更新到最新

Step 3.根据`go mod`包的逻辑，将go.mod文件中涉及的包，以及go.sum中涉及到的对应包的包版本，下载并倒入到内存

## TODO
1.抽取`go build`中更新go.mod和go.sum的流程，用以更新这两个文件
2.这里有一个小问题
```
	在go build的时候，两个文件中对应的包，应该都已经在内存中了，然后才能很好的参与编译<个人猜想>，否则可能会造成package not find问题
```
3.这里提供目前想到的两种实现方式
```
	idea 1.截取go build流程中的go mod流程+倒入内存流程
```
```
	idea 2.自己根据go mod再造个轮子
```


