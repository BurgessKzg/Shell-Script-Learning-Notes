> &emsp;&emsp;GNU/Linux shell是一种特殊的交互式工具。它为用户提供了启动程序、管理文件系统中的文件以及运行在Linux系统上的进程的途径。
> &emsp;&emsp;在Linux系统上，通常有好几种Linux shell可用。不同的shell有不同的特性，有些更利于创建脚本，有些则更利于管理进程。

> &emsp;&emsp;将多个shell命令放入文件中作为程序执行,这些文件被称作shell脚本。

[TOC]

# 1. shell编程脚本基础

## 1.1. 构建基本脚本

### 1.1.1. 使用多个命令

> &emsp;&emsp;shell可以将多个命令串起来一次执行完成,如果要多个命令一起运行，可以把它们放在同一行中，彼此间用分号隔开(不超过最大命令行字符数255)。  

 - **eg:**    
    ```Shell
    $ date ; who
    ```

### 1.1.2. 创建shell脚本文件

> 1. 使用文本编辑器创建一个文件；
> 2. 必须在文件的第一行指定要使用的shell，格式： "`#!/bin/bash`"，"bash"可以换为别的shell；
> 3. 指定shell后就可以在每一行输入命令，然后加一个回车符；

- **注意**
    > - shell脚本中**井号**(#)用作**注释行**，shell**不处理**注释行，**第一行**是个例外，"#"后面的惊叹号告诉shell用哪个shell来运行脚本; 
    > - shell脚本中注释可用"#"添加，可以出现在**任意行**；
    > - shell脚本中可以根据需要使用**分号**将多个命令放在一行上；
    > - shell会按命令在文件中出现的**顺序**进行处理；
    > - shell会通过"**PATH**环境变量"来查找命令；
    > - 执行脚本需要让shell能找到脚本位置，方法：
    >   - 将shell脚本文件所处的目录添加到"PATH"环境变量中；
    >   - 在提示符中用绝对或相对文件路径来引用shell脚本文件；
    > - 如果umask使得创建的文件没有执行权限，使用"chmod"添加执行权限；

### 1.1.3. 显示消息

> 在"echo"命令后加上一个字符串，该命令能回显这个文本字符串；  

- **注意**
    > - 默认情况**不需要**使用引号将要显示的文本字符串划定；
    > - 要显示的字符串包括"'"(单引号)时，需要将字符串用"""(双引号)划定；
    > - 要显示的字符串包括"""(双引号)时，需要将字符串用"'"(单引号)划定；
    > - 以上两条不必用"""(双引号)或"'"单引号将整个字符串划定起来；
    > - "echo"回显的数据默认会换行，可以加"-n"选项去掉换行； 

### 1.1.4. 使用变量

> &emsp;&emsp;变量允许你临时性地将信息存储在shell脚本中，以便和脚本中的其他命令一起使用。

#### 1.1.4.1. 环境变量

> &emsp;&emsp;shell维护一组环境变量，用来记录特定系统信息。比如系统名称、登录到系统上的用户名、用户的系统ID(也称为UID)、用户默认主目录以及shell查找程序的搜索路径。可以用"**set**"命令来显示一份**完整的**当前环境变量列表。

> - 脚本中，可以在环境变量名称前加上"$"(美元符)来使用环境变量(不需要加"-n"选项)，这个时候可以将环境变量放到引号里；
> - 脚本在引号中出现"$"(美元符)，它会以为你在引用一个变量；
> - 要在引号中显示"$"(美元符)，需要在它前面添加"\\"(反斜线)；

#### 1.1.4.2. 用户变量

> &emsp;&emsp;shell脚本允许在脚本中定义和使用自己的变量(用户变量)，定义变量允许临时存储数据并在整个脚本中使用。

> - 用户变量可是字母、数字或下划线任意组成的文本字符串；
> - 用户变量长度不超过20个字符；
> - 用户变量区分大小写；
> - 使用等号赋值给用户变量，在变量、等号和值之间**不能**出现空格；
> - shell脚本会自动决定变量值的数据类型；
> - 在脚本的整个生命周期里，shell脚本中定义的变量会一直保持它们的值，**但**在shell脚本结束时会被**删除**；
> - 与系统变量类似，用户变量可通过"$"(美元符)引用，变量每次被引用时，都会输出当前赋给它的值；
> - 引用一个**变量值时需要**使用美元符，而引用变量来对其进行赋值时则**不需要**使用美元符，没有美元符，shell会将变量名解释成普通的文本字符串；
> - 通过"${variable}"形式引用的变量,变量名两侧额外的花括号通常用来帮助识别美元符后的变量名；

#### 1.1.4.3. 命令替换

> &emsp;&emsp;shell脚本中，可以从命令输出中提取信息，并将其赋给变量。

- 两种方法可以将命令输出赋给变量：
    > - "\`"(反引号)，用一对反引号把整个命令行命令围起来；
    ```Shell
    var=`date`
    ```
    > - "$()"格式    
    ```shell
    var=$(date)
    ```
- **注意**
    > - 反引号字符**不是**用于字符串的那种普通单引号字符；
    > - 命令替换会创建一个子shell来运行对应的命令,由该子shell所执行命令是无法使用脚本中所创建的变量；
    > - 在命令行提示符下使用路径"./"运行命令也会创建子shell；要是运行命令的时候不加入路径就不会创建子shell，如果使用内建的shell命令，并不会涉及子shell；

- **eg:**
    > - 在脚本中通过命令替换获得当前日期并用它来生成唯一文件名
    ```shell
    #!/bin/bash
    # copy the /usr/bin directory listing to a log file
    # 格式化后的"date"命令的输出
    today=$(date +%y%m%d)
    ls /usr/bin -al > log.$today
    ```

### 1.1.5. 重定向输入和输出

> &emsp;&emsp;有时想要保存某个命令的输出而不是让它显示在显示器上，bash shell提供了操作符，可以将命令的输出重定向到另一个位置(比如文件)，重定向可以用于输入，也可以用于输出，可以将文件重定向到命令输入。

#### 1.1.5.1. 输出重定向

> &emsp;&emsp;bash shell用">"(大于号)和">>"(双大于号)来完成将命令的输出重定向到一个文件中。

- **注意**
    > - ">"重定向操作符会创建一个新文件(通过默认的"umask"设置权限)，如果文件之前存在，会覆盖原先数据；
    > - ">>"将命令的输出追加到已有文件中；

#### 1.1.5.2. 输入重定向

> &emsp;&emsp;输入重定向将文件的内容重定向到命令，输入重定向符号是"<"(小于号)。

- **注意**
    > - 另一种输入重定向方法称为内联输入重定向(inline input redirection):
    >   - 这种方法无需使用文件进行重定向，只需要在命令行中指定用于输入重定向的数据；
    >   - 内联输入重定向符号是"<<"(双小于号)；
    >   - 必须指定一个**文本标记**来划分输入数据的开始和结尾,任何字符串都可作为文本标记，但在数据的开始和结尾文本标记必须一致；
    >   - 在命令行上使用内联输入重定向时，shell会用"PS2"环境变量中定义的**次提示符**，次提示符会持续提示，以获取更多的输入数据，直到你输入了作为文本标记的那个字符串；
    
- **eg**
    ```
    $ wc << FLG
    > hello world
    > now
    > happy
    > FLG
            3       4       22
    $
    ```

### 1.1.6. 管道

> &emsp;&emsp;管道连接(piping)可以将一个命令的输出直接重定向到另一个命令(却别与输出重定向的到文件)。

- **注意**
    > - 符号由**两个**竖线构成，一个在另一个**上面**。然而管道符号的印刷体通常看起来更像是单个竖线"|"；
    > - 管道串起的两个命令**不是**依次执行，是同时运行，在第一个命令产生输出的同时，输出会被立即送给第二个命令，数据传输不会用到任何中间文件或缓冲区，第一个命令每有一个输出，基本上同时输入第二个命令；
    > - 可以在一条命令中使用任意多条管道,可以持续地将命令的输出通过管道传给其他命令来细化操作；
    > - 也可以搭配使用重定向和管道来将输出保存到文件中；
    ```
    $ ls -l | sort | more
    $
    $ ls -l | sort > log
    ```

### 1.1.7. 执行数学运算

> &emsp;&emsp;shell脚本处理操作数字的过程会比较麻烦，有两种途径来进行数学运算。

#### 1.1.7.1. expr命令

> &emsp;&emsp;"expr"命令允许在命令行处理数学表达式，但是特别笨拙，"expr"命令能够识别少数的数学和字符串操作符。

- **注意**
    > - 对于那些容易被shell错误解释的字符，在它们传入"expr"命令之前需要使用shell的转义字符(反斜线)将其标出来；
    > - 要将一个数学算式的结果赋给一个变量，需要使用命令替换来获取 expr 命令的输出`var3=$(expr $var1 / $var2)`；

#### 1.1.7.2. 使用方括号

- **注意**
    > - 在bash中，在将一个数学运算结果赋给某个变量时，可以用美元符和方括号"$[ operation ]"将数学表达式围起来，适用于shell脚本；
    > - 在使用方括号来计算公式时，不用担心shell会误解乘号或其他符号；
    > - bash shell数学运算符只支持**整数运算**，z shell(zsh)提供了完整的浮点数算术操作；

#### 1.1.7.3. 浮点解决方案

- 克服bash中数学运算的整数限制的几种解决方案：
    - 使用内建的bash计算器(bc)
        - 通过bc命令访问bash计算器；
        - "quit"命令退出,"q"不可以；
        - 浮点运算是由内建变量"scale"控制(控制小数位，默认0)；
        - "bc -q"中的"-q"，命令行选项可以不显示bash计算器冗长的欢迎信息；
        - bash计算器还能支持变量，变量一旦被定义，你就可以在整个bash计算器会话中使用该变量了；
        - print 语句允许你打印变量和数字；
    - 在脚本中使用 bc
        - shell脚本中可以用命令替换运行"bc"命令，并将输出赋给一个变量，格式：`variable=$(echo "options; expression" | bc)`，"options"允许你设置变量(`var1=$(echo "scale=4; 3.44 / 5" | bc)`),如果不止一个变量，可用分号将其分开，"expression"参数定义了通过"bc"执行的数学表达式。
        - "bc"命令能识别输入重定向，允许你将一个文件重定向到"bc"命令来处理.在shell脚本中使用内联输入重定向
            ```
            variable=$(bc << EOF
            options
            statements
            expressions
            EOF
            )
            ```

### 1.1.8. 退出脚本

- **注意**
    > - shell中运行的每个命令都使用退出状态码告诉shell它已经运行完毕；
    > - 退出状态码是一个0～255的整数值，在命令结束运行时由命令传给shell。可以**捕获**这个值并**在脚本中**使用；
    > - 变量"$?"保存上个已执行命令的退出状态码；
    > - 默认情况下，shell脚本会以脚本中的最后一个命令的退出状态码退出；
    > - "exit"命令允许你在脚本结束时指定一个退出状态码，"exit"命令的参数可以是变量，但其值要在0~255范围；

## 1.2. 使用结构化命令

> &emsp;&emsp;程序要求对shell脚本中的命令施加一些逻辑流程控制,有一类命令会根据条件使脚本跳过某些命令。这样的命令通常称为结构化命令(structured command)。

### 1.2.1. if-then和if-then-else

- 几种格式：

    ```
    if command
    then
        commands
    fi
    ```
    ```
    if command; then
        commands
    fi
    ```
    ```
    if command
    then
        commands
    else
        commands
    fi
    ```
    ```
    if command
    then
        commands
    elif
    then
        if command
        then
            commands
        fi
    elif
    then
        commands
    fi
    ```

- **注意**
> - bash shell的if语句会运行if后面的那个命令，如果该命令的退出状态码是0(该命令成功运行)，位于then部分的命令就会被执行；
> - then语句或else语句后可一次执行多条命令；
> - 可以使用嵌套的"if-then"语句；
> - 同一个级的"elif"语句过多的时候可以考虑使用"case"命令；

### 1.2.2. test命令

> &emsp;&emsp;"if-then"语句是**不能**测试命令退出状态码之外的条件。"test"命令提供了在"if-then"语句中测试不同条件的途径,如果"test"命令中列出的条件成立，"test"命令就会退出并返回退出状态码0，如果条件不成立，" test"命令就会退出并返回非零的退出状态码。

- **两种格式**
    ```
    if test condition
    then
        commands
    fi
    ```
    ```
    if [  condition ]
    then
        commands
    fi
    ```
    - 方括号定义了测试条件,第一个方括号之后和第二个方括号之前必须加上一个空格；
    - condition 是 test 命令要测试的一系列参数和值
    - 如果不写 test 命令的 condition 部分，它会以非零的退出状态码退出

- **test使用**
    - 数值比较；
    - 字符串比较；
    - 文件比较；

### 1.2.3. 复合条件

> &emsp;&emsp;"if-then"语句允许你使用布尔逻辑来组合测试。

- ` [ condition1 ] && [ condition2 ]`
- ` [ condition1 ] || [ condition2 ]`

### 1.2.4. "if-then"高级特性

- 用于数学表达式的**双括号**
- 用于高级字符串处理功能的**双方括号**

####  1.2.4.1. 双括号

- 格式：`(( expression ))`，"expression"可以是任意的数学赋值或比较表达式
- "test"命令只能在比较中使用简单的算术操作；
- 双括号命令在比较中使用高级数学表达式，双括号命令提供了更多的数学符号；
- 不需要将双括号中表达式里的大于号转义

#### 1.2.4.2. 双方括号

- 格式：`[[ expression ]]`
- 双方括号里的"expression"使用了"test"命令的标准字符串比较
- 双方括号提供了模式匹配(pattern matching)
- 不是所有的shell都支持双方括号

### 1.2.5. case命令

- **格式**
    ```
        case  variable in
        pattern1 |  pattern2 )  
                commands1 ;;
        pattern3 )  
                commands2 ;;
        *)   
                commands ;;
        esac
    ```
- "case"命令可以替代"elif"语句；
- "case"命令会采用列表格式来检查单个变量的多个值
- 竖线操作符在一行中分隔出多个模式匹配
- 星号会捕获所有与已知模式不匹配的值

## 1.3. 更多结构化命令

### 1.3.1. for命令

- **格式**
    ```
    for var in list
    do
        commands
    done
    ```

- "for"命令创建一个遍历一系列值的循环,每次迭代都使用其中一个值来执行已定义好的一组命令；
- 在"list"参数中需要提供迭代中要用到的一系列值
- "list"可以通过几种不同的方法指定列表中的值
- 在"do"和"done"语句之间命令可以是一条或多条标准的bash shell命令,在这些命令中，"$var"变量包含着这次迭代对应的当前列表项中的值

#### 1.3.1.1. 读取列表中的值

- **格式**
    ```
        for var in Aaaaaa S Ds Fa Gkjksdf Hhhh Jj
        do
            echo The next state is $var
        done
    ```
- 列表中将可能的值一一罗列；
- 每次“for”命令遍历值列表，它都会将列表中的下个值赋给变量
- 最后一次迭代后变量的值会在shell脚本的剩余部分一直保持有效,之后可以修改并使用；

#### 1.3.1.2. 读取列表中的复杂值

-  使用转义字符"\"(反斜线)来将单引号转义
- 使用双引号来定义用到单引号的值
-  for 循环假定每个值都是用空格分割的,在单独的数据值中有空格，就必须用双引号将这些值圈起来

#### 1.3.1.3. 从变量读取列表

- 将一系列值都集中存储在了一个变量中，然后需要遍历变量中的整个列表
- 向变量中存储的已有文本字符串尾部添加文本的一个常用方法  
- <font color=red>哪些情况下使用双引号？</font>
    ```
    #!/bin/sh
    # expole

    var="aaa bbb wejf lkfjj lksjk kjsdf sd"
    var=$var" lllkjkdj"

    for sta in $var
    do 
        echo "ccurent sta: $sta!"
    done
    ```

#### 1.3.1.4. 从命令读取值

- 列表中所需值可以是命令的输出，用**命令替换**来执行任何能产生输出的命令，然后在 for 命令中使用该命令的输出；
- 一行中的空格和回车换行都看作空格处理；

```
#!/bin/sh
# test

#list=$(cat state)

#for var in $list
for var in $(cat state)
do
        echo "var:$var"
done

结果：
# cat state 
A B C
D
E
F
# . ./test.sh 
var:A
var:B
var:C
var:D
var:E
var:F
# 
```

#### 1.3.1.5. 更改字段分隔符

- 环境变量IFS(内部字段分隔符)定义了shell用作字段分隔符的一系列字符；
- 默认IFS包括：空格、制表符、换行符
- <font color=red>为什么IFS=$'\n'要使用$符号</font>，"IFS='\n'"会将\和n作为换行符
```
#!/bin/sh
# test

file="state"

IFS=$'\n'

for var in $(cat file)
do
        echo "var:$var"
done

结果：
# . ./test.sh 
var:A B C
var:D
var:E
var:F
# 
```

- 保留旧值方便统一脚本中再次使用
```
IFS.OLD=$IFS
IFS=$'\n'
# ...
IFS=$IFS.OLD
#...
```
- 可以使用"IFS=:"来解析/etc/passwd文件；

#### 1.3.1.6. 用通配符读取目录

-  for 命令可以自动遍历目录中的文件，进行此操作时，必须在文件名或路径名中使用通配符，它会强制shell使用文件扩展匹配
- 文件扩展匹配是生成匹配指定通配符的文件名或路径名的过程
- 在Linux中，目录名和文件名中包含空格当然是合法的，所以建议加双引号`if [ -d "$file" ]`
- 可以在 for 命令中列出多个目录通配符，将目录查找和列表合并进同一个 for 语句`for file in /home/rich/.b* /home/rich/badtest`

### 1.3.2. C语言风格的for命令

#### 1.3.2.1. C语言的for命令

- **格式**
    ```
    for (( variable assignment ; condition ; iteration process ))

    for (( a = 1; a < 10; a++ ))
    ```

- **注意**
    - 变量赋值可以有空格；
    - 条件中的变量不以美元符开头；
    - 迭代过程的算式未用 expr 命令格式。

#### 1.3.2.2. 使用多个变量

- **注意**
    - 尽管可以使用多个变量，但只能在"for"循环中定义一种条件;

### 1.3.3. while命令

#### 1.3.3.1. 基本格式

```
    while test command
    do
        other commands
    done
```

- **注意**
    - 每次迭代的一开始测试"test"命令,返回**非零**退出状态码时，"while"命令会**停止**执行那组命令；
    - "test command"可以使用任何普通的bash shell命令，或者用"test"命令进行条件测试；
    - 最常见的"test command"的用法是用方括号来检查循环命令中用到的shell变量的值；

#### 1.3.3.2. 使用多个测试命令

- **注意**
    - "while"命令允许你在"while"语句行定义多个测试命令,只有最后一个测试命令的退出状态码会被用来决定什么时候结束循环；
    - 每个测试命令都出现在单独的一行上；
        ```
            $ cat test11
            #!/bin/bash
            # testing a multicommand while loop

            var1=10

            while echo $var1
                [ $var1 -ge 0 ]
            do
                echo "This is inside the loop"
                var1=$[ $var1 - 1 ]
            done
        ```

### 1.3.4. until命令

- **格式**
    ```
    until test commands
    do
        other commands
    done
    ```

- **注意**
    - "until"命令和"while"命令工作方式**相反**，"until"命令要求你指定一个通常返回非零退出状态码的测试命令；
    - 可以在"until"命令语句中放入多个测试命令，只有最后一个命令的退出状态码决定是否执行循环体； 

### 1.3.5. 循环嵌套

- 循环语句可以在循环内使用任意类型的命令，包括其他循环命令；

### 1.3.6. 循环处理文件数据

- **注意**
    - 使用嵌套循环；
    - 修改IFS环境变量(测试示例表面该环境变量的作用域在循环体内，退出后为原先值！)

- **示例**
    ```
        #!/bin/bash
        # changing the IFS value
       
        IFS.OLD=$IFS

        IFS=$'\n'       
        for entry in $(cat /etc/passwd)
        do
            echo "Values in $entry –"

            IFS=:            
            for value in $entry
            do
                echo " $value"
            done
        done
    ```

### 1.3.7. 控制循环

#### 1.3.7.1. break命令

- **注意**
    - 可以用"break"命令来退出任意类型的循环，包括"while"、"until"和"for"循环；
    - `break n`可以跳出多层循环，默认是1；

#### 1.3.7.2. continue命令

- **注意**
    - "continue"命令可以提前中止某次循环中的命令，但并不会完全终止整个循环；
    - 可以在"while"、"until"和"for"循环中使用；
    - `continue n`也可以使用；

### 1.3.8. 处理循环的输出

- 可以通过在"done"命令同行之后添加一个处理命令来实现重定向或管道；

    ```
        for file in /home/burgesskzg/*
        do
            if [ -d "$file" ]
            then
                echo "$file is a directory"
            elif
                echo "$file is a file"
            fi
        done > output.txt
    ```
    ```
        for state in "North Dakota" Connecticut Illinois Alabama Tennessee
        do
            echo "$state is the next place to go"
        done | sort
    ```

## 1.4. 处理用户输入

### 1.4.1. 命令行参数

- 命令行参数允许在运行脚本时向命令行添加数据以向脚本传递参数；
- 脚本会通过特殊的变量(位置参数\$0，\$1-\$9)来处理命令行参数，\$0是程序名，其他是参数；
- 每个参数都必须用空格分开；
- 命令行参数可以是数值，也可以是文本字符串；
- 要在参数值中包含空格，必须要用引号(单引号或双引号均可)，引号并非数据的一部分，它们只是表明数据的起止位置；
- 参数多余9个时，在脚本中需要在变量数字周围加上花括号引用(\${10})；

- "\$0"参数获取shell在命令行启动的脚本名；
- 以"."或"source"运行脚本，命令会和脚本名混在一起出现在"\$0"参数中！
- 以完整的脚本路径运行脚本时，"\$0"会包括整个路径和脚本！
- "basename"命令会返回不包含路径的脚本名；
    ```shell
        name=$(basename $0)
        echo
        echo The script name is: $name
        #
        $ bash /home/Christine/test5b.sh
        The script name is: test5b.sh
        $
        $ ./test5b.sh
        The script name is: test5b.sh
    ```

- 如果脚本中使用参数，但是执行是没有使用参数或缺少参数会导致出错；
- 在使用参数前一定要检查其中是否存在数据(`if [ -n "$1" ]`)；

### 1.4.2. 特殊参数变量

- bash shell中有些特殊变量，它们会记录命令行参数；
- "\$#"：含有脚本运行时携带的命令行参数的个数(不包括"\$0")；
- "\${\!\#}"：获取最后一个参数(不能在花括号内使用美元符，所用用"\!")；
- 当命令行没有参数时，"\$\#"的值为0，但"\${\!\#}"变量会返回命令行用到的脚本名；

- "\$\*"和"\$\@"：可以用来访问所有的参数，都能够在单个变量中存储所有的命令行参数；
- "\$\*"变量会将命令行上提供的所有参数当作一个单词保存，会将这些参数视为一个整体；
- "\$\@"变量会将命令行上提供的所有参数当作同一字符串中的多个独立的单词，可用for命令遍历；
- 使用"\$*"和"\$@"的使用要加双引号！

### 1.4.3. 移动变量

- "shift"命令会根据命令行参数的相对位置来移动命令行参数；
- 默认情况下会将每个参数变量向左移动一个位置("\$0"不会改变，但是"\$1"等参水会被一一删除)；
- 某个参数被移出无法再恢复！
- "shitf n"可以一次移除n个参数；

### 1.4.4. 处理选项

- 选项是跟在单破折线后面的单个字母；

- 处理简单选项 
    - 像处理命令行参数一样处理命令行选项；
    - "case"命令；
- 分离参数和选项(选项在前半部分，参数在后半部分) 
    - 在shell脚本中同时使用选项和参数的情况；
    - 用特殊字符将二者分开，该字符告诉脚本何时选项结束以及普通参数何时开始；
    - 特殊字符是"--"双破折线，shell会用双破折线来表明选项列表结束；
    - 也是通过"case"命令实现；
- 处理带值选项
    - 巧用"shift"命令；
    - 暂时无法处理**合并选项(-anlp)**的能力；

- "getopt"命令
    - 格式：`getopt optstring parameters`
        - "optstring"定义了命令行有效的选项字母，在"optstring"中列出要在脚本中用到的每个命令行选项字母;
        - "optstring"还定义了哪些选项字母需要参数值，需要参数值的选项字母后加一个冒号；
        - 自动将合并参数分开并加破折线；
        - 自动添加双破折线来分割额外参数；
        - 指定不在"optstring"中的选项，默认情况下会产生错误消息，命令后加"-q"选项可忽略；
        - "getopt"命令选项必须出现在"optstring"之前；
    - 能够识别命令行参数
    - 可以接受一系列任意形式的命令行选项和参数，并自动将它们转换成适当的格式；
- 可以在脚本中使用"getopt"来格式化脚本所携带的命令行选项或参数；
    - 需要借助"set"命令；
    - "set"命令能够处理"shell"中的各种变量；
    - "set"命令的选项之一是"--"(双破折线)，它会将脚本的命令行参数替换成"set"命令"--"之后的命令行值；
    - 实现方式`set -- $(getopt -q abc:d "$@")`；
- "getopt"命令不擅长处理带空格和引号的参数值，它会将空格当作参数分隔符,引号当作字符；
    - `getopt ab -a -b "hello world"`会被解析未`-a -b 'hello world'`四个字段；

- "getopts"命令
    - 格式：`getopts optstring variable`
        - 选项字母列在"optstring"中;
        - 选项字母要求有个参数值，就加一个冒号；
        - 要去掉错误消息的话，可以在"optstring"之前加一个冒号；
        - "getopts"命令将当前参数保存在命令行中定义的"variable"中，**不带破折线**；
        - "getopts"命令会用到两个环境变量:
            - 如果选项需要跟一个参数值，"OPTARG"环境变量就会保存这个值；
            - "OPTIND"环境变量保存了参数列表中"getopts"正在处理的参数位置；
    - "getopts"命令内建于bash shell；
    -  "getopt"命令行上选项和参数处理后<font color=red>只生成一个输出</font>，而"getopts"命令能够和已有的shell参数变量配合默契;
    - "getopts"一次只处理命令行上检测到的一个参数，处理完所有的参数后，退出并返回一个大于0的退出状态码；
    - 可以在参数值中包含空格，用引号包裹含空格的参数，引号不被当作字符；
    - 可以将选项字母和参数值放在一起，不用加空格；
    - 将命令行上找到的所有未定义的选项统一输出成问号；
    - 知道何时停止处理选项，并将参数留给用户自己处理，利用`shift $[ $OPTIND - 1 ]`可移动获取参数；
    - 推荐使用方式：`while getopts :ab:c opt`；

### 1.4.5. 将选项标准化

> &emsp;&emsp;有些字母选项在Linux世界里已经拥有了某种程度的标准含义,建议在脚本中用相同含义实现选项。

|选项|描述|
|:---:|:---|
|-a|显示所有对象|
|-c|生成一个计数|
|-d|指定一个目录|
|-e|扩展一个对象|
|-f|指定读入数据的文件|
|-h|显示命令的帮助信息|
|-i|忽略文本大小写|
|-l|产生输出的长格式版本|
|-n|使用非交互模式(批处理)|
|-o|将所有输出重定向到的指定的输出文件|
|-q||
|-r|递归地处理目录和文件|
|-s|以安静模式运行|
|-v|生成详细输出|
|-x|排除某个对象|
|-y|对所有问题回答yes|

### 1.4.6. 获取用户输入

> &emsp;&emsp;命令行选项和参数是从脚本用户处获得输入的一种重要方式，而在脚本运行时想要和用户交互，可以使用"read"命令。

#### 1.4.6.1. 基本读取

- "read"命令从标准输入(键盘)或另一个文件描述符中接收输入；
- 收到输入后"read"命令会将数据放进一个变量；
    ```shell
        echo -n "Enter your name: "
        read name
        echo "Hello $name, welcome to my program. "
    ```
    - "echo"命令的"-n"选项在字符串结尾不输出换行；

- "read"命令包含"-p"选项，允许直接在"read"命令行指定提示符；
    ```shell
        read -p "Please enter your age: " age
        days=$[ $age * 365 ]
        echo "That makes you over $days days old! "
    ```

- "read"命令会将提示符后输入的所有数据分配给单个变量，如果变量数量不够，剩下的数据就全部分配给最后一个变量
    ```shell
        read -p "Enter your name: " first last
        echo "Checking data for $last, $first…"
        $
        $ ./test.sh
        Enter your name: Rich Blum
        Checking data for Blum, Rich...
    ```

- "read"命令行如果不指定变量，会将它收到的任何数据都放进特殊环境变量"REPLY"中；
    ```shell
        read -p "Enter your name: "
        echo
        echo Hello $REPLY, welcome to my program.
        #
        $
        $ ./test24.sh
        Enter your name: Christine
        Hello Christine, welcome to my program.
    ```

#### 1.4.6.2. 超时

- "-t"选项可以指定"read"命令等待输入的秒数；
- 当计时器过期还未输入数据，"read"命令会返回一个非零退出状态码(可以使用如"if-then"或"while"判断)；
    ```shell
        if read -t 5 -p "Please enter your name: " name
        then
            echo "Hello $name, welcome to my script"
        else
            echo
            echo "Sorry, too slow! "
        fi
    ```
    - "if"命令判断指令的退出码为0时为真！

- "-n"选项让"read"命令统计输入的字符数,当输入的字符达到预设的字符数时自动退出，将输入的数据赋给变量(无需按回车键)；
    ```shell
        read -n1 -p "Do you want to continue [Y/N]? " answer
        
        case $answer in
        Y | y)  echo
                echo "fine, continue on…";;

        N | n)  echo
                echo OK, goodbye
                exit;;
        esac
        echo "This is the end of the script"
    ```

#### 1.4.6.3. 隐藏方式读取

> &emsp;&emsp;敏感数据需要在输入回显时隐藏，如密码等。

- "-s"选项可以避免"read"命令中输入的数据出现在显示器上(实际上，数据会被显示，只是"read"命令会将文本颜色设成跟背景色一样)；
    ```shell
        read -s -p "Enter your password: " pass
        echo
        echo "Is your password really $pass? "
        $
        $ ./test.sh
        Enter your password:
        Is your password really T3st1ng?
        $
    ```
    - 输入提示符输入的数据不会出现在屏幕上，但会赋给变量，以便在脚本中使用。

#### 1.4.6.4. 从文件中读取

- "read"命令可以读取Linux系统上文件里保存的数据；
- 每次读取一行；
- 当文件中再没有内容时，"read"命令会退出并返回非零退出状态码；
    ```shell
        #!/bin/bash

        count=1
        cat test | while read line
        do
            echo "Line $count: $line"
            count=$[ $count + 1]
        done
        echo "Finished processing the file"
        $
        $ cat test
        The quick brown dog jumps over the lazy fox.
        This is a test, this is only a test.
        O Romeo, Romeo! Wherefore art thou Romeo?
        $
        $ ./test.sh
        Line 1: The quick brown dog jumps over the lazy fox.
        Line 2: This is a test, this is only a test.
        Line 3: O Romeo, Romeo! Wherefore art thou Romeo?
        Finished processing the file
        $
    ```
    - "read"命令不能直接读出文件中的内容，需要借助"cat"命令；

## 1.5. 呈现数据

> &emsp;&emsp;目前介绍的脚本都是通过将数据打印在屏幕上或将数据重定向到文件中来显示信息，接下来介绍如何将脚本的输出重定向到Linux系统的不同位置。

### 1.5.1. 理解输入和输出

> &emsp;&emsp;已知显示脚本输出方法："在显示器上显示输出"或者"将输出重定向到文件中"；
> &emsp;&emsp;这两种方法要么将数据输出全部显示，要么什么都不显示。有时需要将一部分数据在显示器上显示，另一部分数据保存到文件中，针对这种需求，需要了解Linux如何处理输入输出，如何用标准的Linux输入和输出系统来将脚本输出导向特定位置。

#### 1.5.1.1. 标准文件描述符

- Linux系统将每个对象当作文件处理，包括输入和输出进程；
- Linux用文件描述符来标识每个文件对象；
- 文件描述符是一个非负整数；
- 唯一标识会话中打开的文件；
- 每个进程一次最多可以有九个文件描述符；
- bash shell保留了前三个文件描述符(0-STDIN、1-STDOUT和2-STDERR)
- STDIN
    - 代表shell的标准输入;
    - 对终端界面来说，标准输入是键盘
    - shell从STDIN文件描述符对应的键盘获得输入，在用户输入时处理每个字符
    - 输入重定向符号会用重定向指定的文件来替换标准输入文件描述符
    - 当在命令行上只输入cat命令时，它会从STDIN接受输入
- STDOUT
    - STDOUT文件描述符代表shell的标准输出
    - 在终端界面上，标准输出就是终端显示器
    - 默认情况下，大多数bash命令会将输出导向STDOUT文件描述符
    - 可以使用输出重定向">"或输出追加">>"来改变输出
    - shell对于错误消息的处理是跟普通输出分开的
- STDERR
    - shell通过特殊的 STDERR 文件描述符来处理错误消息
    - STDERR文件描述符代表shell的标准错误输出
    - shell或shell中运行的程序和脚本出错时生成的错误消息发送到这个描述符
    - 默认情况下，STDERR文件描述符会和STDOUT文件描述符指向同样的地方，也会输出到显示器
    -  STDERR 并不会随着 STDOUT 的重定向而发生改变

#### 1.5.1.2. 重定向错误

- 只重定向错误
    - `ls -al badfile 2> test`
    - 2代表STDERR，将该文件描述符值放在重定向符号前
    - 该值必须紧紧地放在重定向符号前，不能有空格

- 重定向错误和数据
    - `ls -al test badtest 1>succOut 2>failOut`：将普通输出和错误分别定向到不同文件；
    - `ls -al test badtest &> Out`
        - 将普通输出和错误输出重定向到同一文件；
        - 为了避免错误信息散落在输出文件中，相较于标准输出，bashshell自动赋予了错误消息更高的优先级；<font color=red>需要按顺序输出怎么处理?</font>
    - <font color=red>怎么理解`./myproject > /dev/null 2>&1 &`？</font>

### 1.5.2. 在脚本中重定向输出

#### 1.5.2.1. 临时重定向

- `echo "this is an error message !" >&2`
    - 在脚本中单独将一行的输出定向到STDERR：
    - 在重定向到文件描述符时，必须在文件描述符数字之前加一个"&"
    - "... >&1"：将"..."重定向到1；
    - "... >&2"：将"..."重定向到2；

#### 1.5.2.1. 永久重定向

- `exec 1>SuccessOut`和`exec 2>FaileOut`
    - 在脚本中添加该语句，会启动一个新shell并将STDOUT文件描述符重定向到文件
    - 该指令不必放到脚本开始位置，可以在脚本执行过程中重定向；

### 1.5.3. 在脚本中重定向输入

- `exec 0< testfile`
    - "exec"命令将STDIN重定向到Linux系统上的文件中
    - 这个重定向只要在脚本需要输入时就会作用！

### 1.5.4. 创建自己的重定向

> &emsp;&emsp;shell中最多可以有9个打开的文件描述符，其他6个文件描述符均可用作输入或输出重定向。可以将这些文件描述符分配给文件，然后在脚本中使用。

#### 1.5.4.1. 创建输出文件描述符

```SHELL
    exec 3>TestOut

    echo "out to 3" >&3
```
- 用exec命令将文件描述符3重定向到另一个文件；
- 重定向到文件描述符3的echo语句的输出被输出到指定文件；
- 该方法可以将特定信息重定向到文件中(比如日志文件)
- `exec 3>>TestOut`：不创建新文件，使用追加方式；

#### 1.5.4.2. 重定向文件描述符

```SHELL
    exec 5>&1       #1
    exec 1>TestOut  #2
    
    #...    
    exec 1>&5       #3
```
- #1：将文件描述符5重定向到1，5和1都指向显示器；
- #2：将文件描述符1重定向到文件，5指向显示器，1指向TestOut；
- #3：将文件描述符1重定向到5，5和1都指向显示器；
- 这是一种在脚本中临时重定向输出，然后恢复默认输出设置的常用方法。

#### 1.5.4.3. 创建输入文件描述符

```SHELL
    exec 5<&0           #1
    exec 0< testFile    #2

    while read line     #3
    #...

    exec 0<&5           #4
```
- #1：用文件描述符5保存STDIN的位置
- #2：将STDIN重定向到一个文件
- #3："read"命令的所有输入都来自重定向后的STDIN(输入文件)
- #4：将STDIN重定向到文件描述符5，恢复到原先位置；

#### 1.5.4.4. 创建读写文件描述符

```SHELL
    exec 3<> testFile               #1

    read line <&3                   #2

    echo "This is a test line" >&3  #3
```
- #1：单个文件描述符作为输入和输出，用一个文件描述符对同一个文件进行读写；
- #2："read"命令从该文件描述符对应的文件读一行；
- #3："echo"命令向该文件描述符对应的文件写一行；
- 注意，shell在一个文件内部只维护一个读写内部指针，任何读或写都会从文件指针上次的位置开始。

#### 1.5.4.5. 关闭文件描述符

```shell
    exec 3>&-
```
- 将打开的文件描述符重定向到特殊符号"&-"可以关闭该文件描述符；
- 在脚本退出时会自动关闭创建的文件描述符；
- 关闭了文件描述符就不能向它写入数据，否则shell会生成错误消息；
- 打开已经关闭的输出文件，shell会用新内部指针来操作该文件；

### 1.5.5. 列出打开文件描述符的信息

```SHELL
    exec 3> testFile03
    exec 5< testFile05
    exec 7> testFile07

    /usr/sbin/lsof -a -p $$ -d0,1,2,3,5,7
```
- "lsof"命令会列出整个Linux系统打开的所有文件描述符
- 该命令有争议，因为它会向非系统管理员用户提供Linux系统的信息，许多Linux系统隐藏了该命令；
- 该命令会产生大量的输出，
- "-p"选项指定进程ID；
- "-d"选项指定要显示的文件描述符；
- "-a"选项可以对其他两个选项的执行结果执行布尔AND运算；
- "$$"可以获取进程的当前PID；
-  lsof 的默认输出中有7列信息
    |列|描述|
    |:-:|:-|
    |COMMAND|正在运行的命令名的前9个字符|
    |PID|进程的PID|
    |USER|进程属主的登录名|
    |FD|文件描述符号以及访问类型(r-读,w-写,u-读写)|
    |TYPE|文件的类型(CHR-字符型,BLK-块型,DIR-目录,REG-常规文件)|
    |DEVICE|设备的设备号(主设备号和从设备号)|
    |SIZE|如果有的话，表示文件的大小|
    |NODE|本地文件的节点号|
    |NAME|文件名|

### 1.5.6. 阻止命令输出

- /dev/null文件里什么都没有
- shell输出到null文件的任何数据都不会保存，全部都被丢掉
- 重定向到该位置的任何数据都会被丢掉，不会显示
- 可以在输入重定向中将/dev/null作为输入文件，来快速清除现有文件中的数据，而不用先删除文件再重新创建；
- `cat /dev/null > testFile`：清除日志文件的一个常用方法；

### 1.5.7. 创建临时文件

> &emsp;&emsp;Linux系统有特殊目录"/tmp"，专供临时文件使用。多数Linux发行版在启动时自动删除"/tmp"目录的所有文件。
> &emsp;&emsp;系统上的任何用户账户都有权限读写"/tmp"目录中的文件。这个特性提供了一种创建临时文件的简单方法，而且还不用操心清理工作。
> &emsp;&emsp;"mktemp"命令可以在"/tmp"目录下创建唯一的临时文件，该文件不受"umask"影响，有读写权限且自动设置创建者为属主，且除了root用户其他用户不可访问。

#### 1.5.7.1. 创建本地临时文件

- 格式：`mktemp testTmpFileXXXXXX`
    - 默认情况下，"mktemp"会在本地目录中创建一个文件；
    - 文件名末尾加上6个"X"，"mktemp"命令会用6个字符码替换这6个"X"，从而保证文件名在目录中是唯一的；
    - 遇到过"X"必须是大写才能创建的情况；
- 在脚本中使用"mktemp"命令创建临时文件，可能要将文件名保存到变量中，方便后面的脚本引用；
- 只有创建在"/tmp"的文件可以被自动删除；

#### 1.5.7.2. 在"/tmp"目录创建临时文件

- "mktemp"的"-t"选项会强制该命令在临时目录来创建文件，且返回带全路径和文件名的字符串；
- 全路径名可以在Linux系统上的任何目录下引用文件；
- "-p"选项可以指定在其他目录创建临时文件，但是不能被自动删除！

#### 1.5.7.3. 创建临时目录

```SHELL
    tempdir=$(mktemp -d dir.XXXXXX)
    cd $tempdir
    tempfile1=$(mktemp temp.XXXXXX)
    tempfile2=$(mktemp temp.XXXXXX)
    exec 7> $tempfile1
    exec 8> $tempfile2
```
- "-d"选项创建一个目录，不是文件；
- 可以在"-d"选项创建的目录中继续创建临时文件；

### 1.5.8 记录消息

```SHELL
    tempfile=testfile.log

    echo "start of the test" | tee $tempfile
    echo "second line of the test" | tee -a $tempfile
    echo "end of the test" | tee -a $tempfile
```
- 格式：`tee filename`
- 举例：
    - `date | tee testFile`
    - `who | tee testFile`
- tee命令将输出同时发送到显示器和日志文件；
- 默认情况下，"tee"命令会在每次使用时覆盖输出文件内容
- "-a"选项可以将数据追加到文件中，而不是覆盖；

## 1.6. 控制脚本

> &emsp;&emsp;目前学习到的运行脚本的方式是以实时模式在命令行直接运行，还有其他方式。另外还有一些选项可以控制脚本。

### 1.6.1. 处理信号

> &emsp;&emsp;脚本中可以使用信号。

#### 1.6.1.1. 重温信号

- Linux系统和应用程序可以生成30多个信号；
- 默认情况下，bash shell会忽略收到的任何SIGQUIT(3)和SIGTERM(5)信号；
- 默认情况下，bash shell会处理收到的SIGHUP(1)和SIGINT(2)信号；
- bash shell收到SIGHUP(比如离开shell)就会退出，但在退出之前，它会将SIGHUP信号传给所有由该shell启动的进程(包括正在运行的shell脚本)；
- bash shell收到SIGINT，内核停止为shell分配CPU处理时间，shell会将SIGINT信号传给所有由它所启动的进程；
- shell会将信号传给脚本，而shell脚本的默认行为是忽略这些信号；

#### 1.6.1.2. 生成信号

- `ctrl+c`：SIGINT(停止shell中当前运行的进程)

<br>

- `ctrl+z`：SIGTSTP(停止或暂停进程，但不终止进程)
- `[1]+ Stopped sleep 100`，方括号中的数字是shell分配的作业号(job number)；
- "ps"命令将已停止作业的状态为显示为"T"，此时命令要么被跟踪，要么被停止；
- 停止(stopping)和终止(terminating)不同；
- 有停止进程的shell可以输入两次exit退出，或用kill先杀死进程在退出；
- kill命令会在做了能够产生shell提示符的操作(比如按回车键)时输出上次kill命令的消息；

<br>

- `ctrl+d`：EOF(非信号)，shell中表示退出

<br>

- `ctrl+\`：SIGQUIT(停止进程)

#### 1.6.1.3. 捕获信号

- "trap"命令指定shell脚本要监看并从shell中拦截信号；
- 脚本收到"trap"命令中列出的信号，该信号不再由shell处理。<font color=red>所有的信号都是这样处理吗？</font>；
- 格式：`trap commands signals`
- 使用"trap"命令忽略"SIGINT"信号：`trap "echo ' Sorry! I have trapped Ctrl-C'" SIGINT`；

#### 1.6.1.4. 捕获脚本退出

- 可以在shell脚本退出时进行捕获(这是在shell完成任务时执行命令的一种简便方法)；
- 在"trap"命令后加上"EXIT"信号来捕获脚本的退出；
- 格式：`trap "echo Goodbye..." EXIT`
- 正常退出和提前退出(ctrl+c等)都能捕获EXIT信号；

#### 1.6.1.5. 修改或删除捕获

- **修改**
    - 要实现在脚本不同位置进行不同的捕获处理，只需重新使用带有新选项的"trap"命令；
    ```SHELL
        trap "echo ' Sorry... Ctrl-C is trapped.'" SIGINT
        #...
        trap "echo ' I modified the trap!'" SIGINT
    ```

- **删除**
    - 在"trap"命令与信号之间加上两个破折号来删除已设置好的捕获(单破折号也可以)
    ```SHELL
        trap "echo ' Sorry... Ctrl-C is trapped.'" SIGINT
        # ...
        trap -- SIGINT
    ```
    - 移除信号捕获后，脚本按照默认行为来处理信号；

### 1.6.2. 以后台模式运行脚本

> &emsp;&emsp;后台运行的进程运行时，不会和终端会话上的STDIN、STDOUT和STDERR关联，脚本也可以后台执行。

#### 1.6.2.1. 后台运行脚本

- 脚本/命令后加"&"符实现后台运行；
- 当"&"符放到脚本/命令后，脚本/命令和bash shell分离开来，脚本/命令作为系统中一个独立的后台进程；
- shell会给后台运行的脚本/命令分配作业号，运行命令时会方括号提示，同时提示进程ID；
- 是shell分配作业号，linux系统分配进程ID；
- 后台进程结束会提示"Done"
- 当后台进程运行时，它仍然会使用终端显示器来显示STDOUT和STDERR消息，STDOUT和STDERR需要重定向；

#### 1.6.2.2. 运行多个后台作业

- 可以在命令行提示符下同时启动多个后台作业；
- 在终端会话中使用后台进程时要注意，每个后台进程都和终端会话(pts/0)联系(ps命令可查看),如果终端会话退出，那么后台进程也会随之退出。

### 1.6.3. 在非控制台下运行脚本

- 在终端会话启动脚本后台执行，还不想终端会话退出时脚本被终止，可使用nohup命令；
- nohup命令运行另外一个命令来阻断所有发送给该进程的SIGHUP信号；
- 格式：`nohup ./test.sh &`
- nohup命令会解除终端与进程的关联，包括STDOUT和STDERR，并生成一个nohup.out的文件保存输出信息；
- 用nohup运行同一目录下的多个命令，所有命令都会追加到同一个nohup中！
- 用`nohup ./test.sh > LogFile 2>&1 & `可以将输出信息保存到指定文件；
- <font color=red>在/etc/init.d/目录下的脚本中nohup还能使用吗？</font>
- <font color=red>nohup.out会存放在当前目录吗？</font>
- <font color=red>nohup命令加在要执行命令前，且不使用&后台执行，会怎么样？</font> 
    - 测试发现，关闭终端，这个以nohup执行的非后台进程还在。

### 1.6.4. 作业控制

> &emsp;&emsp;启动、停止、终止以及恢复作业的这些功能统称为作业控制。

#### 1.6.4.1. 查看作业

- "jobs"命令允许查看shell当前正在处理的作业；
- 被"ctrl+z"暂停的任务和用&后台执行的任务可以用jobs查看；
- "-l"选项可以让jobs命令同时显示PID；
- jobs命令显示的作业带加减号，带加号的作业会被当做默认作业，作业控制命令不指定作业号时默认操作该作业；
- 当前的默认作业完成处理后，带减号的作业成为下一个默认作业；
- 任何时候都只有一个带加号的作业和一个带减号的作业；
- 默认进程一般是最后启动的那个作业；

#### 1.6.4.2. 重启停止的作业

- 可以将已停止的作业作为后台进程或前台进程重启，前台进程会接管当前工作的终端；

- "ctrl+z"：停止脚本(暂停)；
- "bg"命令加作业号：以后台模式重启一个作业(可以不加作业号，直接操作默认作业)；
- "fg"命令加作业号：以前台模式重启一个作业(可以不加作业号，直接操作默认作业)；

### 1.6.5. 调整谦让度

> &emsp;&emsp;- 多任务操作系统中，内核负责将CPU时间分配给系统上运行的每个进程。
> &emsp;&emsp;- 调度优先级(scheduling priority)是内核分配给进程的CPU时间(相对于其他进程)。
> &emsp;&emsp;- Linux系统中，由shell启动的所有进程的调度优先级默认都是相同的。
> &emsp;&emsp;- 调度优先级是整数值，从-20(最高优先级)到+19(最低优先级)。默认bash shell以优先级0来启动所有进程。
> &emsp;&emsp;- 调度优先级可以修改。

#### 1.6.5.1. "nice"命令

- "nice"命令：设置命令启动时的调度优先级；
- 格式：`nice -n 5 cmd &`
- 格式：`nice -5 cmd &`，可以不使用"n",直接输入谦让度；
- `ps -p PID -o pid,ni,cmd`，中的"ni"参数就是用来查看谦让度的；
- "nice"命令阻止普通系统用户来提高命令的优先级，强制执行，谦让度不会改变，任务执行了；
- "nice"命令只能把谦让度调低，不能调到负值<font color=red>正值范围来回调可以吗？</font>；

#### 1.6.5.2. "renice"命令

- "renice"命令：改变系统上已运行命令的优先级；
- 格式：`renice -n 5 -p PID`；
- 只能对属于你的进程执行"renice"；
- 只能通过"renice"降低进程的优先级；
-  root用户可以通过"renice"来任意调整进程的优先级(root用户或sudo命令)；
- 测试发现，renice命令只允许把谦让度调低，就算是正值范围内，每次调整都要比原先值低才能成功；

### 1.6.6. 定时运行作业

> &emsp;&emsp;Linux系统提供了多个在预设时间运行脚本的方法："at"命令和"cron"表。每个方法使用不同的技术来安排脚本的运行时间和频率。

#### 1.6.6.1. 用"at"命令来计划执行作业

- "at"命令可以指定Linux系统何时运行脚本；
- "at"命令会将作业提交到作业队列中，指定shell何时运行；
- "at"的守护进程"atd"会以后台模式运行，检查作业队列来运行作业；
- "atd"守护进程会检查系统上的一个特殊目录(通常是/var/spool/at)来获取用"at"命令提交的作业；
- 默认情况下，"atd"守护进程会每60秒检查一次特殊目录,同时"atd"守护进程会检查作业设置运行的时间，并决定是否运行该作业；
- 针对不同优先级，存在26种不同的作业队列，作业队列通常用小写字母a~z和大写字母A~Z来指代。
- 作业队列的字母排序越高，作业运行的优先级就越低；
- 默认情况下，"at"的作业会被提交到"a"作业队列,如果想以更高优先级运行作业，可以用"-q"参数指定不同的队列字母；
- "/usr/bin/batch"会调用"at"命令并将作业提交到"b"队列中；
---
- 格式：`at [-f filename] time`；
- 默认情况，"at"命令会将"STDIN"的输入放到队列,可用"-f"参数来指定用于读取命令(脚本文件)的文件名；
- "time"参数指定系统何时运行该作业，如果指定时间已错过，"at"命令会在第二天的那个时间运行指定的作业；
- "at"命令能识别多种不同的时间格式和日期格式:
    - 标准的小时和分钟格式(10:15)；
    - AM/PM指示符(10:15 PM)；
    - 特定可命名时间(now、noon、midnight、teatime(4 PM))；
    - 标准日期格式(MMDDYY、MM/DD/YY、DD.MM.YY)；
    - 文本日期(Jul 4、Dec 25等，可加年份)；
    - 时间增量(当前时间+25 min、明天10:15 PM、10:15+7天等)；
---
- 获取作业的输出：
> &emsp;&emsp;当作业在Linux系统上运行时，显示器并不会关联到该作业。取而代之的是，Linux系统会将提交该作业的用户的电子邮件地址作为STDOUT和STDERR。任何发到STDOUT或STDERR的输出都会通过邮件系统发送给该用户。

- "at"命令会显示分配给作业的作业号以及为作业安排的运行时间；
- 使用"e-mail"作为"at"命令的输出不便时，可在脚本中对STDOUT和STDERR进行重定向；
- "-M"选项可以屏蔽作业产生的输出信息；
---
- "atq"命令可以查看系统中有哪些作业在等待；
---
- "atrm num"命令可以删除自己设置的作业号，不能删除其他人设置的；

#### 1.6.6.2. 安排需要定期执行的脚本

- Linux系统使用"cron"程序来安排要定期执行的作业；
- "cron"程序会在后台运行并检查一个特殊的表(cron时间表)，以获知已安排执行的作业;
---
**时间表**
- 格式：`min hour dayofmonth month dayofweek command`；
- 时间表允许用特定值、取值范围(1~5)、通配符(星号)来指定条目；
- `15 10 * * * command`：指定在每天的10:15运行一个命令；
- `15 16 * * 1 command`：指定在每周一4:15 PM运行一个命令；
- dayofweek：可用：文本值(mon、tue、wed、thu、fri、sat、sun)、数值(0-周日,6-周六)表示；
- `00 12 1 * * command`：指定在每个月的第一天中午12点执行一个命令；
- " 00 12 * * * if [ \` date +%d -d tomorrow \` = 01 ] ; then ; command "：在每天中午12点来检查是不是当月的最后一天，如果是，cron将会运行该命令；
- 命令列表必须指定要运行的命令或脚本的全路径名，可以添加命令行参数和重定向符号；
- "cron"程序会用提交作业的用户账户运行该脚本,需要注意权限问题；

---
**构建cron时间表**
- 每个用户(root或普通用户)可以用自己的cron时间表来运行安排好的任务；
- "crontab"命令可以处理cron时间表；
- 默认情况下，用户的cron时间表文件并不存在；
- "crontab -l"：列出已有的cron时间表；
- "crontab -e"：为cron时间表添加条目；
- 添加条目时，"crontab"命令会启用一个文本编辑器，使用已有的cron时间表作为文件内容，如果时间表不存在，则是空内容；

---
**浏览cron目录**
- 系统提供了**预配置**的四个cron脚本目录：/etc/cron.hourly、/etc/cron.daily、/etc/cron.monthly和/etc/cron.weekly，但这几个目录执行时间不是很精确；
- 例如，将脚本放到daily目录，cron就会每天执行它；

---
**anacron程序**

- cron程序假定Linux系统是7×24小时运行的；
- 如果在cron时间表中某个作业时间已到，但系统处于关机状态，那这个作业不被运行，当系统开机时，cron程序不会再去运行错过的作业。要解决这个问题，可以用anacron程序；
- 当系统开机时，anacron程序会对错过了执行时间的作业检测，并尽快运行该作业；
- anacron程序常用于进行常规日志维护的脚本；
- anacron程序只会处理位于cron目录的程序(/etc/cron.hourly等)；
- anacron程序用时间戳来决定作业是否在正确的计划间隔内运行了，每个cron目录都有个时间戳文件，位于/var/spool/anacron(比如/var/spool/anacron/cron.monthly)；
- anacron程序使用自己的时间表(通常位于/etc/anacrontab)来检查作业目录；
- anacron时间表的基本格式：`period delay identifier command`；
    - period条目定义了作业多久运行一次，以天为单位，anacron程序用此条目来检查作业的时间戳文件；
    - delay条目会指定系统启动后anacron程序需要等待多少分钟再开始运行错过的脚本；
    - command条目包含了run-parts程序和一个cron脚本目录名。run-parts程序负责运行目录中传给它的任何脚本；
    - identifier条目是一种特别的非空字符串，如 cron-weekly 。它用于唯一标识日志消息和错误邮件中的作业；
- anacron不会运行位于/etc/cron.hourly的脚本，因为anacron程序不会处理执行时间需求小于一天的脚本；

#### 1.6.6.3. 使用新"shell"启动脚本

- bash shell启动时，依照下列顺序找到的第一个文件会被运行，其余的文件会被忽略(不是所有的发行版中都包含这些启动文件)：
    - $HOME/.bash_profile
    - $HOME/.bash_login
    - $HOME/.profile
- 应该将需要在登录时运行的脚本放在上面第一个文件中；
- 每次启动一个新shell时，bash shell都会运行.bashrc文件；
- .bashrc文件通常也是通过某个bash启动文件来运行的。因此.bashrc文件会运行两次：一次是当你登入bash shell时，另一次是当你启动一个bash shell时；
- 如果你需要一个脚本在两个时刻都得以运行，可以把这个脚本放进该.bashrc文件中；
- <font color=red>怎样每次运行脚本的时候都启动一个新的bash shell？</font>;

# 2. 高级shell脚本编程

## 2.1. 创建函数

> &emsp;&emsp;bash shell提供了用户自定义函数功能，可以将shell脚本代码放进函数中封装起来，方便在脚本中的任何地方多次使用它。

### 2.1.1. 基本的脚本函数

---

- 函数是一个脚本代码块，需要命名；
- 要在脚本中使用该代码块时，只要使用所起的函数名(函数调用函数)；

---

**创建函数**
- 格式一
    ```SHELL
        function  name {
            commands
            }
    ```
- 格式二
    ```SHELL
        name () {
        commands
        }
    ```
- 第一种格式采用关键字"function"，后跟分配给该代码块的函数名；
- "name"属性定义赋予函数的名称，脚本中定义的每个函数都必须有一个唯一的名称；
- "commands"是构成函数的一条或多条bash shell命令，调用函数时，bash shell会按命令在函数中出现的顺序依次执行；

---

**使用函数**
- 像其他shell命令一样，在脚本某行中指定函数名，即可调用函数；
- 脚本中，函数可以定义在任意位置，但必须要在调用位置前；
- 如果重定义了函数，新定义会覆盖原来函数的定义，新定义之后的调用会执行新定义内容，且不会产生错误消息；

### 2.1.2. 返回值

---

**默认退出状态码**
- 默认，函数的退出码是函数中最后一条命令返回的退出码；
- 可以用标准变量"$?"获取函数的退出状态码；
- 不推荐使用默认退出状态码，因为无法知道函数中除最后一条命令外其他命令中是否成功运行；

---

**使用return命令**
- bash shell可以使用"return"命令来退出函数并返回特定的退出状态码；
- "return"命令允许指定一个整数值来定义函数的退出状态码；
- 注意：函数一结束就取返回值(如果在用"$?"变量提取函数返回值之前执行了其他命令，函数的返回值就会丢失)；
- 注意：退出状态码必须是0~255；

---

**使用函数输出**
- 可以将函数的输出保存到SHELL变量中，可以获取任何类型的函数输出；
- 格式：`result=$(func)`；
- 这种方式是获取函数的输出，而不是查看退出状态码；
- 可以返回正数、浮点、字符串；

### 2.1.3. 在函数中使用变量

> &emsp;&emsp;在函数中使用变量时，你需要注意它们的定义方式以及处理方式。

---

**向函数传递参数**
- bash shell会将函数当作小型脚本来对待；
- 可以像普通脚本那样向函数传递参数；
- 函数可以使用标准的参数环境变量来表示函数命令行上传给函数的参数($0、$1、$2...、$#等)；
- 格式：`result=$(func 10 5)`；
- 格式：`result=$(func $value 5)`；
- 由于函数使用特殊参数环境变量作为自己的参数值，因此它无法直接获取脚本在命令行中的参数值，要在函数中使用这些值，必须在调用函数时手动将它们传过去；

---

**在函数中处理变量**
- 函数中定义的变量与普通变量的作用域不同；
- 对脚本的其他部分而言，它们是隐藏的；
- 函数使用两种类型的变量：全局变量和局部变量；

<br>

- 全局变量在shell脚本中任何地方都有效;
- 在脚本的主体部分定义一个全局变量，可以在函数内读取它的值；
- 在函数内定义了一个全局变量，可以在脚本的主体部分读取它的值；<font color=red>函数内怎么定义一个全局变量？</font>
- 默认，在脚本中定义的任何变量都是全局变量，在函数外定义的变量可在函数内正常访问；

<br>

- 在变量声明的前面加上"local"关键字,函数内部使用的任何变量都可以被声明成局部变量；
- 格式：`local tmep`；
- 也可以在变量赋值语句中使用local关键字
- 格式：`local temp=$[ $value + 5 ]`；
- "local"关键字保证变量只局限在该函数中,如果脚本中在该函数之外有同样名字的变量，shell将会保持这两个变量的值是分离的；

### 2.1.4. 数组变量和函数

---

**向函数传递数组参数**
- `${array[*]}`：访问整个数组的元素；
- 方法一：
    ```SHELL
        function add {
            local newarray
            #newarray=$@
            #newarray=($(echo "$@"))
            #newarray=$(echo "$@")
            newarray=$(echo $@)
        }

        array=(1 2 3)

        add ${array[*]}
    ```
- 方法二：
    ```SHELL
        function add {
            local newarray
            #newarray=$@
            newarray=($(echo "$@"))
        }

        array=(1 2 3)
        arg=$(echo ${array[*]})

        add $arg
    ```

---

**从函数返回数组**
- 函数用"echo"语句来按正确顺序输出单个数组值，然后脚本再将它们重新放进一个新的数组变量中；

### 2.1.5. 函数递归

- 局部函数变量的一个特性是自成体系，函数可以调用自己来得到结果(递归调用)；
- 通常递归函数都有一个最终可以迭代到的基准值；
- 阶乘递归：
    ```SHELL
        function factorial {
            if [ $1 -eq 1 ]
            then
                echo 1
            else
                local temp=$[ $1 - 1 ]
                local result='factorial $temp'
                echo $[ $result * $1 ]
            fi
        }

        read -p "Enter value: " value
        result=$(factorial $value)
        echo "The factorial of $value is: $result"
    ```

### 2.1.6. 创建库

- bash shell允许创建函数库文件，然后在多个脚本中引用该库文件;
- 步骤：
    - 1.创建一个包含脚本中所需函数的公用库文件；
    - 2.在用到这些函数的脚本文件中包含刚创建的公用库文件；
    - 3.然后调用
- 需要注意，需要在脚本中用`source 库路径名`或`. 库路径名`来引用库，否则由于调用库时会创建子shell，导致作用域不同无法调用；

### 2.1.7. 在命令行上使用函数

> &emsp;&emsp;<font color=red>一旦在shell中定义了函数，就可以在整个系统中使用它，无需关心脚本是不是在PATH环境变量里。</font>

---

**在命令行上创建函数**

- 单行方式(在每个命令后面加个分号)
    ```SHELL
        $ function doubleit { read -p "Enter value: " value; echo $[ $value * 2 ]; }
        $
        $ doubleit
        Enter value: 20
        40
    ```
- 多行方式(定义时,shell会用"次提示符"提示输入更多命令)
- 不用在每条命令的末尾放一个分号，只要按下回车键;
- 在函数尾部使用花括号通知shell函数定义结束；
- 在命令行上创建函数时，函数名不可与内建命令或其他命令相同，否则，函数将会覆盖原来的命令；
    ```SHELL
        $ function multem {
        > echo $[ $1 * $2 ]
        > }
        $ multem 2 5
        10
    ```

---

**在.bashrc文件中定义函数**
- 在命令行上直接定义函数，shell退出，函数消失，可将函数定义到".bashrc"文件中；
- 不管时交互式shell，还是现有shell中启动新shell，新shell都会查找主目录下的".bashrc"文件；
- 可以在".bashrc"文件的末尾直接定义函数；
- 可在".bashrc"文件中通过"source 库路径名"(source可以点代替)将库文件中的函数添加到该文件中，便可在shell命令行使用这些函数了，shell还会将定义好的函数传给子shell进程；

### 2.1.8. 实例

- 下载：
    ```
        // 从地址ftp://ftp.gnu.org/gnu/shtool/shtool-2.0.8.tar.gz下载包

        // 解压 tar -zxvf shtool-2.0.8.tar.gz
    ```
- 构建库
    ```
        // ./configure (检查构建shtool库文件所必需的软件,使用工具路径修改配置文件)

        // make (构建shtool库文件)

        // make tess （测试库文件）
    ```
- shtool库使用格式
    `shtool [options] [function [options] [args]]`
- 使用
    ```SHELL
        $ cat test.sh
        #!/bin/bash
        shtool platform
        $ ./test16
        Ubuntu 14.04 (iX86)
    ```

## 2.2. 图形化桌面环境中的脚本编程

### 2.2.1. 创建文本菜单

---

**创建菜单布局**
- 1.先清空显示器，"clear"命令用当前终端会话的terminfo数据清理出现在屏幕上的文本；
- 2.用"echo"命令来显示菜单元素，"-e"选项可以使"echo"命令显示**非**可打印字符，"-n"选项可以去掉行尾的换行符；
- 3.通过"read"命令获取用户输入，"-n"选项可以限定读取几个字符(这种情况下字符够了，不用按回车键)；
    ```SHELL
        #! /bin/sh

        clear
        echo
        echo -e "\t\t\tSys Admiin Menu\n"
        echo -e "\t1. AAA"
        echo -e "\t2. BBB"
        echo -e "\t3. CCC"
        echo -en "Enter option: "
        read -n 1 option

        case $option in
            1)
                echo -e "\nAAA";;
            2)
                echo -e "\nBBB";;
            3)
                echo -e "\nCCC";;
            *)
                echo -e "\nerror";;
        esac
    ```

---

**创建菜单函数**
- 菜单选项作为一组独立的函数，可以方便在任何时候调用；
- 将菜单布局本身作为一个函数来创建；
- 为每个菜单选项创建独立的shell函数
- 为还没有实现的函数创建一个桩函数(stub function)，桩函数是一个空函数，或者只有一个 "echo"语句；
- 桩函数也是从"clear"命令开始，避免受到原菜单干扰；
    ```SHELL
        function menu {
                clear
                echo
                echo -e "\t\t\tSys Admiin Menu\n"
                echo -e "\t1. AAA"
                echo -e "\t2. BBB"
                echo -e "\t3. CCC"
                echo -en "Enter option: "
                read -n 1 option
        }
    ```

---

**添加菜单逻辑**
- "case"命令应根据菜单中输入的字符来调用相应的函数；
- "*"处理所有不正确的菜单项；

---

**整合shell脚本菜单**
```SHELL
    #! /bin/sh                               
                                            
    function menu {                          
            clear                            
            echo                             
            echo -e "\t\t\tSys Admiin Menu\n"
            echo -e "\t1. AAA"       
            echo -e "\t2. BBB"       
            echo -e "\t3. CCC"       
            echo -en "Enter option: "
            read -n 1 option
    }                      
                        
    AAA() {                
            clear          
            echo -e "\nAAA"
    }                      
                        
    BBB() {                
            clear          
            echo -e "\nBBB"
    }                      
                        
    CCC() {                
            clear          
            echo -e "\nCCC"
    }
    while [ 1 ]            
    do                     
        menu               
        case $option in    
            q)             
                break;;    
            1)             
                AAA;;      
            2)             
                BBB;;      
            3)         
                CCC;;  
            *)         
                clear  
                echo -e "\nerror option!";;
        esac                               
        echo -en "\n\n\t\t\t Hit any key to continue"
        read -n 1 line                               
    done                                             
    clear 
```

--- 

**使用select命令**
- 可以避免花太多时间在建立菜单布局和获取用户输入上；
- 格式:
    ```SHELL
        select variable in list
        do
            commands
        done
    ```
    - "list"参数是由空格分隔的文本选项列表，这些列表构成了整个菜单；
    - "select"命令会将每个列表项显示成一个带编号的选项；
    - "select"命令为选项显示一个由"PS3"环境变量定义的特殊提示符；
    - 存储在变量中的结果值是整个文本字符串而不是跟菜单选项相关联的数字，文本字符串值才是要在"case"语句中进行比较的内容；
    ```SHELL
        #!/bin/bash

        AAA() {
            clear
            echo -e "\nAAA"
        }

        BBB() {
            clear
            echo -e "\nBBB"
        }

        CCC() {
            clear
            echo -e "\nCCC"
        }

        PS3="Enter option:"
        select option in "exit" "AAA" "BBB" "CCC"
        do
            case $option in
                exit)
                    break;;
                AAA)
                    AAA;;
                BBB)
                    BBB;;
                CCC)
                    CCC;;
                *)
                    clear
                    echo -e "\nerror option!";;
            esac
        done
        clear
    ```

### 2.2.2. 制作窗口

> - 文本菜单相较于图形化窗口还是有不足；
> - dialog包用ANSI转义控制字符在文本环境中创建标准的窗口对话框，可以将这些对话框融入脚本中，借此与用户进行交互；
> - `sudo apt-get install dialog`在ubuntu环境下安装dialog包和相关库；

#### 2.2.2.1. dialog包

- "dialog"命令使用命令行参数来决定生成哪种窗口部件(widget)；
- 部件是dialog包中窗口元素类型的术语；
- 在命令行上指定某个特定的部件，需使用"双破折线"；
    - eg：`dialog --widget parameters`，"widget"是部件名，"parameters"定义了部件窗口的大小以及部件需要的文本；
- 每个dialog部件提供两种形式的输出："STDERR"和"退出状态码"；
    - 可以通过"dialog"命令的退出状态码来确定用户选择的按钮，"Yes"和"Ok"返回"0"，"Cancel"和"NO"返回"1"，用"$?"获取返回；
    - 如果部件返回数据，"dialog"命令会将数据发送到"STDERR"，可将"STDERR输出"重定向到另一个文件或文件描述符中；

#### 2.2.2.2. dialog选项

- 除了标准部件，还可以在"dialog"命令中定制很多不同的选项；
    - "--title"选项允许设置出现在窗口顶部的部件标题；
    - "--backtitle"选项是为脚本中的菜单创建公共标题的简便办法；
    - 等等...

#### 2.2.2.3. 脚本中使用dialog命令

-  如果有"Cancel"或"No"按钮，检查"dialog"命令的退出状态码；
-  重定向"TDERR"来获得输出值；















# 疑问：
- shell内建命令有哪些？
- 运行命令的时候不加入路径就不会创建子shell？
- select方法和getopt的区别？应用场景？(getopt用于命令行参数，select的用于交互)

```
file=/dir1/dir2/dir3/my.file.txt
可以用${ }分别替换得到不同的值：
${file#*/}：删掉第一个 / 及其左边的字符串：dir1/dir2/dir3/my.file.txt
${file##*/}：删掉最后一个 /  及其左边的字符串：my.file.txt
${file#*.}：删掉第一个 .  及其左边的字符串：file.txt
${file##*.}：删掉最后一个 .  及其左边的字符串：txt
${file%/*}：删掉最后一个  /  及其右边的字符串：/dir1/dir2/dir3
${file%%/*}：删掉第一个 /  及其右边的字符串：(空值)
${file%.*}：删掉最后一个  .  及其右边的字符串：/dir1/dir2/dir3/my.file
${file%%.*}：删掉第一个  .   及其右边的字符串：/dir1/dir2/dir3/my
记忆的方法为：
# 是 去掉左边（键盘上#在 $ 的左边）
%是去掉右边（键盘上% 在$ 的右边）
单一符号是最小匹配；两个符号是最大匹配
${file:0:5}：提取最左边的 5 个字节：/dir1
${file:5:5}：提取第 5 个字节右边的连续5个字节：/dir2
也可以对变量值里的字符串作替换：
${file/dir/path}：将第一个dir 替换为path：/path1/dir2/dir3/my.file.txt

${file//dir/path}：将全部dir 替换为 path：/path1/path2/path3/my.file.txt
```

```
()和{}都是对一串的命令进行执行,但有所区别：
相同点：
()和{}都是把一串的命令放在括号里面,并且命令之间用;号隔开
不同点
()只是对一串命令重新开一个子shell进行执行,{}对一串命令在当前shell执行
()最后一个命令可以不用分号,{}最后一个命令要用分号
()里的第一个命令和左边括号不必有空格,{}的第一个命令和左括号之间必须要有一个空格
()和{}中括号里面的某个命令的重定向只影响该命令,但括号外的重定向则影响到括号里的所有命令
```

```
* 表示零个或多个任意字符
?表示零个或一个任意字符
[...]表示匹配中括号里面的字符
[!...]表示不匹配中括号里面的字符

shell脚本中read命令使用技巧(-r选项)

https://blog.csdn.net/wmjcode/article/details/80662501
```