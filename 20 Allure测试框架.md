# 二十、Allure测试框架
## （一）allure介绍
### 1.介绍
allure是一个轻量级、灵活的、支持多语言的测试报告工具。
- 多平台的、奢华的report框架
- 可以为dev/qa 提供详尽的测试报告、测试步骤、log；
- 也可以为管理层提供high level 统计报告
- java语言开发的，支持pytest、JavaScript、PHP、ruby等
- 可以集成到Jenkins

2.allure报告概览：https://demo.qameta.io/allure/#

## （二）allure安装

1）Windows/mac通用安装方法
- https://github.com/allure-framework/allure2/releases   下载allure2.7.zip 包
- 解压--进入bin目录--运行allure.bat
- 把bin目录加入到PATH环境变量

2）Mac可以使用brew安装：
- brew install allure
- 官网：http://allure.qatools.ru/
- 文档：https://docs.qameta.io/allure/#

3）allure的使用：
在中端输入两条语句：
```
pytest --alluredir=/tmp/my_allure_result
allure serve /tmp/my_allure_result
```

示例：
```python
import pytest

def test_success():
    assert True

def test_failure():
    assert False

def test_skip():
    pytest.skip('for a reason')

def test_broken():
    raise Exception('oops')
```

在中端中输入：
```
pytest test_allure.py --alluredir=./result/1
allure serve ./result/1
```

## （三）pytest-allure插件
1.安装allure-pytest插件
```
pip3 install allure-pytest
```
注：在本机安装完成之后，还要在pycharm中再装一遍。配完环境变量最好重启一遍pycharm。

## （四）Allure报告的生成
### 1.运行：
1）在测试执行期间收集结果
```
pytest  [测试文件] -s -q --alluredir=./result/ 
(--alluredir 这个选项用于指定存储测试结果的路径)
```

2）查看测试报告：

方式1：测试完成后查看实际报告，在线看报告，会直接打开默认浏览器展示当前报告
```
allure serve ./result  （注意这里的serve书写）
```

方式2：从结果生成报告，这是一个启动tomcat的服务，需要两个步骤：生成报告，打开报告
生成报告：
```
allure generate ./result/ -o ./report/ --clean（注意：覆盖路径加 --clean）

打开报告：
allure open -h 127.0.0.1 -p 8883 ./report/
```

## （五）allure特性分析

1.场景：

希望在报告中看到测试功能，子功能或场景，测试步骤，包括测试附加信息。

2.解决：
- @Feature，@story，@step，@step，@attach

3.步骤：
- import  allure
- 功能上加@allure.feature(‘功能名称’)
- 子功能上加@allure.story('子功能名称')
- 步骤上加@allure.step('步骤细节')
- @allure.attach('具体文本信息')，需要附加的信息，可以是数据，文本，图片，视频，网页
- 如果只是测试登录功能运行的时候可以加限制过滤：
- pytest 文件名 --allure-feature '购物车功能'  --allure-stories '加入购物车'

示例（原始代码）：
```python
import pytest
import allure

class TestLogin():

    def test_login_success(self):
        print("这是登录：测试用例，登录成功")
        pass

    def test_login_failure(self):
        print("这是登录：测试用例，登录失败")
        pass


    def test_login_fail_a(self):
        print("用户名缺失")
        pass

    def test_login_fail_b(self):
        print("密码缺失")
        pass

if __name__ == '__main__':
    pytest.main()
    
```

2）增加allure之后的代码：
```python
import pytest
import allure

@allure.feature("登录模块")
class TestLogin():
    @allure.story("登录成功")
    def test_login_success(self):
        print("这是登录：测试用例，登录成功")
        pass

    @allure.story("登录失败")
    def test_login_failure(self):
        print("这是登录：测试用例，登录失败")
        pass

    @allure.story("用户名缺失")
    def test_login_fail_a(self):
        with allure.step("点击用户名"):
            print("请输入用户名")
        with allure.step("点击密码"):
            print("请输入密码")
        print("点击登录")
        with allure.step("点击登录之后登录失败")
            assert '1' ==1
            print("密码不正确，登录失败")
        pass

    @allure.story("密码缺失")
    def test_login_fail_b(self):
        print("密码缺失")
        pass

if __name__ == '__main__':
    pytest.main()
```

3）只执行该模块：
```python
pytest test_allure.py --allure-features '登录模块'

```

## （六）按feature，story运行
1.注解@allure.feature 与@allure.story 的关系
- feature相当于一个功能，一个大的模块，将case分类到某个feature中，报告中behaviors中显示，相当于testsuite
- story相当于对应这个功能或者模块下的不同场景，分支功能，属于feature执行的结构，报告在feature中显示，相当于TestCase
- feature与story类似于父子关系

2.allure之step
- 测试过程中每个步骤，一般放在具体逻辑方法中
- 可以放在关键步骤中，在报告中显示
- 在app、web自动化测试中，建议每切换到一个新的页面当做一个step

用法：
- @allure.step()：只能以装饰器的形式放在类或者方法上面
- with allure.step()：可以放在测试用例方法里面，但测试步骤的代码需要被该语句包含

