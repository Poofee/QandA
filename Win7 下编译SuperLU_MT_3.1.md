# Win7 下编译SuperLU_MT_3.1

2016-11-25 By Poofee

1. 新建VS项目；

2. 将superlumt的c文件和h文件添加到项目当中；

3. VS设置页面，添加openmp支持；

4. 修改slu_mt_machines.h的MACH，或者在project属性里修改预处理定义，添加__OPENMP、WIN32；

5. 项目配置为dll；

6. C++，命令行，/D _CRT_SECURE_NO_WARNINGS

7. 修改EXIT为return 0；

8. 宏添加WIN32；

9. 项目属性->配置属性->C/C++->SDL检查，选测是或者否。

10. 下载openblas，设置链接器，路径为libopenblas.dll.a路径，依赖项为libopenblas.dll.a；

11. matgen.c dlaran函数声明前加上__inline;

12. 这样编译就通过了，生成的是lib文件；

13. 文件的使用，如果是在一个sln下面的话，exe和lib文件就在一个目录下，我觉得不需要设置什么，如果不在的话，需要设置链接依赖，openblas和superlu的路径，以及这两个lib的文件名，还有，要把openblass的dll文件放到可以搜索到的路径下。

    ​