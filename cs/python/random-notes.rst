- 解释器直接执行脚本时, 该脚本中所有代码会在名为 ``__main__`` 的顶层 module
  中执行. (`__main__`, `sys`, `builtins` 是解释器默认 import 的三个 modules.)
  由于身在 `__main__` 中, namespace 中 默认并不包含 `__main__` 这个 identifier.
  需要手动 ``import __main__``.

- coroutine, awaitable, async def/with/for, await 等等这些概念并不适合一般情况下
  手动使用. 只有和某种 event loop 配合使用, 实现单线程的 asynchronous concurrency
  时, 才有价值.

- In CPython, generator-based coroutines (generators decorated with
  ``types.coroutine()`` or ``asyncio.coroutine()``) are awaitables,
  even though they do not have an ``__await__()`` method. Using
  ``isinstance(gencoro, Coroutine)`` for them will return ``False``.
  Use ``inspect.isawaitable()`` to detect them.
  Coroutine objects and instances of the Coroutine ABC are all instances
  of this ABC.
  即, 被 ``types.coroutine``/``asycio.coroutine`` decorated 的 generator
  是 coroutine (没有 ``__await__``), 使用 ``async def`` 定义的函数也是
  coroutine (有 ``__await__``).
  具有 ``__await__`` method 的 object 是 awaitable object. 所有 coroutine
  都是 awaitable object.

- 放在 ``yield from`` 后面的需要是 iterable.

- scheduler 的设置方式, 应该是在每次执行后, re-schedule self, 这样具有最大程度的
  调度灵活性, 也比较自然. 例如, 使用 `sched`:

  .. code:: python

    def periodic(scheduler, interval, action, actionargs=()):
        scheduler.enter(interval, 1, periodic,
                        (scheduler, interval, action, actionargs))
        action(*actionargs)

  使用 `asyncio`:

  .. code:: python

    def display_date(end_time, loop):
        print(datetime.datetime.now())
        if (loop.time() + 1.0) < end_time:
            loop.call_later(1, display_date, end_time, loop)
        else:
            loop.stop()

- ``StopIteration`` 只应该 raised by ``next()`` 和 ``__next__()``.
  对于 generator, generate 行为结束时, 应该直接返回 (``return``), 这样
  generator 的 ``__next__`` method 自动 raise 一个 StopIteration, 且
  它的 ``value`` attribute 是 generator function 的返回值.
  进一步, 对于 ``yield from`` 以及 ``await``, 这个 ``value`` 很自然地
  成为了整个表达式的值.

- 同理, ``StopAsyncIteration`` 只应该 raised by ``__anext__()``.

- 对于 pipe, write 端 close fd 时, read 端读时触发 EOF. 此时 read() == "".

- 命令行上 oneline 的动态输出可以用 sys.stdout.write("...\r")
  多行的动态输出的话就得用 curses 之类的

- 小心 python 中的 cyclic import problem. 需要明确, python 中对 import statement
  的执行逻辑 (排除 importlib 等复杂 paths, hooks 之类的问题) 很简单:

    'import' and 'from xxx import yyy' are executable statements.
    They execute when the running program reaches that line.

    If a module is not in sys.modules, then an import creates the
    new module entry in sys.modules and then executes the code in
    the module. It does not return control to the calling module
    until the execution has completed.

    If a module does exist in sys.modules then an import simply
    returns that module whether or not it has completed executing.
    That is the reason why cyclic imports may return modules which
    appear to be partly empty.

    Finally, the executing script runs in a module named __main__,
    importing the script under its own name will create a new module
    unrelated to __main__.

  因此, 对于以下两个文件

  .. code:: python

    # abcc.py
    from deff import xxx
    def yyy():
        pass
    # deff.py
    from abcc import yyy
    def xxx():
        pass

  会出现错误, 例如从 abcc 开始, import deff, 执行 deff, 又 import abcc, 而
  此时 abcc 的 namespace 里还没有定义 yyy.

  一个治标不治本的方法是遇到这个问题时, 不要使用全局 import, 在使用处使用
  local import, 例如在函数里 import.
  然而事实上, 从代码逻辑角度看, 这种问题根本不该出现. 一个合理的代码模块提供
  的功能, 使用 global import 与 local import 不该有任何使用上的区别. 没有
  任何正经的 python module 存在这个问题. 公共逻辑应该放在一个单独的模块中,
  然后各个执行者都从这个模块中 import 公共的功能.

- testing methods:

  * python -W default, 所有 warnings 都显示, 即开启默认不显示的那些警告

  * doctest

  * unittest

- Creating pipelines with subprocess
  It is possible to create process pipelines using ``subprocess.Popen``,
  by just using ``stdout=subprocess.PIPE`` and ``stdin=otherproc.stdout``.
  开启第二个子进程后, 需要在父进程中关闭前一个子进程的 `stdout`. 这样
  pipe 的两端才分别只有一个 fd 连接着, 保证了 SIGPIPE 的生成.
  Ref: http://www.enricozini.org/blog/2009/debian/python-pipes/

- python 中每个线程本质上成为 cpython interpreter 的线程.
  默认情况下, 最后一个 "普通线程" 退出后解释器退出, 即程序终结.
  `threading.Thread` class 的 `daemon` attribute 实际意思是将一个线程标记为
  所谓 "后台线程", daemon thread 不是 "普通线程", 不在程序是否退出的考虑范围内.
  因此, 相应线程可能受到影响, 比如资源未释放等.

