https://seisman.github.io/how-to-write-makefile/introduction.html

https://www.gnu.org/software/make/manual/make.html

# 前言

## 程序的编译和链接


# 基础

## makefile的组成

- Makefile里主要包含了五个东西：显式规则、隐晦规则、变量定义、文件指示和注释
- 显式规则。显式规则说明了如何生成一个或多个目标文件。这是由Makefile的书写者明显指出要生成的文件、文件的依赖文件和生成的命令。
- 隐晦规则。由于我们的make有自动推导的功能，所以隐晦的规则可以让我们比较简略地书写 Makefile，这是由make所支持的。
- 变量的定义。在Makefile中我们要定义一系列的变量，变量一般都是字符串，这个有点像你C语言中的宏，当Makefile被执行时，其中的变量都会被扩展到相应的引用位置上。
- 文件指示。其包括了三个部分，一个是在一个Makefile中引用另一个Makefile，就像C语言中的include一样；另一个是指根据某些情况指定Makefile中的有效部分，就像C语言中的预编译#if一样；还有就是定义一个多行的命令。
- 注释。Makefile中只有行注释，注释是用`#`字符。如果要在Makefile中使用 # 字符，可以用反斜杠进行转义，如`\#`。

## 基本规则
```
target : prerequisites
    command
```
- 各项含义
  - target: 可以是一个object file（目标文件），也可以是一个执行文件，还可以是一个标签（label）。
  - prerequisites: 生成该target所依赖的文件和/或target
  - command: 该target要执行的命令（任意的shell命令）,必须要以`Tab`键开始
- 规则
  - target这一个或多个的目标文件依赖于prerequisites中的文件，其生成规则定义在command中
  - prerequisites中如果有一个以上的文件比target文件要新的话，command所定义的命令就会被执行


### 一个target多次出现


#### 双冒号::
- 在目标文件和依赖文件之间用`::`而不是用`:`
-  

### 执行流程

- 一般保存为`makefile`或`Makefile`文件
- 在同一目录下直接输入命令`make`，就会执行
- `make target`会执行特定的target
  - 例如，一般`make clean`会用来删除生成的执行文件和目标文件
- `make -f file`或者`make --file file`会执行特定的名为file的文件 

### make的工作流程
- make会在当前目录下找名字叫`Makefile`或`makefile`的文件
- 寻找文件中的第一个目标文件（target），并把这个文件作为最终的目标文件
- 一层又一层地去找文件的依赖关系，直到最终编译出第一个目标文件
- 没有被第一个目标文件直接或间接关联的目标文件不会被自动执行，只能通过`make target`显示执行

### make的自动推导

- 可以自动推导文件以及文件依赖关系后面的命令
- 只要make看到一个 .o 文件，它就会自动的把 .c 文件加在依赖关系中，并且会自动生成对应的命令
```
main.o : defs.h
#等价于
main.o : main.c defs.h
    cc -c main.c
```

### clean的编写
- 每个Makefile中都应该写一个清空目标文件（ .o 和执行文件）的规则，这不仅便于重编译，也很利于保持文件的清洁。
- 成文的规矩是——“clean从来都是放在文件的最后”。


## 引用其他makefile

- `include <filename>`
- filename 可以是当前操作系统Shell的文件模式
- include 和 filename 可以用一个或多个空格隔开
- make命令开始时，会找寻 include 所指出的其它Makefile，并把其内容安置在当前的位置
- 查找路径
  - 如果文件都没有指定绝对路径或是相对路径的话，make会在当前目录下首先寻找
  - 如果make执行时，有`-I`或`--include-dir`参数，那么make就会在这个参数所指定的目录下去寻找
  - 如果目录`<prefix>/include`（一般是： /usr/local/bin 或 /usr/include ）存在的话，make也会去找
- 如果有文件没有找到的话，make会生成一条警告信息，但不会马上出现致命错误。它会继续载入其它的文件，一旦完成makefile的读取，make会再重试这些没有找到，或是不能读取的文件，如果还是不行，make才会出现一条致命信息。
- 如果你想让make不理那些无法读取的文件，而继续执行，你可以在include前加一个减号“-”




