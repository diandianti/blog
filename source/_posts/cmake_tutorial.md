---
title: CMake简单教程
date: 2021/9/16 21:00:00
update: 2021/9/27 21:00:00
tags:
- cmake
- linux
categories: Tool
excerpt: cmake的简单教程，介绍的内容都比较基础，新手可以参考。
---


# 简介

最开始学习c语言的时候使用的是vs之类的IDE，那个时候不用管依赖库，也不用管如何编译，把所有的函数都放进一个main.c里面就可以了。后来在实际的工程中接触到了Makefile 与 Cmake，但是一直都是在改别人已经写好的Cmake文件，没有系统地学习过cmake文件的语法。在查找Cmake的文档的时候找到了一份官方的教程,  [https://cmake.org/cmake/help/latest/guide/tutorial/index.html](https://cmake.org/cmake/help/latest/guide/tutorial/index.html) 。感觉写的还不错，将其翻译为中文，并稍加整理，自己编写了相关的工程文件。

我自己实现的工程文件在 [https://github.com/diandianti/cmake_tutorial](https://github.com/diandianti/cmake_tutorial)

官方的工程文件在 [https://github.com/Kitware/CMake](https://github.com/Kitware/CMake)

# 基础内容

## 最简单的CMake文件

一个最简单的cmake工程中的CMakeList.txt文件的内容可以只有三句话， 如下：

```makefile
#三句话让Cmake给我编译了一个程序

#这个是必须的，用来指定cmake的最低版本
cmake_minimum_required(VERSION 3.10)

#设置工程的名字
project(Tutorial)

# 添加一个可执行的程序，使用文件“tutorial.cpp”来编译一个程序
# 然后输出名为 “Tutorial” 的程序
add_executable(Tutorial tutorial.cpp)
```

## 为程序添加版本号与配置头文件

尽管我们可以可以在代码的源文件中使用“#define”的方式来添加软件的版本等信息，但是在CmakeList文件中添加会让修改版本号信息更加方便，而且在Cmake文件中添加版本信息还可以在后续工程的打包等过程中带来不小的便利

```makefile
cmake_minimum_required(VERSION 3.10)

#指定工程的名字与版本号
project(Tutorial VERSION 1.0)

# 添加一个头文件配置
# 在运行cmake的时候会自动把 “TutorialConfig.h.in” 中的变量替换为相应的值
# 然后输出为 “TutorialConfig.h”
configure_file(TutorialConfig.h.in TutorialConfig.h)

# 添加一个可执行程序
add_executable(Tutorial tutorial.cpp)

#  设置可执行程序在编译使使用的 include路径，即从何处获得头文件
target_include_directoried(Tutorial PUBLIC "${PROJECT_BINARY_DIR}")
```

对于配置文件“TutorialConfig.h.in”，它的内容如下：

```cpp
// 对于 @Tutorial_VERSION_MAJOR@与@Tutorial_VERSION_MINOR@ ， 在执行Cmake的时候会将这两个变量替换为相应的值
//  因为在CMakeList.txt中有 “project(Tutorial VERSION 1.0)”这样的定义
//  所以 @Tutorial_VERSION_MAJOR@ 会被替换为 “1”
//   @Tutorial_VERSION_MINOR@ 会被替换为 0
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```

如果要在源程序中使用版本信息，可以这么写：

```cpp
//tutorial.cpp

#include "TutorialConfig.h"
#include <iostream>

int main(int argc, char *argv[])
{

        if (argc < 2) {
        // report version
        std::cout << argv[0] << " Version " << Tutorial_VERSION_MAJOR << "."
                      << Tutorial_VERSION_MINOR << std::endl;
        std::cout << "Usage: " << argv[0] << " number" << std::endl;
        return 1;
        }

        return 0;
}
```

## 指定C++标准

如果不指定c或c++的标准，在编译的时候cmake会使用一个默认值。但是这个默认值可能并不是我们想要的标准。例如如下的代码就是C++11中的新特性，如果默认标准低于C++11，那么编译就会报错。

```c
const double inputValue = std::stod(argv[1]);
```

指定c++标准的命令如下：

```makefile
cmake_minimum_required(VERSION 3.10)

project(Tutorial VERSION 1.0)

# 指定c++的标准为 c++11
# 要确保这两条命令在要指定标准的程序之前
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

#添加一个程序来测试一下，标准是否指定成功
add_executable(TestStd  TestStd.cpp)
```

写一个简单的程序来测试标准是否设置成功

```cpp
//TestStd.cpp

#include <iostream>

int main(int argc, char *argv[])
{
        /** 显示c++的版本 */
    if (__cplusplus == 201703L)
                std::cout << "C++17\n";
    else if (__cplusplus == 201402L)
                std::cout << "C++14\n";
    else if (__cplusplus == 201103L)
                std::cout << "C++11\n";
    else if (__cplusplus == 199711L)
                std::cout << "C++98\n";
    else
                std::cout << "pre-standard C++\n";

        return 0;
}
```

## 编译

在准备好了源码文件与Cmake文件之后就可以编译整个工程了，一般的步骤如下

- 新建一个名为“build”的文件夹
- 进入到文件夹 执行命令“cmake  ..”
- 执行命令“make”

```bash
❯ mkdir build && cd build && cmake ../ && make
-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /datasets/learn/cmake/Tutorial_part1/build
Scanning dependencies of target Tutorial
[ 25%] Building CXX object CMakeFiles/Tutorial.dir/Tutorial.cpp.o
[ 50%] Linking CXX executable Tutorial
[ 50%] Built target Tutorial
Scanning dependencies of target TestStd
[ 75%] Building CXX object CMakeFiles/TestStd.dir/TestStd.cpp.o
[100%] Linking CXX executable TestStd
[100%] Built target TestStd
```

## 测试

在执行完命令之后会在build目录下面生成相关的可执行文件，执行一下文件看是否符合预期。

```bash
# 查看打印的标准是否为cmake文件中的值
❯ ./TestStd
C++11

# 查看打印的版本号是否为cmake文件中的值
❯ ./Tutorial
./Tutorial Version 1.0
Usage: ./Tutorial number
```

# 添加依赖库

很多时侯大多数的文件并不会直接编译为可执行文件而是编译为一个依赖库（动态库或者静态库）， 这样可以增强工程的灵活性。

## 添加动态库

对于刚才的工程，现在需要添加一个新的库来计算数字的平方根，然后添加一个程序来使用这个库计算用户输入的数字的平方根，操作流程如下：

1. 添加一个文件夹与代码，用来编译库
    1. 新建名为“MathFunctions”的文件夹， 文件夹也可以不新建，所有的代码都放在一个目录中，但是这样不方便管理；
    2. 在新文件夹中添加文件“MathFunction.h”, 内容如下:

        ```c
        //  前面的这两行代码不是必需的
        // 但是加上之后可以避免编译时提示对一个函数多次声名的错误
        #ifndef _MATHFUNCTION_H_
        #define _MATHFUNCTION_H_

        /* 声名一个函数来计算平方根*/
        double mysqrt(double input);

        #endif /* _MATHFUNCTION_H_*/
        ```

    3. 继续添加文件 “mysqrt.cpp”, 内容如下：

        ```c
        #include "MathFunctions.h"

        // 实现计算平方根的函数
        double mysqrt(double input)
        {
        		return sqrt(input);
        }
        ```

    4. 最后添加文件 “CMakeList.txt”，内容如下：

        ```makefile
        # 这里的[SHARED]表示是可选的，如果不加这个选项会编译为静态库
        add_library(MathFunctions [SHARED] mysqrt.cpp)
        ```

2. 将新的文件夹加入到工程中，并使用编译的库来计算平方根

    ```makefile
    # 将刚才新建的文件夹添加进工程
    add_subdirectory(MathFunctions)

    # 添加一个测试程序
    add_executable(TestSqrt  TestSqrt.cpp)

    # 指定测试程序TestSqrt需要依赖那个依赖库
    target_link_libraries(TestSqrt PUBLIC MathFunctions)

    # 指定测试程序TestSqrt需要在哪些目录搜索头文件
    target_include_directories(TestSqrt PUBLIC
                              "${PROJECT_BINARY_DIR}"
                              "${PROJECT_SOURCE_DIR}/MathFunctions"
                              )
    ```

## 使动态库变为可选编译

其实对于简单的项目来说可选编译一些东西是没有必要的，但是对于大型的工程来说将某个模块变为可选项是家常便饭。

第一步就是需要在最外面的cmake文件中添加选项。

```makefile
#这句话必须要在configure_file命令之前，否则会导致一些宏定义无法生成
# 添加一个选项USE_MYMATH 用来指定是否使用工程中提供的数学函数，默认值为ON
option(USE_MYMATH "Use tutorial provided math implementation" ON)

... ...
configure_file(TutorialConfig.h.in TutorialConfig.h)
... ...

# 如果选项 USE_MYMATH 被置为真
if(USE_MYMATH)
  add_subdirectory(MathFunctions)
	#将依赖库MathFunctions加入到依赖库的列表中
  list(APPEND EXTRA_LIBS MathFunctions)
	#将文件夹MathFunctions加入到头文件搜索路径中
  list(APPEND EXTRA_INCLUDES "${PROJECT_SOURCE_DIR}/MathFunctions")
endif()

# 添加一个测试程序来测试平方跟函数
add_executable(TestSqrt  TestSqrt.cpp)

# 指定测试程序TestSqrt需要依赖哪些依赖库
target_link_libraries(TestSqrt PUBLIC ${EXTRA_LIBS})

# 指定测试程序TestSqrt需要在哪些目录搜索头文件
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           ${EXTRA_INCLUDES}
                           )
```

在文件TutorialConfig.h.in中，需要添加

```cpp
#cmakedefine USE_MYMATH
```

而对于测试的程序，可以这么编写

```c
//TestSqrt.cpp

#include <iostream>
#include "TutorialConfig.h"

#ifdef USE_MYMATH
#       include "MathFunctions.h"
#else
#       include <cmath>
#endif //USE_MYMATH

int main(int argc, char** argv)
{
        double inputValue = std::stod(argv[1]);
#ifdef USE_MYMATH
        double sqrtValue = mysqrt(inputValue);
#else
        double sqrtValue = sqrt(inputValue);
#endif //USE_MYMATH

        std::cout << "Input value is :" << inputValue << std::endl;
        std::cout << "Sqrt value is :" << sqrtValue << std::endl;

        return 0;
}
```

如果在编译的时候要禁用自己定义的动态库，那么可以执行这样的命令.

```bash
# 在执行cmake的时候加上参数选项就可以开关相应的option
cmake ../ -DUSE_MYMATH=OFF
```

# 使调用依赖库变简单

在大多数的时候，某一个程序不止会依赖一个库，在某个编译目标要依赖一些已经编译好的库的时候，如果像刚才那样每个库都单独增加依赖路径会比较繁琐，所以可以通过如下的命令来让cmake自动添加头文件路径。

在MathFunctions/CMakeLists.txt文件的最后一行添加如下命令

```makefile
# 这条命令可以将当前路径加入到头文件搜索的目录中
# 其中 INTERFACE 意味这这个库给其他的应用程序提供了相关的接口
target_include_directories(MathFunctions
          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
          )
```

同时最外部的CMakeList.txt文件可以去掉之前加的东西

```makefile
if(USE_MYMATH)
  add_subdirectory(MathOptional)
  list(APPEND EXTRA_LIBS MathOptional)
  # list(APPEND EXTRA_INCLUDES "${PROJECT_SOURCE_DIR}/MathOptional")#这一行可以注释掉
endif()

target_include_directories(test_optional
			PUBLIC
     "${PROJECT_BINARY_DIR}"
			#${EXTRA_INCLUDES} #这个也可以注释掉
)
```

再次执行编译的流程，也不会出现报错，说明这两种方法的效果是一样的。

# 安装与测试

## 安装编译目标

安装本质是就是文件的复制，将编译好的程序复制到${PATH}目录中，程序就可以在终端执行了。要安装编译好的文件，需要在库所在目录的cmake文件的最后添加如下的命令

```makefile
# 将编译好的目标文件 MathFunctions 安装到目的路径的 lib 目录下
install(TARGETS MathFunctions DESTINATION lib)
# 将工程中的文件 MathFunctions.h 安装到目的路径的 include 目录下
install(FILES MathFunctions.h DESTINATION include)
```

在最外层的cmake文件中添加如下的命令：

```makefile
# 将编译好的目标文件 Tutorial 安装到目的路径的 bin 目录下
install(TARGETS Tutorial DESTINATION bin)
# 将工程中的文件 TutorialConfig.h 安装到目的路径的include 目录下
# ${PROJECT_BINARY_DIR} 代指当前工程的二进制文件存放位置，即编译生成文件的目录
install(FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  DESTINATION include
  )
```

在编译完程序之后，就可以执行安装的命令：

```bash
make DESTDIR=/path/to/install install

❯ make DESTDIR=./ install
[ 25%] Built target Tutorial
[ 50%] Built target TestStd
[ 75%] Built target MathFunctions
[100%] Built target TestSqrt
Install the project...
-- Install configuration: ""
-- Installing: ./usr/local/bin/Tutorial
-- Installing: ./usr/local/bin/TestStd
-- Installing: ./usr/local/bin/TestSqrt
-- Set runtime path of ".//usr/local/bin/TestSqrt" to ""
-- Installing: ./usr/local/include/TutorialConfig.h
-- Installing: ./usr/local/lib/libMathFunctions.so
-- Installing: ./usr/local/include/MathFunctions.h
```

## 测试支持

在一个程序编译好了之后，需要对其进行测试，看看输出的内容和预期的是否是一致的。手动测试繁琐耗时，Cmake提供了相关的测试程序。首先，添加一个输出比较简单的程序来进行测试。

```bash
//TestTest.cpp

#include <cstdlib>
#include <iostream>
#include <cmath>

int main(int argc, char** argv)
{
        if (argc < 2) {
                std::cout << "Usage:" << argv[0] << " number" << std::endl;
                return -1;
        }

        double inputValue = std::atof(argv[1]);
        std::cout << sqrt(inputValue) << std::endl;

        return 0;
}
```

在最外层的CMake文件中添加命令编译这个文件

```bash
add_executable(TestTest TestTest.cpp)
```

最后添加测试支持

```makefile
# 开启测试
enable_testing()

# 添加一个测试例子, 查看程序是否可以正常运行
# 添加一个名为Runs的测试项 测试的命令为 TestTest 25
add_test(NAME Runs COMMAND TestTest 25)

# 查看是否会打印Usage
# 添加一个名为 Usage 的测试项 检查程序的输出是否可以匹配正则表达式 "Usage:.*number"
add_test(NAME Usage COMMAND TestTest)
set_tests_properties(Usage
  PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number"
  )

# 定义一个简单的测试函数
# 添加一个名为 Comp${输入数字} 的测试项，检查程序的输出是否可以匹配正则表达式 ${结果}
function(do_test target arg result)
  add_test(NAME Comp${arg} COMMAND ${target} ${arg})
  set_tests_properties(Comp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result}
    )
endfunction(do_test)

# 使用测试函数进行测试
do_test(TestTest 4 "2")
do_test(TestTest 9 "3")
do_test(TestTest 5 "2.236")
do_test(TestTest 7 "2.645")
do_test(TestTest 25 "5")
do_test(TestTest -25 "[-nan|nan|0]")
do_test(TestTest 0.0001 "0.01")
```

在执行完make命令之后可以执行命令 “ctest”或者 “make test”即可测试目标文件是否正确

```c
❯ make test
Running tests...
    Start 1: Runs
1/9 Test #1: Runs .............................   Passed    0.00 sec
    Start 2: Usage
2/9 Test #2: Usage ............................   Passed    0.00 sec
    Start 3: Comp4
3/9 Test #3: Comp4 ............................   Passed    0.00 sec
    Start 4: Comp9
4/9 Test #4: Comp9 ............................   Passed    0.00 sec
    Start 5: Comp5
5/9 Test #5: Comp5 ............................   Passed    0.00 sec
    Start 6: Comp7
6/9 Test #6: Comp7 ............................   Passed    0.00 sec
    Start 7: Comp25
7/9 Test #7: Comp25 ...........................   Passed    0.00 sec
    Start 8: Comp-25
8/9 Test #8: Comp-25 ..........................   Passed    0.00 sec
    Start 9: Comp0.0001
9/9 Test #9: Comp0.0001 .......................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 9

Total Test time (real) =   0.02 sec
```

# 添加对系统的检查

每个人的系统环境都是不尽相同的，很多时候我们的工程中会用到一些比较特别的函数。这是时候如果我们更换一台设备来编译工程，如果新的设备中没有这些函数在编译或者使用的时候就会出错。应对这种情况，则需要检查某些函数在目标系统中是否存在，这里以log与exp函数为例（这两个函数八成都有）。

现在，我们自己实现的mysqrt函数在计算平方根的时候不想使用数值分析的方式逐次递进，而是使用log和exp来实现，那么我们需要检查这两个函数在系统中是否存在。

修改文件“MathFunctions/CMakeList.txt”

```makefile
# 将检查系统函数的命令包含进来
include(CheckSymbolExists)

#检查文件 “math.h”中是否存在 log 函数， 如果有的话将变量HAVE_LOG置为真
check_symbol_exists(log "math.h" HAVE_LOG)
#检查文件 “math.h”中是否存在 exp 函数， 如果有的话将变量HAVE_EXP置为真
check_symbol_exists(exp "math.h" HAVE_EXP)
# 如果没有两个函数
if(NOT (HAVE_LOG AND HAVE_EXP))
		# 取消对于HAVE_LOG HAVE_LOG 变量值的缓存
    unset(HAVE_LOG CACHE)
    unset(HAVE_EXP CACHE)
		# 指定依赖库 m （math库）
    set(CMAKE_REQUIRED_LIBRARIES "m")
    # 再次检查
    check_symbol_exists(log "math.h" HAVE_LOG)
    check_symbol_exists(exp "math.h" HAVE_EXP
		# 如果 HAVE_LOG 与 HAVE_EXP 都为真
	  if(HAVE_LOG AND HAVE_EXP)
				# 在编译MathOptional库的时候链接m（math）库
        target_link_libraries(MathOptional PRIVATE m)
    endif()
endif()

# 如果目标系统中存在相关的函数
# 那么可以在文件中添加相关的宏定义
if(HAVE_LOG AND HAVE_EXP)
    target_compile_definitions(MathOptional
        PRIVATE "HAVE_LOG" "HAVE_EXP")
endif()
```

修改文件“MathFunctions/mysqrt.cpp”

```cpp
#include "MathFunctions.h"
#include <iostream>
#include <cmath>

double mysqrt(double input)
{
        if (input <= 0) {
                return 0;
        }

        double result = input;

#if defined(HAVE_LOG) && defined(HAVE_EXP)
        result = exp(log(input) * 0.5);
        std::cout << "Computing sqrt of " << input << " to be " << result
                << " using log and exp" << std::endl;
#else
        // do ten iterations
        for (int i = 0; i < 10; ++i) {
                if (result <= 0) {
                        result = 0.1;
        }
                double delta = input - (result * result);
                result = result + 0.5 * delta / result;
                std::cout << "Computing sqrt of " << input << " to be " << result << std::endl;
        }
#endif

        return result;
}
```

修改完之后，编译运行，查看程序的输出，系统中存在log与exp函数，并且使用这两个函数来计算的平方根值

```bash
❯ ./TestSqrt 10
Computing sqrt of 10 to be 3.16228 using log and exp
Input value is :10
Sqrt value is :3.16228
```

# 添加自定义命令

现在我想把计算平方根的库中的计算方式改为 “查表 +  逐次递进”的方式。 即先查表找一个近似的值，然后再通过数值分析的方式去找。那么就需要有个程序来生成一个表给计算平方根的程序使用。

首先写一个生成表的程序，在MathFunctions目录下新建一个文件 “MakeTable.cpp”

```cpp
//MakeTable.cpp

#include <cmath>
#include <iostream>
#include <fstream>

int main(int argc, char** argv)
{
        if (argc < 2) {
                return -1;
        }

        std::ofstream tableFile(argv[1]);

        tableFile << "float sqrtTable[10] = {\n\t";

        for (int i = 1; i <= 10; i++) {
                tableFile << sqrt(i) << " ," ;
        }

        tableFile << "\n};\n";
        tableFile.close();

        return 0;
}
```

修改一下“MathFunctions”目录下的cmake文件，添加这个生成表的程序并把生成表的操作写进去

```makefile
# 添加一个程序
add_executable(MakeTable MakeTable.cpp)

# 添加一条自定义命令
add_custom_command(
		# 命令的输出文件
	  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
		# 命令的具体格式
	  COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
    # 命令依赖于那个程序
	  DEPENDS MakeTable
  )

# 让 MathFunctions 库依赖生成的表文件
add_library(MathFunctions
            mysqrt.cpp
            ${CMAKE_CURRENT_BINARY_DIR}/Table.h
            )

# 让 MathFunctions 库可以正确地找到头文件
target_include_directories(MathFunctions
          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
          PRIVATE ${CMAKE_CURRENT_BINARY_DIR}
          )
```

然后修改文件“mysqrt.cpp”

```cpp
#include <iostream>
#include "MathFunctions.h"
#include "Table.h"

double mysqrt(double x)
{
  if (x <= 0) {
    return 0;
  }

  // use the table to help find an initial value
  double result = x;
  if (x >= 1 && x < 10) {
    std::cout << "Use the table to help find an initial value " << std::endl;
    result = sqrtTable[static_cast<int>(x)];
  }

  // do ten iterations
  for (int i = 0; i < 10; ++i) {
    if (result <= 0) {
      result = 0.1;
    }
    double delta = x - (result * result);
    result = result + 0.5 * delta / result;
    std::cout << "Computing sqrt of " << x << " to be " << result << std::endl;
  }

  return result;
}
```

编译运行

```bash
❯ cmake ../
-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
USE_MYMATH is True
-- Configuring done
-- Generating done
-- Build files have been written to: /datasets/learn/cmake/Tutorial_part6/build
❯ make
Scanning dependencies of target Tutorial
[  7%] Building CXX object CMakeFiles/Tutorial.dir/Tutorial.cpp.o
[ 15%] Linking CXX executable Tutorial
[ 15%] Built target Tutorial
Scanning dependencies of target TestTest
[ 23%] Building CXX object CMakeFiles/TestTest.dir/TestTest.cpp.o
[ 30%] Linking CXX executable TestTest
[ 30%] Built target TestTest
Scanning dependencies of target TestStd
[ 38%] Building CXX object CMakeFiles/TestStd.dir/TestStd.cpp.o
[ 46%] Linking CXX executable TestStd
[ 46%] Built target TestStd
Scanning dependencies of target MakeTable
[ 53%] Building CXX object MathFunctions/CMakeFiles/MakeTable.dir/MakeTable.cpp.o
[ 61%] Linking CXX executable MakeTable
[ 61%] Built target MakeTable
[ 69%] Generating Table.h
Scanning dependencies of target MathFunctions
[ 76%] Building CXX object MathFunctions/CMakeFiles/MathFunctions.dir/mysqrt.cpp.o
[ 84%] Linking CXX shared library libMathFunctions.so
[ 84%] Built target MathFunctions
Scanning dependencies of target TestSqrt
[ 92%] Building CXX object CMakeFiles/TestSqrt.dir/TestSqrt.cpp.o
[100%] Linking CXX executable TestSqrt
[100%] Built target TestSqrt
❯ ./TestSqrt 7
Use the table to help find an initial value
Computing sqrt of 7 to be 2.65165
Computing sqrt of 7 to be 2.64576
Computing sqrt of 7 to be 2.64575
Computing sqrt of 7 to be 2.64575
Computing sqrt of 7 to be 2.64575
Computing sqrt of 7 to be 2.64575
Computing sqrt of 7 to be 2.64575
Computing sqrt of 7 to be 2.64575
Computing sqrt of 7 to be 2.64575
Computing sqrt of 7 to be 2.64575
Input value is :7
Sqrt value is :2.64575
```

# 打包文件

在所有的程序都写好了之后就可以打包我们的工程，然后发布软件，cmake提供了相应的打包命令，只需要在最外层的cmake文件的最后添加如下的命令即可：

```makefile
include(InstallRequiredSystemLibraries)
# 这里需要自己新建一个证书文件，来说明你的工程使用的许可证
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set(CPACK_PACKAGE_VERSION_MAJOR "${Tutorial_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${Tutorial_VERSION_MINOR}")
include(CPack)
```

添加完成之后就可以编译工程了，编译之后执行命令 cpack即可打包编译生成的文件 

```bash
❯ cpack
CPack: Create package using STGZ
CPack: Install projects
CPack: - Run preinstall target for: Tutorial
CPack: - Install project: Tutorial
CPack: Create package
CPack: - package: /datasets/learn/cmake/Tutorial_part7/build/Tutorial-1.0-Linux.sh generated.
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: Tutorial
CPack: - Install project: Tutorial
CPack: Create package
CPack: - package: /datasets/learn/cmake/Tutorial_part7/build/Tutorial-1.0-Linux.tar.gz generated.
CPack: Create package using TZ
CPack: Install projects
CPack: - Run preinstall target for: Tutorial
CPack: - Install project: Tutorial
CPack: Create package
CPack: - package: /datasets/learn/cmake/Tutorial_part7/build/Tutorial-1.0-Linux.tar.Z generated.
```

还可以更改打包文件的类型

```bash
cpack -G ZIP -C Debug # 将文件打包为zip格式
```



# 动态库与静态库的选择

在前面的章节中使用了 “add_library()”命令来添加一个依赖库，一个依赖库可以为静态库（STATIC）、动态库（SHARED）等（还有MODULE与OBJECT，但是我不知道这两个该怎么翻译）。

在本章中将添加一个选项来控制是否将依赖库编译为动态库，这一过程主要依赖一个Cmake中的变量“BUILD_SHARED_LIBS”。当然，并不是只有这一种方式，还可以通过设置编译的选择来控制，但是这种方法不是本章的重点。

第一步的修改是对于最外层的CMakeList.txt文件

```makefile
... ...

# 设置静态库的存放位置为 “整个工程二进制文件的目录”
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}")
# 设置动态库的存放位置为 “整个工程二进制文件的目录”
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}")
# 设置运行环境目录为  “整个工程二进制文件的目录”
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}")

# 添加一个选项来控制变量 BUILD_SHARED_LIBS 默认值为 ON
option(BUILD_SHARED_LIBS "Build using shared libraries" ON)

... ...
add_subdirectory(MathFunctions)
... ...
```

在最外层的cmake文件中，直接将子文件夹 MathFunctions 添加进了工程，即无论如何都会编译这个库，所以需要在文件 MathFunctions/CMakeList.txt文件中添加选项来控制是否使用自定义的平方根库。

```makefile
# 添加一个库
add_library(MathFunctions MathFunctions.cpp)

# 确定include的路径
target_include_directories(MathFunctions
          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
          )

# 添加一个选项来控制是否使用 自定义的平方根库
option(USE_MYMATH "Use tutorial provided math implementation" ON)

# 如果USE_MYMATH为真
if(USE_MYMATH)

    # 为库MathFunctions 添加一个定义 “USE_MYMATH”
    # 对于于 “#ifdef USE_MYMATH”
    target_compile_definitions(MathFunctions PRIVATE "USE_MYMATH")

    # 添加一个程序来生成 Table.h
    add_executable(MakeTable MakeTable.cpp)

    # 添加一个自定义命令
    add_custom_command(
        OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
        COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
        DEPENDS MakeTable
        )

		# 添加一个静态库，这个静态库中包含了自定义计算平方根的算法
    add_library(SqrtLibrary STATIC
                mysqrt.cpp
                ${CMAKE_CURRENT_BINARY_DIR}/Table.h
                )
		# 指定静态库的include路径
    target_include_directories(SqrtLibrary PRIVATE
                ${CMAKE_CURRENT_BINARY_DIR}
                )

		# 如果不添加这一句会导致编译出错
		# 添加之后相当于为 g++ 添加了 “-fPIC” 参数
    set_target_properties(SqrtLibrary PROPERTIES
        POSITION_INDEPENDENT_CODE ${BUILD_SHARED_LIBS}
        )

		# 将MathFunctions库依赖SqrtLibrary
    target_link_libraries(MathFunctions PRIVATE SqrtLibrary)
endif()

target_compile_definitions(MathFunctions PRIVATE "EXPORTING_MYMATH")

set(installable_libs MathFunctions)

if(TARGET SqrtLibrary)
    list(APPEND installable_libs SqrtLibrary)
endif()

install(TARGETS ${installable_libs} DESTINATION lib)
install(FILES MathFunctions.h DESTINATION include)
```

修改文件 MathFunctions/mysqrt.cpp 

```makefile
#include <iostream>
#include "MathFunctions.h"
#include "Table.h"

namespace mathfunctions{
namespace detail {

double mysqrt(double x)
{
	if (x <= 0) {
		return 0;
	}

	// use the table to help find an initial value
	double result = x;
	if (x >= 1 && x < 10) {
		std::cout << "Use the table to help find an initial value " << std::endl;
		result = sqrtTable[static_cast<int>(x)];
	}

	// do ten iterations
	for (int i = 0; i < 10; ++i) {
		if (result <= 0) {
			result = 0.1;
		}
		double delta = x - (result * result);
		result = result + 0.5 * delta / result;
		std::cout << "Computing sqrt of " << x << " to be " << result << std::endl;
	}

	return result;
}
}
}
```

添加文件 MathFunctions/MathFunctions.cp

```makefile
#include "MathFunctions.h"

#include <cmath>

#ifdef USE_MYMATH
# include "mysqrt.h"
#endif

namespace mathfunctions {
double sqrt(double x)
{
//如果定义了 USE_MYMATH那么就使用自定义的平方根函数
#ifdef USE_MYMATH
	return detail::mysqrt(x);
#else
	return std::sqrt(x);
#endif
}
}
```

修改文件 MathFunctions/MathFunction.h

```makefile
#ifndef _MATHFUNCTION_H_
#define _MATHFUNCTION_H_

// 这一段是为了让编译出的库可以将需要的函数暴露出来
// 如果不添加，一般情况下没有问题，不过可能会造成外部调用库的程序找不到相应的函数
#if defined(_WIN32)
#  if defined(EXPORTING_MYMATH)
#    define DECLSPEC __declspec(dllexport)
#  else
#    define DECLSPEC __declspec(dllimport)
#  endif
#else // non windows
#  define DECLSPEC
#endif

// 声明函数
namespace mathfunctions {
	double DECLSPEC sqrt(double x);
}

#endif /* _MATHFUNCTION_H_*/
```

都修改之后编译运行程序即可，修改一下参数，查看编译出的MathFunction库是否为想要的类型。

# 添加生成表达式

生成表达式（Generator Expressions， 我也不知道是不是可以这么翻译，就先这么叫吧）会在你执行cmake的时候执行，它可以用来控制你的编译过程。一般使用生成表达式来控制条件链接与条件include。 控制的条件可以有多种多样，比如 构建的属性 平台等信息。条件表达式的详细文档在 [https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#manual:cmake-generator-expressions(7)](https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#manual:cmake-generator-expressions(7)) 。 在本章中就不详细介绍了，后续可能会添加上这一部分的内容。

生成表达式一种常见的用法是来控制编译的选项 即 （-fPIC -Wall）等信息。 而且生成表达式一般计较会与接口库同时使用，这样编译选项就可以一次定义，在多处使用。

在此教程中，以设置c++编译标准为例， 对于最外层的CMakeList.txt文件而言：

```makefile
# 这两行用来设置编译的标准
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# 上面的两行可以被下面的命令代替
# 需要注意的是，如果使用这种方式来设置c++标准，意味着使用的标准大于C++11即可
# 即 如果默认的C++标准为C++14，那么这么设置和默认效果是没有区别的
add_library(tutorial_compiler_flags INTERFACE)
target_compile_features(tutorial_compiler_flags INTERFACE cxx_std_11)
```

下面就通过添加生成表达式来设置编译的选项：

```makefile
# 将变量 gcc_like_cxx 设置为引号内的文本
set(gcc_like_cxx "$<COMPILE_LANG_AND_ID:CXX,ARMClang,AppleClang,Clang,GNU>")
# 将msvc_cxx 设置为引号内的文本
set(msvc_cxx "$<COMPILE_LANG_AND_ID:CXX,MSVC>")

# 设置接口库 tutorial_compiler_flags 编译的选项， 因为生成式表达式是可以嵌套使用的，所以
# 实际的表现为cmake会根据实际存在的编译器来设置不同的编译参数
target_compile_options(tutorial_compiler_flags INTERFACE
  "$<${gcc_like_cxx}:$<BUILD_INTERFACE:-Wall;-Wextra;-Wshadow;-Wformat=2;-Wunused>>"
  "$<${msvc_cxx}:$<BUILD_INTERFACE:-W3>>"
)
# 需要注意的是，这里只是为了演示生成表达式的使用方式，所以使用的是 BUILD_INTERFACE 关键词
# 也就意味着使用这个库的其他程序不会受到这个库编译方式的影响。
```

# 添加导出配置

本教程在安装与测试的部分说明了如何将编译好的程序和依赖库如何安装到系统，在打包文件的部分说明了如何将编译好的文件打包为一个文件便于发行和传播。在接下来的这一部分将说明如何将必要的信息导出到系统中以便于其他的cmake工程可以使用已经安装好的程序。

第一步是修改 install 命令，为 install 命令添加 EXPORT 参数，对于文件 MathFunctions/CMakeLists.txt

```makefile
# 最后的 EXPORT 命令将MathFunctions库导出
install(TARGETS ${installable_libs}
        DESTINATION lib
        EXPORT MathFunctionsTargets)
```

对于最外层的 CMakeList.txt文件， 需要在最后添加如下的命令

```makefile
# 将刚才导出的库的相关文件安装到目标路径的 lib/cmake/MathFunctions目录下面
install(EXPORT MathFunctionsTargets
  FILE MathFunctionsTargets.cmake
  DESTINATION lib/cmake/MathFunctions
)
```

到目前为止，就完成了导出的大部分工作，但是如果这个时候执行cmake命令，会得到以下的错误

```bash
Target "MathFunctions" INTERFACE_INCLUDE_DIRECTORIES property contains
path:

  "/Users/robert/Documents/CMakeClass/Tutorial/Step11/MathFunctions"

which is prefixed in the source directory.
```

这是因为MathFunctions库依赖的include路径是目前的源文件的路径，如果将这个库安装到其他的设备的话是找不到这个目录的，所以需要修改文件 MathFunctions/CMakeList.txt 如下：

```makefile
... ...

# 这个地方用到了生成表达式
target_include_directories(MathFunctions
  INTERFACE
  # 编译的时候使用当前路径
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  # 安装的时候使用对应的include路径
  $<INSTALL_INTERFACE:include>
)

... ...
```

完成这一步修改之后就可以执行cmake命令来编译生成相关的二进制文件了。

在使用cmake的时候会用到 find_package 命令来查找当前设备已经安装的库，为了能够让别的程序在执行 find_package 的时候可以找到 MathFunctions库还需要做其他的一些步骤：

添加一个名为 Config.cmake.in 的文件，内容如下：

```makefile
# 下面的这一个命令是cmake的一个宏，在运行cmake的时候会被替换为其他的命令
@PACKAGE_INIT@

include ( "${CMAKE_CURRENT_LIST_DIR}/MathFunctionsTargets.cmake" )
```

为了能够配置好这个库还需要在最外层的 CMakeLists.txt文件中添加如下的命令：

```makefile
# 安装命令
install(EXPORT MathFunctionsTargets
  FILE MathFunctionsTargets.cmake
  DESTINATION lib/cmake/MathFunctions
)

include(CMakePackageConfigHelpers)
# 使用Config.cmake.in来生成文件MathFunctionsConfig.cmake
# 并将相应的文件安装到 lib/cmake/example
configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfig.cmake"
  INSTALL_DESTINATION "lib/cmake/example"
  NO_SET_AND_CHECK_MACRO
  NO_CHECK_REQUIRED_COMPONENTS_MACRO
  )
# 生成库的版本文件，并指定兼容性
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfigVersion.cmake"
  VERSION "${Tutorial_VERSION_MAJOR}.${Tutorial_VERSION_MINOR}"
  COMPATIBILITY AnyNewerVersion
)

# 安装文件
install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfig.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfigVersion.cmake
  DESTINATION lib/cmake/MathFunctions
  )
```

# 打包Debug版本与Release版本

cmake可以将工程打包为不同配置版本，为了确认打包的文件是正确的，这里为debug版本的程序加上一个 d 的后缀， 修改最外层的CMakeLists.txt文件

```makefile
# 声明变量
set(CMAKE_DEBUG_POSTFIX d)
add_executable(Tutorial tutorial.cxx)

# 给编译出的程序添加debug的后缀
set_target_properties(Tutorial PROPERTIES DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX})

target_link_libraries(Tutorial PUBLIC MathFunctions)
```

修改文件 MathFunctions/CMakeLists.txt 为依赖库添加一个版本号

```makefile
set_property(TARGET MathFunctions PROPERTY VERSION "1.0.0")
set_property(TARGET MathFunctions PROPERTY SOVERSION "1")
```

在做好了以上的修改之后，执行一下命令即可

```makefile
cd debug
cmake -DCMAKE_BUILD_TYPE=Debug ..
cmake --build .
cd ../release
cmake -DCMAKE_BUILD_TYPE=Release ..
cmake --build .
```

在编译之后即可在debug与release文件夹下看到相应的文件。

添加一个名为 MultiCPackConfig.cmake 的文件，内容如下

```makefile
include("release/CPackConfig.cmake")

set(CPACK_INSTALL_CMAKE_PROJECTS
    "debug;Tutorial;ALL;/"
    "release;Tutorial;ALL;/"
    )
```

然后在最外层的目录执行命令，即可将debug版本与release版本打包在一起。

```makefile
cpack --config MultiCPackConfig.cmake
```

