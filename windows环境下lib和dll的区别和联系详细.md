windows环境下lib和dll的区别和联系详细
c++中共有两种库：
1、LIB包含了函数所在的DLL文件和文件中函数位置的信息（入口），代码由运行时加载在进程空间中的DLL提供，称为动态链接库dynamic link library。**（这种方式更灵活，写的程序体积小，但是需要.exe和dll同时发布）**
2、LIB包含函数代码本身，在编译时直接将代码加入程序当中，称为静态链接库static link library。**（这种方式不是很灵活，因为lib被编译到.exe中，写出的程序体积大，但是只需要发布exe即可，不需要dll文件）**
共有两种链接方式：
1、动态链接使用动态链接库，允许可执行模块（.dll文件或.exe文件）仅包含在运行时定位DLL函数的可执行代码所需的信息。
2、静态链接使用静态链接库，链接器从静态链接库LIB获取所有被引用函数，并将库同代码一起放到可执行文件中。
关于lib和dll的区别如下：
（1）lib是编译时用到的，dll是运行时用到的。如果要完成源代码的编译，只需要lib；如果要使动态链接的程序运行起来，只需要dll。
（2）如果有dll文件，那么lib一般是一些索引信息，记录了dll中函数的入口和位置，dll中是函数的具体内容；如果只有lib文件，那么这个lib文件是静态编译出来的，索引和实现都在其中。使用静态编译的lib文件，在运行程序时不需要再挂动态库，缺点是导致应用程序比较大，而且失去了动态库的灵活性，发布新版本时要发布新的应用程序才行。
（3）动态链接的情况下，有两个文件：一个是LIB文件，一个是DLL文件。LIB包含被DLL导出的函数名称和位置，DLL包含实际的函数和数据，应用程序使用LIB文件链接到DLL文件。在应用程序的可执行文件中，存放的不是被调用的函数代码，而是DLL中相应函数代码的地址，从而节省了内存资源。DLL和LIB文件必须随应用程序一起发行，否则应用程序会产生错误。如果不想用lib文件或者没有lib文件，可以用WIN32 API函数LoadLibrary、GetProcAddress装载。
**（静态连接）**使用lib需注意两个文件：
（1）.h头文件，包含lib中说明输出的类或符号原型或数据结构。应用程序调用lib时，需要将该文件包含入应用程序的源文件中。
（2）.LIB文件，略。
**（动态连接）**使用dll需注意三个文件：
（1）.h头文件，包含dll中说明输出的类或符号原型或数据结构的.h文件。应用程序调用dll时，需要将该文件包含入应用程序的源文件中。
（2）.LIB文件，是dll在编译、链接成功之后生成的文件，作用是当其他应用程序调用dll时，需要将该文件引入应用程序，否则产生错误。如果不想用lib文件或者没有lib文件，可以用WIN32API函数LoadLibrary、GetProcAddress装载。
（3）.dll文件，真正的可执行文件，开发成功后的应用程序在发布时，只需要有.exe文件和.dll文件，并不需要.lib文件和.h头文件。
使用lib的方法：
静态lib中，一个lib文件实际上是任意个obj文件的集合，obj文件是cpp文件编译生成的。在编译这种静态库工程时，根本不会遇到链接错误；即使有错，也只会在使用这个lib的EXT文件或者DLL工程里暴露出来。
在VC中新建一个static library类型的工程Lib，加入test.cpp文件和test.h文件（头文件内包括函数声明），然后编译，就生成了Lib.lib文件。
别的工程要使用这个lib有两种方式：
（1）在project->link->Object/Library Module中加入Lib.lib文件（先查询工程目录，再查询系统Lib目录）；或者在源代码中加入指令#pragma comment(lib, “Lib.lib”)。
（2）将Lib.lib拷入工程所在目录，或者执行文件生成的目录，或者系统Lib目录中。
（3）加入相应的头文件test.h。
使用DLL的方法：
使用动态链接中的lib，不是obj文件的集合，即里面不会有实际的实现，它只是提供动态链接到DLL所需要的信息，这种lib可以在编译一个DLL工程时由编译器生成。
创建DLL工程的方法（略）。
（1）隐式链接
第一种方法是：通过project->link->Object/Library Module中加入.lib文件（或者在源代码中加入指令#pragma comment(lib, “Lib.lib”)），并将.dll文件置入工程所在目录，然后添加对应的.h头文件。

![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)#include "stdafx.h"
![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)#include "DLLSample.h"
![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)
![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)#pragma comment(lib, "DLLSample.lib")    //你也可以在项目属性中设置库的链接
![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)
![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)int main()
![img](http://www.cppblog.com/Images/OutliningIndicators/ExpandedBlockStart.gif){
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)        TestDLL(123);   //dll中的函数，在DllSample.h中声明
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)        return(1);
![img](http://www.cppblog.com/Images/OutliningIndicators/ExpandedBlockEnd.gif)}
![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)

（2）显式链接
需要函数指针和WIN32 API函数LoadLibrary、GetProcAddress装载，使用这种载入方法，不需要.lib文件和.h头文件，只需要.dll文件即可（将.dll文件置入工程目录中）。

![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)#include <iostream>
![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)#include <windows.h>         //使用函数和某些特殊变量
![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)typedef void (*DLLFunc)(int);
![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)int main()
![img](http://www.cppblog.com/Images/OutliningIndicators/ExpandedBlockStart.gif){
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)        DLLFunc dllFunc;
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)        HINSTANCE hInstLibrary = LoadLibrary("DLLSample.dll");
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)        if (hInstLibrary == NULL)
![img](http://www.cppblog.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif)        {
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)          FreeLibrary(hInstLibrary);
![img](http://www.cppblog.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif)        }
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)        dllFunc = (DLLFunc)GetProcAddress(hInstLibrary, "TestDLL");
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)        if (dllFunc == NULL)
![img](http://www.cppblog.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif)        {
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)          FreeLibrary(hInstLibrary);
![img](http://www.cppblog.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif)        }
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)        dllFunc(123);
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)        std::cin.get();
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)        FreeLibrary(hInstLibrary);
![img](http://www.cppblog.com/Images/OutliningIndicators/InBlock.gif)        return(1);
![img](http://www.cppblog.com/Images/OutliningIndicators/ExpandedBlockEnd.gif)}
![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)
![img](http://www.cppblog.com/Images/OutliningIndicators/None.gif)

LoadLibrary函数利用一个名称作为参数，获得DLL的实例（HINSTANCE类型是实例的句柄），通常调用该函数后需要查看一下函数返回是否成功，如果不成功则返回NULL（句柄无效），此时调用函数FreeLibrary释放DLL获得的内存。
GetProcAddress函数利用DLL的句柄和函数的名称作为参数，返回相应的函数指针，同时必须使用强转；判断函数指针是否为NULL，如果是则调用函数FreeLibrary释放DLL获得的内存。此后，可以使用函数指针来调用实际的函数。
最后要记得使用FreeLibrary函数释放内存。
注意：应用程序如何找到DLL文件？
使用LoadLibrary显式链接，那么在函数的参数中可以指定DLL文件的完整路径；如果不指定路径，或者进行隐式链接，Windows将遵循下面的搜索顺序来定位DLL：
（1）包含EXE文件的目录
（2）工程目录
（3）Windows系统目录
（4）Windows目录
（5）列在Path环境变量中的一系列目录

