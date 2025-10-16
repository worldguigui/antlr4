# Getting Started with ANTLR v4
# ANTLR v4 入门指南

Hi and welcome to the version 4 release of ANTLR! See [Why do we need ANTLR v4?](faq/general.md) and the [preface of the ANTLR v4 book](http://media.pragprog.com/titles/tpantlr2/preface.pdf).
大家好，欢迎使用 ANTLR 第 4 版！请参阅[为什么我们需要 ANTLR v4？](faq/general.md) 和 [ANTLR v4 书籍的前言](http://media.pragprog.com/titles/tpantlr2/preface.pdf)。

## Getting started the easy way using antlr4-tools
## 使用 antlr4-tools 轻松入门

To play around with ANTLR without having to worry about installing it and the Java needed to execute it, use [antlr4-tools](https://github.com/antlr/antlr4-tools). The only requirement is Python3, which is typically installed on all developer machines on all operating systems. (See below for Windows issue.)
要轻松试用 ANTLR 而无需担心安装它和运行所需的 Java，请使用 [antlr4-tools](https://github.com/antlr/antlr4-tools)。唯一的要求是 Python3，它通常安装在所有操作系统的所有开发机器上。（Windows 相关问题见下文。）

```bash
$ pip install antlr4-tools
```

That command creates `antlr4` and `antlr4-parse` executables that, if necessary, will download and install Java 11 plus the latest ANTLR jar:
该命令会创建 `antlr4` 和 `antlr4-parse` 可执行文件，这些文件会在必要时下载并安装 Java 11 以及最新的 ANTLR jar：

```bash
$ antlr4 
Downloading antlr4-4.13.2-complete.jar
ANTLR tool needs Java to run; install Java JRE 11 yes/no (default yes)? y
Installed Java in /Users/parrt/.jre/jdk-11.0.15+10-jre; remove that dir to uninstall
ANTLR Parser Generator  Version 4.13.2
 -o ___              specify output directory where all output is generated
 -lib ___            specify location of grammars, tokens files
...
```

Let's play with a simple grammar:
让我们试用一个简单的语法：

```
grammar Expr;		
prog:	expr EOF ;
expr:	expr ('*'|'/') expr
    |	expr ('+'|'-') expr
    |	INT
    |	'(' expr ')'
    ;
NEWLINE : [\r\n]+ -> skip;
INT     : [0-9]+ ;
```

### Windows-specific issues
### Windows 特定问题

On Windows, the `pip` command doesn't just work---you need to add the `...\local-packages\python38\scripts` dir to your `PATH`, which itself might require a fun reboot.  If you use WSL on Windows, then the pip install will also properly at the scripts directly (if you run from bash shell).
在 Windows 上，`pip` 命令不能直接使用——您需要将 `...\local-packages\python38\scripts` 目录添加到您的 `PATH` 中，这可能还需要一次烦人的重启。如果您在 Windows 上使用 WSL，那么 pip 安装也会正确设置脚本目录（如果您从 bash shell 运行）。

1. Go to the Microsoft Store
2. Search in Microsoft Store for Python
3. Select the newest version of Python (3.11).
4. Click the "Get" button. Store installs python and pip at "c:\Users...\AppData\Local\Microsoft\WindowsApps\python.exe" and "c:\Users...\AppData\Local\Microsoft\WindowsApps\pip.exe", respectively. And, it updates the search path immediately with the install.
5. Open a "cmd" terminal.
6. You can now type "python" and "pip", and "pip install antlr4-tools". 7. Unfortunately, it does not add that to the search path.
7. Update the search path to contain `c:\Users...\AppData\Local\Packages\PythonSoftwareFoundation.Python.3.10_qbz5n2kfra8p8\LocalCache\local-packages\Python310\Scripts`. You may need to install MSYS2, then do a `find /c/ -name antlr4.exe 2> /dev/null` and enter that path.
8. Or, you can set up an alias to antlr4.exe on that path.
1. 打开 Microsoft Store。
2. 在 Microsoft Store 中搜索 Python。
3. 选择最新版本的 Python (3.11)。
4. 点击"获取"按钮。Store 会将 python 和 pip 分别安装在 "c:\Users...\AppData\Local\Microsoft\WindowsApps\python.exe" 和 "c:\Users...\AppData\Local\Microsoft\WindowsApps\pip.exe"。并且，它会随着安装立即更新搜索路径。
5. 打开一个 "cmd" 终端。
6. 您现在可以输入 "python" 和 "pip"，以及 "pip install antlr4-tools"。7. 不幸的是，它不会将其添加到搜索路径中。
7. 更新搜索路径以包含 `c:\Users...\AppData\Local\Packages\PythonSoftwareFoundation.Python.3.10_qbz5n2kfra8p8\LocalCache\local-packages\Python310\Scripts`。您可能需要安装 MSYS2，然后执行 `find /c/ -name antlr4.exe 2> /dev/null` 并输入该路径。
8. 或者，您可以在该路径上为 antlr4.exe 设置一个别名。

The good news is that the ANTLR4 Python tool downloads the ANTLR jar in a standard location, and you don't need to do that manually. It's also possible to go in a browser, go to python.org, and download the python package. But, it's likely you will need to update the path for antlr4.exe as before.
好消息是 ANTLR4 Python 工具会在标准位置下载 ANTLR jar，您不需要手动操作。也可以通过在浏览器中访问 python.org 来下载 python 包。但是，您很可能需要像之前一样更新 antlr4.exe 的路径。

### Try parsing with a sample grammar
### 尝试使用示例语法进行解析

To parse and get the parse tree in text form, use:
要解析并以文本形式获取解析树，请使用：

```bash
$ antlr4-parse Expr.g4 prog -tree
10+20*30
^D
(prog:1 (expr:2 (expr:3 10) + (expr:1 (expr:3 20) * (expr:3 30))) <EOF>)
```
（注意：`^D` 表示 Control-D，在 Unix 上表示"输入结束"；在 Windows 上使用 `^Z`。）

Here's how to get the tokens and trace through the parse:
以下是获取词法符号并跟踪解析过程的方法：

```bash
$ antlr4-parse Expr.g4 prog -tokens -trace
10+20*30
^D
[@0,0:1='10',<INT>,1:0]
[@1,2:2='+',<'+'>,1:2]
[@2,3:4='20',<INT>,1:3]
[@3,5:5='*',<'*'>,1:5]
[@4,6:7='30',<INT>,1:6]
[@5,9:8='<EOF>',<EOF>,2:0]
enter   prog, LT(1)=10
enter   expr, LT(1)=10
consume [@0,0:1='10',<8>,1:0] rule expr
enter   expr, LT(1)=+
consume [@1,2:2='+',<3>,1:2] rule expr
enter   expr, LT(1)=20
consume [@2,3:4='20',<8>,1:3] rule expr
enter   expr, LT(1)=*
consume [@3,5:5='*',<1>,1:5] rule expr
enter   expr, LT(1)=30
consume [@4,6:7='30',<8>,1:6] rule expr
exit    expr, LT(1)=<EOF>
exit    expr, LT(1)=<EOF>
exit    expr, LT(1)=<EOF>
consume [@5,9:8='<EOF>',<-1>,2:0] rule prog
exit    prog, LT(1)=<EOF>
```

Here's how to get a visual tree view:
以下是获取可视化树视图的方法：

```bash
$ antlr4-parse Expr.g4 prog -gui
10+20*30
^D
```

The following will pop up in a Java-based GUI window:
以下内容将在基于 Java 的 GUI 窗口中弹出：

<img src="https://github.com/antlr/antlr4-tools/blob/master/images/parse-tree.png?raw=true" width="300">

### Generating parser code
### 生成解析器代码

The previous section used a built-in ANTLR interpreter but typically you will ask ANTLR to generate code in the language used by your project (there are about 10 languages to choose from as of 4.11).  Here's how to generate Java code from a grammar:
上一节使用了内置的 ANTLR 解释器，但通常您会要求 ANTLR 用您项目使用的语言生成代码（截至 4.11 版本，约有 10 种语言可供选择）。以下是如何从语法生成 Java 代码：

```bash
$ antlr4 Expr.g4
$ ls Expr*.java
ExprBaseListener.java  ExprLexer.java         ExprListener.java      ExprParser.java
```

And, here's how to generate C++ code from the same grammar:
并且，以下是如何从同一语法生成 C++ 代码：

```bash
$ antlr4 -Dlanguage=Cpp Expr.g4
$ ls Expr*.cpp Expr*.h
ExprBaseListener.cpp  ExprLexer.cpp         ExprListener.cpp      ExprParser.cpp
ExprBaseListener.h    ExprLexer.h           ExprListener.h        ExprParser.h
```

## Installation
## 安装

ANTLR is really two things: a tool written in Java that translates your grammar to a parser/lexer in Java (or other target language) and the runtime library needed by the generated parsers/lexers. Even if you are using the ANTLR Intellij plug-in or ANTLRWorks to run the ANTLR tool, the generated code will still need the runtime library.
ANTLR 实际上包含两部分：一个用 Java 编写的工具，它将您的语法转换为 Java（或其他目标语言）的解析器/词法分析器；以及生成的解析器/词法分析器所需的运行时库。即使您使用 ANTLR Intellij 插件或 ANTLRWorks 来运行 ANTLR 工具，生成的代码仍然需要运行时库。

The first thing you should do is probably download and install a development tool plug-in. Even if you only use such tools for editing, they are great. Then, follow the instructions below to get the runtime environment available to your system to run generated parsers/lexers.  In what follows, I talk about antlr-4.13.2-complete.jar, which has the tool and the runtime and any other support libraries (e.g., ANTLR v4 is written in v3).
您应该做的第一件事可能是下载并安装一个开发工具插件。即使您只使用这些工具进行编辑，它们也非常有用。然后，按照以下说明为您的系统配置运行时环境，以运行生成的解析器/词法分析器。在下文中，我将讨论 antlr-4.13.2-complete.jar，它包含了工具、运行时以及任何其他支持库（例如，ANTLR v4 是用 v3 编写的）。

If you are going to integrate ANTLR into your existing build system using mvn, ant, or want to get ANTLR into your IDE such as eclipse or intellij, see [Integrating ANTLR into Development Systems](https://github.com/antlr/antlr4/blob/master/doc/IDEs.md).
如果您打算使用 mvn、ant 将 ANTLR 集成到现有的构建系统中，或者希望将 ANTLR 集成到您的 IDE（如 eclipse 或 intellij）中，请参阅[将 ANTLR 集成到开发系统](https://github.com/antlr/antlr4/blob/master/doc/IDEs.md)。

### UNIX
### UNIX 系统

0. Install Java (version 11 or higher)
1. Download
0. 安装 Java（版本 11 或更高）
1. 下载
```
$ cd /usr/local/lib
$ curl -O https://www.antlr.org/download/antlr-4.13.2-complete.jar
```
Or just download in browser from website:
    [https://www.antlr.org/download.html](https://www.antlr.org/download.html)
and put it somewhere rational like `/usr/local/lib`.
或者直接从网站下载：
    [https://www.antlr.org/download.html](https://www.antlr.org/download.html)
并将其放在合理的位置，例如 `/usr/local/lib`。

if you are using lower version jdk, just download from [website download](https://github.com/antlr/website-antlr4/tree/gh-pages/download) for previous version, and antlr version before 4.13.2 support jdk 1.8
如果您使用较低版本的 jdk，请从[网站下载](https://github.com/antlr/website-antlr4/tree/gh-pages/download)旧版本，4.13.2 之前的 ANTLR 版本支持 jdk 1.8。

2. Add `antlr-4.13.2-complete.jar` to your `CLASSPATH`:
2. 将 `antlr-4.13.2-complete.jar` 添加到您的 `CLASSPATH`：
```
$ export CLASSPATH=".:/usr/local/lib/antlr-4.13.2-complete.jar:$CLASSPATH"
```
It's also a good idea to put this in your `.bash_profile` or whatever your startup script is.
将其放入您的 `.bash_profile` 或任何启动脚本中也是个好主意。

3. Create aliases for the ANTLR Tool, and `TestRig`.
3. 为 ANTLR 工具和 `TestRig` 创建别名。
```
$ alias antlr4='java -Xmx500M -cp "/usr/local/lib/antlr-4.13.2-complete.jar:$CLASSPATH" org.antlr.v4.Tool'
$ alias grun='java -Xmx500M -cp "/usr/local/lib/antlr-4.13.2-complete.jar:$CLASSPATH" org.antlr.v4.gui.TestRig'
```

### WINDOWS
### WINDOWS 系统

(*Thanks to Graham Wideman*)
（*感谢 Graham Wideman*）

0. Install Java (version 1.7 or higher)
1. Download antlr-4.13.2-complete.jar (or whatever version) from [https://www.antlr.org/download.html](https://www.antlr.org/download.html)
Save to your directory for 3rd party Java libraries, say `C:\Javalib`
2. Add `antlr-4.13.2-complete.jar` to CLASSPATH, either:
  * Permanently: Using System Properties dialog > Environment variables > Create or append to `CLASSPATH` variable
  * Temporarily, at command line:
0. 安装 Java（版本 1.7 或更高）
1. 从 [https://www.antlr.org/download.html](https://www.antlr.org/download.html) 下载 antlr-4.13.2-complete.jar（或任何版本）
保存到您的第三方 Java 库目录，例如 `C:\Javalib`
2. 将 `antlr-4.13.2-complete.jar` 添加到 CLASSPATH，可以通过：
  * 永久方式：使用系统属性对话框 > 环境变量 > 创建或附加到 `CLASSPATH` 变量
  * 临时方式，在命令行：
```
SET CLASSPATH=.;C:\Javalib\antlr-4.13.2-complete.jar;%CLASSPATH%
```
3. Create short convenient commands for the ANTLR Tool, and TestRig, using batch files or doskey commands:
  * Batch files (in directory in system PATH) antlr4.bat and grun.bat
3. 使用批处理文件或 doskey 命令为 ANTLR 工具和 TestRig 创建简短的便捷命令：
  * 批处理文件（位于系统 PATH 中的目录）antlr4.bat 和 grun.bat
```
java org.antlr.v4.Tool %*
```
```
@ECHO OFF
SET TEST_CURRENT_DIR=%CLASSPATH:.;=%
if "%TEST_CURRENT_DIR%" == "%CLASSPATH%" ( SET CLASSPATH=.;%CLASSPATH% )
@ECHO ON
java org.antlr.v4.gui.TestRig %*
```
  * Or, use doskey commands:
  * 或者，使用 doskey 命令：
```
doskey antlr4=java org.antlr.v4.Tool $*
doskey grun =java org.antlr.v4.gui.TestRig $*
```

### Testing the installation
### 测试安装

Either launch org.antlr.v4.Tool directly:
直接启动 org.antlr.v4.Tool：

```
$ java org.antlr.v4.Tool
ANTLR Parser Generator Version 4.13.2
-o ___ specify output directory where all output is generated
-lib ___ specify location of .tokens files
...
```

or use -jar option on java:
或在 java 上使用 -jar 选项：

```
$ java -jar /usr/local/lib/antlr-4.13.2-complete.jar
ANTLR Parser Generator Version 4.13.2
-o ___ specify output directory where all output is generated
-lib ___ specify location of .tokens files
...
```

## A First Example
## 第一个示例

In a temporary directory, put the following grammar inside file Hello.g4:
Hello.g4
在一个临时目录中，将以下语法放入文件 Hello.g4：
Hello.g4

```
// Define a grammar called Hello
grammar Hello;
r  : 'hello' ID ;         // match keyword hello followed by an identifier
ID : [a-z]+ ;             // match lower-case identifiers
WS : [ \t\r\n]+ -> skip ; // skip spaces, tabs, newlines
```

Then run ANTLR the tool on it:
然后在它上面运行 ANTLR 工具：

```
$ cd /tmp
$ antlr4 Hello.g4
$ javac Hello*.java
```

Now test it:
现在测试它：

```
$ grun Hello r -tree
(Now enter something like the string below)
hello parrt
(now,do:)
^D
(The output:)
(r hello parrt)
(That ^D means EOF on unix; it's ^Z in Windows.) The -tree option prints the parse tree in LISP notation.
It's nicer to look at parse trees visually.
$ grun Hello r -gui
hello parrt
^D
```

That pops up a dialog box showing that rule `r` matched keyword `hello` followed by identifier `parrt`.
这将弹出一个对话框，显示规则 `r` 匹配了关键字 `hello` 后跟标识符 `parrt`。

![](images/hello-parrt.png)

## Book source code
## 书籍源代码

The book has lots and lots of examples that should be useful too. You can download them here for free:
这本书有大量示例，也应该很有用。您可以在此免费下载：

[ANTLR reference book examples in Java](https://media.pragprog.com/titles/tpantlr2/code/tpantlr2-code.zip)<br>
[ANTLR 参考书 Java 示例](https://media.pragprog.com/titles/tpantlr2/code/tpantlr2-code.zip)<br>
[ANTLR reference book examples in C#](https://github.com/Philippe-Laval/tpantlr2)
[ANTLR 参考书 C# 示例](https://github.com/Philippe-Laval/tpantlr2)

[Language implementation patterns book examples in Java](https://media.pragprog.com/titles/tpdsl/code/tpdsl-code.zip)<br>
[语言实现模式书籍 Java 示例](https://media.pragprog.com/titles/tpdsl/code/tpdsl-code.zip)<br>
[Language implementation patterns book examples in C#](https://github.com/Philippe-Laval/tpdsl)
[语言实现模式书籍 C# 示例](https://github.com/Philippe-Laval/tpdsl)

Also, there is a large collection of grammars for v4 at github:
此外，github 上有一个大型的 v4 语法集合：

[https://github.com/antlr/grammars-v4](https://github.com/antlr/grammars-v4)
[https://github.com/antlr/grammars-v4](https://github.com/antlr/grammars-v4)
