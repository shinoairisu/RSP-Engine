# RSP-Engine
RS-Engine是Resource SQLite Packer Engine的缩写，是一个暂时很简陋的对程序资源进行打包的工具

------------


- RSP引擎由ANSI-C编写的通用程序资源封装库。在保证读取速度的情况下，对资源进行压缩与加密。并且不会造成内存过度消耗。
- 它在跨平台时只需要SQLite库的移植即可。同样的，SQLite也是使用ANSI-C编写的跨平台支持库。



------------

**主要功能:**

- 将程序资源封装到一个或者多个资源包里去，方便游戏等应用的开发。由于已经存在ZPACK这种强大的库，RSP库将作为同类库的扩充，方便游戏开发者或者应用开发者有更多选择。

- RSP想要做到的是以更加广泛的方式提供一些更有效的资源打包功能。

- RSP的实体化(可视化，为了快)是由易语言开发的，虽然可能报毒并且在日文系统上运行八成是乱码(请试试转区工具？据说不太好使。)，但是个人认为开发者对于这个可视化工具需求不会太高，所以请忽略这个问题。


作为一名C语言初学者开发的库，目标是通俗易用易维护。

**API:**

> 注意: 凡是返回类似string[]的，真实情况为vector<string>

*Class*
RSP

*Struct*
FileInfo
MemoryInfo


RSP(string indexpath="resource.index",string packagepath="resource.asset",bool pass=false,string PW="123456" ,bool encryption=false,int method=ENCYLIB_BASE64)
初始化，创建或者读取一个索引与资源文件。默认不使用加密算法，并且无密码。

现在的版本密码与加密算法只是装样子。后期会到处搜罗加密与压缩算法来补充这部分。前期就算用了true也不会有效果。

为了有最大的易用性，开放了尽量多的可控参数。

~ RSP() 析构函数包括关闭数据库，关闭文件等，将缓冲压入文件中等等。


简洁明了就只有四个部分。返回值通常有三种:
string    int   unsigned char*

**GET系列:**

*GET系列是RSP获取资源的方式。(均可以开启多库查找，和无目录结构查找)*
```cpp
unsigned char*   getElementToBuffer(string name)
string  getElementToPath(sn,rootpath="xxx")
unsigned char**   getElementsToBuffer(string name)
string[]  getElementsToPath(sn,rootpath="xxx")
unsigned char**   getElementsToBufferBySql(sql)
string[]  getElementsToPathBySql(sql,path)
int getElementSize(name)
int[] getElementsSize(name)
unsigned char** getAllElementsToBuffer()
string[] getAllElementaToPath()
FileInfo getElementInfo(sn)
FileInfo[] getElementsInfo(sn)
FileInfo[] getAllInfo(sn)
FileInfo getElementInfoBySql(sql)
FileInfo[] getElementsInfoBySql(sql)
MemoryInfo getMemory()

unsigned char*   getElementbf(string name)
string  getElementpf(sn,rootpath="xxx")
unsigned char**   getElementsbf(string name)
string[]  getElementspf(sn,rootpath="xxx")
```
//最后四个函数是直接访问目录结构下的文件，也就是没有打包时的文件。
//bf=bufferfolder,pf=pathfolder

**SET系列:**

*SET系列是RSP写入资源的方式。(均可以开始目录结构和多库写入(没有会被创建))*
```cpp
void setElementFromBuffer(unsigned char* file,sp)
void setElementFromFile(sp,sp)
void setElementFromString(sp,sp)
void setElementFromFunction(function point,sp)
//允许创建多个库并放在不同的库里。最后一个可以传入lambda表达式或者函数指针。
bool setCompact(sp,float&) 
//将数据从新拼接，删除其中空闲空间，并且会直接清空表2。这个动作在文件很大的情况下会相当耗费时间。可以使用线程访问本函数的第二参数获取进度的百分比。这个功能通常是在可视化工具下使用，自己编写的程序一般是不会使用这个的。
```

**MODIFY系列:**

*MODIFY系列是对RSP对资源修改的方式。*


**UTILS系列:**

*UTILS系列是一些不确定开头的实用工具或者部分私有函数*
```cpp
createLib(sn)  //成员
findBest(size大小,en查找算法) //private
string[] pushAll(sp) 枚举目录下所有文件路径 //非成员
```

> 为了更加自由适应特殊需求，开放了底层的SQL语句API。但暂时认为此作为是无意义的。为了更加自由适应特殊需求，开放了底层的SQL语句API。但暂时认为此作为是无意义的。

**index文件的数据结构(暂时定为两张表):**

*表"数据视图"*
- 文件号
- 文件名
- 文件全名(含扩展名)
- 位置
- 大小
- 标记(是否存活，否的话会被删除)
- 路径(库中相对路径)
- 所在库(多个库情况下这个很重要)


*表"内存空闲"*
- 空闲区域标记:(文件号)
- 空闲区域起始地址:
- 空闲区域大小:


> RSP默认从尾部截取空闲空间给新的文件。或者新开辟空间分给新文件。两张数据表的内容会由触发器链接控制。
通常，删除的如果是最后一个文件，将会另作处理。
由于数据库的支持，允许一次取出多个同名数据。由于数据库的支持，允许一次取出多个同名数据。