# 书写规则


## 通配符
- make支持三个通配符：`*`，`?`和 `~`，含义和shell里的一样
- `~`，用在路径中
- `*`，表示任意字符
- `?`，表示匹配一个字符



## 伪目标文件
- 使用一个特殊的标记`.PHONY`来显式地指明一个目标是伪目标
- 伪目标并不是一个文件，只是一个标签
- 伪目标不是文件，所以make无法生成它的依赖关系和决定它是否要执行。我们只有通过显式地指明这个“目标”才能让其生效
- 伪目标一般没有依赖的文件。但是，我们也可以为伪目标指定所依赖的文件
- 伪目标同样可以作为“默认目标”，只要将其放在第一个
- 目标也可以成为依赖。所以，伪目标同样也可成为依赖
```
all : prog1 prog2 prog3
.PHONY : all

prog1 : prog1.o utils.o
    cc -o prog1 prog1.o utils.o

prog2 : prog2.o
    cc -o prog2 prog2.o

prog3 : prog3.o sort.o utils.o
    cc -o prog3 prog3.o sort.o utils.o
```

## 多目标
- 多个目标同时依赖于一个文件，并且其生成的命令大体类似。于是我们就能把其合并起来
- 自动化变量`$@`,表示着目前规则中所有的目标的集合，依次取出目标，并执于命令
```
bigoutput littleoutput : text.g
    generate text.g -$(subst output,,$@) > $@
#等价于
bigoutput : text.g
    generate text.g -big > bigoutput
littleoutput : text.g
    generate text.g -little > littleoutput
```

## 静态模式

```
<targets ...> : <target-pattern> : <prereq-patterns ...>
    <commands>
    ...
```
- targets定义了一系列的目标文件，可以有通配符。是目标的一个集合。
- target-pattern是指明了targets的模式，也就是的目标集模式。
- prereq-patterns是目标的依赖模式，它对target-pattern形成的模式再进行一次依赖目标的定义。
```
objects = foo.o bar.o
all: $(objects)
$(objects): %.o: %.c
    $(CC) -c $(CFLAGS) $< -o $@


```




# 变量
- 类似于C/C++中的宏，只是简单的字符串替换
- `$`使用变量
- 括号不是必须的，给变量加上括号完全是为了更加安全地使用这个变量，可以用小括号，也可以用大括号

## 变量的定义

- 有四种赋值运算符
  - `=`
    - 使用`=`进行赋值，变量的值是整个makefile中最后被指定的值。
    - make会将整个makefile展开后，再决定变量的值。也就是说，变量的值将会是整个makefile中最后被指定的值
  - `:=`
    - 赋予当前位置的值。
    - 变量的值决定于它在makefile中的位置，而不是整个makefile展开后的最终值
  - `?=`
    - 表示如果该变量没有被赋值，则赋予等号后的值
  - `+=`
    - 表示将等号后面的值添加到前面的变量上
- 在定义变量的值时，我们可以使用其它变量来构造变量的值
  - 右侧变量的值可以定义在文件的任何一处，也就是说，右侧中的变量不一定非要是已定义好的值，其也可以使用后面定义的值

## 变量的替换

- `$(var:a=b)`，把var中所有以a结尾的字符串的尾部的a变成b
```
foo := a.o b.o c.o
bar1 := $(foo:.o=.c) #bar是a.c b.c c.c
bar2 := $(foo:%.o=%.c)#bar是a.c b.c c.c
```

## 把变量的值再当成变量


## 多行变量
- 使用define关键字设置的变量可以有换行
- define指示符后面跟的是变量的名字，而重起一行定义变量的值，定义是以endef 关键字结束
```
define two-lines
echo foo
echo $(bar)
endef
```

## 环境变量
- make运行时的系统环境变量可以在make开始运行时被载入到Makefile文件中，但是如果Makefile中已定义了这个变量，或是这个变量由make命令行带入，那么系统的环境变量的值将被覆盖。（如果make指定了“-e”参数，那么，系统环境变量将覆盖Makefile中定义的变量）



## override指示符

