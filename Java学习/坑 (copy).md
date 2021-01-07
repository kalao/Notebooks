# 一 SpringBoot 访问不到static文件夹下的静态资源

标签：[src](http://www.bubuko.com/so/1/src)  [ron](http://www.bubuko.com/so/1/ron)  [wid](http://www.bubuko.com/so/1/wid)  [col](http://www.bubuko.com/so/1/col)  [oot](http://www.bubuko.com/so/1/oot)  [ctrl+](http://www.bubuko.com/so/1/ctrl_1_)  静态资源的文件夹`"classpath:/META-INF/resources/",  "classpath:/resources/", "classpath:/static/",  "classpath:/public/"  "/"：当前项目的根路径` **这里有个坑==》启动项目后访问不到静态资源，一直宝404**这是静态资源的存放位置：[![技术分享图片](http://image1.bubuko.com/info/202006/20200617223731674441.png)](https://img2020.cnblogs.com/blog/1365514/202006/1365514-20200617213321250-679224877.png)此时 :需要 maven clean,然后重启项目即可**或者**ctrl+f5清除浏览器缓存，再进行访问[![技术分享图片](http://image1.bubuko.com/info/202006/20200617223731803339.png)](https://img2020.cnblogs.com/blog/1365514/202006/1365514-20200617213322358-1651062657.png)[![技术分享图片](http://image1.bubuko.com/info/202006/20200617223731957626.png)](https://img2020.cnblogs.com/blog/1365514/202006/1365514-20200617213323899-559356703.png)



# 二 Java创建Process对象调用linux shell过程乱码的探究

![img](https://csdnimg.cn/release/blogv2/dist/pc/img/original.png)

[czw25](https://blog.csdn.net/czw25) 2013-07-30 21:56:33 ![img](https://csdnimg.cn/release/blogv2/dist/pc/img/articleReadEyes.png) 2144 ![img](https://csdnimg.cn/release/blogv2/dist/pc/img/tobarCollect.png) 收藏

分类专栏： [知识传递](https://blog.csdn.net/czw25/category_1458275.html) 文章标签： [乱码](https://so.csdn.net/so/search/s.do?q=乱码&t=blog&o=vip&s=&l=&f=&viparticle=) [UTF-8](https://so.csdn.net/so/search/s.do?q=UTF-8&t=blog&o=vip&s=&l=&f=&viparticle=) [SHELL](https://www.csdn.net/tags/OtTacg3sMzk5Mi1ibG9n.html) [JAVA](https://www.csdn.net/tags/NtTaIg5sMzYyLWJsb2cO0O0O.html) [encoding](https://www.csdn.net/tags/MtjaEg3sNzMxNjItYmxvZwO0O0OO0O0O.html)

https://blog.csdn.net/myjavacode/article/details/9633699

在多语言环境中，这个比较常用到：

***\**\*设置\*\*JVM启动项：-Dfile.encoding=UTF-8\****

***\*其他两个一般比较少\****

 



 以下内容*：我仅以我的实际经验为准做的猜测性分析，当中有些内容可能和真实的技术实现不同或有错，敬请指出，谢谢！*

我这里想分享一个关于乱码问题的探究和尚待完善的解决方案！ 

故事是这样开始的，一天，我通过项目的java代码调用linux的shell，比如：ssh admin@104.218.139.40:/export1/servers/nginx.sh , 脚本不存在，我得到了错误提示，但是错误提示是乱码。

为什么是乱码呢，我查看了我们的底层代码，发现 InputStreamReaderThread.java 的run方法，是把shell脚本调用的返回流用UTF-8解码。

 

**乱码产生的根本原因是编码字符集和解码字符集不一致。**

 

既然UTF-8解码有乱码，那么肯定是编码不是用UTF-8的，那么在这个交互过程中，编码的字符集由什么决定呢？

我查看了Java创建Process对象的相关文档，其中有说到，Process对象在创建的时候，如果不显式指定env，会从其父进程（JVM进程）获取环境参数（参考：http://tech.hexun.com/2009-06-08/118437291.html），

这些环境变量中，就有决定Process子进程在向linux发送命令时，申明自己的charset，这个charset就是JVM的defaultCharset，比如LANG=zh_CN.GBK, 所以在java代码中调用Charset.getDefaultCharset得到的

字符集，就是和linux shell交互时的字符集，只有保证了这个字符集也为UTF-8，那么就不会显示乱码了。

 

小结：***\*JVM的defaultCharset要设置为UTF-8。那么怎么设置呢，有绝对能控制defaultCharset的方法吗\****？

 

回答：据我的了解，JVM的启动参数中没有能直接设置JVM defualtCharset的方法，JVM的defaultCharset是jvm内部逻辑代码根据jvm所在物理机器的locale和其他一些区域信息设置默认值的，比如我们安装一台机器，我们选的语言是中文，我们选的国家是中国，我们选的区域是中国上海，可能JVM的内部逻辑就猜测这个机器要用GBK编码，于是，该JVM的defaultCharset就定位GBK了。

 

据我实际的测试，能在大部分情况下保证 Charset.getDefaultCharset返回的是UTF-8：

***\*1：设置JVM启动项：-Dfile.encoding=UTF-8\****

***\*2：设置JVM启动项：-Duser.language=en\****

***\*3：设置JVM启动项：-D user.country=US\****

 

 

Java code:



```java
System. getenv ():
```





Result:

**Windows:**



```xml
{java.vm.version=20.9-b04, shared.loader=, java.vm.vendor=Sun Microsystems Inc., user.<strong>country=CN</strong>, sun.os.patch.level=Service Pack 1, java.vm.specification.name=Java Virtual Machine Specification,
os.name=Windows 7, sun.jnu.<strong>encoding=GBK</strong>, java.library.path=C:\Program Files\Java\jdk1.6.0_34\bin;C:\Windows\Sun\Java\bin;C:\Windows\system32;C:\Windows; user.timezone=Asia/Shanghai, , java.home=C:\Program Files\Java\jdk1.6.0_34\jre, sun.arch.data.model=32, user.language=zh, java.specification.vendor=Sun Microsystems Inc., awt.toolkit=sun.awt.windows.WToolkit, java.vm.info=mixed mode, java.version=1.6.0_34, file.<strong>encoding= GBK</strong> }
```







**Linux:**



```javascript
{TERM=xterm,…..SSH_CONNECTION=104.218.134.25 56047 104.218.139.42 22, LD_LIBRARY_PATH=/java/jdk1.6.0_26/jre/lib/i386/server:/java/jdk1.6.0_26/jre/lib/i386:/java/jdk1.6.0_26/jre/../lib/i386, SHELL=/bin/bash…JRE_HOME=/java/jdk1.6.0_26/jre, USER=admin, CATALINA_HOME=/export/servers/tomcat6.0.33, HOME=/home/admin, JAVA_BIN=/export/servers/jdk1.6.0_25/bin, LESSOPEN=|/usr/bin/lesspipe.sh %s, LS_COLORS=no=00:fi=00:di=00;34:ln=00;36:pi=40;33:so=00;35:bd=40;33;01:cd=40;33;01:or=01;05;37;41:mi=01;05;37;41:ex=00;32:*.cmd=00;32:*.exe=00;32:*.com=00;32:*.btm=00;32:*.bat=00;32:*.sh=00;32:*.csh=00;32:*.tar=00;31:*.tgz=00;31:*.arj=00;31:*.taz=00;31:*.lzh=00;31:*.zip=00;31:*.z=00;31:*.Z=00;31:*.gz=00;31:*.bz2=00;31:*.bz=00;31:*.tz=00;31:*.rpm=00;31:*.cpio=00;31:*.jpg=00;35:*.gif=00;35:*.bmp=00;35:*.xbm=00;35:*.xpm=00;35:*.png=00;35:*.tif=00;35:, LANG=<strong>zh_CN.UTF-8</strong>, SSH_ASKPASS=/usr/libexec/openssh/gnome-ssh-askpass}
```



# 三 java调用Bat cmd命令和Linux Shell，并获取执行中的输出（字符乱码问题解决）

![img](https://csdnimg.cn/release/blogv2/dist/pc/img/original.png)

[Betteronly](https://blog.csdn.net/betteronly) 2019-01-16 09:53:42 ![img](https://csdnimg.cn/release/blogv2/dist/pc/img/articleReadEyes.png) 419 ![img](https://csdnimg.cn/release/blogv2/dist/pc/img/tobarCollect.png) 收藏

分类专栏： [java相关](https://blog.csdn.net/betteronly/category_8615207.html) 文章标签： [java](https://www.csdn.net/tags/NtTaIg5sMzYyLWJsb2cO0O0O.html) [shell](https://www.csdn.net/tags/OtTacg3sMzk5Mi1ibG9n.html) [bat](https://www.csdn.net/tags/MtzaMg1sMDM1MDYtYmxvZwO0O0OO0O0O.html) [cmd](https://www.csdn.net/tags/MtTaEg0sMzk0NzUtYmxvZwO0O0OO0O0O.html) [乱码](https://so.csdn.net/so/search/s.do?q=乱码&t=blog&o=vip&s=&l=&f=&viparticle=)

版权

https://blog.csdn.net/betteronly/article/details/86502362

 java调用windows Bat或者Linux Shell，并获取执行中的输出（解决了字符乱码问题）

```java
String strCmd = "dir";  // bat
//String strCmd = "ls";   // shell 
Process process;
BufferedReader input;
try {
    if (SYstem.getProperties().getProperty("os.name").matches("Windows.*$")){
        process = Runtime.getRuntime().exec(new String[]{"cmd.exe", "/c", strCmd}); //bat
        process.waitFor();
        input = new BufferedReader(new InputStreamReader(process.getInputStream(), Charset.forName("GBK")));
    } else {
        process = Runtime.getRuntime().exec(new String[]{"/bin/sh", "-c", strCmd}); //shell
        process.waitFor();
        input = new BufferedReader(new InputStreamReader(process.getInputStream(), Charset.forName("UTF-8")));
    }
    String line = "";
    while((line = input.readLine()) != null){
        System.out.println(line);
    }
    input.close();
} catch (IOException e) {
    e.printStackTrace();
}
```

# 四 [git 中文乱码问题](https://www.cnblogs.com/lhdre/p/8493507.html)

- 在cygwin中，使用git add添加要提交的文件的时候，如果文件名是中文，会显示形如 274\232\350\256\256\346\200\273\347\273\223.png 的乱码。 

  - 解决方案：在bash提示符下输入： `git config --global core.quotepath false`

  - core.quotepath设为false的话，就不会对0x80以上的字符进行quote。中文显示正常。 

    

- 在MsysGit中，使用git log显示提交的中文log乱码。 

  解决方案： 

  1. 设置git gui的界面编码 `git config --global gui.encoding utf-8`

  2. 设置 commit log 提交时使用 utf-8 编码，可避免服务器上乱码，同时与linux上的提交保持一致！ `git config --global i18n.commitencoding utf-8`

  3. 使得在 $ git log 时将 utf-8 编码转换成 gbk 编码，解决Msys bash中git log 乱码。`git config --global i18n.logoutputencoding gbk`

  4. 使得 git log 可以正常显示中文（配合i18n.logoutputencoding = gbk)，在 /etc/profile 中添加：`export LESSCHARSET=utf-8` 

     

- 在MsysGit自带的bash中，使用ls命令查看中文文件名乱码。cygwin没有这个问题。 

  - 解决方案： 

    1. 使用 `ls --show-control-chars` 命令来强制使用控制台字符编码显示文件名，即可查看中文文件名。

    2. 为了方便使用，可以编辑 /etc/git-completion.bash ，新增一行 alias ls=”ls –show-control-chars” 

       

- 终极的解决方案是通过修改git和TortoiseGit源码实现，有网友这么做了：让Windows下Git和TortoiseGit支持中文文件名/UTF-8 ，也可以直接访问这个开源的Google项目：utf8-git-on-windows 。 

  

- 如果不抗拒命令行的话，直接用Cygwin来提交Git库。因为Cygwin其实是一个在Windows平台上的模拟器，它完全模拟GNU/Linux的方式运行，所以Cygwin中的Git是采用UTF-8编码来保存中文的。|
  参考：http://blog.csdn.net/tyro_java/article/details/53439537





1![image-20210102111234976](https://github.com/kalao/Images/blob/master/坑.md/20210102111234976.png)

2

4

![image-20210102121114918](https://github.com/kalao/Images/blob/master/坑.md/20210102121114918.png)

![image-20210102120203395](https://github.com/kalao/Images/blob/master/坑.md/20210102120203395.png)



![image-20210102121521934](https://github.com/kalao/Images/blob/master/坑.md/20210102121521934.png)
