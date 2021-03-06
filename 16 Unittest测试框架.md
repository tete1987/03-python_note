# 十六、Unittest测试框架
## （一）单元测试概述
### 1.什么是单元测试？
单元测试是开发者编写的一小段代码，用于检验被测代码的一个很小的、很明确的功能是否正确。通常而言，一个单元测试是用于判断某个特定条件（或者场景）下某个特定函数的行为。

2.单元测试需要注意：

单元测试的时候一个大前提就是需要清楚的知道，自己要测试的程序块所预期的输入和输出，然后根据这个预期和程序逻辑来书写case。这里的预期结果一定要针对需求/设计的逻辑去写，而不是针对程序的实现去写，否则单元测试失去了意义，照着错误的实现设计出的case也很可能是错误的。

3.单元测试框架列举及国内大厂现状
- Unittest：Python内置的标准类库。它的API跟java 的Junit、.net的Nunit，C++的CppUnit很相似。
- Pytest：丰富、灵活的测试框架，语法简单，可以结合allure生成一个酷炫的测试报告，现在比较主流。
- Nose：Nose是对unittest的扩展，使得python的测试更加简单。
- Mock：unittest.mock是用来测试python的库。这个是一个标准库（出现在python3.3版本以后）
- 
4.单元测试覆盖率

1）代码覆盖率也被用于自动化测试和手工测试来度量测试是否全名的指标之一，应用覆盖率的思想增加测试用例的设计。

2）单元测试覆盖率类型：
- 语句覆盖
- 条件覆盖
- 判读覆盖
- 路径覆盖

示例：
被测代码片段：
```python
def demo_mathod(a,b,x):
    if (a>1 and b==0):
          x = x/a
    if (a == 2 or x>1):
          x = x+1  
```

1)语句覆盖：
- 通过设计一定量的测试用例，保证被测试的方法每一行代码都会执行一遍
- 运行测试用例的时候被击中的代码即成为被覆盖的语句

测试用例：
- 仅需要一条case，即可实现行覆盖，a = 3,b= 0 ,x=3
- 漏洞：and ->or 
- 行覆盖是一个最基础的覆盖方式，但是也是最薄弱的，如果完全依赖行覆盖，会出现很严重的问题

2）判断覆盖

定义：运行测试用例的过程被击中的判定语句

测试用例：
|TestCase| a b x| a>1 and b==0 |a == 2 or x>1| ExecutePath
|--|--|--|--|--|
|Case1| 2 0 3 |T |T| 135|
|Case2| 1 0 1|F| F| 124|
|Case3| 3 0 3 |T| F| 134|
|Case4 |2 1 1|  F |T |125|

漏洞：
大部分的判定语句是由多个逻辑条件组合而成的，若仅仅判断其整个最终结果，二忽略每个条件的取值情况，必然会遗漏部分测试路径，
```a ==2 或 x>1 → a==2或x<1```

3)条件覆盖

定义：条件覆盖和判断覆盖类似，不过判定覆盖关注整个判定语句，而条件覆盖则关注某个判断条件。

测试用例: if(a>1 and b==0)
|TestCase| a>1| b==0|
|--|--|--|
|Case1| T| T| 
|Case2| T | F |
|Case3| F|  T |
|Case4| F | F |

缺陷：
测试用例指数级增加（2**conditions）

4）路径覆盖

定义：覆盖所有可能执行的路径

测试用例：
|TestCase| a b x| ExecutePath|
|--|--|--|
|Case1| 2 0 3| 135|
|Case2| 1 0 1| 124|
|Case3| 3 0 3 |134|
|Case4 |2 1 1|  125|


