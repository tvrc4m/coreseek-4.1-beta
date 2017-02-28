#####README
coreseek-4.1-beta源码解读
[最新使用文档](http://www.coreseek.cn/products/products-install/)  

===========================
####　　　　　　　Author:wondywang
===========================

目录说明：
csft-x.y.z：coreseek源代码  
mmseg-i.j.k：mmseg源代码  
testpack：测试配置和数据包  

testpack测试说明：  
目录说明：  
api：api接口和测试脚本  
etc：配置文件  
etc/pysource：python数据源脚本  
var：运行数据  
var/data：索引文件  
var/log：搜索日志  
var/test：测试源数据  

---------------------------
####目录结构
+   |-- csft-3.2.14    coreseek源代码
+   |   |-- api  包括java,ruby,C/C++，php的sphinx访问api接口
+   |   |-- codeblocks    code block的项目工程文件
+   |   |-- config     编译环境的配置文件
+   |   |-- contrib   放置第三方扩展的api接口和常用脚本，但目前基本为空
+   |   |-- csft.doc   空置
+   |   |-- csft.pytest   python的一个脚本，可以忽略
+   |   |-- doc    用户手册和部分说明文档。对使用和阅读代码有作用，强烈建议阅读。
+   |   |-- example.sql   生成手册和测试里面提到的示例数据库schema的sql脚本
+   |   |-- libexpat   vc的工程项目文件
+   |   |-- libstemmer_c    vc的工程项目文件
+   |   |-- misc  一些辅助文件，可以忽略
+   |   |-- mysqlse  sphinxSE的文件，是mysql的引擎文件，放在编译mysql的时候进行编译
+   |   |-- pymmseg   mmseg提供出来的python接口
+   |   |-- src  coreseek源代码目录。核心代码全部在这里
+   |   |-- test   测试代码
+   |   `-- win   windows下的工程配置文件
+   |-- mmseg-3.2.14   mmseg源代码
+   |   |-- config   编译配置文件
+   |   |-- data  词典文件
+   |   |-- python  python接口api代码
+   |   |-- ruby  ruby接口api代码
+   |   |-- script   python的一部分生成字典的脚本
+   |   |-- src  mmseg的源代码目录，核心代码都在这里
+   `-- testpack   测试配置和数据包
+       |-- api  测试环境需要的api接口和测试脚本
+       |-- etc 测试环境配置文件
+   `-- var 测试环境运行数据


**Csft/src目录下的文件用途说明**
+   csft-3.2.14/src
+   |-- indexer.cpp   索引程序index的入口主函数
+   |-- indextool.cpp  工具程序indextool的入口主函数
+   |-- llsphinxql.c    sphinql的语法分析器Flex
+   |-- md5.cpp  实现md5算法的代码
+   |-- md5.h  实现md5算法的代码
+   |-- py_helper.cpp 跟python语言交互的接口代码
+   |-- py_helper.h  跟python语言交互的接口代码
+   |-- py_layer.cpp 跟python语言相关的代码
+   |-- py_layer.h   跟python语言相关的代码
+   |-- py_source.cpp  跟python语言相关的代码
+   |-- py_source.h  跟python语言相关的代码
+   |-- py_sphinx.c  跟python语言相关的代码
+   |-- py_sphinx_interface.cpp  跟python语言相关的代码
+   |-- py_sphinx_interface.h  跟python语言相关的代码
+   |-- search.cpp  工具程序search的入口主函数
+   |-- searchd.cpp  查询程序searchd的入口主函数
+   |-- spelldump.cpp  工具程序spelldump的入口主函数
+   |-- sphinx.cpp   主要的逻辑代码，索引建立合并和查询主要的逻辑都在这里。
+   |-- sphinx.h  
+   |-- sphinx_internal.h
+   |-- sphinxcustomsort.inl  支持用户自定义排序的一个文件，用于支持 @custom 的排序方式
+   |-- sphinxexcerpt.cpp   产生文本摘要和高亮的代码
+   |-- sphinxexcerpt.h  
+   |-- sphinxexpr.cpp    跟语法分析器有关的代码
+   |-- sphinxexpr.h  跟语法分析器有关的代码
+   |-- sphinxexpr.y   语法分析器yacc的输入文件
+   |-- sphinxfilter.cpp   sphinx过滤器filter的实现代码
+   |-- sphinxfilter.h
+   |-- sphinxmetaphone.cpp  实现Metaphone算法的代码，它是一种基于音标的词干组织法。
+   |-- sphinxql.l  sphinxql的语法分析器lex的输入文件
+   |-- sphinxql.y  sphinxql的语法yacc的输入文件
+   |-- sphinxquery.cpp  sphinx查询语句的解析代码，对查询语句进行解析，并生成语法分析树
+   |-- sphinxquery.h
+   |-- sphinxquery.y sphinxql的语法yacc的输入文件
+   |-- sphinxselect.y  sphinxql的语法yacc的输入文件
+   |-- sphinxsort.cpp  排序算法实现代码
+   |-- sphinxsoundex.cpp  语音编码算法代码
+   |-- sphinxstd.cpp 一部分通用的代码，如lock锁，Mutex，随机器等封装好的代码。
+   |-- sphinxstd.h 
+   |-- sphinxstem.h  词干提炼代码的头文件
+   |-- sphinxstemcz.cpp  捷克语词干的提炼代码
+   |-- sphinxstemen.cpp  英语的词干提炼代码
+   |-- sphinxstemru.cpp 俄语的词干提炼代码
+   |-- sphinxstemru.inl  俄语的词干提炼代码
+   |-- sphinxtimers.h 计时器代码，这个是用来做sphinx内部性能分析用，看耗时主要在哪部分
+   |-- sphinxutils.cpp  对配置文件进行解析的代码
+   |-- sphinxutils.h
+   |-- sphinxversion.h   定义sphinx版本的宏
+   |-- tests.cpp  对分词器进行测试的代码
+   |-- tokenizer_zhcn.cpp 中文分词器的实现代码
+   |-- tokenizer_zhcn.h 中文分词器的实现代码
+   |-- yy.cmd  后面这部分yyxxx.xx的文件都是跟语法分析器相关的文件,不再一一分析。
+   |-- yysphinxexpr.c  跟语法分析器相关的文件
+   |-- yysphinxexpr.h  跟语法分析器相关的文件
+   |-- yysphinxql.c  跟语法分析器相关的文件
+   |-- yysphinxql.h  跟语法分析器相关的文件
+   |-- yysphinxquery.c  跟语法分析器相关的文件
+   |-- yysphinxquery.h  跟语法分析器相关的文件
+   |-- yysphinxselect.c  跟语法分析器相关的文件
+   `-- yysphinxselect.h  跟语法分析器相关的文件

===========================
| 配置 | 测试对象  | 对应配置 | 测试数据 | PHP程序  | 测试说明 | 在线说明 |
| :------- |:-------|:-----| :----- |:------|:-----|:-----|
| 配置1 | xml数据源，中文分词与搜索 | etc/csft.conf | var/test/test.xml | api/test_coreseek.php | - | [在线说明](http://www.coreseek.cn/products/products-install/install_on_bsd_linux/) |
| 配置2 | xml数据源，单字切分与搜索 | etc/csft_cjk.conf | var/test/test.xml  | api/test_coreseek.php | - | [在线说明](http://www.coreseek.cn/products-install/ngram_len_cjk/) |
| 配置3 | mysql数据源，中文分词与搜索 |  etc/csft_mysql.conf | var/test/documents.sql  | api/test_coreseek.php | 请先将测试数据导入数据库，并设置好配置文件中的MySQL用户密码数据库 | [在线说明](http://www.coreseek.cn/products-install/mysql/) |
| 配置4 | python数据源，中文分词与搜索 | etc/csft_demo_python.conf | etc/pysource/csft_demo/__init__.py  | api/test_coreseek.php | 请先安装Python 2.6 (x86) | [在线说明](http://www.coreseek.cn/products-install/python/) |
| 配置5 | python+mssql数据源，中文分词与搜索 |  etc/csft_demo_python_pymssql.conf | etc/pysource/csft_demo_pymssql/__init__.py | api/test_coreseek.php | 请先安装Python 2.6 (x86)、pymssql（py2.6） | [在线说明](http://www.coreseek.cn/products-install/python/) |
| 配置6 | RT实时索引，中文分词与搜索 | etc/csft_rtindex.conf | - | api/test_coreseek_rtindex.php | coreseek-4.y.z测试 | [在线说明](http://www.coreseek.cn/products-install/rt-indexes/) |
| 配置6 | RT实时索引，单字切分与搜索 | etc/csft_rtindex_cjk.conf | - | api/test_coreseek_rtindex.php | coreseek-4.y.z测试 | [在线说明](http://www.coreseek.cn/products-install/rt-indexes/) |