- Set and Change buffering mode

  若要修改 stdout/err stream (text stream) 的 buffering mode, 可以 ``open``
  来 reopen underlying file descriptor in other buffering mode:

  .. code:: python

    sys.stdout = open(sys.stdout.fileno(), mode="w", encoding="utf-8", buffering=1024)
    sys.stderr = open(sys.stderr.fileno(), mode="w", encoding="utf-8", buffering=1024)

- 线程的目的不仅仅是为了 *同时的* 并行计算, 而是为了构建多个独立的运算单元.
  将这些运算单元分配到不同的 CPU 核上才具有 "同时并行" (parallel computing) 的意义.
  python 虽然有 GIL, 但这影响的是单 python 进程进行 parallel computing 的能力,
  并没有影响多线程所带来的其他可能性.

- 通过 `Thread` object constructor 的 `args`, `kwargs` 参数传递的只应该是 `target`
  API 所需的量. 并不是说线程间共享的量都需要从这里传递进线程.
  在设计时, 不要忘了模块化, 设计一个线程的逻辑时, 只该考虑这个线程的事务, 不该考虑其他
  线程如何调用这个线程.

- 如何 redirect stdin/out/err:
  1. 直接给 `sys.stdin|stdout|stderr` 赋值一个新的 file-like object.
  2. 使用 python3 的 `contextlib.redirect_stdin|stdout|stderr`.
  3. 注意以上方法都只是在 python 层面上转移了 stream, cpython 解释器的 fd 0,1,2
     根本没受影响. 根本的办法是调用 `os.dup2()` 直接将想要的目的文件 fd 复制到
     0,1,2 fd 上面. 例如:

     .. code:: python

       class _RedirectStream:
           """
           Redirect standard stream `_stream` at OS level, rather at python level.

           This differs with `contextlib._RedirectStream`.
           """

           _stream = None

           def __init__(self, target_stream):
               self._target_stream = target_stream
               self.new_fd = target_stream.fileno()
               self.old_fd = None
               self.copied = None

           def __enter__(self):
               stream = getattr(sys, self._stream)
               stream.flush() # flush buffer that dup2 knows nothing about
               self.old_fd = stream.fileno()
               self.copied = os.dup(self.old_fd)
               os.dup2(self.new_fd, self.old_fd)
               return self._target_stream

           def __exit__(self, type, value, tb):
               self._target_stream.flush()
               os.dup2(self.copied, self.old_fd)
               os.close(self.copied)
               return False

- python3.6+ class 的 `__dict__` 中 key 的顺序符合 method 定义的顺序,
  函数的 `**kwargs` dict 中 key 的顺序符合 keyword args 的传递顺序.
  实际上 `dict` 类型实现了 key-order 符合 key 的 creation order. 在 dict 的 ``repr``
  输出中, key 按照 collating sequence order 来排序, 但实际的 key 的顺序仍然是
  creation order, 这可以用 ``dict.keys()`` 看出. 但是我还是更愿意用 `OrderedDict`.
  Ref: https://mail.python.org/pipermail/python-dev/2016-September/146327.html
  Ref: https://docs.python.org/3/whatsnew/3.6.html#pep-520-preserving-class-attribute-definition-order

- 关于 list 和 dict 等 `Sequence` 和 `Mapping` iteration 的一些 pitfalls.

  * 不该在 iterate list 或 map 的时候修改原对象的长度, 例如删除 element or key-val pair.
    对于 dict, 在 py3 中这是 `RuntimeError: dictionary changed size during iteration`.
    若要修改, 见下述.

  * 对于 list, 若要在 iterate 时修改, 先复制一份: ``list(list)`` 或 ``list[:]``.
    遍历复制过的 list, 修改原 list. 对 dict 的类似操作也是同理.

  * ``dict.keys|.values|.items|dict.__iter__`` 这些 methods 返回的 view object
    与原 dict 是同步的.
    因此, 若在 iterate 这些 view object 时要对原来字典的 key, value 等进行修改,
    需要在必须的时候先生成一个 list, 即 ``list(dict.keys())``, 再遍历这个 list.
    否则, 遍历时, 可能会漏掉一些项或重复一些项.

- class decorator 应该应用于对它修饰的 class 的某些方面进行修改, 进行某种外部
  注册等等并不覆盖 class 本身的行为, 或者创建一个新类对原有的类进行覆盖命名.
  前两种的很好的应用有 ``django.utils.python_2_unicode_compatible``,
  ``django.contrib.admin.register``.
  上述最后一种行为, 在操作逻辑上类似与常见的 function decorator.

  一些情况下, class decorator 和 ``super()`` 在 python2 中不能配合使用. 例如,
  decorator 要创建新类覆盖原来的类, 或者 decorator 中要对类进行实例化.
  出现这些问题是因为 py2 中 ``super`` 第一参数需要写明类的名字, py3 中 super
  可以无参数, 不存在这个问题.

  .. code:: python

    def overwrite_class(cls):
        new_class = type(cls.__name__ + 'Dec', (cls,), {})
        return new_class

    def instantiate_class(cls):
        cls()
        return cls

    class Base(object):
        def __init__(self):
            print('Base init')

    # python2
    @overwrite_class
    class MyClass(Base):
        def __init__(self):
            print('MyClass init')
            # 此处 MyClass 引用全局的 MyClass, 后者是被覆盖命名的, 其
            # 父类仍然是这里的 MyClass 定义, 所以造成无限递归.
            super(MyClass, self).__init__()

    @instantiate_class
    class MyClass(Base):
        def __init__(self):
            print('MyClass init')
            # 该类在 decorator 中实例化时, MyClass identifier 尚未
            # bind 至 module 的 global namespace 中, 因此这里会
            # 报错: NameError: global name 'MyClass' is not defined
            super(MyClass, self).__init__()

    # python3
    @overwrite_class
    class MyClass(Base):
        def __init__(self):
            print('MyClass init')
            # 隐性的 __cls__ 直接引用类对象本身, 不依赖于类是否有全局名称.
            # 因此不存在问题.
            super().__init__()


