# Python 风格要素

本文超出 PEP8 的范畴以涵盖我认为优秀的 Python 风格。本文虽然坚持己见，却不偏执。不仅仅涉及语法、模块布局等问题，同时深入范式、组织及架构的领域。希望本文能成为精简版 Python 代码[《风格的要素》][strunk-white]。

[strunk-white]: https://en.wikipedia.org/wiki/The_Elements_of_Style

# 目次

  * [Python 风格要素](#the-elements-of-python-style)
    * [基本遵从 PEP 准则](#follow-most-pep8-guidelines)
    * [灵活的单行长度](#flexibility-on-line-length)
    * [一致的命名](#consistent-naming)
    * [不要在这些地方吹毛求疵](#nitpicks-that-arent-worth-it)
    * [写好文档字符串](#writing-good-docstrings)
    * [范式和模式](#paradigms-and-patterns)
    * [代码风格小禅理](#a-little-zen-for-your-code-style)
    * [平分秋色](#six-of-one-half-a-dozen-of-the-other)
    * [标准工具及项目架构](#standard-tools-and-project-structure)
    * [灵感来源](#some-inspiration)
    * [撰稿人](#contributors)

## 基本遵从 [PEP 准则][pep8]

…… 但是，命名和单行长度更灵活。

PEP8 涵盖了诸如空格、函数/类/方法之间的换行、`import`、对已弃用功能的警告之类的寻常东西，大都不错。

应用这些准则的最佳工具是 [flake8][flake8]，还可以用来发现一些愚蠢的语法错误。

PEP8 原本只是一组指导原则，不必严格甚至虔诚地信奉。一定记得阅读 PEP8 「愚蠢的一致性就是小人物的小妖精」一节。若要进一步了解，可以听一下 Raymond Hettinger 的精彩演讲，[「超越 PEP8」](https://www.youtube.com/watch?v=wf-BqAjZb8M)。

唯一引起过多争议的准则事关单行长度和命名。要调整起来也不难。

## 单行长度的灵活性

若是厌烦 `flake8` 死板的单行长度不得超过 79 个字符的限制，完全可以忽略或修改这一准则。这仍然不失为一条不错的经验法则，就像英语中句子不能超过 50 个单词，段落不能超过 10 个句子之类的规则一样。这是 [flake8 配置文件][f8config] 的链接，可以看到 `max-line-length` 配置选项。值得注意的是，可以给要忽略 `flake8` 检查的那一行加上 `# noqa` 注释，但是请勿滥用。

尽管如此，超过九成的代码行都不应该超过 79 个字符，原因很简单，「扁平胜于嵌套」。如果函数每一行都超出了 79 个字符，肯定有别的东西出错了，这时要看看代码而不是 `flake8` 配置。

[pep8]: https://www.python.org/dev/peps/pep-0008/
[flake8]: https://flake8.readthedocs.org
[f8config]: https://flake8.readthedocs.org/en/latest/config.html

## 一致的命名

关于命名，遵循几条简单的准则就可以避免众多足以影响整个小组的麻烦。

### 推荐的命名规则

下面这些准则大多改编自 [Pacoo 小组][pocoo]。

- 类名：`驼峰式` 和全大写首字母缩略词：`HTTPWriter` 优于 `HttpWriter`。
- 变量名：`lower_with_underscores`。
- 方法名和函数名：`lower_with_underscores`。
- 模块名：`lower_with_underscores.py`。（但是不带下划线的名字更好！）
- 常量名：`UPPER_WITH_UNDERSCORES`。
- 预编译的正则表达式：`name_re`。

[pocoo]: http://www.pocoo.org/internal/styleguide/

通常都应该遵循这些准则，除非要参照其他工具的命名规范，比如数据库 schema 或者消息格式。

还可以用 `驼峰式` 给类似类却不是类的东西命名。使用 `驼峰式` 的主要好处在于让人们以「全局名词」来关注某个东西，而不是看作局部标记或动词。值得注意的是，Python 给 `True`，`False` 和 `None` 这些根本不是类的东西命名也是用 `驼峰式`。

### 不要用前缀后缀

…… 比如 `_prefix` 或 `suffix_` 。函数和方法名可以用 `_prefix` 标记来暗示其是「私有的」，但是最好只在编写预期会广泛使用的 API 以及用 `_prefix` 标记来[隐藏信息][infohiding]的时候谨慎使用。

[infohiding]: http://c2.com/cgi/wiki?InformationHiding

PEP8 建议使用结尾的下划线来避免与内置关键字重名，比如：

```python
sum_ = sum(some_long_list)
print(sum_)
```

临时这样用也可以，不过最好还是选一个别的名字。

用 `__mangled` 这种双下划线前缀给类/实例/方法命名的情况非常少，这实际上涉及特殊的[名字改编行为][mangling]，非常罕见。不要起 `__dunder__` 这种格式的名字，除非要实现 Python 标准协议，比如 `__len__`；这是为 Python 内部协议保留的命名空间，不应该在其中增加自定义的东西。

[mangling]: https://docs.python.org/2/tutorial/classes.html#private-variables-and-class-local-references

### 不要用单字符名字

（不过）一些常见的单字符名字可以接受。

在 `lambda` 表达式中，单参数函数可以命名为 `x` 。比如：

```python
encode = lambda x: x.encode("utf-8", "ignore")
```

解包元组时可以用 `_` 丢弃不需要的标记。比如：

```python
_, url, urlref = data
```

意思就是说「忽略第一个元素」。

和 `lambda` 类似，在解析列表/字典/集合的时候，以及在生成器表达式或者一到两行的 for 循环中，可以使用单字符迭代标记。通常选择 `x`，比如：

```python
sum(x for x in items if x > 0)
```

可以求 `items` 序列中所有正整数之和。

另外比较常见的是 `i`，代表 `index`，通常和内置的 `枚举` 一起使用。比如：

```python
for i, item in enumerate(items):
    print("%4s: %s" % (i, item))
```

除却上述情形，要极少甚至**避免**使用单字符用作标记/参数/方法的名字。因为这样就无法用 `grep` 进行检索了。

### 使用 `self` 及类似的惯例

应该：

- 永远将方法的第一个变量命名为 `self`
- 永远将 `@classmethod` 的第一个参数命名为 `cls`
- 永远在变量参数列表中使用 `*args` 和 `**kwargs`

## 不要在这些地方吹毛求疵

不遵循如下准则没有什么好处，干脆照它说的做。

### 永远[继承自 `object`][newstyle] 并使用新式类

```python
# bad
class JSONWriter:
    pass

# good
class JSONWriter(object):
    pass
```

对于 Python 2 来说遵循这条准则很重要。不过由于 Python 3 所有的类都隐式继承自 `object`，这条准则就没有必要了。

### 不要在类中重复使用实例标记

```python
# bad
class JSONWriter(object):
    handler = None
    def __init__(self, handler):
        self.handler = handler

# good
class JSONWriter(object):
    def __init__(self, handler):
        self.handler = handler
```

### [列表/字典/集合推导式][mapfilter]优于 map/filter

```python
 # bad
map(truncate, filter(lambda x: len(x) > 30, items))

 # good
[truncate(x) for x in items if len(x) > 30]
```

尽管在大多数简单情况下最好使用推导式，不过有时候 `map()` 或者 `filter()` 可读性更佳，需要自己判断。

### 用圆括号 `(...)` 折行

```python
# bad
from itertools import groupby, chain, \
                      izip, islice

# good
from itertools import (groupby, chain,
                       izip, islice)
```

### 用圆括号 `(...)` 写 API 更利落

```python
# bad
response = Search(using=client) \
           .filter("term", cat="search") \
           .query("match", title="python")

# good
response = (Search(using=client)
            .filter("term", cat="search")
            .query("match", title="python"))
```

### 函数调用中使用隐式行延续

```python
# bad -- simply unnecessary backslash
return set((key.lower(), val.lower()) \
           for key, val in mapping.iteritems())

# good
return set((key.lower(), val.lower())
           for key, val in mapping.iteritems())
```

### 用 `isinstance(obj, cls)`, 不要用 `type(obj) == cls`

因为 `isinstance` 涵盖更多情形，包括子类和抽象基类。同时，不要过多使用 `isinstance` ，因为通常应该使用鸭子类型！

### 用 `with` 处理文件和锁

`with` 语句能够巧妙地关闭文件并释放锁，哪怕是在触发异常的情况下。所以：

```python
# bad
somefile = open("somefile.txt", "w")
somefile.write("sometext")
return

# good
with open("somefile.txt", "w") as somefile:
    somefile.write("sometext")
return
```

### 和 `None` 相比较要用 `is`

`None` 值是一个单例，但是检查 `None` 的时候，实际上很少真的要在 LHS 变量上调用 `__eq__`。所以：

```python
# bad
if item == None:
    continue

# good
if item is None:
   continue
```

好的写法不仅执行更快，而且更准确。使用 `==` 并不会更简洁，所以请记住本规则！

### 不要修改 `sys.path`

通过 `sys.path.insert(0, "../")` 等操作来控制 Python 的导入方法或许让人心动，但是要坚决避免这样做。

Python 有一套有几分复杂，却易于理解的模块路径解决方法。可以通过 `PYTHONPATH` 或诸如 `setup.py develop` 的技巧来调整 Python 导入模块的方法。还可以用 `-m` 运行 Python 得到需要的效果，比如使用 `python -m mypkg.mymodule` 而不是 `python mypkg/mymodule.py`。代码能否正常运行不应依赖于当前执行 Python 的工作路径。David Beazley 用 PDF 幻灯片再一次扭转了大家的看法，值得一读，["Modules and Packages: Live and Let Die!"][modules]。

[modules]: http://www.dabeaz.com/modulepackage/ModulePackage.pdf

### 尽量不要[自定义异常类型][exceptiontypes]

…… 如果一定要，也不要创建太多。

```python
 # bad
 class ArgumentError(Exception):
     pass
 ...
 raise ArgumentError(url)

 # good
 raise ValueError("bad value for url: %s" % url)
```

要知道 Python 引入了 [丰富的内建异常类][ex-tree]。值得充分利用。而且通过描述那些触发特定错误条件的字符串消息，来实例化这些异常类，就能达到「定制」的目的。在用户代码中抛出 `ValueError`（参数错误），`LookupError`（键错误）以及 `AssertionError`（用 `assert` 语句）最为常见。

至于是否应该自己创建异常类有一个不错的经验法则，也就是搞清楚函数调用方**每次**调用函数之时是否都应该捕获该异常。如果是，那么的确**应该**自己创建异常类。不过这相当少见。关于这类明显不得不使用的自定义异常类有一个不错的例子，[tornado.web.HTTPError][http-error]。但是要留心 Tornado 是如何避免走极端的：框架或用户代码抛出的**所有** HTTP 错误同属一个异常类。

[ex-tree]: https://docs.python.org/2/library/exceptions.html#exception-hierarchy
[http-error]: http://www.tornadoweb.org/en/stable/web.html#tornado.web.HTTPError

### 短文档字符串应是名副其实的单行句子

```python
# bad
def reverse_sort(items):
    """
    sort items in reverse order
    """

# good
def reverse_sort(items):
    """Sort items in reverse order."""
```

把三引号 `"""` 放在同一行，首字母大写，以句号结尾。四行精简到两行，`__doc__` 属性没有糟糕的换行，最吹毛求疵的人也会满意的！

### 文档字符串使用 [reST][docstrings]

标准库和大多数开源项目皆是如此。Sphinx 提供支持，开箱即用。赶紧试试吧！Python `requests` 模块由此取得了极佳的效果。看看[`requests.api`][requests-api] 模块的例子。

### 删除结尾空格

最挑剔也不过如此了吧，可是若做不到这一点，有些人可能会被逼疯。不乏能自动搞定这一切的编辑器；这是我[用 vim 的实现][whitespace]。

[whitespace]: https://github.com/amontalenti/home/blob/master/.vim/bundle/whitespace/plugin/whitespace.vim

## 文档字符串要写好

下面是在函数文档字符串中使用 Sphinx 风格的 reST 的快速参考：

```python
def get(url, qsargs=None, timeout=5.0):
    """Send an HTTP GET request.

    :param url: URL for the new request.
    :type url: str
    :param qsargs: Converted to query string arguments.
    :type qsargs: dict
    :param timeout: In seconds.
    :rtype: mymodule.Response
"""
return request('get', url, qsargs=qsargs, timeout=timeout)
```

不要为文档而写文档。写文档字符串要这样思考：

```python
好名字 + 显式指明默认值 优于 罗嗦的文档 + 类型的详细说明
```

也就是说，上例中没有必要说 `timeout` 是 `float`，默认值 `5.0`，显然是 `float`。在文档中指出其语义是「秒」更有用，就是说 `5.0` 意思是 5 秒钟。同时调用方不知道 `qsargs` 应该是什么，所以用 `type` 注释给出提示，调用方也无从知道函数的预期返回值是什么，所以 `rtype` 注释是合适的。

最后一点。吉多·范罗苏姆曾说过，他对 Python 的主要领悟是「读代码比写代码频率更高」。直接结论就是**有些文档有用，更多的文档有害**。

基本上只需要给预计会频繁重用的函数写文档。如果给内部模块的每一个函数都写上文档，最后只能得到更加难以维护的模块，因为重构代码之时文档也要重构。不要「船货崇拜」文档字符串，更不要用工具自动生成文档。

[newstyle]: https://docs.python.org/2/reference/datamodel.html#new-style-and-classic-classes
[mapfilter]: http://www.artima.com/weblogs/viewpost.jsp?thread=98196
[exceptiontypes]: https://twitter.com/amontalenti/status/665338326396727297
[docstrings]: https://www.python.org/dev/peps/pep-0287/
[requests-api]: https://github.com/kennethreitz/requests/blob/master/requests/api.py

## 范式和模式

### 函数还是类

通常应该用函数而不是类。函数和模块是 Python 代码重用的基本单元，还是最灵活的形式。类是一些 Python 功能的「升级路径」，比如实现容器，代理，描述符，类型系统等等。但是通常函数都是更好的选择。

或许有人喜欢为了更好地组织代码而将关联的函数归在类中。但这是错的。关联的函数应该归在**模块**中。

尽管有时可以把类当作「小型命名空间」（比如用 `@staticmethod`）比较有用，一组方法更应该对同一个对象的内部操作有所贡献，而不仅仅作为行为分组。

与其创建 `TimeHelper` 类，带有一堆不得不引入子类才能使用的方法，永远不如直接为时间相关的函数创建 `lib.time` 模块。类会增殖出更多的类，会增加复杂性，降低可读性。

### 生成器和迭代器

生成器和迭代器是 Python 中最强大的特性 —— 应该掌握迭代器协议，`yield` 关键字和生成器表达式。

生成器不仅仅对要在大型数据流上反复调用的函数十分重要，而且可以让自定义迭代器更加简单，从而简化了代码。将代码重构为生成器通常可以在使得代码在更多场景下复用，从而简化代码。

Fluent Python 的作者 Lucinao Ramalho 通过 30 分钟的演讲，[「迭代器和生成器： Python 之道」](https://www.youtube.com/watch?v=z4P6hSa6K9g)，给出了一个不错的、快节奏的概述。Python Essential Reference 和 Python Cookbook 的作者 David Beazley 有个深奥的三小时视频教程，[「生成器：最后的前沿」](https://www.youtube.com/watch?v=5-qadlG7tWo)，给出了令人满足的生成器用例的详细阐述。因为应用广泛，掌握这一主题非常有必要。

### 声明式还是命令式

声明式编程优于命令式编程。代码应该表明你想要做**什么**，而不是描述**如何**去做。Python 的[函数式编程概览][func]介绍了一些不错的细节并给出了高效使用该风格的例子。

[func]: https://docs.python.org/3/howto/functional.html

使用轻量级的数据结构更好，比如 `列表`，`字典`，`元组`和`集合`。将数据展开，编写代码对其进行转换，永远要优于重复调用转换函数/方法来构建数据。

一个例子是重构常见的列表解析：

```python
# bad
filtered = []
for x in items:
    if x.endswith(".py"):
        filtered.append(x)
return filtered
```

应该改成 ：

```python
# good
return [x
        for x in items
        if x.endswith(".py")]
```

但是还有一个不错的例子是将 `if`/`elif`/`else` 链改成 `字典` 查询。

### 「纯」函数和迭代器更好

这是个从函数式编程社区借来的概念。这种函数和迭代器亦被描述为「无副作用」，「引用透明」或者有「一成不变的输入/输出」。

一个简单的例子，要避免这种代码：

```python
# bad
def dedupe(items):
    """Remove dupes in-place, return items and # of dupes."""
    seen = set()
    dupe_positions = []
    for i, item in enumerate(items):
        if item in seen:
            dupe_positions.append(i)
        else:
            seen.add(item)
    num_dupes = len(dupe_positions)
    for idx in reversed(dupe_positions):
        items.pop(idx)
    return items, num_dupes
```

这个函数应该重新写成：

```python
# good
def dedupe(items):
    """Return deduped items and # of dupes."""
    deduped = set(items)
    num_dupes = len(items) - len(deduped)
    return deduped, num_dupes
```

这是个惊人的例子。函数不仅更加纯粹，而且更加精简了。不仅更加精简，而且更好。这里的纯粹是说 `assert dedupe(items) == dedupe(items)` 在「好」版本中恒为真。在「坏」版本中， `num_dupes` 在第二次调用时**恒**为 `0`，这会在使用时导致难以理解的错误。

这个例子也阐明了命令式风格和声明式风格的区别：改写后的函数读起来更像是对需要的东西的描述，而不是构建需要的东西的一系列操作。

### 简单的参数和返回值类型更好

函数应该尽可能处理数据，而不是自定义的对象。简单的参数类型更好，比如`字典`，`集合`，`元组`，`列表`，`int`，`float` 和 `bool`。从这些扩展到标准库类型，比如 `datetime`, `timedelta`, `array`, `Decimal` 以及 `Future`。只有在真的必要时才使用自定义类型。

判断函数是否足够精简有个不错的经验法则，问自己参数和返回值是否总是可以 JSON 序列化。结果证明这个经验法则相当有用：可以 JSON 序列化通常是函数在并行计算时可用的先决条件。但是，就本文档而言，主要的好处在于：可读性，可测试性以及总体的函数简单性。

### 避免「传统的」面向对象编程

在「传统的面向对象编程语言」中，比如 Java 和 C++ ，代码重用是通过类的继承和多态或者语言声称的类似机制实现的。对 Python 而言，尽管可以使用子类和基于类的多态，事实上在地道的 Python 程序中这些功能极少使用。

通过模块和函数实现代码重用更为普遍，通过鸭子类型实现动态调度更为常见。如果发现自己通过超类实现代码重用，停下来，重新思考。如果发现自己大量使用多态，考虑一下是否用 Python 的 dunder 协议或者鸭子类型策略会更好。

看一下另一个不错的 Python 演讲，一位 Python 核心贡献者的 [「不要再写类了」][stop-classes]。演讲者建议，如果构建的类只有一个命名像一个类的方法（比如 `Runnable.run()`），那么实际上只是用函数模拟了一个类，这时候应该停下来。因为在 Python 中，函数是「最高级的」类型，**没有理由**这样做。

[stop-classes]: https://www.youtube.com/watch?v=o9pEzgHorH0

### Mixin 有时也没问题

可以使用 Mixin 实现基于类的代码重用，同时不需要走极端使用类型层次。但是不要滥用。「扁平胜于嵌套」也适用于类型层次，所以应该避免仅仅为了分解行为而引入不必要的必须层次的一层。

Mixin 实际上不是 Python 的特性，多亏了 Python 支持多重继承。可以创建基类将功能「注入」到子类中，而不必构成类型层次的「重要」组成部分，只需要将基类列入 `bases` 列表中的第一个元素。

```python
class APIHandler(AuthMixin, RequestHandler):
    """Handle HTTP/JSON requests with security."""
```

要考虑顺序，同时不妨记住：`bases` 自底向上构成层次结构。这里可读性的好处在于关于这个类所需要知道的一切都包含在`类`定义本身：「它混入了权限行为，是专门定制的 Tornado RequestHandler。」

### 小心框架

Python 有大量的 web，数据库等框架。Python 语言的一大乐趣在于创建自定义框架相当简单。使用开源框架时，应该注意不要将自己的「核心代码」和框架本身结合得过于紧密。

考虑为自己的代码创建框架的时候应当慎之又慎。标准库有很多内置的东西，PyPI 有的就更多了，而且通常[你不会需要它][yagni]。

[yagni]: http://c2.com/cgi/wiki?YouArentGonnaNeedIt

### 尊重元编程

Python 通过一些特性来支持 「元编程」，包括修饰器，上下文管理器，描述符，import 钩子，元类和抽象语法树（AST）转换。

使用并理解这些特性没有什么问题，这些特性是 Python 的核心组成部分并且有着充分地支持。但是应当意识到使用这些特性之时，也引入了复杂的失败场景。所以，要把为自己的代码创建元编程工具与决定「创建自定义框架」同等对待。它们意味着同一件事情。真要这么做的时候，把元编程工具写成独立的模块，写好文档！

### 不要害怕 「dunder」方法

许多人将 Python 的元编程工具和其对 「双下划线」或「dunder」方法（比如 `__getattr__`）的支持混为一谈。

正如博文 ——[「Python 双下划线，双倍惊喜」][dunder]—— 所言，双下划线没有什么「特殊的」。它们只不过是 Python 核心开发人员为所有的 Python 内部协议所起的轻量级命名空间。毕竟，`__init__` 也是双下划线，没有什么神奇的。

的确，有些双下划线比其他的会导致更多令人困惑的结果，比如，没有很好的理由就重载操作符通常不是什么好主意。但是它们中也有许多，比如 `__repr__`，`__str__`，`__len__` 以及 `__call__` 是 Python 语言的完整组成部分，应该在地道的 Python 代码中充分利用。不要回避！

[dunder]: http://www.pixelmonkey.org/2013/04/11/python-double-under-double-wonder

## 代码风格小禅理

作为一位核心 Python 开发者，Barry Warsaw 曾经说过「Python 之禅」（[PEP 20][pep20]）被用作 Python 代码风格指南使他沮丧，因为这本是为 Python 的**内部**设计所作的一首小诗。也就是语言的设计以及语言的实现本身。不过必须承认，PEP 20 中有些行可以当作相当不错的地道 Python 代码指南，所以我们就把它加上了。

[pep20]:  https://www.python.org/dev/peps/pep-0020/

### 美胜于丑

这一条有些主观，实际上等同于问：接手代码的人会被折服还是感到失望？如果接手的人就是三年后的你呢？

### 显胜于隐

有时为了重构以去除重复的代码，会有一点抽象。应该能够将代码翻译成显现的英语并且大致了解它是干什么的。不应该有太多的「神奇之处」。

### 扁平胜于嵌套

这一条很好理解。最好的函数没有嵌套，既不用循环也不用 `if` 语句。第二好的函数只有一层嵌套。如果有两层及以上的嵌套，最好重构成更小的函数。

同样，不要害怕将嵌套的 if 语句重构为多部分布尔条件。比如：

```python
# bad
if response:
    if response.get("data"):
        return len(response["data"])
```

最好写成：

```python
# good
if response and response.get("data"):
    return len(response["data"])
```

### 可读性确实重要

不要害怕用 `#` 添加行注释。也不要滥用或者写过多文档。一点点逐行解释，通常很有帮助。不要害怕使用稍微长一些的名字，因为描述性更好。将 「`response`」写成「`rsp`」没有任何好处。使用 doctest 风格的例子在文档字符串中详细说明边界情况。简洁至上！

### 错误不应被放过

单独的`except: pass` 子句危害最大。永远不要使用。制止**所有**的异常实在危险。将异常处理限制在一行代码，并且总是将 `except` 处理器限制在特定的类型下。除此之外，可以自如地使用 `logging` 模块和 `log.exception(…)`。

### 如果实现难以解释，那就是个坏主意

这虽是通用软件工程原则，但是特别适用于 Python 代码。大多数 Python 函数和对象都可以有易于解释的实现。如果难以解释，很可能是一个坏主意。通常可以通过「分而治之」将一个难以解释的函数重写成易于解释的函数，也就是分割成多个函数。

### 测试是个好主意

好吧，我们篡改了「Python 之禅」中的这一行，原文中「命名空间」才是个绝妙的好主意。

不过说正经的，优雅却没有测试的代码简直比哪怕是最丑陋却测试过的代码还要差劲。至少丑陋的代码可以重构成优雅的，但是优雅的代码却不能重构为可以证明是正确的代码，至少不写测试是做不到的！所以，写测试吧！拜托！

## 平分秋色

我们把宁愿不去解决的争论放在这个部分。不要因为这些重写别人的代码。这里的东西可以自由地交替使用。

### `str.format` 还是重载格式化操作符 `%`

`str.format` 更健壮，然而 `%` 使用 `"%s %s"` printf 风格的字符串更加简洁。两者会永远共存。

如果需要保存 unicode，记得在格式模板中使用 unicode 字符串：

```python
u"%s %s" % (dt.datetime.utcnow().isoformat(), line)
```

如果最后选择 `%`，应该考虑 `"%(name)s"` 语法，这样可以使用字典而不是元组，比如：

```python
u"%(time)s %(line)s" % {"time": dt.datetime.utcnow().isoformat(), "line": line}
```

此外，不要重新发明轮子。`str.format` 有一点毫无疑问比 `%` 更好，那就是支持各种[格式化模式][str-format]，比如人性化的数字和百分数。直接用。

但是选择哪一个都没有问题。我们没有强制规定。

[str-format]: https://docs.python.org/2/library/string.html#formatspec

### `if item` 还是 `if item is not None`

本条和之前的对于 `None` 是用 `==` 还是 `is` 没有关系。这里我们实际上利用了 Python 的 「真实性规则」来处理 `if item`，这实际上是「item 不是 None 或者空字符串」的简写。

Python 中的真实性[有些复杂][truth-values]。显然第二种写法对于某些错误而言更安全。但是第一种写法在 Python 代码中非常常见，而且更短。对此我们并没有强制规定。

[truth-values]: https://docs.python.org/2/library/stdtypes.html#truth-value-testing

### 隐式多行字符串还是三引号 `"""`

Python 编译器在语法分析时，如果多个字符串之间没有东西，会自动将其拼接为一个字符串。比如：

```python
msg = ("Hello, wayward traveler!\n"
       "What shall we do today?\n"
       "=>")
print(msg)
```

大致上等价于：

```python
msg = """Hello, wayward traveler!
What shall we do today?
=>"""
print(msg)
```

第一种写法可以保持缩进整洁，但是需要丑陋的换行符，第二种写法不需要换行符，但是打破了缩进。我们没有强制规定哪种更好。

### 在类还是实例上使用 `raise` 

事实上给 `raise` 语句传入异常**类**或者异常**实例**都可以。比如，下面两行代码大致等价：

```python
raise ValueError
raise ValueError()
```

本质上，Python 会[将第一种写法自动转换为第二种][raises]。或许第二个写法更好，即使没有其他原因，它也能**实际上提供一个有用的理由**，就像有条有用的消息来解释为什么 `ValueError` 会发生一样。但是这两种写法**是**等价的，不应该为这一点就重写代码。我们不做强制规定。

[raises]: https://docs.python.org/2.7/reference/simple_stmts.html#the-raise-statement

## 标准工具和项目结构

我们选择了一些「最佳组合」工具，以及像样的 Python 项目会用到的最小初始结构。

### 标准库

- `import datetime as dt`: 永远像这样导入 `datetime`
- `dt.datetime.utcnow()`: 优于 `.now()`, 后者使用的是当地时间
- `import json`: 数据交换的标准
- `from collections import namedtuple`: 用来做轻量级数据类型
- `from collections import defaultdict`: 用来计数/分组
- `from collections import deque`: 快速的双向队列
- `from itertools import groupby, chain`: 为了声明式风格
- `from functools import wraps`: 用来编写合乎规范的装饰器
- `argparse`: 为了构建「健壮的」命令行工具
- `fileinput`: 用来快速构建易于和 UNIX 管道结合使用的工具
- `log = logging.getLogger(__name__)`: 足够好用的日志
- `from __future__ import absolute_import`: 修复导入别名

### 常见第三方库

- `python-dateutil` 用来解析时间和日历
- `pytz` 用来处理时区
- `tldextract` 为了更好地处理 URL
- `msgpack-python` 比 JSON 更加紧凑地编码
- `futures` 为了 Future/pool 并发原语
- `docopt` 用来快速编写一次性命令行工具
- `py.test` 用来做单元测试，与 `mock` 和 `hypothesis` 结合使用

### 本地开发项目框架

对所有的 Python 包和库而言：

- 根目录下不要有 `__init__.py`：目录名用作包名！
- `mypackage/__init__.py` 优于 `src/mypackage/__init__.py`
- `mypackage/lib/__init__.py` 优于 `lib/__init__.py`
- `mypackage/settings.py` 优于 `settings.py`
- `README.rst` 用来给新手描述本项目；使用 rst
- `setup.py` 用来构建简单工具，比如 `setup.py develop`
- `requirements.txt` 是为 `pip` 准备的包依赖环境
- `dev-requirements.txt` 是为 tests/local 准备的额外的依赖环境
- `Makefile` 用来简化 (!!!) build/lint/test/run 步骤

另外，永远记得[详细说明包依赖环境](http://nvie.com/posts/better-package-management/)。

## 灵感来源

下面这些链接或许可以给你启发，有助于书写具有良好风格和品味的 Python 代码。

- Python 标准库中的 [`Counter` class][Counter] 代码, 作者是 Raymond Hettinger
- [`rq.queue` 模块][rq], 原作者是 Vincent Driessen
- 本文作者还写过 [这篇关于 "Pythonic" 代码的博文][idiomatic]

出发吧，写更具 Python 风格的代码！

```
$ python
>>> import antigravity
```

[Counter]: https://github.com/python/cpython/blob/57b569d8af2b3263c5d9e6d75fb308f89ea17ac6/Lib/collections/__init__.py#L446-L841
[rq]: https://github.com/nvie/rq/blob/master/rq/queue.py
[idiomatic]: http://www.pixelmonkey.org/2010/11/03/pythonic-means-idiomatic-and-tasteful

## 撰稿人

- Andrew Montalenti ([@amontalenti][amontalenti]): 原作者
- Vincent Driessen ([@nvie][nvie]): 编辑并提出意见

[amontalenti]: http://twitter.com/amontalenti
[nvie]: http://twitter.com/nvie

---

喜欢优雅的 Python 风格吗？欢迎加入我们在 Parse.ly 公司的 [Pythonistas 小组][tweet]!

[tweet]: https://twitter.com/amontalenti/status/682968375702716416