- 如果有变量是通常make的命令行参数设置的，那么Makefile中对这个变量的赋值会被忽略。如果你想在Makefile中设置这类参数的值，那么，你可以使用“override”指示符。其语法是:
```
override <variable>; = <value>;
override <variable>; := <value>;
```

## 特殊技巧

### 定义空格
- 利用`#`注释符来添加空格

```
nullstring :=
space := $(nullstring) #end of the line
# space会是值是一个空格的变量
dir := /foo/bar    # directory to put the frobs in
# dir这个变量的值是“/foo/bar”，后面还跟了4个空格，
```

- 利用空变量
```
empty:=
space:= $(empty) $(empty)
```

## 在变量中展开通配符

- 例如`obj = *.c`
- 如果只是简单的`$(obj)`,`*.c`并不会展开



## 常见的特殊变量

### `VPATH`
- 增加寻找依赖文件和目标文件的路径
- 如果没有指明`VPATH`，make只会在当前的目录中去找寻依赖文件和目标文件
- 如果定义了`VPATH`，那么，make就会在当前目录找不到的情况下，到所指定的目录中去找寻文件
- `VPATH = a:b:c`
  - 目录由冒号分隔
- 
## 自动化变量

- `$@`，表示着目前规则中所有的目标的集合
- 


# 命令，command

## 书写规则
- 每条命令的开头必须以 Tab 键开头，或者紧跟在依赖规则后面的分号后
- 命令行之间中的空格或是空行会被忽略
- 


## 显示规则
- make 默认会把其要执行的命令行在命令执行前输出到屏幕上。
- 在命令行前加上`@`字符，这个命令将不被make显示出来
- `make -n`或者`make --just-print`,只是显示命令，但不会执行命令
- `make -s`或者`make --silent`或者`make --quiet`，全面禁止显示命令 


## 执行规则
- 当依赖目标新于目标时，也就是当规则的目标需要被更新时，make会一条一条的执行其后的命令
- 如果要让上一条命令的结果应用在下一条命令时，应该把两条命令写在同一行，并用分号分隔。不能写在两行

## 错误检测
- 每当命令运行完后，make会检测每个命令的返回码，如果命令返回成功，那么make会执行下一条命令
- 如果一个规则中的某个命令出错了（命令退出码非零），那么make就会终止执行当前规则，这将有可能终止所有规则的执行
- 忽略命令的出错，继续执行
  - 可以在Makefile的命令行前加一个减号`-`
  - 或者用`make -i`或者`make --ignore-errors`执行
  - 或者用`.IGNORE`显示指明这个目标里的命令会忽略错误
  - 还有一个特殊的`make -k`或者`make --keep-going`，如果某规则中的命令出错了，那么就终止该规则的执行，但继续执行其它规则。不同之处是上面三个方法会继续执行同一规则里的命令

## 嵌套执行
- 当我们把不同模块或是不同功能的源文件放在不同的目录中，我们可以在每个目录中都书写一个该目录的Makefile，然后用一个上级makefile控制这些下级makefile的执行



# 关键字

## vpath
- 设置文件搜索路径，和`VPATH`特殊变量的功能类似
- `vpath <pattern> <directories>`
  - 为符合模式`<pattern>`的文件指定搜索目录`<directories>`
- `vpath <pattern>`
  - 清除符合模式`<pattern>`的文件的搜索目录。
- `vpath`
  - 清除所有已被设置好了的文件搜索目录。
- `<pattern>`需要包含`%`字符，表示匹配零或若干字符，
- 可以连续地使用vpath语句，以指定不同搜索策略。如果连续的vpath语句中出现了相同的`<pattern>`，或是被重复了的`<pattern>`，那么，make会按照vpath语句的先后顺序来执行搜索。如：




# 条件判断

