## **ubuntu16.04下 boost python将C++封装成python3模块**

由于研究需要，在python3环境下要对C++写好的文件进行调用，直接对C++项目进行python实现不现实，考虑用boost_python模块将其封装成python扩展module，主要涉及两方面内容，即Linux环境下的boostpython编译和对源代码cpp文件的编译过程。

环境准备

ubuntu16.04下有两种方式安装配置boost：

（1）直接安装所有相关文件，在安装好的文件夹中可以找到libbooost_python3.x和libbooost_python2.x的版本，但这种方法在后期用python3进行编译时会出现libboost_python版本对应的问题，可以进行相关link设置，有回答指出是boost1.58版本的问题，可以下载最新版本源码自行编译。

```
sudo apt-get install libboost-all-dev
```

（2）[下载](https://www.boost.org/doc/libs/1_69_0/more/getting_started/unix-variants.html)源码自行编译

```
#解压
unzip boost_1_69_0.zip

#配置环境变量
cd boost_1_69_0
#-help查看指令内容
./bootstrap.sh -help
#指定python版本
./bootstrap.sh --with-python-version=3.5

#编译boost
sudo ./b2 install 
./b2 cxxflags=-fPIC cflags=-fPIC --c++11

'''
成功后的显示信息
The Boost C++ Libraries were successfully built!
'''
```

hello_world简单示例

[官网](https://www.boost.org/doc/libs/1_69_0/libs/python/doc/html/tutorial/tutorial/hello.html#tutorial.hello.running_bjam)有简单示例及boost_python模块的使用说明及wrapper文件的编写规范，编译工具采用的时bjam；其实编译方式有很多种，可以自己写[cmake文件](https://zhuanlan.zhihu.com/p/24059497)或者可以[利用Py++自动生成封装文件](https://josephchenhub.github.io/2018/08/15/boost_python/)进行make，还可以通过终端指令编译。本例采用终端命令行对c++源码进行编译。

hello.cpp

```c++
#include <string>
#include <boost/python.hpp>

using namespace std;
using namespace boost::python;
    
struct World{
    void set(string msg) { this->msg = msg; }
    string greet() { return msg; }
    string msg;
};

//特别注意下面的模块名hello同将来引入Python的模块名、编译完成的文件名，三者必须相同 
BOOST_PYTHON_MODULE(hello){
   class_<World>("World")
        .def("greet", &World::greet)
        .def("set", &World::set)
   ;
}
```

编译

```
#生成.o临时编译文件
g++ -fpic -c hello.cpp $(pkg-config --cflags python3)
#生成.so工作文件
g++ -shared -o hello.so hello.o -lboost_python35 $(pkg-config --cflags --libs python3)
```

上面的两行编译命令中，有两个编译参数可能是需要根据具体版本做调整的，一个是pkg-config库管理工具中的python3，这个名称和版本号可以检查相应配置文件，$(pkg-config --cflags python3)即自动获取相应的信息

　　另外一个是第二行命令中的-lboost_python35，这个检查已经安装的库版本来决定，比如-lboost_python35对应需要有libboost_python35.dylib文件，特别注意这个版本同将来运行的python环境版本必须精确一致，小版本也必须相同：

```javascript
ls /usr/local/lib/libboost_python*
```

编译生成hello.so文件后可以直接在终端python交互解释器界面进行验证：

```
python3
Python 3.5.2 (default, Nov 12 2018, 13:43:14) 
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import hello
>>> test=hello.World()
>>> test.set("hello 又")
>>> test.greet()
'hello 又'
>>> exit()
```

Tips：编译通过，import模块报错

（1）ImportError: libboost_python35.so.1.69.0: cannot open shared object file: No such file or directory

这种情况说明编译文件没有问题，是环境变量问题，如下指令可解决：

```
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
```

（2）ImportError: ./hello.so: undefined symbol: _ZNK5boost6python7objects21py_function_impl_base9max_arityEv 

ldd发现没有链接libboost_python这个库的任何信息，import 报错；这种情况可能是boost_python库的路径不在ld默认的库中，可以通过-L添加，参考[blog](https://blog.csdn.net/Frank_Abagnale/article/details/83018504)解决。