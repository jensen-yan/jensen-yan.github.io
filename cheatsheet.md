# 通用

## Git

## proxy

### apt
```bash
# /etc/apt/apt.conf
Acquire::http::Proxy "http://127.0.0.1:7890";
``` 
### nix
```bash
# /etc/systemd/system/nix-daemon.service.d/override.conf
[Service]
Environment=http_proxy=http://127.0.0.1:7890
Environment=https_proxy=http://127.0.0.1:7890  
```

### bash
```bash
# ~/.bashrc or ~/.zshrc
# where proxy, all use http://
proxyon () {
  export http_proxy="http://127.0.0.1:7890"
  export https_proxy="http://127.0.0.1:7890"
  export HTTP_PROXY="http://127.0.0.1:7890"
  export HTTPS_PROXY="http://127.0.0.1:7890"
  echo "HTTP Proxy on"
}
# where noproxy
proxyoff () {
  unset http_proxy
  unset https_proxy
  unset HTTP_PROXY
  unset HTTPS_PROXY
  echo "HTTP Proxy off"
}
proxyon
```

## apt
```bash
# /etc/apt/sources.list
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ noble main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ noble-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ noble-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ noble-security main restricted universe multiverse
# /etc/apt/sources.list.d/*.list
```

## gcc

## BLOG
使用github pages + Jekyll


# 命令行

## tools
tldr xxx



# 语言

## C/CPP

## Python

## Java

## matplotlib

## Bash

### 变量
```bash
name="himiao"
echo $name
greeting="hello, "$name" !" # 拼接
greeting_1="hello, ${name} !"   # 拼接
for file in $(ls); do
  echo $file
done
unset name  # 删除变量
my_array=(1 2 3)    # 数组
echo ${my_array[1]} # 数组元素
```

### args
```bash
$0: 脚本名
$1: 第一个参数
$#: 参数个数
$@: 所有参数
```

### 条件
```bash
# if-elif-else-fi
if [ -f file ]; then
  echo "file exists"
elif [ -d dir ]; then
  echo "dir exists"
else
  echo "not exists"
fi
```

## Makefile

### 参数
```makefile
$@: 目标
$^: 所有依赖
$<: 第一个依赖
test-aarch64:
test-%: himiao/% himiao/%.aot
	${GEM5} -c $(abspath $<) --aot $(abspath $(word 2,$^))
# word 2,$^: 第二个依赖

# 隐式匹配
%:%.c
    $(CC) $< -o $@
```

### wildcard
```makefile
EXECUTABLES=$(patsubst %.cpp, %, $(wildcard *.cpp))
all: ${EXECUTABLES}
# wildcard: 所有cc文件
# patsubst: 替换cc为d
```

## awk

```bash
awk '{print $1}' file   # 打印第一列
awk '/ipc/ {print FILENAME,$1}' file  # 打印包含ipc的行的文件名和第一列
awk '{ sum += $1 } END { print sum }' file  # awk for循环求和
```

# 指令集

## ARM

## x86

## RISC-V