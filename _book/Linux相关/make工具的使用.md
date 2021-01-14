### Linux下C语言简单实现cat、ls、cd命令

由于Linux命令一般都含有很多可选参数，可以完成很多操作，这里仅实现其最基本的不加参数功能

#### cat命令的简单实现

```c
//mycat.c
#include <stdio.h>
int main(int argc, char *argv[])
{
    FILE *fp = fopen(argv[1], "r");   // 以只读方式打开文件，argv[1]是输入的要显示的文件路径名
    int read_ret;
    if(argc < 2)  //说明没有输入要显示的文件名，只有本程序名argv[0]
    {
        printf("please input source file!\n");
    }
    if(fp == NULL)  //如果打开成功的话就把该文件的信息结构体地址赋给文件指针fp，如果打开不成功的话就将文件指针fp置为无效(NULL)
    {
        printf("open source %s failed!\n", argv[1]);
        return -1;
    }
    while(1)  //成功打开
    {
        read_ret = fgetc(fp);  //单个字符读写
        if(feof(fp))  // feof判断文件结束，若fp已经指向文件末尾，则feof（fp）函数值为“真”，即返回非零值
        {
            printf("read file %s endl\n",argv[1]);   //提示读写结束       
            break;
        }
        fputc(read_ret,stdout);  //把单个字符输出到屏幕
    }
}
```

#### ls命令的简单实现

```c
//myls.c 
#include <stdio.h>
 #include <sys/types.h>
 #include <dirent.h>
 
 int main(int argc, char* argv[])
 {	
 	DIR *dp=NULL;
 	struct dirent * dt=NULL;
 	dp=opendir("./");
 	if(dp==NULL)
 	{
 		perror("opendir err");
 		return -1;
 	}
 
 	while(1)
 	{
 		dt = readdir(dp);
 		if(dt==NULL)
 		{
 			break;
 		}
 		if(dt->d_name[0] != '.')
 			printf("%s\n", dt->d_name);
 	}
 
 	closedir(dp);
 
 	return 0;
 }
```

#### cd命令的简单实现

```c
//mycd.c
#include <stdio.h>
#include <unistd.h>
int main()//正确的
{
         char s[]="test";//当前目录下面有名为test的文件夹
         chdir (s);
         char xxx[1024];//获取当前目录并打印
         getcwd(xxx,1024);
         printf("The current directory is : %s\n",xxx);
}
```

##### chdir函数——change working directory

```c
#include <unistd.h>
int chdir(const char *path);
```

需要注意的是，chdir并不是直接把终端当前的目录切换，而是在编写的程序(mycd.c)进程中切换当前工作目录

关于chdir更详细的信息可在Linux系统终端输入命令`man chdir`查看

### make工具的简单使用

编写makefile文件，以刚才编写的mycd.c为例

```makefile
#makefile
mycd: mycd.o
        gcc -o mycd mycd.o
clean:
        rm -f *.o
```

执行make命令

![执行make命令](http://q2cgswswp.bkt.clouddn.com/blog/imgs/20191212131441.png)

生成了mycd.o文件以及可执行文件mycd

执行make clean可以删除mycd.o文件

![make_clean命令](http://q2cgswswp.bkt.clouddn.com/blog/imgs/20191212131633.png)

测试可执行文件

![测试可执行文件](http://q2cgswswp.bkt.clouddn.com/blog/imgs/20191212132351.png)

注意：终端这里的路径是不会变的，不是程序写错了哦！