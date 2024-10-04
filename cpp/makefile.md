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