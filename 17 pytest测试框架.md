# 十七、Pytest测试框架
## （一）pytest介绍
1.pytest是一个非常成熟的全功能的python测试框架
- 简单灵活，容易上手；
- 支持参数化；
- 测试用例的skip的xfail，自动失败重试等处理；
- 能够支持简单的单元测试和复杂的功能测试，还可以用来做selenium/appium 等自动化测试、接口自动化测试（pytest+requests）；
- pytest具有很多第三方插件，并且可以自定义扩展，比较好用的如pytest-allure（完美html测试报告生成），pytest-xdist（多cpu分发）等；
- 可以很好的和Jenkins集成；
- 文档：http://docs.pytest.org/en/latest/contents.html#toc
- 第三方库：https://pypi.org/search/?q=pytest
![pytest1](https://github.com/tete1987/picture_resource/blob/master/python%E5%9B%BE/pytest1.png)

### 2.pytest安装与依赖
- pip install -U pytest   U表示升级
- pip install pytest-sugar
- pip install pytest-rerunfailures
- pip install pytest-xdist
- pip install pytest-assume
- pip install pytest-html
- ……
- pip list
- pytest -h  帮助

### 3.测试用例的识别与运行

1）测试文件：
```python
test_*.py
*_test.py
```

2）用例识别：
- Test*类包含的所有test_*的方法（测试类不能带有__init__方法）
- 不在class中的所有test_*方法
- pytest也可以执行unittest框架写的用例和方法
- pytest无需集成TestCase类，可直接执行。

3）终端执行：
- pytest/py.test
- pytest -v （最高级别信息 --verbose）：打印详细运行日志信息
- pytest -v -s 文件名：（s是带控制台输出结果，也是输出详细）
- pytest 文件名.py   ：执行单独一个pytest模块
- pytest 文件名.py::类名::方法名：运行某个模块里的某个类里面的某个方法
- pytest 文件名.py::类名：运行某个模块里面的某个类
- pytest -v -k “类名 and not 方法名” ：跳过运行某个用例
- pytest -m[标记名]：   @pytest.mark.[标记名]将运行有这个标记的测试用例
- pytest -x 文件名 ： 一旦运行到报错就停止运行
- pytest --maxfail=[num] ：  当运行错误到达num的时候就停止运行

示例：
```python
import pytest
def test_one():
    print("开始执行 test_one 方法")
    x = "this"
    assert "h" in x

def test_two():
    print("开始执行 test_two 方法")
    x = "hello"
    assert "e" in x

def test_three():
    print("开始执行 test_three 方法")
    a = "hello"
    b = "hello world"
    assert a in b

if __name__ == "__main__":
    pytest.main()
```

示例：（可以在pycharm的 terminal 中执行）
```
(venv) D:\python\pytest测试框架>pytest -v test_pytest01.py
```

示例2（在pycharm中写在 main函数中）
```python
import pytest
from pytest_assume.plugin import assume

class Test01():
    def test_one(self):
        print("开始执行 test_one 方法")
        x = "this"
        assert "h" in x

    def test_two(self):
        print("开始执行 test_two 方法")
        x = "hello"
        assert "e" in x

    def test_three(self):
        print("开始执行 test_three 方法")
        a = "hello"
        b = "hello world"
        assert a not in b

    def test_four(self):
        print("开始执行 test_four 方法")
        a = "hello"
        b = "hello world"
        assert a in b

if __name__ == "__main__":
    pytest.main("-v -x Test01")
    #或者以下这种写法，均可
    # pytest.main(["-v", "-x","Test01"])
```

## （二）pytest测试用例的识别与运行
### 1.pytest执行-失败重新运行

1）场景：
测试失败后要重新运行n次，要在重新运行直接添加延迟时间，间隔 n秒 再运行。

2）安装：
```
pip install pytest-rerunfailures
```

3）执行：
```
pytest --reruns 3 -v -s test_class.py
pytest -v --reruns 5 --reruns-delay 1
```

### 2.多条断言有失败也都执行

1）场景：一个方法中写多条断言，通常第一个过不去，下面就不执行了。我们想报错也都执行一下。

2）安装：
```
pip install pytest-assume
```

3）执行：
```
pytest.assume(1==4)
pytest.assume(2==4)
```

### 3.pycharm 配置与执行pytest测试框架

运行方法：
```
pytest.main(['-v','TestCase'])（所有的参数和pytest命令方式是一样的）
```

![pytest2](https://github.com/tete1987/picture_resource/blob/master/python%E5%9B%BE/pytest2.png)

## （三）pytest框架结构
1.import pytest 类似的 setup，teardown 同样更灵活：
- 模块级：（setup_module/teardown_module）模块始末，全局的（优先最高）
- 函数级：（setup_function/teardown_function）只对函数用例生效（不在类中）
- 类级：(setup_class/teardown_class)只在类中前后运行一次（在类中）
- 方法级：（setup_method/teardown_mothod）开始于方法始末（在类中）
- 类里面的（setup/teardown）运行在调用方法前后

示例：
```python
def setup_module():
    print("这是一个setup_module 方法")

def teardown_module():
    print("这是teardown_module 方法")

def setup_function():
    print("这是 setup_function 方法")

def teardown_function():
    print("这是 teardown_function 方法")

class TestDemo:
    def setup_class(self):
        print("setup_class")

    def setup_method(self):
        print("setup_method")

    def teardown_method(self):
        print("teardown_method")

    def teardown_class(self):
        print("teardown_class")

class Test01():
    def test_one(self):
        print("开始执行 test_one 方法")
        x = "this"
        assert "h" in x

    def test_two(self):
        print("开始执行 test_two 方法")
        x = "hello"
        assert "e" in x

    def test_three(self):
        print("开始执行 test_three 方法")
        a = "hello"
        b = "hello world"
        assert a  in b

    def test_four(self):
        print("开始执行 test_four 方法")
        a = "hello"
        b = "hello world"
        assert a in b

if __name__ == "__main__":
    pytest.main("-v -x Test01")
```

### 2.pytest-fixture 的用法

1）场景：
- 用例1需要先登录
- 用例2不需要登录
- 用例3需要登录

这种场景无法用setup 与teardown 实现
用法：
在方法前面加@pytest.fixture()


举例1：

场景：测试用例执行时，有的用例需要先登录才能执行，有些用例不需要登录。setup和teardown无法满足。fixture可以。默认scope（范围）是function

步骤：
- 导入pytest
- 在登录的函数上面加@pytest.fixture()
- 在要使用的测试方法中传入（登录函数名称）就先登录
- 不传入的就不登录直接执行测试方法

示例1：
```python
import pytest

@pytest.fixture()
def login():
    print("这是个登录方法")

def test_case1(login):
    print("test_case1")
    pass

def test_case2():
    print("test_case2")
    pass

def test_case3(login):
    print("test_case3")
    pass

if __name__ == "__main__":
    pytest.main()
```

2）conftest的应用：
可以将所有公共测试用例放到conftest.py文件中。
- 手动建立conftest.py，该目录中必须包含init 文件。
- 将模块带@pytest.fixture 写在conftest文件中。

![pytest3](https://github.com/tete1987/picture_resource/blob/master/python%E5%9B%BE/pytest3.png)

使用conftest配置需要注意：
- conftest文件名是不能换的。
- conftest与运行的用例要在同一个package下，并且有init文件。
- 不需要import导入conftest，pytest用例会自动查找
- 全局的配置和前期工作都可以写在这里，放在某个包下，就是这个包数据共享的地方。

3）fixture的自动应用：
场景：不想原测试方法有任何改动，或全部都自动实现自动应用，没特例，也都不需要返回值时可选择自动应用。

解决：使用fixture中参数 auouse=True实现

步骤：
- 在方法上面加@pytest.fixture(autouse=True)
- 在测试方法上加@pytest.mark.usefixture("start")

示例：
```python
import pytest

@pytest.fixture(autouse=True)
def open():
    print("打开浏览器")

def test_search(open):
    print("test_search")
    pass

def test_search2(login):
    print("test_search2")
    pass

if __name__ == "__main__":
    pytest.main()
    
```

4）fixture带参数传递

场景：测试离不开数据，为了数据灵活，一般数据都是通过参数传的

解决：fixture通过固定参数request传递；

步骤：
在fixture中增加@pytest.fixture(params=[1,2,3,'linda']) 在方法参数写request

示例1：
```python
import pytest

@pytest.fixture(params=[1,2,3,'linda'])
def test_data(request):
    return request.param

def test_one(test_data):
    print('\ntest.data: %s'% test_data)

```

示例2：
```python
import pytest
import sys

@pytest.mark.parametrize("test_input,expected",[("3+5",8),("5*8",40),("8+3",89)])
def test_eval(test_input,expected):

    assert eval(test_input) == expected

注：eval：将字符串转成表达式传值
```

示例3：
```python
import pytest
test_user_data = ['Tom','Lily']
@pytest.fixture(scope="module")
def login_r(request):
    user = request.param
    print(f"\n 打开首页准备登录，登录用户：{user}")
    return user
    
@pytest.mark.parametrize("login_r",test_user_data,indirect=True)
def test_login(login_r):
    a = login_r
    print(f"测试用例中login 的返回值：{a}")
    assert a != ''
```
注：
- 当indirect为True的时候，参数为固件函数名称的，执行的时候会当做函数来执行。
- 当indirect为false的时候，参数为固件函数名称的，执行的时候会当做一个参数来执行。

示例4（跳过某条测试用例）：
```
@pytest.mark.skip(sys.platform == 'darwin',reason="不在macos上执行")
```
注：跳过某条测试用例时可定义执行条件，reason是单独的包，需要独立安装。
可使用：```pip install reason```

或下载好安装包，放到python的Lib目录下。

示例4（xfali的使用）：

将该用例标记成xfail失败，并且该用例中的后续代码不会执行。以下示例中，test_one 打印了start ，但是xfail后面的代码没有执行，test_two 和three正常执行。
```python
import pytest
class Test_Pytest():

        def test_one(self,):
                print("----start------")
                pytest.xfail(reason='该功能尚未完成')
                print("test_one方法执行" )
                assert 1==1

        def test_two(self):
                print("test_two方法执行" )
                assert "o" in "love"

        def test_three(self):
                print("test_three方法执行" )
                assert 3-2==1

if __name__=="__main__":
    pytest.main(['-s','-r','test_Pytest.py','test_Pytest.py'])
```

### 总结：

1）skip使用场景：
- 调试时不想运行这个用例
- 标记无法在某些平台上运行的测试功能
- 在某些版本中执行，其他版本中跳过
- 当前的外部资源不可用时跳过（如果测试数据是从数据库中取到的，连数据库的功能如果返回结果未成功就跳过，因为执行也都报错）

2）xfail场景：
- 功能测试尚未试试或尚未修复的错误，当测试通过时尽管预计会失败（标记为pytest.mark.xfail），它是一个xpass，将在测试摘要中报告。
- 你希望测试由于某值情况而应该失败

### 3.yield 的用法
1）场景：你已经可以将测试方法前要执行的或依赖的解决了，测试方法后销毁清除数据的要如何进行呢？范围是模块级别的。类似setupClass

2）解决：通过在同一模块中加入yield关键字，yield是调用第一次返回结果，第二次执行它下面的语句返回。

3）步骤：在 @pytest.fixture(scope=module)

4）在登录的方法中加入yield，之后加销毁清除的步骤注意，这种方式没有返回值，如果希望返回使用addfinalizer。

示例1：
```python
import pytest

@pytest.fixture(scope="module")
def open():
    print("打开浏览器")
    #在yield关键字后面的语句最后执行
    yield
    print("执行teardown")
    print("最后关闭浏览器")

def test_search(open):
    print("test_search")
    pass

def test_search2(login):
    print("test_search2")
    pass

if __name__ == "__main__":
    pytest.main()
```


### 4.定义标记mark
1）场景：
- 只执行符合要求的某一部分用例，可以把一个web项目划分多个模块，然后执行模块名称执行。
- App自动化时，如果想Android 和IOS 公用一套代码时，也可以使用标记功能，标明哪些是IOS的用例，那些是Android 的，运行代码是指定mark名称运行就可以。

2）解决：在测试用例方法上加 @pytest.mark.webtest

3）执行：

-s参数：输出所有测试用的print信息

-m：执行自定义标记的相关用例
- pytest -s test_mark_zi_09.py
- pytest -s test_mark_zi_09.py -m=webtest
- pytest -s test_mark_zi_09.py -m apptest
- pytest -s test_mark_zi_09.py -m “not ios”

步骤：

1.在conftest文件中将标记名称全部写进去：
```python
def pytest_configure(config):
  marker_list = ["case", "demo", "smoke"] # 标签名集合
  for markers in marker_list:
    config.addinivalue_line("markers", markers)

2.手动新建一个file文件（文件名称：pytest.ini），进行配置：
[pytest]
markers=
  smoke:this is a smoke tag
  case:case
  testdemo:testdemo

3.在代码中添加mark标记：
import pytest

def test_case1(login):
    print("test_case1")
    pass

def test_case2():
    print("test_case2")
    pass

@pytest.mark.case
def test_case3(login):
    print("test_case3")
    pass

if __name__ == "__main__":
    pytest.main()
```

4.在terminal中执行：
```
 pytest test_pytest02.py -m "case"  -v
```
test_pytest02.py 是文件名，case是定义的mark标记。

### 5.多线程并行与分布式执行
1）场景：
- 测试用例1000条，一个用例执行一分钟，一个测试人员执行需要1000分钟。通常我们会用人力成本换取时间成本，加几个人一起执行，时间就会缩短。如果10人一起执行只需要100分钟，这就是一种并行测试，分布式场景。

2）解决：
- pytest分布式执行插件：pytest-xdist，多个cpu或主机执行。前提：用例之间都是独立的，没有先后顺序，随机都能执行，可重复运行不影响其他用例。

3）安装：
```
pip install pytest-xdist
```
- 多个cpu并行执行测试用例，直接加-n 3，是并行数量，如：pytest -n 3
- 在多个终端下一起执行

### 6.pytest-html生成报告
安装：
```
pip install pytest-html
```
生成html报告：
```
pytest -v -s --html=report.html --self-contained-html
```