- python3.4+ 中, module ``__file__`` attribute 总是该 module 的绝对路径, 唯一的
  例外是作为 `__main__` module 执行的命令行脚本. ``__main__.__file__`` 的值与
  命令行上的脚本路径一致, 因此可能是相对或绝对. 这有助于对脚本 invocation 方式
  的判断.
  ref: https://docs.python.org/3.4/whatsnew/3.4.html#other-language-changes

- 关于编译. 直接在命令行上指定的 python module (一般是可执行脚本) 的编译结果不会被
  cache 到文件系统中.
  编译的 pyc 文件是 platform-independent.
  对于 non-source distribution, 编译后的 pyc 必须位于源文件的目录, 且必须不能有源文件
  存在.
  `-O` 去掉 ``assert``, `-OO` 进一步去掉 docstring. 因此一般不该也不需要优化编译.
  编译只会加载更快, 不会影响运行速度.
  ref: https://docs.python.org/3/tutorial/modules.html#compiled-python-files

- py3 中, bytes 的 printf-style string formatting 支持 `b` 和 `a` conversion
  specifiers. `b` 生成对象的 bytes object 表达形式 (``__bytes__`` 或 buffer
  protocol); `a` 生成对象的 ascii 表达形式, 即将所有非 ascii 字符转义为
  escape sequence, 严格地讲是 ``repr(obj).encode("ascii", "backslashreplace")``.
  bytes 的 `b` 和 `a` 对应于 str 的 `s` 和 `r`. 对于 str, 也有 `a` 这种 ascii
  表达形式.

- py3 中, exception object 包含一切 exception 相关信息::

    exc_type    type(e)
    exc_value   e
    traceback   e.__traceback__

- py3 中没有 basestring, 因为 string 就是 string, bytes 就是 bytes, 是两码事.

- 不要随便使用 binary IO mode, 只有需要时才使用. 如果担心 line ending 的转换
  等问题, 使用 ``open`` 的 `newline` 参数.

- 不能 ``bytes("string")``, 必须指定 `encoding`.

- `zip` object is iterator, i.e. it has ``__next__`` method.
  所以 `zip` object 不能重复使用.

- py3 中 `int` type 自带与 bytes 相关的方法:
  `int.from_bytes`, `int.to_bytes`, `int.bit_length`.

- 对于 py2 py3 兼容的代码, 不能用 py3 特有的 syntax, 否则在解释器第一步 lexical analysis
  时就会报出 `SyntaxError`. 所以必须用 py2 py3 兼容的语法来写. 然后在 runtime 对不同版本
  的 python 进行不同的调用和处理.

- python 的 executable script 一般设计为把代码的主要实现部分放在一个 package/module
  中, 将极其少量的调用部分, 即 entrypoint 放在单独的可执行脚本中. 并且该 entrypoint
  具有明确的返回值, 如下所示:

  .. code:: python

    import sys
    from somemodule import main # main is the typical entrypoint name
    # ...
    # preparations
    # ...
    sys.exit(main(...))

- ``install_requires`` of `setup.py` vs `requirements.txt`:

  * ``install_requires`` 是从 package 发布的角度来规定 dependency 的.
    因此, 它规定的依赖应该是这个 package 的直接依赖, 并且它对 deps 的版本限定是
    必要性的限制, 即不满足这些限制这个 package 就无法正常工作. 这也意味着, 不该
    限制 deps 的来源, 只该相对抽象地说明所需的包和至少需要满足的版本.

  * `requirements.txt` 是从部署和运行的角度来规定要安装的包和所需安装的版本的.
    也就是说, `requirements.txt` 描述的是 "如果你想要干某件事情, 你需要首先
    用 `pip` 保证当前环境满足 `requirements`". 比如, 某个软件包含一系列 python
    写的 tests, 那么要运行这些测试, 当前环境需满足 `test_requirements.txt` 里
    指定的条件. 注意我们不是要发布这些测试代码, 因此没有 `setup.py`. 再比如,
    生产部署时当然需要所有包的版本是固定的经过测试的, 所以应该将开发或测试环境
    中的所有 packages 用 `pip freeze` 生成一个严格的包含所有 packages 的列表,
    以在生产机器上对这个完整的 python 运行环境进行 repeatable installation.

  Ref: https://packaging.python.org/requirements/

- 正则表达式字符串应该使用 raw string. 这样才能保证出现在字符串中的任何 literal 字符,
  经过解释器处理之后得到的值仍然是其原始的 literal 的内容.
  这是因为 ``\`` backslash 在 string syntax 和 regex syntax 都是转义作用. 所以对于一个
  正则的 escape sequence, 在代码中输入时必须 escape 两次. 为避免这种 clumzy, 需要使用
  raw string.

- 正则的 ``$`` 会匹配

  * end of string 这个空字符串位置

  * just before the newline at the end of string 这个空字符串位置, 因此 ``abc\n`` 有
    两个可能的匹配位置.

  在 multiline mode, ``^`` ``$`` 还会对以 ``\n`` 分隔的每行进行匹配.

