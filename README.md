# LearningNotes
It's my learning note including RL, Python, and everything.

1、python快速创建二维数组单个元素赋值操作导致整列元素均发生改变问题；

2、ubuntu16.04下用boost_python将c++文件封装成python3可调用的模块；

3、RL文件夹主要记录了学习强化学习的前导知识及经典算法总结；

4、python中os.path.isdir()和os.path.isfile()的正确用法；

5、win10+python3.6+vs2015+boost实现c++封装成python模块方法；

6、本地pycharm调用远程服务器开发配置说明；
    tips:文件里描述的方法有一处需要说明，就是能够进行转发本地显示画图结果的前提是能够进行x11forward相关服务，这个可以找到一些方法；对于远程服务器环境          变量中没有$DISPLAY的情况，通过export进行指定可能起不到效果，这种情况下是可以进行savefig的，只不过matplotlib使用的backend要改，亲测改为matplotlib.use('Agg')可以成功保存图片。

7、windows下 vs code 本地解释器调用远程服务器
