# 发展史
单个源代码文件compiled by gcc/g++-->源文件增加 compiled by makefile to 提效-->工程体量增大 用Cmake来处理
cmake:对make 工具的生成器，简化编译过程，可以管理大型的项目，有良好的拓展性
## 源代码到可执行程序
1. 预处理："#"开始的指令就是预处理指令，展开头文件&宏定义，删掉注释。#include //把头文件的所有代码全部copy过来，实现hello.c（源程序）--预处理器cpp--> hello.i（修改了的源程序）
2. 编译：hello.i --编译器ccl--> hello.s（汇编程序），汇编语言是CPU真正能直接读懂并执行的底层纸令，介于cpp代码和机器码之间
3. 汇编：hello.i --汇编器as--> hello.o（可重定位的目标程序）（二进制）
4. 链接：许多目标文件链接成可执行程序，尤其在有多个源代码时可实现文件增量更改
### 举例
- g++ -E main.cpp -o main.ii //-E：预处理；main.cpp：要处理的文件；-o：-output，输出----> 将预处理结果输出文件main.ii中（.ii是预处理后cpp文件的后缀名）
- cat main.ii 就能看到头文件的内容
- g++ -S main.ii -o main.s //-S：编译
- g++ -c main.s -o main.o //-c：Compile/Assemble，将汇编语言完全翻译成机器码
- g++ main.o -o demo 
- ./demo //运行demo这个可执行程序
### 以上四步，是g++ main.cpp -o demo 的拆分
### 对于有多个源代码文件，可以用g++ main.cpp add.cpp -o demo来将二者链接在demo这一个可执行文件里，比如说可以使得在main.cpp里面定义的函数可以运用到add.cpp里面
- 增量链接：此时如果修改其中一个cpp文件比如修改了add.cpp，那么就可以只对add.cpp进行再一次汇编，g++ -c
## Makefile
-  file：一份自动编译的说明书，告诉make工具，哪些文件要编译，编译顺序是什么，需要哪些命令，实现程序构建的自动化
- 操作：vim一个makefile，把上面提及到流程所需要的代码打包放进去，然后运行makefile就好了
```makefile
demo: add.o main.o
        g++ add.o main.o -o demo
add.o: add.cpp
        g++ -c add.cpp -o add.o
main.o: main.cpp
        g++ -c main.cpp -o main.o
clean:
        rm -rf *.o demo /*把带有.o后缀的文件和demo删掉*/
```
(有点像函数的作用，作为一个封装）
## Cmake
- 过程式语言，是一个跨平台(在linux生成makefile，在windows生成project) 的编译工具，可以产生标准的建构档makefile或者project
- 一定要将该文件命名为CMakeLists.txt
- 然后执行 cmake . （.：在当前目录执行该程序）
- 出现makefile后可以像之前一样执行 make ...
### 使用1：最基础
```cmake
cmake_minimum_required(VERSION 3.16) /*最低版本号*/
project(add_demo) /*定义了项目的名称*/
add_executable(demo main.cpp add.cpp) /*添加可执行程序，后面还要加如头文件之类的的话直接加在这个后面就好*/
```
### 使用2：自动扫描
```
cmake_minimum_required((VERSION 3.16) 
project(add_demo) 
add_executable(demo 
    src/main.cpp
    src/add.cpp
)

/*目标头文件目录*/
taget_include_directories(demo PRIVATE
include)
/*在include文件夹里面寻找执行demo项目的所有头文件*/
```
