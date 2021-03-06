# 十一、python标准库
## 1.python标准库常见模块：
- 操作系统相关：os
- 时间与日期：time，datetime
- 科学计算：math
- 网络请求：urllib

## 2.os模块

1）os模块主要是对文件、目录操作

2）常用方法：
- os.mkdir()：创建目录
- os.removedirs()：删除文件
- os.gercwd()：获取当前目录
- od.path.exists(dir or file)：判断文件或者目录是否存在

示例1：
```python
import  os
#1.创建目录
os.mkdir("testdir")
#打印lesson下的所有文件目录，以列表形式展示
print(os.listdir("../lessons"))
```

示例2（删除目录）
```python
import  os
#1.创建目录
os.mkdir("testdir")
#打印lesson下的所有文件目录，以列表形式展示
print(os.listdir("../lessons"))
#删除目录
os.removedirs("testdir")
```

示例3（获取当前路径，展示绝对路径）：
```python
#2.获取当前路径
print(os.getcwd())
```

示例4：创建b文件夹下的一个txt文件
```python
print(os.path.exists("b"))
if not os.path.exists("b"):
    os.mkdir("b")
if not os.path.exists("b/test.txt"):
    f = open("b/test.txt","w")
    f.write("hello, os using")
    f.close()
```

## 3.time模块：获取当前时间以及时间格式的模块

导入模块：import time

1）time模块常用的方法：
- time.asctime():国外的时间格式
- time.time():时间戳
- time.sleep():等待
- time.localtime():时间戳转成时间元组
- time.strftime():将当前的时间戳转成带格式的时间
  - 格式：time.strftime("%Y-%m-%d%H-%M-%s",time.localtime())


示例1：
```python
print(time.asctime())
print(time.time())
print(time.localtime())
print(time.strftime("%Y-%m-%d %H:%M:%S",time.localtime()))
```

示例2（获取两天前的时间）：
```python
now_time = time.time()
two_day_before = now_time - 60*60*24*2
time_tuple = time.localtime(two_day_before)
print(time.strftime("%Y-%m-%d %H:%M:%S",time_tuple))
```

## 4.urllib库（用的较少）

1)python 2：
```python
imprt urllib2
response = urllib2.urlopen("http://www.baidu.com")
```

2)python3:
```python
import urllib.request
response = urllib.request.urlopen("http://www.baidu.com")
```
示例：
```python
response = urllib.request.urlopen("http://www.baidu.com")
print(response.status)
```

## 5.math库

1)math.ceil(x) :返回大于等于参数x的最小整数

2)math.floor(x):返回小于等于参数x 的最大整数

3)math.sqrt(x)：平方根

示例：
```python
print(math.ceil(5.76))
print(math.floor(5.89))
print("七的平方根是：%.4f"%math.sqrt(7))
```
