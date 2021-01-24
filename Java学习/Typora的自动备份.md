# Typora Helper

## version 1.0实现了如下功能,目前只有Linux版本:

1. 定时的检测是否有更新(修改或者新增)的文件(现阶段写死为"md文件"),对新增修改文件中的本地图片链接进行图片的备份(现阶段只能要上传到Github的图片仓库上)并替换为github的图片地址,然后将其上传到笔记仓库上.

## 如何使用我们的程序

1. 在Github上新建一个笔记仓库和图片仓库
2. 设置全局用户,密码
3. 执行代码

## 需要改进的地方:

1. 代码设计上完善,主要是实现自定义配置,提供良好的用户接口
2. 功能上添加实现本地备份一份只含本地的
3. 加入日志机制(*)
4. 异常处理
5. 性能优化

## 未来可以实现的东西

1. 自定义配置(同时备份到多个地方(Github,GitEE,自己的网站))
2. 做成UI程序

## version 1.1实现了如下功能,目前只有Linux版本:

1. 本地副本机制 (1月7日新增)  

将本地代码和远程代码分开,线程监测本地代码库副本是否被修改,如果修改,将原来的删除,用现有的副本进行替换.(这里如果出现中断是很危险的,所以得知道上次的任务是否完成)

![image-20210107235937164](https://github.com/kalao/Images/blob/master/Typora的自动备份.md/20210107235937164.png)

需要改进的地方:

1. 没有处理中断的情况如何将任务持久化
2. 没有封装任务





```
public static final String
        NOTEBOOK_FILE_PATH
        ="这是你的笔记本地仓库";
public static final String
        IMAGE_LOCAL_PATH=
        "这是你的图库";
public static final String
        PRO_PATH
        =Thread.currentThread().getContextClassLoader().getResource("").getPath();
public static final String
        SUFFIX="com/Typora/";
        
public static final String
        GIT_PREFIX="这是你图库的前缀";
public static final String
        BASH_PUSH="my.sh";
public static final String
        BASH_UPDATE="update.sh";
public static final String
        LOCAL_PRO_COPY="这是本地笔记仓库的副本";
public static final String
        BASH_LOCAL_COPY="localmy.sh";
```
