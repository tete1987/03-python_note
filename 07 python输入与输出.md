# 七、python输入与输出
## （一）字面量打印与格式化
1.字面量定义：字面量（literal）是以变量或常量给出的原始数据。在程序中可以直接使用字面量。

1）字面量的类型：
- 数值型
- 字符型
- 布尔型
- 字面量集合：列表（list）、元组（tuple）、字典（dict）和集合（set）
- 特殊字面量：None

2.字面量插值：是指将变量、常量以及表达式插入的一种技术，它可以避免字符串拼接的问题，很多语言都支持了此功能。

1）字面量插值方法：
- 格式化输出
- 通过string.format()方法拼接
- 通过F-string拼接，一种字符串格式化机制。

2）格式化输出的 % 的用法
|转换符说明| 解释|
|--|--|
|%d，%i| 转换为带符号的十进制整数|
|%o |转换为符号的八进制整数|
|%x，%X |转换为带符号的十六进制整数|
|%e |转化为科学技术法表示的浮点数（e小写）|
|%E| 转化为科学计数法表示的浮点数（E大写）|
|%f，%F| 转化为十进制浮点数|
|%g |智能选择使用%f 或 %e格式|
|%G |智能选择使用%F或%E格式|
|%c| 格式化字符及其ASCII 码|
|%r |使用repr()函数将表达式转换为字符串|
|%s| 使用str()函数将表达式转换为字符串|

示例1：
```
str = "my name is %s,my age is %d"%(tete,20)
name = "Tom"
print(str)
```

示例2：
```
print("pi= %.2f"%3.1415926)
```
注：%.2f 是保留2位小数

3）format()方法：

用法：str.format() 

字符串：
```
name = 'tom'
name2 = 'jerry'
print('two boy are {} and {}'.format(name,name2))
print('two boy are {0} and {1},{0} is tomm,{1} is jerry'.format(name,name2))
```

列表：
```
list1 = ['tom',20]
print("my name is {0},age is {1}".format(*list1))
```

字典：
```
dic1 = {"name":"tom","age":18}
print("my name is {name},age is {age}".format(**dic1))
```

## （二）文件读取
1.定义：文件读写就一种常见的I/O操作，操作I/O的能力是由操作系统提供的，而现在的操作系统不允许普通程序直接操作磁盘，所以读写文件时要请求操作系统打开一个对象，这就是我们在程序中要操作的文件对象。

2.不同的编程语言读写文件的操作步骤大体都是一样的，都分为以下几个步骤：

第一步：打开文件，获取文件描述符

第二步：操作文件描述符（读/写）

第三步：关闭文件

注意：文件读写操作完成后，应及时关闭。

示例：
```
f = open('data/txtfile','rt')
print(f.read())
print(f.readale())
f.close
```
注：描述符有很多种，主要常用的有“r，t”，分别为“读，写”模式。readable为是否可读。

示例2：
```
f = open('data/txtfile','rt')
print(f.readlines())

f.close
```
注：readlines 返回的是列表模式

示例3（更简洁的写法）：
```
with open('data/txtfile','rt')
   print(f.readlines())
```
注：该种写法可直接关闭。

2）open：

模式：open(file,mode='r',buffering=-1,encoding=None,errors=None,newline=None,closed=True，pener=None)

参数说明：
- name：文件名称的字符串值
- mode：只读r ， 写入w，追加a，默认文件访问模式为只读（r）
- buffring：寄存区缓冲
- 0：不寄存
- 1：访问文件时会寄存
- `>`1：寄存区的缓冲大小
- 负值：寄存区的缓冲大小则为系统默认。

## （三）json格式转换

1.定义：什么是json
- json是一种非常流行的，轻量级的数据交换格式。
- json对人友好，易于读写（比xml，protobuf要好）
- json对机器人友好，易于解析和生成
- json是由列表和字典组成的

2.使用场景：
- 生成：将对象生成为字符串，存入文件、数据库、在网络传输等
- 解析：解析来自文件，数据库，网络传输的字符串成python对象
- 跨语言的数据交换：比如python 和 c/c++/java/javascript的数据交换

3.json使用方式：

1）如何使用json？(json是标准库)

`import json`

2）常用的几种方式：
- json.dumps(python_obj)：把数据类型转换成字符串
- json.loads(json_string)：把字符串转换成json
- json.dump()：把数据类型转换成字符串并存储在文件中
- json.load(file_stream)：把文件打开，里面的字符串转换成数据类型

示例1：
```
import json
info = [{
    "name":"Tom",
    "gender":"male",
    "other":None
},{
    "name":"Jack",
    "gender":"male",
    "other":None
}]

#dumps：将pyhton中的字典，转换为字符串
data = json.dumps(info,sort_keys=True,indent=4)
print(data)
print(type(data))
```
注：sort_keys 是以key值进行排序的意思，indent是缩进

示例2（将json转换为字符串，存储到文件里）：
```
import json
info = [{
    "name":"Tom",
    "gender":"male",
    "other":None
},{
    "name":"Jack",
    "gender":"male",
    "other":None
}]

print("读取json文件")
#第一种写法：
# with open('json_dump.json','w') as f:
#     json.dump(info,f)

#第二种写法，不带关闭
json.dump(info,open("json","w"))

```
注：json.dump  是转换为字符串之后，存储到一个文件里。json.dumps 是对数据做类型转换，转换为字符串。

示例3（load,把文件打开从字符串转换为json）：
```
使用上一示例中创建的文件：
js_obj = json.load(open("json","r"))
print(js_obj)
print(type(js_obj))
#取其中的某个值：
print(js_obj[0]["name"])
print(js_obj[1]["gender"])

```