![unittest1](https://github.com/tete1987/picture_resource/blob/master/python%E5%9B%BE/unittest1.png)


应用这些方法设计测试用例。

## （二）unittest测试框架
1.简介：
- python自带的单元测试框架，常用在单元测试
- 在自动化测试中提供用例组织与执行
- 提供丰富的断言方法-验证函数等功能
- 加上HTMLTestRunner可以生成html的报告
- 现在依然有一些公司在用这个框架

2.编写与规范
- Unittest提供了test cases、test suites、test fixtures、test runner相关的组件
- 编写规范：
    - 测试模板首先import unittest
    - 测试类必须继承 unittest.TestCase
    - 测试方法必须以“test_”开头
    - 模块名字，类名没有特殊要求

3.测试框架结构：

1）setUp用来为测试准备环境，tearDown用来清理环境。

2）如果想要在所有case执行之前准备一次环境，并在所有case执行结束之后再清理环境。我们可以用setUPClass() 与 tearDownClass()；比如：数据库连接及销毁。

3）如果想有些方法不再本次执行中，使用： @unittest.skip

4）测试方法的命名：以test开头

5）各种执行-单一用例，全部


## （三）unittest实战
示例1：
```python
import unittest
class Demo(unittest.TestCase):
    def setUp(self) -> None:
        print("开始")

    def tearDown(self) -> None:
        print("结束")

    def test_demo01(self):
        print("test_demo01")
        self.assertEqual(1,2,"判断相等")

if __name__ == '__main__':
    unittest.main()
```

示例2：
（setUp和tearDown会在没条测试用例执行完都执行，因此可加入setUpClass，在class中所有方法执行时只执行一次，但使用setUpClasss时是需要加@classmethod）
```python
import unittest
class Demo(unittest.TestCase):
    @classmethod
    def setUpClass(cls) -> None:
        print("这是setUpClass")

    @classmethod
    def tearDownClass(cls) -> None:
        print("这是tearDownClass")

    def setUp(self) -> None:
        print("开始")

    def tearDown(self) -> None:
        print("结束")

    def test_demo01(self):
        print("test_demo01")
        self.assertEqual(1,2,"判断相等")

    def test_demo02(self):
        print("test_demo02")
        self.assertEqual(2,2,"判断相等")

if __name__ == '__main__':
     unittest.main()
```

示例3：（跳过某个测试用例）
```python
import unittest
class Demo(unittest.TestCase):
    @classmethod
    def setUpClass(cls) -> None:
        print("这是setUpClass")

    @classmethod
    def tearDownClass(cls) -> None:
        print("这是tearDownClass")

    def setUp(self) -> None:
        print("开始")

    def tearDown(self) -> None:
        print("结束")

    def test_demo01(self):
        print("test_demo01")
        self.assertEqual(1,2,"判断相等")

    def test_demo02(self):
        print("test_demo02")
        self.assertEqual(2,2,"判断相等")

    @unittest.skipIf(1+1==2,"跳过这条用例")
    def test_demo03(self):
        print("test_demo03")
        self.assertIn('h', 'hello')

if __name__ == '__main__':
    unittest.main()
```

1.unittest断言
用unittest组件测试用例的时候，断言的方法还是很多的，下面介绍几种常用的断言方法：assertEqual,aeertIn,asserTrue

2.基本断言方法：基本的断言方法提供了测试结果是True还是FALSE，所有的断言方法都有一个msg参数，如果指定msg参数的值，则将该信息作为失败的错误信息返回。

|序号 |断言方法 |断言描述|
|--|--|--|
|1 |assertEqual(arg1,arg2,msg=None)| 验证arg1=arg2，不等则fail|
|2| assertNotEqual(arg1,arg2,msg=None)| 验证arg1！=arg2，相等则fail|
|3 |assertTrue(expr,msg=None)| 验证expr是true，如果为false ，则fail|
|4 |assertFalse(expr,msg=None)| 验证expr是false ，如果为true，则fail
|5 |assertIs(arg1,arg2,msg=None)| 验证arg1、arg2是同一个对象，不是则fail|
|6 |assertIsNot(arg1,arg2,msg=None)| 验证arg1、arg2不是同一个对象，如果是则fail|
|7| assertIsNone(expr,msg=None)| 验证expr是否为None，如果不是则fail
|8 |assertIsNotNone(expr,msg=None)| 验证expr是否为None，如果是则fail|
|9 |assertIn(arg1,arg2,msg=None)| 验证arg1是arg2的字串，不是则fail|
|10 |assertNotIn(arg1,arg2,msg=None)| 验证arg1不是arg2的字串，如果是则fail|
|11| assertIsInstance(obj,cls,msg=None)| 验证obj是cls的实例，不是则fail|
|12 |assertNotIsInstance(obj,cls,msg=None)| 验证obj不是cls的实例，是则fail|

3.unittest执行测试用例：

1）多个测试用例集合就是测试套件，通过测试套件来管理多个测试用例。

2）执行方法一：unittest.main() (执行所有以test开头的测试用例)

3）执行方法二：加入容器中执行
- suite=unittest.TestSuite()
- suite.addTest(TestMethod("test_01"))
- suite.addTest(TestMethod("test_02"))
- unittest.TextTestRunner().run(suite)

示例：
```python
#4.加入测试套件
class Demo(unittest.TestCase):
    @classmethod
    def setUpClass(cls) -> None:
        print("这是setUpClass")

    @classmethod
    def tearDownClass(cls) -> None:
        print("这是tearDownClass")

    # def setUp(self) -> None:
    #     print("开始")
    #
    # def tearDown(self) -> None:
    #     print("结束")

    def test_demo01(self):
        print("test_demo01")
        self.assertEqual(1, 2, "判断相等")

    def test_demo02(self):
        print("执行test_demo02")
        self.assertEqual(2, 2, "判断相等")

    # @unittest.skipIf(1 + 1 == 2, "跳过这条用例")
    def test_demo03(self):
        print("执行test_demo03")
        self.assertIn('h', 'hello')


class Demo2(unittest.TestCase):
    @classmethod
    def setUpClass(cls) -> None:
        print("这是Demo2 的 setUpClass")

    @classmethod
    def tearDownClass(cls) -> None:
        print("这是Demo2 的tearDownClass")

    def test_Demo2_case1(self):
        print("执行test_Demo2_case1")
        self.assertIn('h', 'hello')


if __name__ == '__main__':
    suit = unittest.TestSuite()
    suit.addTest(Demo("test_demo03"))
    suit.addTest(Demo2("test_Demo2_case1"))
    unittest.TextTestRunner().run(suit)

```
![unittest2](https://github.com/tete1987/picture_resource/blob/master/python%E5%9B%BE/unittest2.png)

![unittest3](https://github.com/tete1987/picture_resource/blob/master/python%E5%9B%BE/unittest3.png)


4）执行方法三：此用法可以同时测试多个类
```python
suite1=unittest.TestLoader().loadTestsFromTestCase(TestCase1)
suite2=unittest.TestLoader().loadTestsFromTestCase(TestCase2)
suite=unittest.TestSuite([suite1,suite2])
unittest.TextTestRunner(verbosity=2).run(suite)
```

示例：
```python
class Demo(unittest.TestCase):
    @classmethod
    def setUpClass(cls) -> None:
        print("这是setUpClass")

    @classmethod
    def tearDownClass(cls) -> None:
        print("这是tearDownClass")

    # def setUp(self) -> None:
    #     print("开始")
    #
    # def tearDown(self) -> None:
    #     print("结束")

    def test_demo01(self):
        print("test_demo01")
        self.assertEqual(1, 2, "判断相等")

    def test_demo02(self):
        print("执行test_demo02")
        self.assertEqual(2, 2, "判断相等")

    # @unittest.skipIf(1 + 1 == 2, "跳过这条用例")
    def test_demo03(self):
        print("执行test_demo03")
        self.assertIn('h', 'hello')


class Demo2(unittest.TestCase):
    @classmethod
    def setUpClass(cls) -> None:
        print("这是Demo2 的 setUpClass")

    @classmethod
    def tearDownClass(cls) -> None:
        print("这是Demo2 的tearDownClass")

    def test_Demo2_case1(self):
        print("执行test_Demo2_case1")
        self.assertIn('h', 'hello')


if __name__ == '__main__':
    suite1 = unittest.TestLoader().loadTestsFromTestCase(Demo)
    suite2 = unittest.TestLoader().loadTestsFromTestCase(Demo2)
    suite = unittest.TestSuite([suite1, suite2])
    unittest.TextTestRunner().run(suite)
```

5）执行方法四：匹配某个目录下所有以test开头的py 文件，执行这些文件下的所有测试用例
- test_dir = './test_case'
- discover = unittest.defaultTestLoader.discover(test_dir,pattern="test*.py")
- discover 可以一次调用多个脚本
- test_dir 被测试脚本的路径
- pattern 脚本名称匹配规则

示例：
```python
class Demo(unittest.TestCase):
    @classmethod
    def setUpClass(cls) -> None:
        print("这是setUpClass")

    @classmethod
    def tearDownClass(cls) -> None:
        print("这是tearDownClass")

    # def setUp(self) -> None:
    #     print("开始")
    #
    # def tearDown(self) -> None:
    #     print("结束")

    def test_demo01(self):
        print("test_demo01")
        self.assertEqual(1, 2, "判断相等")

    def test_demo02(self):
        print("执行test_demo02")
        self.assertEqual(2, 2, "判断相等")

    # @unittest.skipIf(1 + 1 == 2, "跳过这条用例")
    def test_demo03(self):
        print("执行test_demo03")
        self.assertIn('h', 'hello')


class Demo2(unittest.TestCase):
    @classmethod
    def setUpClass(cls) -> None:
        print("这是Demo2 的 setUpClass")

    @classmethod
    def tearDownClass(cls) -> None:
        print("这是Demo2 的tearDownClass")

    def test_Demo2_case1(self):
        print("执行test_Demo2_case1")
        self.assertIn('h', 'hello')


if __name__ == '__main__':
    discover = unittest.defaultTestLoader.discover("./","test*.py")
    unittest.TextTestRunner().run(discover)
```

## （四）测试用例执行过程
1.首先要写好TestCase

2.然后由TestLoader加载TestCase到TestSuite

3.然后由TextTestRunner来运行TestSuite

4.运行的结果保存在TextTestResult中，整个过程集成在unittest.main模块中

## （五）unittest结合HTMLtestrunner 生成的日志的测试报告
- http://tungwaiyip.info/software/HTMLTestRunner.html
- https://github.com/huilansame/HTMLTestRunner_PY3

示例：
```python
class Demo(unittest.TestCase):
    @classmethod
    def setUpClass(cls) -> None:
        print("这是setUpClass")

    @classmethod
    def tearDownClass(cls) -> None:
        print("这是tearDownClass")

    # def setUp(self) -> None:
    #     print("开始")
    #
    # def tearDown(self) -> None:
    #     print("结束")

    def test_demo01(self):
        print("test_demo01")
        self.assertEqual(1, 2, "判断相等")

    def test_demo02(self):
        print("执行test_demo02")
        self.assertEqual(2, 2, "判断相等")

    # @unittest.skipIf(1 + 1 == 2, "跳过这条用例")
    def test_demo03(self):
        print("执行test_demo03")
        self.assertIn('h', 'hello')


class Demo2(unittest.TestCase):
    @classmethod
    def setUpClass(cls) -> None:
        print("这是Demo2 的 setUpClass")

    @classmethod
    def tearDownClass(cls) -> None:
        print("这是Demo2 的tearDownClass")

    def test_Demo2_case1(self):
        print("执行test_Demo2_case1")
        self.assertIn('h', 'hello')

class HTMLTestRunnerNew(object):
    pass

if __name__ == '__main__':
    now = time.strftime("%Y-%m-%d-%H_%M_%S", time.localtime(time.time()))
    filename = 'D:\\python\\unittest测试框架\\report.html'
    fp = open(filename, 'wb')
    # 定义测试报告
    runner = HTMLTestRunner.HTMLTestRunner(
    stream=fp,
    title=u'测试报告',
    description=u'用例执行情况：')
    runner.run(discover)
    fp.close()

```