## 语法
```
<conditional-directive>
<text-if-true>
endi

#或者
<conditional-directive>
<text-if-true>
else
<text-if-false>
endif

# 以下写法均可
ifeq (<arg1>, <arg2>)
ifeq '<arg1>' '<arg2>'
ifeq "<arg1>" "<arg2>"
ifeq "<arg1>" '<arg2>'
ifeq '<arg1>' "<arg2>"
```
- `<conditional-directive>` 表示条件关键字，共有四个
  - `ifeq (<arg1>, <arg2>)`，比较参数 arg1 和 arg2 的值是否相同，相同为真
  - `ifneq (<arg1>, <arg2>)`，不同，则为真
  - `ifdef <variable-name>`，如果变量`<variable-name>`的值非空，那表达式为真。否则，表达式为假
  - `ifndef <variable-name>`，如果变量`<variable-name>`的值为空，那表达式为真。否则，表达式为假
- `<text-if-false>`表示不同情况下执行的命令
- make是在读取Makefile时就计算条件表达式的值，并根据条件表达式的值来选择语句，所以，你最好不要把自动化变量（如 $@ 等）放入条件表达式中，因为自动化变量是在运行时才有的。

```
#举例
libs_for_gcc = -lgnu
normal_libs =

foo: $(objects)
ifeq ($(CC),gcc)
    $(CC) -o foo $(objects) $(libs_for_gcc)
else
    $(CC) -o foo $(objects) $(normal_libs)
endif
```

# 函数

## 语法

-`$(<function> <arguments>)`
  - `<function>` 是函数名
  - `<arguments>`为函数的参数，参数间以逗号 , 分隔，
  - 函数名和参数之间以“空格”分隔
  - 圆括号也可以用花括号

## 字符串处理函数

- `$(subst <from>,<to>,<text>)`
  - 字符串替换函数
  - 把字串`<text>`中的`<from>`字符串替换成`<to>`
  - 返回被替换过后的字符串
- `$(patsubst <pattern>,<replacement>,<text>)`
  - 模式字符串替换函数
  - 查找`<text>`中的单词（单词以“空格”、“Tab”或“回车”“换行”分隔）是否符合模式 `<pattern>`，如果匹配的话，则以`<replacement>`替换。
    - `<pattern>`可以包括通配符 % ，表示任意长度的字串。如果`<replacement>`中也包含 % ，那么，`<replacement>`中的这个 % 将是`<pattern>`中的那个 % 所代表的字串
  - 返回被替换过后的字符串
- `$(strip <string>)`
  - 去掉`<string>`字串中开头和结尾的空字符。
  - 返回被去掉空格后的字符串值。
- `$(findstring <find>,<in>)`
  - 在字串`<in>`中查找`<find>`字串。
  - 如果找到，那么返回`<find>`，否则返回空字符串
- `$(filter <pattern...>,<text>)`
  - 过滤函数
  - 以`<pattern>`模式过滤`<text>`字符串中的单词，保留符合模式`<pattern>`的单词。可以有多个模式
  - 返回符合模式`<pattern>`的字串
- `$(filter-out <pattern...>,<text>)`
  - 反过滤函数
  - 去除符合模式`<pattern>`的单词。可以有多个模式
  - 返回不符合模式`<pattern>`的字串
- `$(sort <list>)`
  - 给字符串`<list>`中的单词排序（升序）
  - 会去掉`<list>`中相同的单词。
- `$(word <n>,<text>)`
  - 取字符串`<text>`中第`<n>`个单词。（从一开始）
  - 返回字符串`<text>`中第`<n>`个单词。如果`<n>`比`<text>`中的单词数要大，那么返回空字符串
- `$(wordlist <ss>,<e>,<text>)`
  - 从字符串`<text>`中取从`<ss>`开始到`<e>`的单词串，左闭右闭
  - 如果`<ss>`比`<text>`中的单词数要大，那么返回空字符串。如果`<e>`大于`<text>`的单词数，那么返回从`<ss>`开始，到`<text>`结束的单词串。
- `$(words <text>)`
  - 统计`<text>`中字符串中的单词个数。
- `$(firstword <text>)`
  - 返回字符串`<text>`中的第一个单词

## 文件名操作函数
- 下面函数的参数字符串都会被当做一个或是一系列的文件名来对待
- `$(dir <names...>)`
  - 取目录函数
  - 从文件名序列`<names>`中取出目录部分。
  - 目录部分是指最后一个反斜杠（ / ）之前的部分(包含反斜杠)。如果没有反斜杠，那么返回`./` 
