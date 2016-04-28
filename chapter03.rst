=============================
第三章: 视图和URL配置
=============================

上一章里我们介绍了如何创建一个Django项目并启动Django的开发服务器。本章你将学到用Django创建动态
网页的基本知识。


你的第一个Django页面：Hello World
===========================================

我们的第一个目标，创建一个网页，输出那个最著名的例子：“Hello world.”。

如果你曾经在没有Web框架的情况编写过一个简单的“Hello world”页面，你只要简单地在一个文本文件中输入“Hello world”
，将文件保存为 ``hello.html`` ，然后上传到你的Web服务器的一个目录中即可。不过注意，在这个过程中，你已经
指定了这个网页的两个关键信息：它的内容(字符串： ``Hello world`` )和它的URL( ``http://www.example.com/hello.html`` ，如果
你吧文件放在一个子目录中，也可能是 ``http://www.example.com/files/hello.html`` )。

在Django中，你也需要指定这两个信息，只不过得用不同的方法。网页的内容是靠视图函数(view function)来生成的，
URL则专门配置在URL配置中(URLconf)。首先，让我们来写我们的“Hello World” view function吧。

第一个视图
---------------

进入到上一章我们用 ``django-admin.py startproject`` 创建的 ``mysite`` 目录中。创建一个叫做
``views.py`` 的空文件。这个Python模块(module)将包含这一章我们将要编写的视图。这个 ``views.py`` 的名字
并没有特别的要求，Django并不要求它叫什么名字，不过根据管理，把它叫做 ``views.py`` 会让其他开发者更容易读懂
你的代码。

我们的“Hello world”视图很简单，完全是写定义函数，和导入包的语句。在 ``views.py`` 中输入这些内容：

::

    from django.http import HttpResponse

    def hello(request):
        return HttpResponse("Hello world")

让我们来逐句分析这段代码：

* 首先，我们从 ``django.http`` 中导入了 ``HttpResponse`` 这个类。我们会在接下来用到这个类。

* 接着，我们定义了一个叫做 ``hello`` 的视图函数(view function)。

  每个view function至少要有一个参数，通常被叫做 ``request`` 。这是一个对象，包含了触犯这个
  view function的Web请求的信息， 它是 ``django.http.HttpRequest`` 的一个示例(instance)。
  在这个例子中，虽然我们不对这个 ``request`` 做任何处理，但是我们还必须要把它作为视图的第一个参数。

  view function的名字并不重要；Django并不需要它以某种特定的方式命名。这里我们叫它 ``hello``
  是因为 ``hello`` 清晰地表明这个视图的用意。你当然也可以叫它 ``hello_wonderful_beautiful_world``
  或者其他差不多恶心的名字。下一小节“第一个URLconf”将会介绍Django如何找到这个函数。

* 这个函数只有一行简单的代码：它仅仅返回一个 ``HttpResponse`` 对象，这个对象包含了文本“Hello world”。

记住，一个视图本身就是一个Python的函数，这个函数接受一个 ``HttpRequest`` 作为它的第一个参数，并返回
一个 ``HttpResponse`` 的实例。要想让一个Python函数成为一个Django的视图，你需要让它满足这两个条件。
（也有例外，我们稍后会讲到。）

第一个URLconf
------------------

现在，如果你再运行 ``python manage.py runserver`` ，你还是会看到“Weclome to Django”的欢迎页面，
而看不到我们刚刚写的“Hello world”视图。因为我们的项目 ``mysite`` 还不知道有 ``hello`` 这个视图；
我们需要显式地告诉Django我们要让哪个URL来激活这个视图。 (这就像上面那个发布静态HTML的例子中，我们已经
创建了HTML文件，但是还没有把它上传到服务器上。) 在Django里，要关联view function到一个URL，需要用到
URLconf。

*URLconf* 就像是你的Django站点的目录。本质上，它是一个URL和这个URL将调用的view function之间的映射关系。
通过这种方式，你就可以告诉Django：“对于这个URL，调用这段代码，对于那个URL，调用那段代码。”例如，当用户访问
``/foo/`` 时，调用view function ``foo_view()`` ， 这个view function在 ``views.py`` 中。

在前一章，你执行 ``django-admin.py startproject`` 时，Django已经自动为你创建了一个
URLconf，就是 ``urls.py`` 那个文件。默认的 ``urls.py`` 会是下面这个样子：

::

    from django.conf.urls import patterns, include, url

    # Uncomment the next two lines to enable the admin:
    # from django.contrib import admin
    # admin.autodiscover()

    urlpatterns = patterns('',
        # Examples:
        # url(r'^$', 'mysite.views.home', name='home'),
        # url(r'^mysite/', include('mysite.foo.urls')),

        # Uncomment the admin/doc line below to enable admin documentation:
        # url(r'^admin/doc/', include('django.contrib.admindocs.urls')),

        # Uncomment the next line to enable the admin:
        # url(r'^admin/', include(admin.site.urls)),
    )

URLconf中带了一些被注释的功能，这些在Django中经常会被用到，所有你只要去掉那些功能的注释就可以
开启这些功能。下面是忽略掉被注释掉的内容后，``urls.py`` 中实际的内容：

::

    from django.conf.urls.defaults import patterns, include, url

    urlpatterns = patterns('',
    )

照例，我们来逐行分析这些代码：

* 第一行从 ``django.conf.urls.defaults`` 中导入了三个函数。这三个函数是Django URLconf的基本构造：
  ``patterns``, ``include`` 和 ``urls`` 。

* 第二行调用 ``patterns`` 这个函数，并把结果保存在一个叫做 ``urlpatterns`` 的变量中。 ``patterns``
  函数接受一个空字符串作为参数。(这个字符串被用作一个视图函数的通用前缀，我们会在 :doc:`chapter08` 深入介绍。)

这样要注意的是 ``urlpatterns`` 这个变量，Django会在你的URLconf模块中寻找它。这个变量定义了
URL和处理这个URL请求的代码的映射关系。默认情况下，URLconf是空白的，你的Django程序还是白板一块。
(如果你的URLconf是空的，Django就会认为这个项目是刚刚创建的，所以它会显示那个欢迎页面。)

要在URLconf中电价一个URL和view，只需要添加一个URL模式和view function的映射即可。这里演示如果
将我们的 ``hello`` 视图关联起来：

::

    from django.conf.urls.defaults import patterns, include, url
    from mysite.views import hello

    urlpatterns = patterns('',
        url(r'^hello/$', hello),
    )

(注意，为了简洁，我们移除了注释代码。如果你喜欢的话，你也可以保留那些行。)

我们做了两处修改：

* 首先，我们从模块 ``mysite/views.py`` 中导入了 ``hello`` 这个view function。Python的导入语法用
  ``mysite.views`` 表示 ``mysite/views.py`` 。(这段代码假设了 ``mysite/views.py`` 在Python的搜索路径中，下面的注释中会有详细介绍。)

* 接着，我们添加了 ``url(r'^hello/$', hello),`` 到 ``urlpatterns`` 中。这一行被叫做一个
  *URLpattern* 。 ``url()`` 函数会告诉Django如何处理你配置的url。 ``url()`` 的第一个参数是一个模式匹配字符串(
  正则表达式，稍后会有详细介绍)，第二个参数处理这个URLpattern的view function。 ``url()`` 还接受第三个参数，在
  :doc:`chapter08` 再详细介绍。

.. admonition:: 注意

  另一个值得注意的地方是，我们在正则表达式字符串前面加了一个 ``r`` 。在Python中，这表示一个原始字符串(raw
  string)，Python不过去转义那些反斜杠(``\``)。在普通的Python字符串中，反斜杠会被用来转义特殊字符，比如字符串
  ``'\n'`` 会被解释成换行符。当你加上 ``r`` 之后，Python就不会转义那些字符了， ``r'\n'`` 就是一个包括了一个反斜杠和一个小写字母“n”的字符串。正则表达式中反斜杠的用法和Python字符串中反斜杠的用法刚好冲突了。所以在Python中使用正则表达式的时候最好都加上 ``r`` 。本书中所有的URLpattern都会用这种形式。

简单来说，我们只是告诉了Django所有对URL ``/hello/`` 的请求都由 ``hello`` 这个view function来处理。

.. admonition:: Python的搜索路径(Python path)

    Python的搜索路径是指你用 ``import`` 导入语句时，Python所查找的系统目录清单。

    举例来说，如果你的Python路径设为 ``['',
    '/usr/lib/python2.7/site-packages', '/home/username/djcode']`` 。如果你执行语句
    ``from foo import bar`` 时，Python先在当前目录寻找叫 ``foo.py`` 的模块(Python路径中的第一个目录，用空字符串表示当前目录)。如果文件不存在，Python会继续查找
    ``/usr/lib/python2.7/site-packages/foo.py`` 。如果还是找不到，Python会接着寻找
    ``/home/username/djcode/foo.py`` 。最后，如果这个文件真的不存在是，Python会抛出异常 ``ImportError`` 。

    要检查你的Python路径，只需要在Python的交互解释器中输入：

    ::

        >>> import sys
        >>> print sys.path

    通常你都不需要去关心Python路径的设置，Python和Django会帮你处理好。
    (Django项目中Python路径的的设置是由 ``manage.py`` 控制的)。

讨论一下URLpattern的语法也是很有必要的，因为它并不那么直观。我们是想要匹配地址 ``/hello`` ，
但是URLpattern里面的匹配模式(pattern)却有些不一样。下面我们来详细介绍一下：

* Django在检查URL模式之前，会移除每个URL最开始的斜杠。这意味着URLpattern并不包括 ``/hello`` 开头那个斜杠。(一开始，这可能不太直观，但是这样做简化了很多事情，我们会在 :doc: chapter08 里详细介绍。)

* 匹配模式里面包括了一个脱字符( ``^`` )和一个美元符( ``$`` )。这两个符号在正则表达式里面的有特别的意义： ``^`` 要求从字符串开头开始匹配， ``$`` 则是要求对字符串尾部进行匹配。

  最好还是用实例来说明。如果我们不用尾部匹配符 ``$`` ，所有以 ``/hello/`` 开头的URL都可以匹配，比如
  ``/hello/foo`` 或者 ``/hello/bar`` 和 ``/hello`` 。类似的，如果我们省掉头部匹配符
  ``^`` ，Django会匹配所有以 ``hello/`` 结尾的URL，比如 ``/foo/bar/hello/`` 。如果我们同时省掉
  这两个字符的话，只要包含了 ``hello/`` 的URL都将会匹配， 比如 ``/foo/hello/bar`` 。所以，加上
  ``^`` 和 ``$`` 是为了保证只有 ``/hello/`` 匹配，不多也不少。

  大多数的URLpattern都会以一个脱字符( ``^`` )开头，以一个美元符( ``$`` )结尾。
  但是拥有匹配复杂URL的灵活性会也很好。

  你也许会问，如果由人访问 ``/hello`` (尾部没有斜杠)会怎样，因为我们的URL模式要求结尾
  有一个斜杠，这样这个URL并不匹配我们定义的模式。不过，默认情况下，一个没有以斜杠结尾的URL
  找不到匹配的URLpattern的话，会被重定向(redirect)到一个添加了斜杠的相同URL去。(者是受Django
  设置里面的 ``APPEND_SLASH`` 选项控制的，参见 附录D_ 。)

  如果你是喜欢所有的URL都以 ``/`` 结尾的人(Django开发者都喜欢这样)。你只需要在每个URL
  后添加斜杠并且设置 ``APPEND_SLASH`` 为 ``True`` 。如果你更愿意不要结尾的那个斜杠，
  或者根据每个URL的情况来决定的话，那么需要将 ``APPEND_SLASH`` 设置为 ``False`` ，
  然后根据你的意愿来添加结尾的斜杠到你想要添加的URL上。

另一个要注意的地方是。这个URLconf中我们是把 ``hello`` 这个view function直接作为一个对象传递的，而不是去调用它。
这是Python(作为动态语言)的一个重要特征，函数是一级对象(first-class objects)，你可以像
传递其它变量一样传递一个函数。很酷吧？

要检查我们配置的URLconf的话，如同第二章一样， 用 ``python manage.py runserver``
启动Django的开发服务器。(如果你还没有关掉之前启动的那个，就不用再启动一个了。Django开发
服务器会自动检测到你做的更改，然后自动加载它们，不需要你去重启它。) 开发服务器会运行在
``http://127.0.0.1:8000/`` ，打开一个浏览器，访问
``http://127.0.0.1:8000/hello/`` 。你应该会看到你的Django视图输出的文本--“Hello world”。

耶! 你用Django成功创建了你的第一个网页了。

.. admonition:: 正则表达式

    正则表达式(*Regular expressions* 或者 *regexes* )是通用的文本模式匹配方法。
    Django的URLconf运行你使用任意的正则表达式来做URL匹配，不过实际情况中，你通常只需要使用到其中很少的一部分功能。下面是写基本的语法。

    ============  ==========================================================
    符号           匹配
    ============  ==========================================================
    ``.`` (dot)   匹配任一字符

    ``\d``        匹配任意一个数字

    ``[A-Z]``     匹配A-Z中的任意一个大写字母(即匹配任意一个大写的英文字母)

    ``[a-z]``     匹配A-Z中的任意一个小写字母(即匹配任意一个小写的英文字母)

    ``[A-Za-z]``  匹配a-z中任意一个字母(不区分大小写)

    ``+``         匹配一个或更多其前面的表达式 (例如： ``\d+`` 匹配一个或多个数字)

    ``[^/]+``     匹配一个或多个不为‘/’的字符

    ``?``         匹配 **零个** 或多个其前面的表达式(例如： ``\d?`` 匹配0个或1个数字)

    ``*``         匹配 **零个** 或多个其前面的表达式(例如： ``\d*`` 匹配0个，1个或多个数字)

    ``{1,3}``     介于1个和3个之前的表达式(例如： ``\d{1,3}`` 匹配1个，2个或者3个数字)
    ============  ==========================================================

    更多关于正则表达式的内容，请查阅 http://www.djangoproject.com/r/python/re-module/ 。

404错误简介
-----------------------------

目前，我们的URLconf中只定义了一个URLpattern：处理到 ``/hello/`` 的请求。如果请求其他的URL会怎么样呢？

运行你的Django开发服务器，然后访问一个类似 ``http://127.0.0.1:8000/goodbye/``, ``http://127.0.0.1:8000/hello/subdirectory/`` 或者是 ``http://127.0.0.1:8000/`` (网站根目录)的页面。你会看到一个“Page
not found”信息(如图3-1所示)。因为你没有定义如何处理这个URL请求，所有Django显示这样的信息。

.. figure:: graphics/chapter03/404.png
   :alt: Django的404页面截图.

   图3-1. Django的404页面

这个页面比原始的404错误信息更加实用，它还显示了哪个URLconf被用到，以及这个URLconf里面的
每一个匹配模式。你可以通过这个信息知道为什么你请求的URL会抛出404错误。

当然，这些敏感的信息只会显示给开发者，如果是已经部署到因特网上的生产站点就不能再将这些信息暴露出来。
所以，这个“Page not found”的页面只会在 *调试模式* 下显示，我们会在以后说明怎么关闭调试模式，
你现在只要知道，每个项目创建时默认是处在调试模式的，如果关闭调试模式后，Django会输出一个不同的
404响应。


网站根目录简介
--------------------------------

上一节中，我们访问网站根目录 ``http://127.0.0.1:8000/`` 时，得到一个404错误消息。
Django不会在你的网站根目录下增加任何东西，Django不会区别对待这个URL。这需要你到URLconf中
为它指定一个URLpattern，和URLconf中其他条目完全一样。

要去匹配网站根目录可能不是那么直观，所以这里专门列出来一下。当你写好你的网站根目录的视图之后，
用 ``'^$'`` 这个模式去匹配，因为它会匹配一个空字符串。如下面的例子所示：

::

    from mysite.views import hello, my_homepage_view

    urlpatterns = patterns('',
        url(r'^$', my_homepage_view),
        # ...
    )

Django如何处理一个请求
==============================

在开始我们的第二个视图之前，我想暂停一下，先去了解一点Django的原理。当你通过浏览器访问
``http://127.0.0.1:8000/hello/`` ，你会看到你的“Hello world”信息。Django在后台
都做了哪些事情呢？

所有均开始于setting文件。当你运行 ``python manage.py runserver`` 时，这个脚本会去
内层那个 ``mysite`` 文件夹内查找一个 ``setting.py`` 的文件。这个文件包裹了当前这个Django
项目的各种各样的配置。这些配置信息都是大写的，如： ``TEMPLATE_DIRS`` ，``DATABASES`` 等等。
最重要的一个配置是 ``ROOT_URLCONF`` 。 ``ROOT_URLCONF`` 定义了那个Python模块被用在这个网站
的URLconf。

还记得运行 ``django-admin.py startproject`` 的时候创建了 ``settings.py`` 和 ``urls.py``
这两个文件吧？这个自动创建的 ``settings.py`` 包括了 ``ROOT_URLCONF`` ，并且将其指向了自动生产的
``settings.py`` 。打开这个 ``setting.py`` ，你会看到：

::

    ROOT_URLCONF = 'mysite.urls'

其对应的文件就是 ``mysite/urls.py`` 。

当你访问一个特定的URL时，比如 ``/hello/`` ， Django会加载 ``ROOT_URLCONF`` 指向的URLconf，
然后按顺序逐个检查里面的URLpattern，知道找到和请求的URL匹配的URLpattern。当找到匹配的URLpattern后，
Django会调用和这个URLpattern相关联的view function，并把当前的请求作为一个 ``HttpRequest`` 对象
传到view function的第一个参数。(稍后我们会介绍 ``HttpRequest`` 的标准。)

正如我们的第一个例子一样，一个view function必须返回一个 ``HttpResponse`` 对象。剩下的事情就由Django
来完成，Django会把这个Python对象转换成合适的Web响应，设置合适的HTTP头部以及内容(比如网页的内容)。

总结一下：

1. 一个请求进来，请求 ``/hello/`` 。
2. Django通过 ``ROOT_URLCONF`` 找到根URLconf。
3. Django在URLconf中找到第一个匹配 ``/hello/`` 的URLpattern。
4. 如果找到匹配项，Django调用对应的视图函数。
5. 视图函数返回一个 ``HttpResponse`` 。
6. Django将 ``HttpResponse`` 转换成正确的HTTP响应，就是浏览器中显示的网页了。

你现在知道怎么做一个Django页面了。真的很简单，只需要编写一些view function，然后通过URLconf将他们
和URL一一对应起来。


第二个视图： 动态内容
=================================

我们的“Hello world”视图是用来演示Django的工作方式，它还不是一个 *动态* 的网页，因为
它的内容总是一样的。每次你访问 ``/hello/`` 都会看到一样的内容，这还是和一个静态HTML文件
一样。

那让我们的第二个视图更动态一点儿吧，我们还做一个显示当前日期和时间的页面。这个例子足够简单，
不涉及数据库或者用户输入，仅仅是将服务器上的时间显示到页面上。这个例子只是比上面那个稍微有
意思一点，不过它也能用来展示几个概念。

这个视图需要做两件事：计算当前的日期和时间，以及返回一个包含这个值的 ``HttpResponse`` 。
如果你有过Python的经验，你也许知道Python有一个 ``datetime`` 模块可以用来计算日期。
下面我们来看看如何用它：

::

    >>> import datetime
    >>> now = datetime.datetime.now()
    >>> now
    datetime.datetime(2008, 12, 13, 14, 9, 39, 2731)
    >>> print now
    2008-12-13 14:09:39.002731

很简单，也并没有涉及到Django。它仅仅是Python的代码。(我们希望你注意哪些是纯Python代码，哪些
是有Django特性的代码，这样，在你学习Django的过程中，你也可以学到一些Python的知识，并用到其他的
不用Django的Python项目中。)

要想我们的Django视图显示当前的日期和时间，我们仅需把语句 ``datetime.datetime.now()`` 放到
视图函数里面，然后返回一个 ``HttpResponse`` 。代码如下：

::

    from django.http import HttpResponse
    import datetime

    def current_datetime(request):
        now = datetime.datetime.now()
        html = "<html><body>It is now %s.</body></html>" % now
        return HttpResponse(html)

和我们的 ``hello`` 视图一样，这个函数也保存在 ``views.py`` 当中。为了简洁，我们没有把
``hello`` 函数列出来。下面是完整的 ``views.py`` 的内容：

::

    from django.http import HttpResponse
    import datetime

    def hello(request):
        return HttpResponse("Hello world")

    def current_datetime(request):
        now = datetime.datetime.now()
        html = "<html><body>It is now %s.</body></html>" % now
        return HttpResponse(html)

(以后，如无必要，我们就不再重复列出先前的代码。你应该能识别出哪些是先前的代码，哪些是新的代码)。

我们来回顾一下刚刚添加 ``current_datetime`` 时做的更改。

* 首先，我们在文件顶端添加了一条语句 ``import datetime`` , 这样我们就可以计算日期了。

* ``current_datetime`` 函数计算当前的日期和时间，以 ``datetime.datetime`` 的形式
  保存在 ``now`` 这个局部变量中。

* 函数的第二行，我们用Python的格式化字符串(format-string)构造了一段HTML。
  字符串中的 ``%s`` 是一个占位符，字符串后面的百分号(%)表示用变量 ``now`` 的值代替前面
  字符串中的 ``%s`` 。``now`` 是一个 ``datetime.datetime`` 而不是一个字符
  串， ``%s`` (格式化字符)会把它转换 ``"2008-12-13 14:09:39.002731"``
  这样的字符串表现形式。所以，最后会输出 ``"<html><body>It is now 2008-12-13 14:09:39.002731.</body></html>"``
  这样的HTML字符串。

  我们现在的HTML是有错误的，我们这样做是为了保持例子的简短。

* 最后，我们的视图和刚刚的 ``hello`` 视图一样返回一个 ``HttpResponse`` 对象，包含了刚刚生成的响应。

在 ``views.py`` 中添加视图之后，我们还要在 ``urls.py`` 中添加URLpattern来告诉Django由哪个URL来处理
这个视图。用 ``/time`` 之类的字眼会比较容易理解：

::

    from django.conf.urls.defaults import patterns, include, url
    from mysite.views import hello, current_datetime

    urlpatterns = patterns('',
        url(r'^hello/$', hello),
        url(r'^time/$', current_datetime),
    )

这里，我们修改了两个地方，首先，在顶部导入了 ``current_datetime`` 函数。 然后，更重要的一步是我们
添加了一个URL ``/time`` 关联到这个新视图。理解了吗？

写好了视图，添加了URLpattern，现在运行 ``runserver`` 并访问在浏览器里面访问
``http://127.0.0.1:8000/time/`` ，你将会看到当前的时间和日期。

.. admonition:: Django中的时区

  你看到的时间可能会和当前时间相差几个小时，这是因为Django是会处理时区的，Django中默认的
  时区是 ``America/Chicago`` (因为必须有一个值，所以就默认设置为Django的诞生地的时区了)。
  如果你是处在其他的时区，你需要在 ``settings.py`` 中修改这个值。请参考它里面的注释，以
  获取最新世界时区列表。

URLconfs和松耦合
===========================

现在我们该来讲讲Django中URLconf背后的哲学：松耦合原则。简单说，松耦合是一个重要的保证不同部
分互换性的软件开发方法。如果软件中的两部分是松耦合的，其中一部分的改动对另一部分的影响很小
甚至没有影响。

Django的URLconf就是松耦合的一个很好的例子，在一个Django程序中，URL的定义和它要调用的视图就
是松耦合的。换句话说就是，决定URL返回哪个视图函数和实现这个视图函数是在不同的地方。这使得
你可以修改一块而不会影响另一块。

比如我们的 ``current_datetime`` 视图。如果我们要把它的URL从 ``/time/`` 改成
``/current-time/`` 。 我们只需要简单该它的URLconf就可以了，而不需要去管视图函数的实现。
同样的，如果我们想修改这个函数的内部实现，也不用担心会影响对应的URL。

此外，如果我们想要多个URL都可以访问这个函数，我们也只需要修改URL配置而不用去动视图的代码。
下面这个例子里，有两个URL都可以调用我们的 ``current_datetime`` 视图。这个一个故弄玄虚
的例子，但是这个方法迟早用得上。

::

    urlpatterns = patterns('',
        url(r'^hello/$', hello),
        url(r'^time/$', current_datetime),
        url(r'^another-time-page/$', current_datetime),
    )

URLconf和View是松耦合的，在本书中你会继续看到这一哲学。

第三个视图：动态URL
==========================

在我们的上一个视图 ``current_datetime`` ，尽管内容是动态的，但是URL (``/time/``) 是静态的。
在大多数动态的Web应用程序中，URL通常可以包含一些参数可以控制页面的输出。比如一个在线书店会
给每本书分配一个URL，如 ``/books/243/`` ， ``/books/81196/`` 这样。

那让我们来创建我们的第三个视图，显示当前时间加上一个偏移量的时间。我们的目标是
``/time/plus/1/`` 显示当前时间+1个小时的页面， ``/time/plus/2/`` 显示当前时间+2个小时的
页面， ``/time/plus/3/`` 显示+3个小时的页面，以此类推。

新手可能会用不同的视图函数来处理每个时间偏差量，会有这样的URL配置：

::

    urlpatterns = patterns('',
        url(r'^time/$', current_datetime),
        url(r'^time/plus/1/$', one_hour_ahead),
        url(r'^time/plus/2/$', two_hours_ahead),
        url(r'^time/plus/3/$', three_hours_ahead),
        url(r'^time/plus/4/$', four_hours_ahead),
    )

显然，这样的处理是不妥当的，不但需要写很多的视图，而且我们的程序还只能处理那些已经被定义的时
间段。如果哪天我们要再加一个显示+5小时的视图，我们又得去创建一个view function和配置一条
URLpattern。我们需要在这里做一些抽象。


.. admonition:: 关于漂亮URL的一点建议

  如果你有其他Web平台的开发经验(比如PHP或Java)， 你可能会想：嘿！让我们用查询字符串参数
  吧！像 ``/time/plus?hours=3`` 这样，里面的小时应该在查询字符串中被参数 ``hours``
  指定(问号后面的部分)。

  你可以这样做(我们会在第七章讲到)，但是Django的一个核心理念是URL看起来必须漂亮。像
  ``/time/plus/3/`` 这样的URL更清晰，更简单，也更具可读性，可以很容易被大声念出来。
  漂亮的URL就是高质量的Web应用的一个标志。

  Django的URL配置可以使你很容易配置出漂亮的URL。

那么，我们要怎样来处理任意小时的偏差呢？我们要用到通配符。我们前面有提到，一个URL模式就是一
个正则表达式，因此，我们可以在这里用 ``\d+`` 来匹配一个以上的数字。

::

    urlpatterns = patterns('',
        # ...
        url(r'^time/plus/\d+/$', hours_ahead),
        # ...
    )

(这里我们用 ``# ...`` 表示省略的了其他的URL模式。)

这个URLpattern可以匹配任意类似 ``/time/plus/2/`` ， ``/time/plus/25/`` 甚至是  ``/time/plus/100000000000/`` 这样的URL。更进一步，让我们把它限制在最大允许99个小时，这样
我们就只允许一个或两个数字，正则表达式的语法是 ``\d{1,2}``::

    url(r'^time/plus/\d{1,2}/$', hours_ahead),

.. admonition::  注意

  当我们编写Web应用的时候，尽可能考虑可能的数据输入是很重要的，然后决定哪些我们可以接受，
  这里，我们就显示了99个小时的时间差。

我们已经为我们的URL配置好了通配符，我们还需要把它的值传到view function里去，这样我们才能只用一个
view function去处理任意的时间偏移数。我们要做的是在URLpattern里用括号把我们需要的数据括起来。
我们这个例子里，我们需要那个数字作为参数，所以我们把 ``\d{1,2}`` 括起来：

::

    url(r'^time/plus/(\d{1,2})/$', hours_ahead),

如果你熟悉正则表达式，我们这里就是用正则表达式的方式，用圆括号从文本里提取数据的。

这里是我们最终的URLconf::

    from django.conf.urls.defaults import *
    from mysite.views import hello, current_datetime, hours_ahead

    urlpatterns = patterns('',
        url(r'^hello/$', hello),
        url(r'^time/$', current_datetime),
        url(r'^time/plus/(\d{1,2})/$', hours_ahead),
    )

搞定这些后，让我们来写 ``hours_ahead`` 视图了。

``hours_ahead`` 和前面那个 ``current_datetime`` 很相似。只有一点不同：它还接受一个参数，
即偏移的小时数 ``offset`` 。 代码如下：

::

    from django.http import Http404, HttpResponse
    import datetime

    def hours_ahead(request, offset):
        try:
            offset = int(offset)
        except ValueError:
            raise Http404()
        dt = datetime.datetime.now() + datetime.timedelta(hours=offset)
        html = "<html><body>In %s hour(s), it will be %s.</body></html>" % (offset, dt)
        return HttpResponse(html)

我们来逐行分析一下代码：

* view function ``hours_ahead`` 接受两个参数， ``request`` 和 ``offset`` 。

  * ``request`` 是一个 ``HttpRequest`` 对象，和前面的 ``hello`` 和 ``current_datetime`` 一样。
    再强调一次，一个view function必须以一个  ``HttpRequest`` 对象作为它的第一个参数。
  * ``offset`` 是从URL中匹配出来的。如果请求的URL是 ``/time/plus/3/`` ，那么 ``offset`` 就会是字符串
    ``'3'`` ；如果请求的URL是 ``/time/plus/21/`` ，那么 ``offset`` 就会是字符串
    ``'21'`` 。请注意，这个捕获值永远是字符串类型，而不是整数类型，即便是这个字符串全部由数字构成（比如  ``'21'`` ）。

    (更准确的讲，捕获值会是一个Unicode对象，而不是简单的Python字符串。但是目前我们不需要在意这点差别)

    这里，我们把这个变量叫做 ``offset`` ，你也可以叫它任何名字。变量的名字是无关紧要的，只要
    它符合Python的语法，只要它是这个view function的第二个参数，在 ``request`` 之后。（这里不用位置，而是用
    关键字来定义它也是可以的，会在第八章介绍。）

* 在这个函数中，我们做的第一件是是在 ``offset`` 上调用 ``int()`` ，将它从字符串转换为一个整数。

  如果这个值不能被转换成整数，Python会抛出一个 ``ValueError`` 异常。（比如 ``int(foo)``）。
  在本例中，如果我们遇到 ``ValueError`` 我们会抛出一个 ``django.http.Http404`` 的异常，
  最终得到一个404“Page not found”错误。

  精明的读者可能会问：我们在URLpattern的正则表达式中已经用了 ``(\d{1,2})`` 来约束它仅接受
  数字了，怎么可能还会有出现 ``ValueError`` 的情况呢？ 捕获值不是只可能是由数字组成的吗？
  答案是: 我们不会这么做，因为URLpattern提供的是“适度但有用”级别的输入校验。万一这个视图
  函数被其它方式调用，我们仍需自行检查ValueError。 实践证明，在实现视图函数时，不臆测参
  数值的做法是比较好的。 松散耦合，还记得么？

* 下一行，计算当前日期/时间，然后加上适当的小时数。 在 ``current_datetime`` 视图中，我们
  已经见过 ``datetime.datetime.now()`` 。这里新的概念是执行日期/时间的算术操作。我们需要
  创建一个 ``datetime.timedelta`` 对象和增加一个 ``datetime.datetime`` 对象。 结果保存在变量dt中。

* 接着，我们构建这个view function的HTML输出。和我们在 ``current_datetime`` 里差不多，只是
  这个格式化字符串中有两个变量了。因此，在字符串中有两个 ``%s`` 占位符，``%`` 号后面是一个要插入
  的值的元组(tuple)。

* 最后，返回一个HTML的 ``HttpResponse`` 。现在，这行代码已经毫不稀奇了。

在完成视图函数和URL配置编写后，启动Django开发服务器，用浏览器访问 ``http://127.0.0.1:8000/time/plus/3/``
来确认它工作正常。 然后是 ``http://127.0.0.1:8000/time/plus/5/`` 。再然后是
``http://127.0.0.1:8000/time/plus/24/`` 。最后，访问 ``http://127.0.0.1:8000/time/plus/100/``
来检验URL配置里设置的模式是否只 接受一个或两个数字；Django会显示一个 Page not found error 页面,
和以前看到的 404 错误一样。 访问URL ``http://127.0.0.1:8000/time/plus/`` (没有 定义时间差) 也会抛
出404错误。

.. admonition:: 编码顺序

    这个例子中，我们先写了URLpattern，然后才写视图，但是上一个例子中我们是先写视图，才是URLpattern。
    哪一种方式更好呢？

    嗯，怎么说呢，每个开发者都不一样。

    如果你是一个喜欢从总体上来把握的人，你应该更喜欢在项目开始的时候就写下所有的URL配置。
    然后再去编写每个对应的视图。这种方式的一个好处是它会给你一个很清晰的列表，一个to-do list，
    还明确地定义了你需要编写的视图函数的参数。

    如果你更像一个自底向上的开发者，你可能更喜欢先写视图，然后把他们和URL联系起来。这样也是没问题的。

    最后，这两种方式都是可行的，取决于你的大脑更适应哪种思考方式。


Django漂亮的出错页面
===========================

哈，让我花点时间来欣赏一下我们写好的Web程序吧……然后，我们来搞点儿小破坏。我们来把 ``offset = int(offset)``
注释掉，这会让 ``views.py`` 中产生一个错误。

::

    def hours_ahead(request, offset):
        # try:
        #     offset = int(offset)
        # except ValueError:
        #     raise Http404()
        dt = datetime.datetime.now() + datetime.timedelta(hours=offset)
        html = "<html><body>In %s hour(s), it will be %s.</body></html>" % (offset, dt)
        return HttpResponse(html)

启动开发服务器，然后访问 ``/time/plus/3/`` 。 你会看到一个包含大量信息的出错页，最上面是一条 ``TypeError``
信息 ``"unsupported type for timedelta hours component: unicode"`` 。

怎么回事呢？ ``datetime.timedelta`` 函数要求参数 ``hours`` 是一个整数，但是我们注释掉
了将 ``offset`` 转换为整数的代码。这就导致了 ``datetime.timedelta`` 抛出 ``TypeError``
的异常。这是每个程序员都会遇到的小bug。

这个例子是为了展示Django的出错页面。我们花点时间来看看这个出错页，看看里面有哪些
信息。

以下几点值得注意：

* 在页面顶部，你会得到关键的异常信息：异常类型，造成异常的参数(如本例中的
  ``"unsupported type"`` 信息)，异常发生在哪个文件，出错的行号等等。

* 在关键异常下方，页面上列出了完整的Python追踪信息(traceback)。这些信息和你在
  Python命令行解释器中获得的类似，只是多了一些交互性。在追踪信息的每一级，Django会
  显示文件的名字，方法名，以及行号和那一行的代码。

  点击改行代码(深灰色显示的)，你还可以看到出错行的前后几行。

  点击“Local vars”，你还可以看到一个表格，有当前视图抛出异常的时候所有的局部变量和他们的值。
  这些信息在调试的时候非常有用。

* 注意“Traceback”下面有一个“Switch to copy-and-paste view”的文字。点击它，追踪
  信息会以另外一种方便复制粘贴的方式显示。当你想要把这些异常信息分享给别人以获取技术支持
  的时候(比如在DjangoIRC聊天室或者Django用户邮件列表中)，都可以用它。

  下面还有个“Share this traceback on a public Web site”按钮，可以让你一键完成这个工作，
  它会把错误信息发布到 http://www.dpaste.com/ ，你可以把你得到一个唯一的URL分享给别人。

* 再下面的“Request information”部分包括了有关产生错误的Web请求的大量信息：GET和POST，
  cookie值和一些元数据(比如CGI头)。`Appendix G`_ 有Request对象的完整参考。

  在Request信息下面，有“Settings”的部分列出了当前Django程序的所有设置信息。（我们
  已经提过 ``ROOT_URLCONF`` ，本书还会介绍接Django其他的设置。附录D会介绍
  所有可用的设置。）

在某些情况下，Django的错误页面还会显示更多的信息，比如模板的语法错误。我们稍后就介绍Django
的模板系统，我们就会遇到这种错误。现在，取消 ``offset = int(offset)`` 这行的注释，让
它重新运作起来。

不知道你时不时那种在程序里面仔细地添加一些 ``print`` 语句来帮助调试的程序员？你其实可以用
Django出错页来做这些，而不用 ``print`` 语句。 你可以在视图的任何位置，临时插入一个 ``assert False``
来触发出错页。这样你就可以看到局部变量的值和程序的状态了。请看这个 ``hours_ahead`` 的例子：

::

    def hours_ahead(request, offset):
        try:
            offset = int(offset)
        except ValueError:
            raise Http404()
        dt = datetime.datetime.now() + datetime.timedelta(hours=offset)
        assert False
        html = "<html><body>In %s hour(s), it will be %s.</body></html>" % (offset, dt)
        return HttpResponse(html)

显然，这些信息里面很多都是敏感的，它会暴露你Python代码的内部结构以及Django配置，在Internet
上公开这些信息是很不明智的。不怀好意的人会尝试用逆向工程去破坏你的程序，做些下流的事情。
所以，Django出错信息仅在debug模式下才会出现。我们会在12章介绍如果禁用debug模式，现在，你只
需要知道每个新的Django项目都默认运行在debug模式下。(听起来很熟悉，对，这就和前面
的“Page not found”错误一样。)


下一章
============

本章里，我们编写了view function，把HTML直接hard-code在Python代码里了。我们这样做是为了
演示方便，但是在实际情况中，一般这样的方式都不好。

Djano提供了一个简单但强大的模板引擎，可以让你将页面的设计和底层的代码分隔开来。`下一章`_
，我们就将深入Django的模板引擎。

.. _下一章: chapter04.html
.. _附录D: appendixD.html
.. _`Appendix G`: appendixG.html
