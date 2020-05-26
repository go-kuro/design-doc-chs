# mod设计文档

`github.com/go-kuro/kuro/mod`

## 目标

在`kuro build`的过程中，可以通过解析go.mod和go.sum文件，获取所有涉及到的包的物理文件地址(一般为github.com的包地址)，
方便后续按需加载(暂时不确定是手动加载还是自动加载)

## 实现思路

分两种情况：

第一种 自动解析go.mod和go.sum

Step 1.检查go files，更新一波go.mod和go.sum文件，看下是否有包漏加；有需要的话，进行一次write in mod

Step 2.解析go.mod和go.sum文件；其中，go.mod主要需要解析path，比如`golang.org/x/text`，go.sum主要解析version

Step 3.用得到的path和version生成`[]Version`

```
type Version struct {
    Path string
    Version string `json:",omitempty"`
}
```

Step 4.使用得到的`[]Version`，load对应的包

## API Documentation

参数说明：

`ParseGoMod(cmd kuro.Command, args []string, version *Version)`

   cmd：kuro的命令结构体

   args：kuro的命令参数

   version：是否指定加载某个包的某个版本，为Version结构体的指针

## 接口

粗略的接口实现如下：

```
import "cmd/go/internal/modload"

func ParseGoMod(cmd kuro.Command, args []string, version *Version) {
    // 解析go.mod和go.sum文件得到buildList
    var buildList []Version
    if version == nil || version == &Version{} {
        buildList = modload.LoadBuildList()
    } else if version.Path != "" {
        buildList = []Version{
            {Path:version.Path, Version:version.Version}
        }
    } else {
        throw("...")
    }

    // 改写modget.runGet函数, 主体流程不变
    modget.runGet(cmd, args, buildList)
}
```