- python 正则的 character class 只支持 single char escape sequence, 不支持 ``[:name:]``
  形式的 POSIX name.

- 设置了 ``re.VERBOSE`` flag 的 pattern 里, 所有的 whitespace chars, 无论所在的位置和
  层级, 都会被忽略掉, 除了两种情况:

  * 位于 character class 中的 whitespace chars, e.g., ``[ \t\n]``.

  * 使用 ``\`` 进行转义的 whitespace chars.

  此外, 在 char class 以外且没有被转义的 ``#`` 代表 comment 的开始.

- Indentation is rejected as inconsistent if a source file mixes tabs and spaces
  in a way that makes the meaning dependent on the worth of a tab in spaces;
  a `TabError` is raised in that case.

  .. code:: python

    if __name__ == '__main__':
        print("b")
    	print("a")

  以上代码会导致 `TabError`, 因为 第二行 print 前面是 tab char, 对它的大小的解释依赖于
  第一行.

- ``types.DynamicClassAttribute`` 是个 descriptor, 它在很大程度上的使用与 `property`
  类似, 而它的目的是如果对这个 attribute 的访问是基于 class, 则转至 ``__getattr__``,
  从而达到对结果自定义的目的. ``enum.Enum`` 是个例子. `Enum` 需要使用
  ``DynamicClassAttribute`` 的原因是:

  - `Enum` object 需要支持 ``.name`` ``.value`` 两个属性.

  - `Enum` 的成员可能命名为 ``name`` ``value`` 等, 这要求 ``Enum.name`` 是 instance,
    ``Enum.name.name`` 是 instance 的名字. 所以需要对 ``.name`` attribute 的访问进行
    控制.

- locale settings, 准确地讲是 ``LC_CTYPE`` 会影响 python 中读写文件系统时使用的
  encoding, 即 `sys.getfilesystemencoding()`. 所以为了保证 UTF-8 的 filesystem encoding,
  恰当的 locale 环境变量必须被设置. 否则的话, 默认的 C locale 会导致 filesystem encoding
  变成 ascii. 在读取普遍编码为 utf-8 的 linux 文件系统时会报错.

- `os` module 里有大量的 syscall wrapper.

- 关于 python3 中 filesystem encoding 的处理相关问题.

  * 默认情况下, 所有的文件系统上的文件路径都会使用固定的 encoding 来 decode/encode.
    这样, 在 python 中出现的路径默认都是解码后的 str 类型量, 而不是原始的 bytes.
    这个固定的 encoding 可以通过 ``getfilesystemencoding`` 来获取, 通过 ``LC_CTYPE``
    来设置或影响. 在 encode/decode 过程中的错误处理, 则是依据 ``getfilesystemencodeerrors``
    来获取.

    这个 encode/decode 的结果, 可以通过 ``os.fsencode`` ``os.fsdecode`` 来模拟.

  * `os`, `os.path` 中的很多函数支持输入 str 或 bytes 两种类型参数, 输入前者时结果中
    自动对文件名进行默认的 encode/decode 处理; 输入后者时则返回 bytes 不做处理.

  * ``sys.argv`` 的值已经用 ``os.fsdecode`` 解成了 str. 若要访问原始的 bytes 参数值,
    应对参数值 ``os.fsencode``.

  * 环境变量的访问, 提供了 ``os.environ`` 和 ``os.environb``, 分别是解码和未解码的版本.
    对这个 dict 直接写入或删除会自动调用 ``putenv()``, ``unsetenv()`` 来修改 python
    解释器进程的环境变量. 从而在 python 层和系统层一致. 但反之, 使用 ``os.putenv``
    ``os.unsetenv`` 并不会将修改同步至 ``os.environ``, 只会修改本进程的环境变量.

  * 在 unix-like 系统中, 这种自动的编码解码使用的 error handler 是 ``'surrogateescape'``.
    使用这个 error handler, 解码时无法识别的 bytes 会转换成一个 unicode 字符集中的占位
    字符, 从而保留了全部原始信息, 保证了再次编码时能够恢复原始的 bytes.

  * 在 native unix-like 文件系统中, 一般情况下使用 str 类型量作为 pathname 是最合适的.
    str 与 bytes 对一个 pathname 的表达通过 ``os.fsencode`` ``os.fsdecode`` 来转换.
    如果一个程序需要处理任意编码的二进制文件名 (比如使用了不同的字符编码集), 则应该
    在程序中使用 bytes objects 来代表 pathname.

- ``__str__`` 默认 call ``__repr__``, 反之却不行. 即严格的 ``__repr__`` 可以代替
  informal 的 ``__str__``, 但反之不能用 ``__str__`` 代替 ``__repr__``.

- ``__debug__`` 是 builtin constant. cpython 解释器默认情况下设置 ``__debug__ == True``,
  此时 assert statement 会执行; 使用 ``-O`` 命令行参数后为 ``False``, assert statement
  被忽略.

- 启动时, `site` module 会给 __main__ module 增加 ``quit()``, ``exit()``, ``copyright``,
  ``license``, ``credits`` 这几个 callable 对象. 它们本意是用在 interactive session 中,
  平时的程序中不该使用它们.

- ``NotImplemented`` vs ``NotImplementedError``

  * ``NotImplemented`` 用在 rich comparison method 中. 当某个 rich comparison method
    返回该值时, 表示该操作 (在这个具体情况下) 未被实现.

  * ``NotImplementedError`` 用在 method definition 中, 就是一个 exception,
    表示该方法没有被实现或尚未完成. 例如是一个 abstract method, 或者是开发中的
    method.