## （七）allure之issue，testcase
1.关联测试用例（可以直接给测试用例的地址链接）
- 关联bug：执行的时候需要加个参数
```
--allure-link-pattern=issue:http://www.mytesttracker.com/issue/{}
```
示例：
```python
import allure

@allure.link("http://192.168.124.149:31000/")
def test_with_link():
    print("这是一条加了链接的测试")
    
```
2）给链接起别名：
```python
import allure

@allure.link("http://192.168.124.149:31000/",name="万达项目")
def test_with_link():
    print("这是一条加了链接的测试")
```

3）也可以将地址传给一个参数，用参数传递：
```python
import allure

test_case_link = "http://192.168.124.149:31000"
@allure.link(test_case_link,"test_case_title")
def test_link():
    print("这是一条测试用例的链接，链接到测试用例里面")
    pass
```

4）issue的使用（点开链接之后未能显示bug，还需再检查问题）：
```python
#--allure-link-pattern=issur:http://192.168.124.120/zentao/issue/bug-view-4870.html
@allure.issue('4870','这是一个issue')
def test_issue():
    print("这是一个bug")
    pass
```
在终端执行：
```
pytest test_allure_link.py --allure-link-pattern=issur:http://192.168.124.120/zentao/issue/{} --alluredir=./result/8
allure serve ./result/8
```


## （八）allure按重要性级别进行一定范围测试
1.场景：
- 通常测试有P0、冒烟测试、验证上线测试。按重要性级别来分别执行的，比如上线要把主流程和重要模块都跑一遍。

2.解决：
- 通过附加pytest.mark标记
- 通过allure.feature，allure.story
- 也可以通过allure.severity来附加标记
- 级别：Trivial：不重要，Minor：不太重要，Normal：正常问题，Critical：严重，Blocker：阻塞

3.步骤：在方法上面加 @allure.severity(allure_serverity_level.TRIVIAL)

4.执行时：pytest -s -v 文件名 --allure-serverities normal,critical

5.示例：
```python
import allure
import pytest

class TestLevel():
    def test_no_lable(self):
        pass

    @allure.severity(allure.severity_level.TRIVIAL)
    def test_trival(self):
        print("这是一个trivial类的测试用例")
        pass

    @allure.severity(allure.severity_level.CRITICAL)
    def test_critical(self):
        print("这是一个critical类的测试用例")
        pass

    @allure.severity(allure.severity_level.NORMAL)
    def test_normal(self):
        print("这是一个normal类的测试用例")
        pass

if __name__ == '__main__':
    pytest.main()
```

在终端里执行：
```
pytest test_allure_level.py --alluredir=./result/11 --allure-severities normal
allure serve ./result/11
```

6.前端自动化测试-截图

示例：
```python
import pytest
import allure

def test_attach_text():
    allure.attach("这是一个纯文本",attachment_type=allure.attachment_type.TEXT)

def test_attach_html():
    allure.attach("这是一个html",attachment_type=allure.attachment_type.HTML)

def test_attach_photo():
    allure.attach.file("D:\Test\微信截图_20200911163252.png",name="这是一个图片",attachment_type=allure.attachment_type.PNG)
```

以上代码执行之后可以执行，但会报warning，因为有 d\ ，所以需要在前面加 r ，就不会报了，如下：
```python
import pytest
import allure

def test_attach_text():
    allure.attach("这是一个纯文本",attachment_type=allure.attachment_type.TEXT)

def test_attach_html():
    allure.attach("这是一个html",attachment_type=allure.attachment_type.HTML)

def test_attach_photo():
    allure.attach.file(r"D:\Test\微信截图_20200911163252.png",name="这是一个图片",attachment_type=allure.attachment_type.PNG)
```

## （十）allure+pytest+selenium 实战演示
1.原代码：
```python
import allure
import pytest
from selenium import webdriver
import time
import selenium

@pytest.mark.parametrize('data',['allure','test','pytest'])
def test_step_demo(data):
    driver = webdriver.Chrome()
    driver.get("https://www.baidu.com/")

    driver.find_element_by_id("kw").send_keys(data)
    time.sleep(1)
    driver.find_element_by_id("su").click()
    time.sleep(1)

    driver.save_screenshot("./result/b.png")
    allure.attach.file("./result/b.png",attachment_type=allure.attachment_type.PNG)
    allure.attach("<head></head><body>首页<body>",'Attach with HTML type',allure.attachment_type.HTML)
    driver.quit()
```

2.优化代码：
```python
import allure
import pytest
from selenium import webdriver
import time
import selenium

@allure.feature('百度搜索')
@pytest.mark.parametrize('data',['allure','test','pytest'])
def test_step_demo(data):
    with allure.step('打开百度'):
        driver = webdriver.Chrome()
        driver.get("https://www.baidu.com/")

    with allure.step('输入关键词，并进行点击'):
        driver.find_element_by_id("kw").send_keys(data)
        time.sleep(1)
        driver.find_element_by_id("su").click()
        time.sleep(1)

    with allure.step('进行截图'):
        driver.save_screenshot("./result/b.png")
        allure.attach.file("./result/b.png",attachment_type=allure.attachment_type.PNG)
        allure.attach("<head></head><body>首页<body>",'Attach with HTML type',allure.attachment_type.HTML)

    with allure.step('关闭浏览器'):
        driver.quit()
```
