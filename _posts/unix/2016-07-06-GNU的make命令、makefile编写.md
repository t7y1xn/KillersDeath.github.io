---
layout: post
title: GNU的make命令、makefile编写
categories: Unix
description: GNU的make命令、makefile编写
keywords: GNU, linux, make
---

## **makefile简介**
--------------------------------------------------------------------------------

- makefile可实现工程的自动化编译，只需一个make命令即可一键完成。makefile定义了一些规则，指定哪些文件需要先编译、后编译、重新编译等。

- 一般的C或者C++程序，都需要先编译成中间文件，windows下为.obj文件，UNIX下为.o文件，这个过程称为编译（compile）。

- 每个原文件都应该对应一个中间文件（.obj文件或者.o文件），把大量的中间文件合成执行文件的过程，称为链接（Link）；链接时，主要是链接函数和变量，即可以使用.obj文件或者.o文件链接应用程序。链接器只关系函数的中间文件，不关心源文件的位置，为了避免大量的中间文件的复杂管理，需给中间文件打包，windows下称为库文件（Library File），即.lib文件，UNIX下为Archive File，即 .a文件。

## **makefile规则**
--------------------------------------------------------------------------------
- **流程**
  ```
  目标 : 需要的条件(注意 : 两边的空格)
      命令(以Tab键开头)

  解释一下：
      1\. 目标 可以是一个或者多个，可以是ObjectFile文件、执行文件、甚至标签
      2\. 条件 指的是 依赖的文件或者目标
      3\. 命令 指的是 生成目标需要执行的脚本


  总结：
      即一条makefile规则定义了编译的依赖关系，目标文件依赖于条件，生成规则用命令
  ```

- **举个例子**
  ```c++
  objects = main.o kbd.o command.o display.o /
                insert.o search.o files.o utils.o
      edit : $(objects)
              cc -o edit $(objects)
      main.o : main.c defs.h
              cc -c main.c
      kbd.o : kbd.c defs.h command.h
              cc -c kbd.c
      command.o : command.c defs.h command.h
              cc -c command.c
      display.o : display.c defs.h buffer.h
              cc -c display.c
      insert.o : insert.c defs.h buffer.h
              cc -c insert.c
      search.o : search.c defs.h buffer.h
              cc -c search.c
      files.o : files.c defs.h buffer.h command.h
              cc -c files.c
      utils.o : utils.c defs.h
              cc -c utils.c
      clean :
              rm edit $(objects)
  ```

## **make命令**
--------------------------------------------------------------------------------

1. 首先在当前目录下寻找"makefile"或者"Makefile"文件找到，则定位到第一个目标文件（target），上例中，会找到"edit"文件，并把此文件作为最终目标文件
2. 若eidt文件不存在，或者eidt后依赖的.o文件的修改时间比edit文件新，则执行后面定义的命令生成edit文件
3. 若edit后依赖的.o文件也不存在，make会在当前文件中寻找.o文件的依赖性，若找到，则根据那个规则生成.o文件.c和.h文件存在，make生成.o文件，最后执行edit

- **自动推导**
  GNU的make命令支持自动推导，只要make看到一个.o文件，就会自动把.c文件加到依赖关系中，则上述例子可简化为以下形式
  ```c++
  objects = main.o kbd.o command.o display.o /
                insert.o search.o files.o utils.o
      edit : $(objects)
              cc -o edit $(objects)

      main.o : defs.h
      kbd.o : defs.h command.h
      command.o : defs.h command.h
      display.o : defs.h buffer.h
      insert.o : defs.h buffer.h
      search.o : defs.h buffer.h
      files.o : defs.h buffer.h command.h
      utils.o : defs.h
  　　clean :
              rm edit $(objects)
  ```

- **文件功能**

  make支持多文件共简规则，上述例子可进一步简化为如下形式(但不推荐，因为较难维护)
  ```c++
  objects = main.o kbd.o command.o display.o /
                insert.o search.o files.o utils.o
      edit : $(objects)
              cc -o edit $(objects)

      $(objects) : defs.h
      kbd.o command.o files.o : command.h
      display.o insert.o search.o files.o : buffer.h
      clean :
              rm edit $(objects)
  ```

- **清空目标文件规则**

  每一个makefile文件都应该清楚的写一个清空目标的(.o和执行文件)规则，有利于重编译和文件的清结性

  .PHONY表示clean是一个"伪目标"，rm前的减号表示，某些文件出问题但是不要管，继续往下走

  clean默认放在文件尾，不然会被当作默认目标

  ```c++
   `一般的风格为：`
  clean:
              rm edit $(objects)
  `较为稳健的方法：`
  .PHONY : clean
          clean :
                  -rm edit $(objects)
  ```

--------------------------------------------------------------------------------

## **make编译安装**

- **流程**

  首先下载.tar.gz或者.tar.bz2软件包文件

  使用tar命令解压

  进入到解压后文件目录（因为make是搜索当前文件位置）

  使用./configure -prefix={Your Install Path} （其他configure参数自行百度）

  make编译（或者使用make all，过程有时很慢，有时会报错，可能是依赖包的问题，解决依赖关系即可）

  make install 安装（需足够权限，部分软件需要make check 或者 make test 进行测试）

  make clean 清除编译产生的中间文件（Objectfile或者.o文件）

- **举个例子**
  ```bash
  //1.解压缩
  tar -zxf xxxxx-4.0.2.tar.gz  
  //2.进入目录
  cd xxxxx-4.0.2
  //3.配置
  ./configure --prefix=/usr/local/xxxxx     
  //4.编译
  make all
  //5.安装
  make install && make install-init && make install-commandmode && make install-config
  //6.清除中间文件
  make clean
  ```

--------------------------------------------------------------------------------
# 相关链接

- **<http://blog.csdn.net/haoel/article/details/2887>**
- **<http://www.cnblogs.com/xwdreamer/p/3623454.html>**
- **<http://www.linuxidc.com/Linux/2011-02/32211.htm>**