- ``urlparse`` vs ``urlsplit`` of `urllib.parse`

  URL 的一般化结构::

    scheme://netloc/path1;params[/path2;params...]?query#fragment

  注意根据 RFC 2396 ``params`` 是属于每个 path segment 的. ``urlparse`` 会把最后一个
  path segment 的参数和路径本身分隔开来, 这是不合规范的. ``urlsplit`` 不会这样做.
  所以 ``SplitResult`` 比 ``ParseResult`` 少一个 ``params`` attribute.

  一般情况下, 应该用 ``urlsplit``.

- buffer protocol 的意义在于避免复制内存, 使得代码高效很多, 甚至接近 C/C++ 代码效率.
  ``bytes``, ``bytearray``, ``array.array`` 都实现了 buffer protocol. 配合
  ``memoryview`` 和 file-like object 的 ``.readinto`` 和 socket object 的 ``.recv_into``
  等 methods, 达到避免复制的目的.

- ``\b`` backspace char 只是把光标向左移动 1 格, 并不删除涉及的字符;
  ``\r`` carriage return 只是把光标移至当前行首, 并不删除本行内容.

- C 的 struct 对应到 python 中和 struct sequence 或 namedtuple 比较像.

  namedtuple 为了和 field 区分, 它自己的 methods 和 attributes 都是以 ``_`` 起始的,
  让人误以为 API 只有 tuple 的 ``.index`` 和 ``.count``. 实际上它还有 ``_make``,
  ``_asdict``, ``_replace``, ``_source``, ``_fields``.

- ``key=`` parameter in various comparison-related functions

  ``sorted``, ``list.sort``, ``max``, ``min`` 等函数中的 ``key`` keyword parameter
  的逻辑是输入要排序的元素, 然后对每个元素的输出值进行比较, 根据这个比较结果进行排序.
  注意它不要求 key function 输出的是数值, 而只需要是可以比较大小的 object 即可.

  ``functools.cmp_to_key`` 通过实现 rich comparison methods 很好地利用了这一点.

- User-defined class 默认有 ``__hash__`` method. 这个默认的 hash method 保证它实例
  的 hash value 与该实例的 identity 一致. 即所有实例的 hash 不同, 通过 hash 值可以
  判断是否是同一个对象.

- binascii, base64, hashlib

  * binascii 包含 binary data 和各种基于 ASCII 的 printable 表达形式或编码形式,
    以及一些底层相关函数, crc 函数也在这里.

  * base64 包含更丰富的统一的 binary data 和各种进制转换.

  * hashlib 包含各种 hash 以及相关函数.

- python 中, 各种 protocol 实际上是各种 interface 的规定. 满足这些协议 (interface)
  就可以按照相应的方式去使用. 这是 duck typing 的一种体现.

  已知的 protocols 有:

  * sequence protocol

  * iterator protocol

  * mapping protocol

  * context manager protocol

  * descriptor protocol

  * buffer protocol

  * file-like object protocol

- ``range`` 实际上是一种 builtin immutable sequence type. 因此支持常见的 sequence
  interface. 严格地讲, 就是 ``collections.abc.Sequence`` ABC 所定义的 interface.
  它是 iterable, 但不是 iterator.

- python 的一些函数和 haskell 类似函数的对应关系.

  * builtin::

      filter(pred, iterable)                 filter pred iterable
      range(stop)                            [0..(stop-1)]
      range(start, stop[, step])             [start,(start+step)..(stop-1)]

  * itertools::

      count(start[, step])                   [start,(start+step),..]
      cycle(p)                               cycle p
      repeat(elem[, n])                      take n $ repeat elem
      accumulate(p[, func])                  scanl1 func p
      takewhile(pred, iterable)              takeWhile pred iterable
      dropwhile(pred, iterable)              dropWhile pred iterable

  * functools::

      reduce(func, iterable[, initial])      foldl func initial iterable

- ``itertools.chain`` 用于将 iterable 连在一起, ``collections.ChainMap``
  用于将 mapping 连在一起.

- `struct` module

  * 对于一个 structure format ``fmt``, padding 只有在结构体成员之间自动添加, 而没有
    识别结构体末尾的 padding. 如需在 ``fmt`` 中指定结构体末尾的 padding, 可以用两种
    方式: 使用 ``x`` 来明确添加指定个数的 padding; 使用 ``0[t]`` 来隐性地添加 padding,
    其中 ``[t]`` 为 structure 的 alignment requirement (即结构体中各元素的最大 alignment
    需求).

  * 默认的模式是 ``@``, 即 byteorder, size of primitive types, alignment 都采用
    native value. 此外还有 ``=``, ``<``, ``>``.

- lambda 表达式中的变量是局域化在 Lambda 函数定义表达式中的:

  .. code:: python

    >>> [lambda x: x+1 for x in range(10)]
    [<function __main__.<listcomp>.<lambda>>,
    <function __main__.<listcomp>.<lambda>>,
    <function __main__.<listcomp>.<lambda>>,
    <function __main__.<listcomp>.<lambda>>,
    <function __main__.<listcomp>.<lambda>>,
    <function __main__.<listcomp>.<lambda>>,
    <function __main__.<listcomp>.<lambda>>,
    <function __main__.<listcomp>.<lambda>>,
    <function __main__.<listcomp>.<lambda>>,
    <function __main__.<listcomp>.<lambda>>]

  ``lambda :`` operator 的优先级是所有算符中最低的. 注意 ``lambda`` 和 ``:`` 是一体的,
  整个部分要作为一个整体.

