# make和makefile

## make

make 工具可以看成是一个智能的**批处理**工具,它本身并没有编译和链接的功能，而是用类似于批处理的方式—通过调用**makefile文件**中用户指定的命令来进行编译和链接的

## makefile

make工具就是根据makefile中的命令进行编译和链接的.一些简单的工程,makefile可以通过人工完成;但是对于大工程,手写makefile会非常麻烦,所以就有了Cmake工具;

Makefile的规则:

> **target … : prerequisites …**
>
> ​	**command**
>
> ​	**…**
>
> ​	**…**
>
> 或是:
>
> **targets : prerequisites ; command
>       command
>       ...**
>
> + target也就是一个目标文件，可以是Object File，也可以是执行文件。
> + prerequisites就是，要生成那个target所需要的文件或是目标。
> + command也就是make需要执行的命令。
>
> 这是一个文件的依赖关系，也就是说，target这一个或多个的目标文件依赖于prerequisites中的文件，其生成规则定义在command中。说白一点就是说，prerequisites中如果有一个以上的文件比target文件要新的话，command所定义的命令就会被执行。这就是Makefile的规则。也就是Makefile中最核心的内容。
>

在**makefile文件**所处的目录下输入命令**“make”**,就可以生成可执行文件.

如果要删除执行文件和所有的中间目标文件**(object file)**,只需要执行**“make clean”**.

> 命令一定要以一个Tab键开头,make不会管命令是怎么工作的,他只管执行所定义的命令.
>
> make会比较targets文件和prerequisites文件的修改日期，如果prerequisites文件的日期要比targets文件的日期要新，或者target不存在的话，那么，make就会执行后续定义的命令。

### 变量

例如:

```makefile
objects = main.o kbd.o command.o display.o /          #这里objects是一个变量,其值是生成可执行文件的依赖关系
              insert.o search.o files.o utils.o
              
edit : $(objects)                                     #这里的edit是可执行文件
            cc -o edit $(objects)                     
```

**$(变量名)** 是一种引用变量的方式,其作用是获取变量中存储的值,并将其替换到Makefile中的相应位置.

### 自动推导

GNU的make,可以自动推导文件以及文件依赖关系后面的命令.

只要make看见一个**[*.o]**文件,它就会自动把**[ *.c]**文件加在依赖关系中;例如:如果**make**找到一个**whatever.o**，那么**whatever.c**，就会是**whatever.o**的依赖文件。并且 **cc -c whatever.c** 也会被推导出来；

```makefile
main.o : defs.h
```

### 清空目标文件

每个Makefile中都应该写一个清空目标文件（.o和执行文件）的规则，这不仅便于重编译，也很利于保持文件的清洁。

```makefile
.PHONY : clean
clean :
	-rm edit $(objects)
```

**.PHONY**意思表示**clean**是一个“伪目标”.而在**rm**命令前面加了一个小减号的意思就是，也许某些文件出现问题，但不要管，继续做后面的事。

**clean**规则一般都是放在文件的最后面的.

由于**clean**不是文件,而是“伪目标”,所以make无法根据其依赖关系等来决定是否要不要执行,所以只有通过显示地指明该目标才能使其生效: **make clean**

### Makefile的文件名和引用

可以用别的文件名来书写**Makefile**,此时要指定特定的**Makefile**,就要使用**make**的**“-f”**和**“- - file”** 参数:

`make -f 文件名`

在**Makefile**中使用**include**关键字就可以把别的**Makefile**包含进来,像c语言一样,被包含的文件内的内容会被原模原样的放在当前文件的包含位置.

**include <filename>**

include 可以一次引入多个文件,例如:

````makefile
 include foo.make *.mk $(bar)              #bar变量中包含了e.mk,f.mk
 等价于
 include foo.make a.mk b.mk c.mk e.mk f.mk
````

### make的工作执行步骤

1. 读入所有的Makefile。
2. 读入被include的其它Makefile。
3. 初始化文件中的变量。
4. 推导隐晦规则，并分析所有规则。
5. 为所有的目标文件创建依赖关系链。
6. 根据依赖关系，决定哪些目标要重新生成。
7. 执行生成命令。

在Makefile中,规则的顺序很重要,而且Makefile中只有一个最终目标;定义在Makefile中的目标可能很多,但是第一条规则中的目标将被确定为最终的目标.

### 文件搜寻

在较大的工程中,有大量的源文件;此时就需要进行文件搜索,那么最好的方法就是提供一个路径给make,让make自动去找.

**Makefile**文件中的变量**“VPATH”**就是用于提供路径的.

如:   `VPATH = src:../headers`

这里指定了两个目录,目录用冒号分隔;

另一个设置文件搜索路径的方法是使用make的**“vpath”**关键字,这个关键字更灵活,因为它可以指定不同的文件在不同的目录进行搜索:

1. vpath <pattern> <directories>

为符合模式<pattern>的文件指定搜索目录<directories>。

2. vpath <pattern>

清除符合模式<pattern>的文件的搜索目录。

3. vpath

清除所有已被设置好了的文件搜索目录。

用例:   `vpath %.h  . . /headers`          pattern需要包含“%”字符,“%”可以匹配0或多个字符.

