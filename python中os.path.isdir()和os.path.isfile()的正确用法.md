# python中os.path.isdir()和os.path.isfile()的正确用法

（1）`os.listdir()`方法：返回一个列表，其中包含有指定路径下的目录和文件的名称

```python
import os
dirct = '/home/workespace/notebook/'
for i in os.listdir(dirct):
    print(i)
```

```
test.ipynb
mnist_dataset
.ipynb_checkpoints
Untitled.ipynb
working
```

**注意：**os.path.isdir()和os.path.isfile()需要传入的参数是绝对路径，但是`os.listdir()`返回的只是一个某个路径下的文件和列表的名称.

**常见错误：**直接使用`os.listdir()`的返回值当做`os.path.isdir()`和`os.path.isfile()`的入参

**正确用法**：需要先使用python路径拼接`os.path.join()`函数，将`os.listdir()`返回的名称拼接成文件或目录的绝对路径再传入`os.path.isdir()`和`os.path.isfile()`.

（2）`os.path.join()`

```python
import os
dirct = '/home/workespace/notebook/'
for i in os.listdir(dirct):
    fulldirct = os.path.join(dirct,i)
    print(fulldirct)
```

```
/home/workespace/notebook/study_test.ipynb
/home/workespace/notebook/mnist_dataset
/home/workespace/notebook/.ipynb_checkpoints
/home/workespace/notebook/Untitled.ipynb
/home/workespace/notebook/working
```

(3)`os.path.isdir()`用于判断某一对象(需提供绝对路径)是否为目录

```python
import os
dirct = '/home/workespace/notebook/'
for i in os.listdir(dirct):
    fulldirct = os.path.join(dirct, i)
    if os.path.isdir(fulldirct): #入参需要是绝对路径
        print(i)
```

```
mnist_dataset
.ipynb_checkpoints
working
```

(4)`os.path.isfile()`用于判断某一对象(需提供绝对路径)是否为文件

```python
import os
dirct = '/home/workespace/notebook/'
for i in os.listdir(dirct):
    fulldirct = os.path.join(dirct, i)
    if os.path.isfile(fulldirct): #入参需要是绝对路径
        print(i)
```

```
study_test.ipynb
Untitled.ipynb
```