- 默认的 ``sys.stdout`` file-like object 使用的 error handler 是 strict,
  而 ``sys.stderr`` 使用的是 backslashreplace (从而向 stderr 输出的错误信息
  本身不会再次触发一个 ``UnicodeEncodeError``, 让原本的 traceback 等错误信息
  更加 messy). 若我们向 stdout 输出的信息可能包含无法编码的 binary data, 可以
  替换一个使用 backslashreplace handler 的对象:

  .. code:: python

    sys.stdout = open(sys.stdout.fileno(), mode="w", errors="backslashreplace")

- membership test ``x in y`` 如何进行判断.

  * For container types such as list, tuple, set, frozenset, dict, or collections.deque,
    the expression ``x in y`` is equivalent to ``any(x is e or x == e for e in y)``.
    也就是说, 对于 builtin container types, 一个对象 ``is`` 或者 ``==`` container
    中的某个元素, 则认为这个对象在 container 中.

  * For the string and bytes types, ``x in y`` is True if and only if
    x is a substring of y.

  * 对于 user-defined class, 若实现了 ``__contains__`` 则使用这个 special methods
    来进行判断.

  * 对于 user-defined class, 若没有实现 ``__contains__``, 但实现了 ``__iter__``
    则通过 iterate 生成的 iterator 来找相等的元素.

- 如何创建 read-only class attribute?

  在 metaclass 上设置 property 确实管用.
  但 1) 对于每个需要 readonly property 的类需要写一个单独的 metaclass, 当需要
  功能合并时还需要不同的 metaclass 继承出一个综合的 metaclass. 太麻烦了.
  2) 根据 attribute lookup 规则, 这样的 readonly property 在 instance 中不可见. 

  .. code:: python

    class Meta(type):
        @property
        def x(cls):
            return 1

    class A(metaclass=Meta):
        pass

  其实在 python 中, 由于没有真正的访问限制, 没有真正能做到的 readonly class
  attribute.

- django extension packages can be found on https://djangopackages.org/
  and downloaded from PyPI.

- string literal concatenation 操作具有最高优先级, 高于 subscription, slicing,
  call, attribute reference 等操作. 例如

  .. code:: python

    >>> ("{} sef"
    ... "sefe".format(111))
    '111 sefsefe'

- 当一个系统中需要多个 python 版本, 不同项目需要不同版本时, 或者仅仅是不想使用
  系统自带的 python 时, 使用 pyenv.
  当不同项目需要同一个 python 版本, 但各自的依赖有冲突时, 或者仅仅是因为不想
  将 package 安装至全局范围内时, 使用 venv.

- choose dnf/apt/pacman vs pip for python module installations

  * 系统层的 python 解释器被所有程序和脚本共用, 尤其是来自发行版的脚本或者
    通过 package manager 安装的 python 应用. 因此应该使用系统的 package manager
    去安装 python modules. 这样才能保证各个 package 的版本依赖关系得到满足,
    即某个 module 的版本是经过和其他 package 的兼容性测试的.

    注意不要在全局范围内使用 pip 安装 packages. 这可能导致的麻烦有: 1) 覆盖
    package manager 管理的版本, 导致 package manager 数据库与实际情况不符,
    出现 broken dependency; 2) 存在一些安全问题. ``sudo pip`` 相当于执行
    任意从 PyPI 上下载的 python 代码.

  * 对于某个用户希望自己能够随处使用的时候, 使用 ``pip install --user``
    安装到 ``$HOME/.local`` 下.

  * 某个项目或某个程序自己使用的 python, 构建 virtualenv/venv, 使用 pip 安装.
    这将 modules 安装在局限的范围内, 与全局独立.

    若该项目需要特别的 python 版本, 还可考虑使用 pyenv.

  * 对于容器化的应用, 一般可以在全局范围内直接使用 pip 安装. 因为该应用本身是
    容器环境内执行的唯一一组程序, 整个环境即为其服务的, 并且容器不存在系统
    升级问题, 所以上述系统层的考虑并不适用, 可以放心地安装.

    但是这不是 100% 可行的. 如果应用的 python 依赖与某些系统工具的 python 依赖
    冲突, 则应该引入 virtualenv.

  * 容器使用 multi-stage build 时, 更应该使用 virtual env, 这样可以将构建后的
    应用和依赖一起方便地复制到 production 镜像中.

- 一个 package 中的 private module/subpackage 命名应该以 ``_`` 起始.

- shutil

  * ``copy()`` 和 ``copy2`` 的区别是, 后者保留所有 metadata, 包含权限, atime,
    ctime, mtime 等; 前者只保留权限信息.

  * ``shutil.chown`` 比 ``os.chown`` 方便, 后者只接受 uid/gid.

- zipfile

  * "a" mode of ``ZipFile`` object:

    If mode is 'a' and file refers to an existing ZIP file, then additional
    files are added to it. If file does not refer to a ZIP file, then a new
    ZIP archive is appended to the file. If mode is 'a' and the file does not
    exist at all, it is created.

    在没有 ``zipapp`` 的 py2 中, 可以用这个技巧生成 pyz executable.

