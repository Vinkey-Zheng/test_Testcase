配置测试项目
======

本节主要介绍如何修改测试项目的配置文件 settings.py来修改测试框架的行为。如果需要查询QTA框架的全部配置项，请参考《:doc:`./settingslist`》。

====
配置语法
====

测试项目的配置文件是一个python模块，所以配置项都是python模块的模块变量，如下所示::

   DEBUG = True
   RUNNER_THREAD_COUNT = 5
   LOCAL_REPORT = 'console'

由于使用的是python模块表示，因此需要符合以下要求：
   
   * 需要符合python语法要求

除此之外，对于配置项还需要符合以下要求：

   * 配置变量名必须使用大写
   * 配置变量名不可以双下划线开头
   
比如下面的变量都是非法的::

   lower_test = 34
   __CONFIG = "XXX"
   
======
配置文件
======

QTA配置文件分为三种：
   
   * 用户配置文件
   * 依赖Egg包的配置文件
   * Testbase配置文件（即qtaf_settings模块）
   
.. note:: 注意依赖Egg包的配置文件只有通过“manage.py installlib”方式安装到测试项目中，其配置文件才会被加载，具体的依赖egg，可以参考exlib下的installed_libs.txt
   
用户配置文件存放在测试项目的顶层位置；而QTAF配置文件打包在QTAF的egg包中，在QTAF egg包的顶层位置上；如下::

   test_proj/
            qt4a/
            exlib/
                 qtaf.egg/
                         testbase/
                         tuia/
                         pyqq/
                         qtaf_settings.py # Testbase配置
                 qt4i.egg/
                         qt4i/settings.py # 依赖Egg包的配置文件
            mqlib/
            mqtest/
            settings.py # 用户配置
            
            
当两个配置文件中的配置项存在冲突时，按照以下优先级从高到低处理:

   * 用户配置文件
   * 依赖Egg包的配置文件
   * Testbase配置文件

也就是说，用户配置文件可以重载QTAF配置中的默认配置。

======
配置文件定位
======

上面提到的三种配置文件，对于存在整个工程的情况来说，就可以直接使用，不需要额外处理。
如果想要独立使用qtaf或其他qta的egg模块，可以采用定义环境变量的方式告诉qtaf配置文件的位置::

	QTAF_EXLIB_PATH: 指定qta相关egg包存放的路径，qtaf、qt4s、qt4a等egg都会去这里查找，并加载配置
	QTAF_INSTALLED_LIBS: 指定已安装并计划使用的第三方模块（即qtaf除外的），多个模块间用分号隔开，例如：qt4s;qt4a;qt4i
	QTAF_SETTINGS_MODULE: 指定用户自定义的配置模块，python在运行时可以找到的模块，支持多级路径，例如：myproject.settings_20160705
	
.. warning:: 特别注意，如果环境变量存在，仅仅使用环境变量指定的内容，例如存在QTAF_INSTALLED_LIBS环境变量，就不会使用exlib目录下的installed_libs.txt中的内容了

======
使用测试配置
======

配置使用的接口统一使用conf接口，如下::

   from testbase.conf import settings
   if settings.DEBUG:
       print 'debug mode'
   else:
       print 'release mode'

也可以使用get接口查询配置，比如::

   from testbase.conf import settings
   my_conf = settings.get('MY_SETTING', None)

.. warning:: settings.py和qtaf_settings.py也是可以直接import使用的，但是不建议这样做，如果这样使用，可能会遇到非预期的结果。

注意settings配置不允许动态修改配置的值，如::
   
   settings.DEBUG = False

会导致异常::

   Traceback (most recent call last):
     File "D:\workspace\qtaftest\test.py", line 17, in <module>
       settings.DEBUG = 9
     File "build\bdist.win32\egg\testbase\conf.py", line 85, in __setattr__
   RuntimeError: 尝试动态修改配置项"DEBUG"

=====
增加配置项
=====

QTA对配置项的新增没有严格的限制，但是为避免冲突，最好按照以下的原则：

  * 测试项目自定义的配置，增加一个统一的前缀，比如QQ的测试项目增加前缀“QQ_”
  
  * QTA相关组件的配置项目，除了统一增加前缀外，还需要更新到《:doc:`./settingslist`》
  


