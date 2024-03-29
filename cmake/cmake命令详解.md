



https://www.bookstack.cn/read/CMake-Cookbook/README.md



## 测试功能

###  enable_testing 
* 打开当前及以下目录中的测试功能。
* `enable_testing()`
* 为当前及其下级目录打开测试功能。

### add_test 
* 以指定的参数为工程添加一个测试。
* `add_test(testname Exename arg1 arg2 ... )`
* 如果已经运行过了ENABLE_TESTING命令，这个命令将为当前路径添加一个测试目标。如果ENABLE_TESTING还没有运行过，该命令啥事都不做。
* 以指定的参数执行Exename文件。Exename或者是由该工程构建的可执行文件，也可以是系统上自带的任意可执行文件。
* 该测试会在CMakeList.txt文件的当前工作路径下运行，这个路径与二进制树上的路相对应。

* `add_test(NAME <name> [CONFIGURATIONS [Debug|Release|...]]  
           COMMAND <command> [arg1 [arg2 ...]])`
* 如果COMMAND选项指定了一个可执行目标（用add_executable创建），它会自动被在构建时创建的可执行文件所替换。如果指定了CONFIGURATIONS选项，那么该测试只有在列出的某一个配置下才会运行。
* 在COMMAND选项后的参数可以使用“生成器表达式”，它的语法是"$<...>"。这些表达式会在构建系统生成期间，以及构建配置的专有信息的产生期间被评估。合法的表达式是：  
```cmake
$<CONFIGURATION>          = 配置名称  
$<TARGET_FILE:tgt>        = 主要的二进制文件(.exe, .so.1.2, .a)  
$<TARGET_LINKER_FILE:tgt> = 用于链接的文件(.a, .lib, .so)
$<TARGET_SONAME_FILE:tgt> = 带有.so.的文件(.so.3)
其中，"tgt"是目标的名称。目标文件表达式TARGET_FILE生成了一个完整的路径，但是它的_DIR和_NAME版本可以生成目录以及文件名部分：
$<TARGET_FILE_DIR:tgt>
$<TARGET_FILE_NAME:tgt>
$<TARGET_LINKER_FILE_DIR:tgt>
$<TARGET_LINKER_FILE_NAME:tgt>
$<TARGET_SONAME_FILE_DIR:tgt>
$<TARGET_SONAME_FILE_NAME:tgt> 
```

### set_tests_properties
* 设置若干个测试的属性值。  
* `set_tests_properties(test1 [test2...] PROPERTIES prop1 value1 prop2 value2)`
* 为若干个测试设置一组属性。若属性未被发现，CMake将会报告一个错误。这组属性包括：
    * WILL_FAIL， 如果设置它为true，那将会把这个测试的“通过测试/测试失败”标志反转。
    * PASS_REGULAR_EXPRESSION，如果它被设置，这个测试输出将会被检测是否违背指定的正则表达式，并且至少要有一个正则表达式要匹配；否则测试将会失败。例子: `PASS_REGULAR_EXPRESSION "TestPassed;All ok"`
    * FAIL_REGULAR_EXPRESSION: 如果该属性被设置，那么只要输出匹配给定的正则表达式中的一个，那么测试失败。例子: `FAIL_REGULAR_EXPRESSION "[^a-z]Error;ERROR;Failed"`
    * PASS_REGULAR_EXPRESSION和FAIL_REGULAR_EXPRESSION属性都期望一个正则表达式列表（list）作为其参数。
    * TIMEOUT: 设置该属性将会限制测试的运行时长不超过指定的秒数。