- `$(notdir <names...>)`
  - 取文件函数
  - 从文件名序列`<names>`中取出非目录部分。
  - 非目录部分是指最后一个反斜杠（ / ）之后的部分
- `$(suffix <names...>)`
  - 取后缀函数
  - 从文件名序列`<names>`中取出各个文件名的后缀,如果文件没有后缀，则返回空字串
  - 包含.
- `$(basename <names...>)`
  - 取前缀函数
  - 从文件名序列`<names>`中取出各个文件名的前缀部分
- `$(addsuffix <suffix>,<names...>)`
  - 加后缀函数
  - 把后缀`<suffix>`加到`<names>`中的每个单词后面
  - 返回加过后缀的文件名序列
- `$(addprefix <prefix>,<names...>)`
  - 加前缀函数
  - 把前缀`<prefix>`加到`<names>`中的每个单词前面
  - 返回加过前缀的文件名序列
- `$(join <list1>,<list2>)`
  - 连接函数
  - 把`<list2>`中的单词对应地加到`<list1>`的单词后面
  - 如果`<list1>`的单词个数要比`<list2>`的多，那么，`<list1>`中的多出来的单词将保持原样。如果`<list2>`的单词个数要比`<list1>`多，那么，`<list2>`多出来的单词将被复制到`<list1>`中

## 流程控制函数

- `$(foreach <var>,<list>,<text>)`
  - 把参数`<list>`中的单词逐一取出放到参数`<var>`所指定的变量中，然后再执行 `<text>`所包含的表达式
  - 每一次`<text>`会返回一个字符串，循环过程中，`<text>`的所返回的每个字符串会以空格分隔，最后当整个循环结束时，`<text>`所返回的每个字符串所组成的整个字符串（以空格分隔）将会是foreach函数的返回值。
  - `<var>`最好是一个变量名，`<list>`可以是一个表达式，而`<text>`中一般会使用 `<var>`这个参数来依次枚举`<list>`中的单词
  - `<var>`参数是一个临时的局部变量，foreach函数执行完后，参数`<var>`的变量将不在作用，其作用域只在foreach函数当中。
```
names := a b c d
files := $(foreach n,$(names),$(n).o)
#结果是a.o b.o c.o d.o
```

- `$(if <condition>,<then-part>,<else-part>)`
  - 可以包含“else”部分，或是不含
  - `<condition>`参数是if的表达式，如果其返回的为非空字符串，那么这个表达式就相当于返回真，于是，`<then-part>`会被计算，否则`<else-part>`会被计算

## 自定义函数
- `$(call <expression>,<parm1>,<parm2>,...,<parmn>)`
-  `<expression>`参数中的变量，如 `$(1)` 、 `$(2)` 等，会被参数 `<parm1>` 、 `<parm2>`依次取代。而`<expression>`的返回值就是 call 函数的返回值
```
reverse =  $(2) $(1)
foo = $(call reverse,a,b)
#结果为b a
```

## origin
- `$(origin <variable>)`
- 告诉你你的这个变量是哪里来的
- `<variable>`是变量的名字，不应该是引用。最好不要在`<variable>`中使用
$ 字符
- 返回值共有以下几种情况
  - undefined。如果`<variable>`从来没有定义过，
  - default。如果`<variable>`是一个默认的定义
  - environment。如果`<variable>`是一个环境变量，并且当Makefile被执行时， -e 参数没有被打开。
  - file。如果`<variable>`这个变量被定义在Makefile中。
  - command line。如果`<variable>`这个变量是被命令行定义的。
  - override。如果`<variable>`是被override指示符重新定义的。
  - automatic。如果`<variable>`是一个命令运行中的自动化变量

## shell函数
- 参数就是操作系统Shell的命令
- 把执行操作系统命令后的输出作为函数返回

## 错误控制
- `$(error <text ...>)`
  - 产生一个致命错误，`<text ...>`是错误信息
- `$(warning <text ...>)`
  - 输出一段警告信息，而make会继续执行