- DB-API

  如果 ``cursor.execute()`` 中的 sql 语句需要填入 SQL 语句里的 identifier
  部分, 则不能放在 ``args`` 参数里. 这个参数只包含值的填充. 这么涉及的理由
  可能是需要防着用户进行 sql injection 的只有值, 根本不该去允许用户去修改别的
  东西, 比如动态创建数据库.

- Zen of Python --- ``this`` module --- ROT13 algorithm

- extended indexing syntax, slice object

  * extended indexing syntax 的形式是 ``[a:b:c]``. 本质上转换为 ``[slice(a, b, c)]``.

  * slice object 代表一系列 index 的值. start, stop, step 三项的默认值都是 ``None``.
    ``slice(3) == slice(None, 3, None)``.

  * 由于 extended indexing 本质转换为 slice object, 所以 a, b, c 可以任意省去,
    以 None 代替. 整个 slice object 传递给 ``[]`` index 算符左侧的对象的
    ``__getitem__`` method. 这个方法解析 slice object 的 start/stop/step,
    按规则输出所需的值.

  * 对于 list, 它的 ``__getitem__`` 则定义 a 默认是 0, b 默认是 len(list), c 默认是 1.

  * slice object 本身不限制 start/stop/step 值的类型. 只是作用在的对象去限制.
    例如 ``[1 : 2, 3] -> [slice(1, (2, 3), None)]``.

- duck typing

  * Python 的 duck typing 思想与物理学思想有点像. 即我们认识事物的方式是根据事物
    表现出来的行为, 而不是事物的所谓 "本质". 这样的本质并不存在, 因其不可观测.

- 实数转换整数应该用 ``round()``, 它基本上会达到四舍五入的效果. ``int()`` 的话,
  若输入是 ``float``, 结果是 truncate to zero 的.

- 矩阵转置.

  .. code:: python

    orig = [(1,2,3,4), (5,6,7,8)]
    rotated = list(zip(*orig))
    orig = list(zip(*rotated))

- django 非常方便, 但不要局限于 django magic 本身. 要明白它的原理, 它的局限,
  web 各部分的工作机制是如何与 django 的抽象相对应的. 这样才知道何时该扩展它,
  在什么部分可以用更合适的东西取代它等等. 灵活地使用, 而不是局限在它的条条框框
  之中.

- csv module.

  * reader, writer objects wrap a source or target. reader 所需的 object 要满足
    iterator protocol 即可, writer 所需的 object 具有 ``.write()`` method 即可.
    对于 file object, 应该用 ``newline=""``.

  * DictReader, DictWriter 在 reader/writer 基础上, 加入了 field names 概念.
    DictReader 返回的行从 list 改成 OrderedDict, 从而
    能够同时表示所在列的名字和位置; DictWriter 的 ``.writerow()`` 只需 dict 即可,
    因位置在 fieldnames 中有规定.

  * reader/DictReader 的主要使用方式是 ``for row in csvfile``;
    writer/DictWriter 的主要使用方式是 ``.writeheader()``, ``.writerow()``,
    ``.writerows()``.
    ``.writerow()`` 的返回值是 constructor 中 target object 的 ``.write()`` method
    的返回值.

  * py2 的 csv module 不直接支持 unicode 输入输出. 需要单独做封装处理.

- 对于包含可选参数的 decorator, 应该按照如下方式去设计:

  .. code:: python

    def decorator(func=None, arg1=None, arg2=None):
        def actual_decorator():
            pass # decorator logic using arg1, arg2
        if func:
            return actual_decorator(func)
        else:
            return actual_decorator

  这样的 decorator 可以加参数或不加参数两种方式使用.

  .. code:: python

    @decorator
    def f():
        pass
    @decorator(arg1=1, arg2=2)
    def g():
        pass

- 一个 decorator 在定义时, 可能只适用于函数; 也可能只适用于 method (即单独考虑到了
  对 ``self`` 的处理); 若要同时支持函数和 method, 除了简单地传递 ``*args`` (从而
  无论有无 ``self`` 参数原始 function/method 都能收到预期参数) 之外, 并无很好地
  通用解决办法. 也就是说, 如果一个 decorator 对 wrap 的函数的参数形式有限制, 则
  该 decorator 无法直接应用到包含 self 的同类 method 上 (显然参数形式发生了变化).
  这时, 可以参考 ``django.utils.decorators.method_decorator`` 的实现, 将 decorator
  转化为预期有 self 参数的新的 decorator.

- 我们不可能定义一个 instance method decorator, 作用在本类的其他 method 上.
  这是因为 decorator syntax 要求它作用在 class namespace 中, 在这里 self
  实例尚未定义.

- 只有需要考虑到跨平台兼容性时, 才使用 ``os.path.join`` 和 ``os.path.sep``
  来构建路径; 对于仅在 unix 平台上运行的程序, 直接写 ``/path/some/thing``
  就好了, 这样比较可读和方便. 或者在 python3.6+ 可以一概都使用 ``pathlib.Path``,
  即跨平台, 又方便使用. (py3.6+ 是因为 ``os.PathLike`` 是在 py3.6 引入的.)

