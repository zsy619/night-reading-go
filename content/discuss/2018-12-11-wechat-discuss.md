---
title: "2018-12-11 微信讨论"
date: 2018-12-11 PM
---

来源：《Go 夜读》微信群

## go module初始化

```go

go mod init [module name]

```
如：
go mod init cmpp

## go module文件

调用go mod init会自动生成go.mod文件，使用如下：
```go

module tx

require github.com/sirupsen/logrus v1.2.0

require (
	github.com/acroca/go-symbols v0.0.0-20180523203557-953befd75e2 // indirect
	github.com/ramya-rao-a/go-outline v0.0.0-20181122025142-7182a932836a // indirect
	github.com/rogpeppe/godef v1.1.0 // indirect
	github.com/sqs/goreturns v0.0.0-20181028201513-538ac6014518 // indirect
	github.com/stamblerre/gocode v0.0.0-20181212030458-2f9d39d8f31d // indirect
)

replace (
	golang.org/x/crypto v0.0.0-20180904163835-0709b304e793 => github.com/golang/crypto v0.0.0-20181203042331-505ab145d0a9 // indirect
	golang.org/x/net v0.0.0 => github.com/golang/net v0.0.0-20181207154023-610586996380 // indirect
	golang.org/x/sys v0.0.0-20180905080454-ebe1bf3edb33 => github.com/golang/sys v0.0.0-20181213200352-4d1cda033e06 // indirect
	golang.org/x/text v0.0.0 => github.com/golang/text v0.3.1-0.20181211190257-17bcc049122f // indirect
	golang.org/x/tools v0.0.0-20181130195746-895048a75ecf => github.com/golang/tools v0.0.0-20181213210126-fe2443f7b950 // indirect
)

```

## mod版本号解读

github.com/acroca/go-symbols v0.0.0-20180523203557-953befd75e2含义：
```txt

前面部分是包的名字，也就是import时需要写的部分，而空格之后的是版本号，版本号遵循如下规律：
vX.Y.Z-pre.0.yyyymmddhhmmss-abcdefabcdef
vX.0.0-yyyymmddhhmmss-abcdefabcdef
vX.Y.(Z+1)-0.yyyymmddhhmmss-abcdefabcdef
vX.Y.Z

也就是版本号+时间戳+hash，我们自己指定版本时只需要制定版本号即可，没有版本tag的则需要找到对应commit的时间和hash值。
默认使用最新版本的package。

```
这个组合的版本号可称为伪版本号。

总不能让大家记住每个包的伪版本号吧？显然不太现实，初始化mod文件可以如下写：
```go

require github.com/sirupsen/logrus latest

```
然后运行 go run main.go ，会主动更新版本号，如果运行出现提示不支持latest，可将latest更改为master。
注：如果的确获取不到版本号，实在不知道的话（比如需要翻墙才可获取的类库），就试试v0.0.0

## mod文件格式化

使用如下命令:
```go

go mod edit -fmt

```
1.会删除融于的类库
2.自动格式化，并排序类库
注：mod文件可以有多个require，括号要与关键词隔开。

## go module下golang.org如何处理被墙

一、系统提示

go: golang.org/x/sys@v0.0.0-20180905080454-ebe1bf3edb33: unrecognized import path "golang.org/x/sys" (https fetch: Get https://golang.org/x/sys?go-get=1: dial tcp 216.239.37.1:443: i/otimeout)
go: golang.org/x/crypto@v0.0.0-20180904163835-0709b304e793: unrecognized import path "golang.org/x/crypto" (https fetch: Get https://golang.org/x/crypto?go-get=1: dial tcp 216.239.37.1:443: i/o timeout)
go: error loading module requirements

 

二、解决方案

在go.mod进行如下设置：
```go

replace (
    golang.org/x/crypto v0.0.0-20180820150726-614d502a4dac => github.com/golang/crypto v0.0.0-20180820150726-614d502a4dac
    golang.org/x/net v0.0.0-20180821023952-922f4815f713 => github.com/golang/net v0.0.0-20180826012351-8a410e7b638d
    golang.org/x/text v0.3.0 => github.com/golang/text v0.3.0
    golang.org/x/sys v0.3.0=>github.com/golang/sys v0.3.0
)

```

## go: writing stat cache:, permission denied

问题：
没有权限

处理：
```shell

sudo chown -R $(whoami):admin /Users/zhushuyan/go/pkg && sudo chmod -R g+rwx /Users/zhushuyan/go/pkg

```