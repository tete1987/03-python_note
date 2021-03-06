# 八、python错误与异常
## （一）语法错误与定位
### 1.定义：什么是异常
1）错误与异常的区别：
- 错误与异常都是在程序编译和运行时出现的错误
- 异常可以被开发人员捕捉和处理
- 错误一般是系统错误，一般不需要开发人员处理（也无法处理），比如内存溢出

2）什么是异常
- 异常即是一个事件，该事件会在程序执行过程中发送，影响了程序的正常执行。
- 有些是由于拼写、配置、选项等等各种引起的程序错误，有些是由于程序功能处理逻辑不完善引起的漏洞，这些统称为程序中的异常。
![python异常](https://github.com/tete1987/picture_resource/blob/master/python%E5%BC%82%E5%B8%B8.png)

## （二）异常捕获、异常处理
### 1.异常解决方案：
- 如果是拼写、配置等引起的错误，根据出错的信息进行排查错误出现的位置进行解决
- 如果是程序设计不完善引起的漏洞，根据漏洞的情况进行设计处理漏洞的逻辑

![python异常捕获](https://github.com/tete1987/picture_resource/blob/master/python%E5%BC%82%E5%B8%B8%E6%8D%95%E8%8E%B7.png)

示例：
```
try:
    num1 = int(input("请输入一个除数"))
    num2 = int(input("请输入一个被除数"))
    print(num1/num2)

#若知道异常的名称字时捕获异常：
# except ZeroDivisionError:
#     print("被除数不可为0")

#当不知道异常的名称时，直接捕获所有异常
except:
    print("这是一个异常")

#正常执行时执行的代码
else:
    print("执行成功")

#不论是否有异常都可以执行的代码：
finally:
    print("无论是否有异常均执行")
```

## （三）自定义异常
### 1.可使用方法、类等自定义异常
示例：
```
class MyException(Exception):
    def __init__(self,num1,num2):
        self.num1 = num1
        self.num2 = num2
try:
    num1 = input("请输入一个除数")
    num2 = int(input("请输入一个被除数"))
#如果输入的第一个值不是整型数据，则捕获异常
    if not num1.isdigit():
        raise MyException("num1","num2")
    print(num1 / num2)

except MyException:
    print("输入有误")
except:
    print("这是一个异常")

```