- ``django.utils.functional.cached_property`` 是将调用 class 上 property 函数的
  访问结果 cache 到 instance 的同名 attr 上. 代价是这个 attr 是可写的. 使用与之
  类似的方式, 我们之后写涉及复杂计算的 property 时, 不需要手动设置一个 private
  attr 作为 cache:

  .. code:: python

    class cached_property(object):
        """
        Decorator that converts a method with a single self argument into a
        property cached on the instance.

        Optional ``name`` argument allows you to make cached properties of other
        methods. (e.g.  url = cached_property(get_absolute_url, name='url') )
        """
        def __init__(self, func, name=None):
            self.func = func
            self.__doc__ = getattr(func, '__doc__')
            self.name = name or func.__name__

        def __get__(self, instance, cls=None):
            if instance is None:
                return self
            res = instance.__dict__[self.name] = self.func(instance)
            return res

  进一步, 我们可以扩展该代码让它不仅可读, 也可写. 见
  `snippet <snippets/mutable_cached_property.py>`_.

- 需要缓存结果时的几种方式:

  * property + private attribute

  * cached_property, 优点是比上个方法干净, 缺点是可以直接写. 起不到一点防范的作用.

  * lru_cache, 当只是缓存函数执行结果时, 而不是 object method 时使用.

- 注意大部分情况下可以使用 function 的地方都可以一般化地使用 callable. 定义
  callable class 有助于优化代码组织方式和重用等可能性 (应用所有 class 的优点
  来定义 function).

  反之亦然. 所以, 很多时候, 以下两种形式是效果类似的:

  .. code:: python

    def callable_factory(*args, **kwargs):
        def actual_callable(*args_, **kwargs_):
            # use args, kwargs, args_, kwargs_
        return actual_callable

    class Clazz:

        def __init__(self, *args, **kwargs):
            # use args, kwargs

        def __call__(self, *args_, **kwargs_):
            # use args_, kwargs_

- variable definition, scope & UnboundLocalError.

  python 语法没有单纯的变量定义语句. 它定义凡是 在一个 scope 中赋值的变量,
  都是这个 scope 下的 local variable. (这么定义 是为了简化变量引用逻辑,
  让程序易懂.  然后对于在 scope 中只引用不修改的量 则定义 free variable 概念.
  对于需要修改的 outer scope 量定义 global/nonlocal keyword.)

  然而在解释器在运行代码时, 遇到一个 scope, 创建一个 stack, 是必须要首先给 scope
  中定义的各个变量创建内存区域的. 也就是说, 需要在 scope 起始处 implicitly 定义
  各个变量. 这就是 UnboundLocalError 的由来.

  考虑以下代码:

  .. code:: python

    x = 10
    def foo():
        print(x)
        x += 1
    foo()

  解释器在解释 foo function body 时, 发现 x 有赋值, 故认为是 local definition,
  在起始处添加定义 (但注意不赋值). 这导致 global x 被 shadow, 并且 print(x)
  语句引用了未赋值的 x, 导致
  ``UnboundLocalError: local variable 'x' referenced before assignment``
  错误. 这需要注意的是, 解析函数体和运行函数体是不同阶段的事. 发现 local variable
  并添加隐性定义代码是在解析编译阶段做的事. 而上述错误需要等到运行时才能发现.

- 判断两个条件中有且仅有一个条件为真是, 可使用 exclusive OR operator ``^``.

- 简化 ``x == a or x == b`` 之类的条件成 ``x in (a, b)``.

- f-string 和 string.format 不能混用. 因为两者使用相同 ``{...}`` 语法.
  f-string 最先 interpolation, 它就要转换所有 ``{...}`` 的内容.
  但它可以和 %-formating 混用.

- 当代码中需要一个没有任何含义的量来表达某种具有唯一性的东西时, 可以使用
  ``object()``, 而不是 ``None``, 因后者仍然是有意义的. 而每个 object instance
  是唯一的, 且毫无意义的.

- virtual env 中的 sys.path 包含或不包含哪些路径是基于第三方原则. 因此默认不包含
  user 或 system 层的 site-packages directory. 但注意全局的 ``/usr/lib/pythonX.X``
  路径下的 modules, 即 stdlib 部分的、随 python 一同发布的 modules, 以及一些其他
  的 sys.path 中的路径, 都是可以访问的. 因为它们不是第三方的.

- 由于从 instance 上只能读取 class attribute, 不能修改和删除 class attribute,
  所有修改和删除只操作 instance 的 ``__dict__``, 可以将 class attribute 设置为
  属性默认值. 在需要改动的 instance 上再做修改.

- object cleanup.

  cleanup 应该在以下情况下生效:

  * explicit cleanup. 例如 direct cleanup call, or via context manager methods.

  * cleanup during GC.

  * cleanup before interpreter exit.

  通过 ``__del__`` or ``weakref.finalize``. 两者优缺点:

  - 注册 finalize callback 是比实现 ``__del__`` method 更通用的 cleanup 方式.

  - 只有部分对象可以 weak reference, 所以适用性又有限.

  - ``__del__`` method 在 cpython 中使用没有任何问题. 但由于它依赖于解释器的
    GC 机制, 所以不如 finalize callback 通用.

  - ``__del__`` 在实现时比 weakref 容易很多.

- 在 function argument list 中, bare start ``*`` 若要用于分隔 positional &
  keyword-only argument, 后面必须确实有至少一个 argument.

- Sort Stability and Complex Sorts. ``list.sort()`` and ``sorted()`` is
  guaranteed to be stable. That means that when multiple records have the same
  key values, their original order is preserved (or reversed if
  ``reverse=True``). This wonderful property lets you build complex sorts in a
  series of sorting steps. For example, to sort the student data by descending
  grade and then ascending age, do the age sort first and then sort again using
  grade.::

    # sort on secondary key
    s = sorted(student_objects, key=attrgetter('age'))
    # now sort on primary key, descending
    sorted(s, key=attrgetter('grade'), reverse=True)
