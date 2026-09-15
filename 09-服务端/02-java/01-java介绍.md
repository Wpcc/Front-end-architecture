
# Java学习
## Java介绍
- Java SE(Java standard Edition) - 标准版 - Java技术的核心和基础
- Java EE(Java Enterprise Edition) - 企业版 - 企业级应用开发的一套解决方案
- Java ME(Java Micro Edition)：小型版 - 针对移动设备应用的解决方案

## Java使用

Java语言的产品是JDK（Java Development Kit：Java开发者工具包），必须安装JDK才能使用Java语言。

- 2014年JDK（8.0.LTS）
- 2021年9月14日JDK（17.0.LTS） - 学习版

**下载：**

- oracle官网，下载17版本
- 通过命令语句`java -version`、`javac -version`验证是否安装
  - javac：编辑工具 - 将java语言编译成机器语言
  - java：执行工具 - 执行编译后的机器语言

**简单命令行语句**

- 切盘
  - `D:`
- dir
  - 显示文件目录
- cd 路径
  - 进入文件，输入文件时按 tab 键可补齐文件名
- cls
  - 清理

**简单的java程序**

- 步骤
  - 编写、编译（Javac）、运行（Java）
- 基本要求
  - 文件名称后缀必须是Java结尾
  - 文件名必须与代码的类名称一致
  - 必须使用英文模式下的符号

## IDEA

IDEA：intelliJ IDEA，业界公认最好的 java 开发工具。

集成开发环境（IDE，Integrated Development Environment）

破解：https://www.exception.site/essay/idea-reset-eval

**结构：**

- project - module - package - class

**常用快捷键：**

- main/psvm、sout/"hello".sout
- Ctrl + D 
  - 复制当前行
- Ctrl + Y / Ctrl + X
  - 删除当前行
- Crtl + ALT + L
  - 格式化代码
- Alt + Shift + 箭头
  - 上下移动代码
- Ctrl + / ， Ctrl + Shift + /
  - 代码注释

**工程操作：**

- 导入模块

  - file - new - module from exisiting sources
  - 选择带有IJ图标的文件夹（模块）进行导入
    - 该操作会关联路径，最好新建项目再进行代码复制操作

- 删除模块

  - 建议直接去磁盘中进行文件删除，并删除`.idea`中的文件路径
  - 也可以通过目录上方模块的命令行`delete`进行删除操作

  