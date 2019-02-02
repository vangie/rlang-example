# 函数计算 R 语言模板项目

该项目模板是一个在阿里云函数计算平台运行 R 语言的模板项目，目前阿里云函数计算平台并没有原生支持 R 语言，该项目使用 Python3  runtime，借助 rpy2 执行 R 语言。由于 Python3 runtime 中没有内置 R 语言运行时，所以该模板已经将精简过的 R 语言环境（去掉了文档和测试代码）预制在函数内部，打包后的体验大概在 35 M 左右。

## 依赖工具

本项目是在 MacOS 下开发的，涉及到的工具是平台无关的，对于 Linux 和 Windows 桌面系统应该也同样适用。在开始本例之前请确保如下工具已经正确的安装，更新到最新版本，并进行正确的配置。

* [Docker](https://www.docker.com/)
* [Fun](https://github.com/aliyun/fun)
* [Fcli](https://github.com/aliyun/fcli)

Fun 和 Fcli 工具依赖于 docker 来模拟本地环境。

对于 MacOS 用户可以使用 [homebrew](https://brew.sh/) 进行安装：

```bash
brew cask install docker
brew tap vangie/formula
brew install fun
brew install fcli
```

Windows 和 Linux 用户安装请参考：

1. https://github.com/aliyun/fun/blob/master/docs/usage/installation.md
2. https://github.com/aliyun/fcli/releases

安装好后，记得先执行 `fun config` 初始化一下配置。

**注意**, 如果你已经安装过了 fun，确保 fun 的版本在 2.10.1 以上。

```bash
$ fun --version
2.10.1
```

## 快速开始

### 初始化

使用 fun init 命令可以快捷地将本模板项目初始化到本地。

```bash
fun init vangie/rlang-example
```

### 本地测试

测试代码 index.py 的内容为：

```python
import rpy2.robjects as robjects
from rpy2.robjects import pandas2ri

def handler(event, context):  
    pandas2ri.activate()
    return str(robjects.r('paste0("1 + 1 = ", 1 + 1)'))
```

上面的代码 import 了 rpy2 ,用 R 语言执行了一个简单的加法运算。使用 `fun local` 命令可以本地测试一下函数。该步骤依赖本地环境正确安装了 docker。

```bash
$ fun local invoke onePlusOne
skip pulling image aliyunfc/runtime-python3.6:1.4.0...
['1 + 1 = 2']

RequestId: 6e1f2402-9443-4392-9f6a-d87b4f79887a 	 Billed Duration: 7543 ms 	 Memory Size: 1998 MB 	 Max Memory Used: 123 MB
```

### 部署

```bash
$ fun deploy
using region: cn-shanghai
using accountId: ***********4733
using accessKeyId: ***********KbBS
using timeout: 60

Waiting for service rlang to be deployed...
	Waiting for function onePlusOne to be deployed...
		Waiting for packaging function onePlusOne code...
		package function onePlusOne code done
	function onePlusOne deploy success
service rlang deploy success
```

### 执行

```bash
$ fcli function invoke -s rlang -f onePlusOne
['1 + 1 = 2']
```

## 编译 R 语言

预编译好的 R 语言环境、rpy2 库以及相关的 apt 依赖文件已经放置在 [.fun](https://github.com/vangie/rlang-example/tree/master/%7B%7BprojectName%7D%7D/.fun) 目录下了，正常使用不用自行编译 R 语言，编译一次在 MacBook Pro 15 上大概需要半个小时左右。假如当前的 R 语言的编译选项不满足业务需求可以参考一下 [fun.yml](https://github.com/vangie/rlang-example/blob/master/%7B%7BprojectName%7D%7D/fun.yml) 文件，该文件有完整的编译和安装方法，进行适当调整后使用 `fun install` 命令安装即可。

## 参考阅读

1. https://support.rstudio.com/hc/en-us/articles/218004217-Building-R-from-source
2. https://cran.r-project.org/sources.html
3. https://rpy2.readthedocs.io/en/version_2.8.x/