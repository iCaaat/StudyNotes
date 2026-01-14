## 1. **基本语法**

### 1.1 注释

```bat
REM 这是一个注释
:: 这也是注释
```

### 1.2 打印输出

```bat
echo Hello World
```

- 关闭回显（不显示命令本身）：

```bat
@echo off
```

------

## 2. **变量**

### 2.1 设置变量

```bat
set name=James
```

### 2.2 使用变量

```bat
echo My name is %name%
```

### 2.3 读取用户输入

```bat
set /p username=请输入你的名字：
echo 你好，%username%
```

------

## 3. **条件判断**

```bat
set /p age=请输入你的年龄：
if %age% GEQ 18 (
    echo 你已成年
) else (
    echo 你未成年
)
```

常见判断符号：

| 符号 | 含义     |
| ---- | -------- |
| EQU  | 等于     |
| NEQ  | 不等于   |
| LSS  | 小于     |
| LEQ  | 小于等于 |
| GTR  | 大于     |
| GEQ  | 大于等于 |

------

## 4. **循环**

### 4.1 for 循环（遍历文件）

```bat
for %%i in (*.txt) do (
    echo 发现文件 %%i
)
```

### 4.2 指定范围循环

```
for /l %%i in (1,1,5) do (
    echo 第 %%i 次
)
```

解释：`(起始,步长,结束)`

------

## 5. **跳转与标签**

```bat
:menu
echo 1. 选项1
echo 2. 选项2
set /p choice=请选择：
if %choice%==1 goto option1
if %choice%==2 goto option2
goto menu

:option1
echo 这是选项1
goto menu

:option2
echo 这是选项2
goto menu
```

------

## 6. **运行其他程序**

```bat
start notepad.exe
```

等待程序执行完成：

```bat
call myscript.bat
```

------

## 7. **常用小技巧**

- **延迟变量扩展**（变量在循环中变化时）

```bat
setlocal enabledelayedexpansion
set num=0
for %%i in (*.txt) do (
    set /a num+=1
    echo 文件编号 !num! ：%%i
)
endlocal
```

- **管道与重定向**

```bat
dir > list.txt   :: 输出到文件
dir >> list.txt  :: 追加到文件
find "关键字" file.txt
```

---

实战解析

```bat
```

