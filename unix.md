**linux一切皆文件**

# 一.my  ls/more/cp

## 1.my_ls

- 访问目录下的文件 opendir   readdir closedir
- 访问文件信息 stat lstat（当文件是link文件时，lstat返回的是所连接到的文件信息）  inode文件状态信息
- 获取命令选项option  getopt
- 获取文件user/group   getpwuid/getgruid
- 获取文件修改时间  ctime

ls的实现：

stream 流

errno   全局错误代码  

perror 可以打印错误代码表示的信息

opendir 打开目录，返回目录地址

readdir 循环读目录里的元素，返回文件信息的结构体

closedir 关闭目录

stat(2) 能拿到文件在执行ls -l时所有的信息，文件权限

getpwuid/getgrgid通过id得到用户名

getopt实现参数-l -a等

排序问题   格式问题    一行放几个 

```c
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<dirent.h>
#include<sys/types.h>
#include<string.h>
#include<sys/ioctl.h>
#include<math.h>
#include<sys/stat.h>
#include<grp.h>
#include<pwd.h>
#include<time.h>
#define FILEMAX 1024
#define NAMEMAX 256

int flag_a = 0;
int flag_l = 0;
int fg_c;
int bg_c;
int dir_num = 0;

//获取终端的尺寸
void size_window(char filename[][NAMEMAX], int cnt, int *row, int *col){
    struct winsize size;
    int len[cnt], max = 0, total = 0;
    memset(len, 0, sizeof(int) * cnt);
    if(isatty(STDOUT_FILENO) == 0){
        exit(1);
    }
    if(ioctl(STDOUT_FILENO, TIOCGWINSZ, &size) < 0){
        perror("ioctl");
        exit(1);
    }
    //printf("windowsize row = %d, col = %d\n", size.ws_row, size.ws_col);
    for(int i = 0; i < cnt; i++){
        len[i] = strlen(filename[i]);
        if(max < len[i]) max = len[i];
        total += len[i] + 1;
    }    
    if(max + 1 >= size.ws_col){
        *row = cnt;
        *col = 1;
        return;
    }
    if(total < size.ws_row){
        *row = 1;
        *col = cnt;
        return ;
    }
    
    int try_begin = 0;
    for(int i = 0, tmp = 0; i < cnt; i++){
        tmp += (len[i] + 1);
        if(tmp >= size.ws_col){
            try_begin = i;
            break;
        }
    }

    for(int i = try_begin; ; i--){
        int *wide = (int *)malloc(sizeof(int) * i);
        memset(wide, 0, sizeof(int) * i);
        *row = (int)ceil(cnt / i);
        int try_sum = 0;
        
        for(int x = 0; x < i; x++){
            //找到每一列最大长度
            for(int y = x * *row; y < (x + 1) * *row && y < cnt; y++){
                if(wide[x] < len[y]) wide[x] = len[y];
            }
            try_sum += (wide[x] + 1);

        }
        //所有列的最大长度总和不超过终端的宽度 跳出
        if(try_sum > size.ws_col) continue;
        if(try_sum <= size.ws_col){
            *col = i;
            break;
        }
    }

}


void update_color(mode_t mode){
    bg_c = 0;
    fg_c = 37;
    
    if(mode & (S_IXUSR | S_IXGRP | S_IXOTH)) bg_c = 1, fg_c = 32;
   
    switch(mode & S_IFMT){
        case S_IFDIR: bg_c = 1, fg_c = 34; break;
        case S_IFLNK: bg_c = 1, fg_c = 36; break;

    }
}

//显示多个文件名 
void show_files(char filename[][NAMEMAX], int cnt, int row, int col){
    int wide_file[cnt];
    struct stat tmp_st;
    memset(wide_file, 0, sizeof(int) * cnt);
    //求每一列的最大总长度 占用的位数
    for(int i = 0; i < col; i++){
        for(int j = (i * row); j < (i + 1) * row && j < cnt; j++){
            if(wide_file[i] < strlen(filename[j])) wide_file[i] = strlen(filename[j]);
        }
    }

    for(int i = 0; i < row; i++){
        for(int j = i; j < i + (row * col) && j < cnt; j = j + row){
            int tmp = j / row;
            lstat(filename[j], &tmp_st);
            update_color(tmp_st.st_mode);
            printf("\033[%d;%dm%-*s\033[0m", bg_c, fg_c, wide_file[tmp] + 1, filename[j]);
        }
        printf("\n");
    }
    
}



void mode_to_str(mode_t mode, char *str){
    //文件类型
    if(S_ISREG(mode)) str[0] = '-';
    if(S_ISDIR(mode)) str[0] = 'd';
    if(S_ISCHR(mode)) str[0] = 'c';
    if(S_ISBLK(mode)) str[0] = 'b';
    if(S_ISSOCK(mode)) str[0] = 's';
    if(S_ISLNK(mode)) str[0] = 'l';
    if(S_ISFIFO(mode)) str[0] = 'p';

    //用户权限
    if(mode & S_IRUSR) str[1] = 'r';
    if(mode & S_IWUSR) str[2] = 'w';
    if(mode & S_IXUSR) str[3] = 'x';

    //组权限
    if(mode & S_IRGRP) str[4] = 'r';
    if(mode & S_IWGRP) str[5] = 'w';
    if(mode & S_IXGRP) str[6] = 'x';
    
    //其他人权限
    if(mode & S_IROTH) str[7] = 'r';
    if(mode & S_IWOTH) str[8] = 'w';
    if(mode & S_IXOTH) str[9] = 'x';
    
    if((mode & S_IXUSR) && (mode & S_ISUID)) str[3] = 's';

    update_color(mode);

}

char *uid_to_name(uid_t uid){
    struct passwd *pw_ptr;
    static char tmpstr[10] = {0};
    if((pw_ptr = getpwuid(uid)) == NULL){
        sprintf(tmpstr, "%d", uid);
        return tmpstr;
    }
    else{
        return pw_ptr->pw_name;
    }
}

char *gid_to_name(gid_t gid){
    struct group *gr_ptr;
    static char tmpstr[10] = {0};
    if((gr_ptr = getgrgid(gid)) == NULL){
        sprintf(tmpstr, "%d", gid);
        return tmpstr;
    }
    else{
        return gr_ptr->gr_name;
    }
}

//ls -l时打印文件信息
void show_info(char *filename, struct stat *info){
    char modestr[11] = "----------";//10个字符 结尾为\0 加起来11个
    //printf("%s", filename);
    mode_to_str(info->st_mode, modestr);
    printf("%s ", modestr);
    printf("%4ld ", info->st_nlink);
    printf("%10s ", uid_to_name(info->st_uid)); 
    printf("%10s ", gid_to_name(info->st_gid));
    printf("%10ld ", info->st_size);
    printf("%.15s ", 4 + ctime(&info->st_mtime));
    printf("\033[%d;%dm%s\033[0m ", bg_c, fg_c, filename);
    if(modestr[0] == 'l'){
        int cnt;
        char buf[NAMEMAX] = {0};
        if((cnt = readlink(filename, buf, NAMEMAX)) < 0){
            perror("readlink");
        }
        else{
            printf("-> %s", buf);
        }
    }
    printf("\n");

    
}

//ls -l时打印文件信息
void do_stat(char *filename){
    struct stat st;
    if((lstat(filename, &st) < 0)) {
        perror(filename);
    }
    else{
        show_info(filename, &st);
    }
    //printf("doing with %s status.\n", filename);
}

//names的排序函数
int cmp_name(const void* _a, const void* _b){
    char *a = (char *)_a;
    char *b = (char *)_b;
    return strcmp(a, b);
}

//处理文件和目录
void do_ls(char *dirname){
    DIR *dirp = NULL;
    struct dirent *direntp;
    char names[FILEMAX][NAMEMAX] = {0};
    //此文件不是目录 是单纯的文件
    if((dirp = opendir(dirname)) == NULL){
        //这个文件是否存在
        if(access(dirname, R_OK) == 0){
            if(!flag_l) {
                dir_num--;
                struct stat tmp_st;
                lstat(dirname, &tmp_st);
                update_color(tmp_st.st_mode);
                printf("\033[%d;%dm%s\033[0m\n", bg_c, fg_c, dirname);
                return ;
            }
            else{
                dir_num--;
                do_stat(dirname);
                return ;
            }
        }else{
            perror(dirname);
            return ;
        }
    }
    //此文件是目录文件
    else{
        if(dir_num) printf("%s:\n", dirname);
        chdir(dirname);//切换工作目录
        int cnt = 0;
        //打开目录流，将目录下的文件名放在names数组中
        while((direntp = readdir(dirp)) != NULL){
            //处理-a选项
            if(direntp->d_name[0] == '.' && !flag_a) continue;
            strcpy(names[cnt++], direntp->d_name);
            
        }
        qsort(names, cnt, NAMEMAX, cmp_name);
        if(flag_l == 1){
            for(int i = 0; i < cnt; i++){
                do_stat(names[i]);
            }
        }
        else{
            //printf("printf all file\n");
            int row, col;
            size_window(names, cnt, &row, &col);
            //printf("row = %d, col = %d\n", row, col);
            show_files(names, cnt, row, col);
        }
        
    }
    //printf("doing with dir %s\n", dirname);
}



int main(int argc, char **argv){
    int opt;
    //处理选项
    while((opt = getopt(argc, argv, "al")) != -1){
        switch(opt){
            case 'a': flag_a = 1; break;
            case 'l': flag_l = 1; break;
            default: fprintf(stderr, "Usage: %s -[al] [filename]", argv[0]); exit(1);
        }
    }
    //printf("flag_a = %d, flag_l = %d\n", flag_a, flag_l);
    
    //printf("optind = %d\n", optind);
    argc -= (optind - 1);
    argv += (optind - 1);
    //printf("argc = %d, argv = %s\n", argc,*(argv + 1));
    //是否有文件名的输入 没有就是当前的.文件
    if(argc == 1) {
        dir_num = 0;
        do_ls(".");
    }
    else{
        dir_num = argc - 2;
        while(--argc){
            do_ls(*(++argv));
        }
    }
    
    return 0;
}


```

## 2.my_more

- 打开/关闭文件流 fopen/fclose  以ASCII码流打开
- 读文件 fread/fgets
- 输出文件fputs
- 终端tty man 4 tty
- 获取终端宽度ioctl

![image-20200319180729939](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192548.png)

fopen  fgets

```c
#include<stdio.h>
#include<stdlib.h>
#include<sys/ioctl.h>
#include<unistd.h>
#define PAGELINE 15
#define LENLINE 512


void do_more(FILE *fp);

int main(int argc, char **argv){
    FILE *fp;
    if(argc == 1) do_more(stdin);
    //支持./a.out < 1.log 
    //man ls | ./a.out
    //问题 文件流中的/n space 和 q当作成了控制信号
    else{
        while(--argc){
            if((fp = fopen(*++argv, "r")) != NULL){
                do_more(fp);
            }
        }
    }
    return 0;
}
//获取当前终端的宽度
int windows_size(){
    struct winsize size;
    ioctl(STDIN_FILENO, TIOCGWINSZ, &size);
    return size.ws_row;
}

void do_more(FILE *fp){
    char line[LENLINE] = {0};
    FILE *cmd = fopen("/dev/tty", "r");//这个文件是终端中的输入！！！防止文件流中的文本变成输入
    int num_line = 0, reply, get_cmd(FILE *);
    while(fgets(line, LENLINE, fp)){//将fp中的LENLINE个字符放在line中
        //已经打印了15行需要等待命令
        if(num_line == windows_size()){
            reply = get_cmd(cmd);
            if(reply == 0) break;
            num_line -= reply;

        }   
        //如果当前打印的行数不足15，打印文件内容
        if(fputs(line, stdout) == EOF){//将line输出显示在stdout
            perror("fputs");
            exit(1);
        }
        num_line++;
    }
}

//只能从终端上读 不能将打开的文件流中的q space \n 当成控制信号
int get_cmd(FILE *fp){
    printf("more:");
    int c;
    while((c = getc(fp)) != EOF){
        if(c == 'q') return 0;
        if(c == ' ') return windows_size();
        if(c == '\n') return 1;
    }
    return -1;
}

```

## 3.my_cp

- 打开/关闭文件 open/close（比fopen更加底层） 返回值为文件描述符fd
- 读/写文件 read /write
- 创建新文件creat

sizeof 是操作符 不是函数  类似+-*/

strlen不包含'\0'

```c
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<fcntl.h>
#include<sys/stat.h>
#include<sys/types.h>
#include<string.h>
#include<unistd.h>
#define BUFSIZE 128
int main(int argc, char **argv){
    int fd_in, fd_out;
    char buf[BUFSIZE + 5] = {0};
    ssize_t nread;
    if(argc != 3) {
        printf("Usage: %s sourcefile destfile!\n", argv[0]);
        exit(1);
    }

    if((fd_in = open(argv[1], O_RDONLY)) == -1){
        perror(argv[1]);
        exit(1);
    }
    if((fd_out = creat(argv[2], 0644)) == -1){
        perror(argv[2]);
        exit(1);
    }
    while((nread = read(fd_in, buf, BUFSIZE)) > 0){
        if(write(fd_out, buf, strlen(buf)) != nread){
            perror("write");
            exit(1);
        }
        memset(buf, 0, BUFSIZE + 5);

    }
    close(fd_in);
    close(fd_out);
    return 0;
}

```

## 4.shell（c语言）

1.功能需求

1. 能实现内置的cd，pwd命令
2. 输出要跟真正的shell尽量一致
3. 可以实现ls,ps,vim等功能（这里可以用fork+exec，popen等实现，程序中采用的是fork+exec）

2.代码演示

- 头文件head.h

  ```c
  #ifndef _HEAD_H
  #define _HEAD_H
  #include<stdio.h>
  #include<stdlib.h>
  #include<unistd.h>
  #include<sys/types.h>
  #include<sys/socket.h>
  #include<arpa/inet.h>
  #include<pthread.h>
  #include<string.h>
  #include<sys/ioctl.h>
  #include<fcntl.h>
  #include<stdbool.h>
  #include<pwd.h>
  #include<sys/wait.h>
  #include<signal.h>
  #include<sys/select.h>
  #include<poll.h>
  #include<dirent.h>
  #endif
  
  ```

- 颜色宏定义color.h

  ```c
  #ifndef _COLOR_H
  #define _COLOR_H
  
  #define NONE "\033[0m"
  #define BLACK "\033[0;30m"
  #define L_BLACK "\033[1;30m"
  #define RED "\033[0;31m"
  #define L_RED "\033[1;31m"
  #define GREEN "\033[0;32m"
  #define L_GREEN "\033[1;32m"
  #define YELLOW "\033[0;33m"
  #define L_YELLOW "\033[1;33m"
  #define BLUE "\033[0;34m"
  #define L_BLUE "\033[1;34m"
  #define L_PINK "\033[1;35m"
  #define PINK "\033[0;35m"
  
  #endif
  
  ```

- 主程序shell.c

  ```c
  include"../head/head.h"
  #include"../head/color.h"
  //打印信息
  void printMsg(){
      char hostname[50];
      //获取主机名
      if(gethostname(hostname, 50) < 0){
          perror("gethostname");
          return ;
      }
      printf(GREEN"%s@%s"NONE":", getpwuid(getuid())->pw_name, hostname);
      char dirbuf[100];
      //获取当前工作目录
      if(getcwd(dirbuf, 100) <  0){
          return ;
      }
      //将前面的/home/用户名变为~
      printf(BLUE"~%s"NONE"$", dirbuf + 6 + strlen(getpwuid(getuid())->pw_name));
      return ;
  }
  
  //判断输入的参数个数是否合法
  bool argumentNum(char *cmd, int num, char *tips){
      int arg = 1;
      //1个空格2个参数 2个空格3个参数 ...
      for(int i = 0; i < strlen(cmd); i++){
          if(cmd[i] == ' ') arg++;
      }
      if(arg != num){
          fprintf(stderr, "Usage: %s\n", tips);
          return false;
      }
      return true;
  }
  //pwd函数
  void mypwd(){
      char dirbuf[100];
      getcwd(dirbuf, 100);
      printf("%s\n", dirbuf);
      return ;
  }
  //cd函数
  void mycd(char *cmd){
      char *pathname = strstr(cmd, " ") + 1;
      //printf("pathname:%s\n", pathname);
      //~目录变为/home/用户名
      if(!strcmp(pathname, "~")){
          sprintf(pathname, "/home/%s", getpwuid(getuid())->pw_name);
      }
      //判断是否有此目录（能否打开）
      if((opendir(pathname)) == NULL){
          perror("opendir");
          return ;
      }
      //改变工作目录
      chdir(pathname);
      return ;
  
  }
  //子进程调用sh来执行其他函数ls ps等
  void mysh(char *cmd){
      pid_t pid;
      if((pid = fork()) < 0){
          perror("fork");
          return ;
      }
      if(pid == 0){
          if(execl("/bin/sh", "sh", "-c", cmd, NULL) < 0){
              perror("execl");
              return ;
          }
      }else{
          wait(NULL);
          return ;
      }
  }
  
  int main(int argc, char **argv){
      char cmd[100];
      while(1){
          printMsg();
          scanf("%[^\n]s", cmd);
          getchar();
          char *cmdp = NULL;
          if((cmdp = strstr(cmd, "pwd")) != NULL){
              //printf("pwd\n");
              mypwd();
          }else if((cmdp = strstr(cmd, "cd")) != NULL){
              //printf("cd\n");
              if(argumentNum(cmd, 2, "cd dirname")){
                  mycd(cmd);
              }else continue;
          }else{
              mysh(cmd);
          }
      }
      return 0;
  }
  
  ```

3.代码效果

![image-20200425114135531](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192638.png)

代码功能还不完善，后续会继续完善，包括加上通道等功能

# 二.内核概念

内核是操作系统最基本的部分。它是为众多应用程序提供对计算机硬件的安全访问的一部分软件

内核，是一个操作系统的核心，是基于硬件的第一层软件扩充，提供操作系统的最基本的功能，是操作系统工作的基础，它负责管理系统的进程、内存、内核体系结构、设备驱动程序、文件和网络系统，决定着系统的性能和稳定性。

  操作系统是一个用来和硬件打交道并为用户程序提供一个有限服务集的低级支撑软件。一个计算机系统是一个硬件和软件的共生体，它们互相依赖，不可分割。计算机的硬件，含有外围设备、处理器、内存、硬盘和其他的电子设备组成计算机的发动机。但是没有软件来操作和控制它，自身是不能工作的。完成这个控制工作的软件就称为操作系统，在Linux的术语中被称为“内核”，也可以称为“核心”。Linux内核的主要模块（或组件）分以下几个部分：存储管理、CPU和进程管理、文件系统、设备管理和驱动、网络通信，以及系统的初始化（引导）、系统调用等。

https://blog.csdn.net/look595271601/article/details/14645093

操作系统和内核的概念https://blog.csdn.net/qq_26849233/article/details/74527779

![image-20210311145119638](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210311145119638.png)

（1）操作系统包括操作系统内核（这是必然的），也就是说内核程序是操作系统所包含的一组计算机程序中的一个子集，所以内核程序也是一组计算机程序，而这些内核程序是操作系统中最常使用基本模块，直接与硬件打交道，主要由用于管理存储器、文件、外设和系统资源的那些部分组成。

（2）内核程序一直占据内存中的一段内存，这样处理器可以随时调用这些内核程序；

（3）而操作系统除了内核程序外，还有包括其他一些基本组件，如文本编辑器、编译器、用来与用户进行交互的程序等

![image-20200528165028743](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192718.png)

内核版本

内核态和用户态：

- [https://www.cnblogs.com/maxigang/p/9041080.html](https://www.cnblogs.com/maxigang/p/9041080.html)
- [https://www.cnblogs.com/bakari/p/5520860.html](https://www.cnblogs.com/bakari/p/5520860.html)

系统调用和库函数：

- https://blog.csdn.net/lht1314tttt/article/details/79150776
- [https://blog.csdn.net/windeal3203/article/details/79083453](https://blog.csdn.net/windeal3203/article/details/79083453)
- [https://www.cnblogs.com/yanlingyin/archive/2012/04/23/2466141.html](https://www.cnblogs.com/yanlingyin/archive/2012/04/23/2466141.html)

# 三.进程

并行与并发：

1. 并行：真正意义上的并行 多个cpu同时执行多个进程/线程
2. 并发：通过基于时间片的调度 在一个cpu跑多个进程/线程

## 1.base

程序是存储在磁盘介质上的编译过的二进制文件

进程是程序跑起来的状态

程序是死的，进程是活的

进程是正在执行的程序的一个实例

malloc函数从内存的堆中分配储存

静态变量（通常是所说的程序中的全局变量）会使得线程化的程序不安全，除非保证各个线程访问时是互斥的！！

pid每个进程都有一个id和一个父进程id

获取进程pid/father pid    getpid/getppid



进程的返回值是一个8位数  0-255  返回很大的数只会取低8位数值

进程返回值来源（主函数中return  和  exit() ）

函数的返回值是给调用此函数的使用

0值表示子进程成功执行  >0都是未成功执行

## 2.进程状态

![image-20200526181954409](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192649.png)

就绪的进程就是非阻塞的

阻塞态不会直接去运行

阻塞时不会占用cpu

## 3.进程创建fork函数

进程可以通过调用fork函数来创建，调用进程称为父进程

fork函数会复制父进程的内存映像，新进程会收到父进程地址空间的一份copy

fork函数在子进程中返回0

```c
#include<stdio.h>
#include<unistd.h>

int main(){
    int pid;
    printf("before fork()...\n");
    if((pid = fork()) < 0) perror("fork");
    if(pid == 0) printf("In child\n");
    else{
        sleep(1);
        printf("In parent\n");
    }
    printf("end fork\n");
    return 0;
}

```

![image-20210311152358648](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210311152358648.png)

```c
#include<stdio.h>
#include<unistd.h>

int main(){
    int pid;
    printf("before fork()...");
    if((pid = fork()) < 0) perror("fork");
    if(pid == 0) printf("\nIn child\n");
    else{
        sleep(1);
        printf("\nIn parent\n");
    }
    printf("end fork\n");
    return 0;
}

```

![image-20210311152442842](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210311152442842.png)

为什么before fork（）...输出了两次？？

printf（）底层是write实现的，对write进行了封装，行缓冲，当进行到程序的第6行时没有换行

当程序将数据提交给内核时，内核没有看到换行会以为后面还有数据（行缓冲优化）

fork（）之后，两个进程的输出缓冲区都有befoe fork，当两个进程的输出缓冲区都读入到换行时进行输出

创建10个子进程

```c
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>


int main(){
    int pid, x;
    for(int i = 1; i <= 10; i++){
        if((pid = fork()) < 0){
            perror("fork");
            continue;
        }
        if(pid == 0){
            x = i;
            break;
        }
    }
    printf("x = %d\n", x);
    sleep(1);

    return 0;
}

```

return和exit的区别：[https://blog.csdn.net/qq_27320195/article/details/85236873](https://blog.csdn.net/qq_27320195/article/details/85236873)



## 4.等待函数wait

wait函数等待某进程结束

父进程可以通过wait函数一直阻塞到子进程结束

wait(NULL)可以等待当前进程任意一个子进程结束

![image-20210311161257082](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210311161257082.png)



```c
#include<stdio.h>
#include<unistd.h>
#include<sys/wait.h>
int main(){
    int pid;
    printf("before fork()...\n");
    if((pid = fork()) < 0) perror("fork");
    if(pid == 0) printf("In child\n");
    else{
        wait(NULL);
        printf("In parent\n");
    }
    printf("end fork\n");
    return 0;
}

```

```c
#include<stdio.h>
#include<unistd.h>
#include<sys/wait.h>

int main(){
    int pid;
    for(int i = 1; i <= 5; i++){
        if((pid = fork()) < 0) perror("fork");
        if(pid == 0){
            printf("I'm a child\n");
            return 0;
        }
    }
    //wait(NULL);
    for(int i = 1; i <= 5; i++){
    wait(NULL);
	}
    printf("I'm parent\n");
    return 0;
}

```

只wait了一次 所有parent不一定最后执行，要wait 5次才行

## 5.覆盖函数exec

fork()函数创建了调用程序的一份copy，但很多子程序要求执行不同的代码，exec函数提供了新映像覆盖调用进程映像的功能

The exec() family of functions replaces the current process image with a new process image.

用fork-exec方法使子进程执行新程序，父进程执行原程序

```c
#include<stdio.h>
#include<unistd.h>
#include<sys/wait.h>

int main(){
    int pid;
    if((pid = fork()) < 0) perror("fork");
    if(pid == 0){
        execl("/bin/ls", "ls", "-l", NULL);//之后的程序子进程就没有了 execl已经替换了
        perror("child failed to exec ls");//下面的命令不执行 因为已经被替换掉了
        return 1;
    }
    wait(NULL);
    return 0;
}

```

git commit不加-m选项时：

fork（）一个子进程，子进程打开一个vim  保存退出后 父进程读取刚刚写的命令继续执行后续命令

task：a.out a.c

打开a.c文件（没有的话创建一个），编辑保存退出后编译这个文件，编译成功后执行，最后退出

```c
 ************************************************************************/
//task：a.out a.c
//打开a.c文件（没有的话创建一个），编辑保存退出后编译这个文件，编译成功后执行，最后退出

#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<string.h>
#include<sys/wait.h>
int main(int argc, char **argv){
    pid_t pid;
    char filename[512] = {0};
    char o_name[512] = {0};//编译后的文件名称
    char f_type[256] = {0};
    char cmd[512] = {0};
    if(argc < 2){
        fprintf(stderr, "Usage: %s filename arg...\n", argv[0]);
        return 1;
    }
    strcpy(filename, argv[1]);
    
    char *sub = NULL;//找到.的位置 逆序找到最后一个.
    if((sub = strrchr(filename, '.')) == NULL){
        fprintf(stderr, "Not support File type!\n");
        return 2;
    }
    strncpy(o_name, filename, sub - filename);
    strcpy(f_type, sub);
    
    if(!strcmp(f_type, ".c")){
        strcpy(cmd, "gcc");
    }else if(!strcmp(f_type, ".cpp")){
        strcpy(cmd, "g++");
    }else{
        fprintf(stderr, "Not support File type\n");
        return 2;
    }
    if((pid = fork()) < 0){
        perror("fork");
        return 3;
    }
    
    if(pid == 0){
        execlp("vim", "vim", filename, NULL);
    }
    wait(NULL);
    
    if((pid = fork()) < 0){
        perror("fork");
        return 3;
    }
    if(pid == 0){
        execlp(cmd, cmd, filename, "-o", o_name, NULL);
    }

    int status;
    wait(&status);
    //status = 0表示子进程执行成功
    if(status == 0){
        char exe_cmd[50] = {0};
        sprintf(exe_cmd, "./%s", o_name);
        execlp(exe_cmd, o_name, NULL);//./a.out
    }else{
        printf("compile Failed!\n");
        return 4;
    }

    return 0;
}

```

https://www.cnblogs.com/mickole/p/3187409.html

## 6.高级进程管理

### 6.1传统的系统调度

![image-20200526181529238](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192647.png)

![image-20200526181954409](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192649.png)

就绪的进程就是非阻塞的

阻塞态不会直接去运行

阻塞时不会占用cpu

![image-20200528161819825](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192651.png)





![image-20200528162236972](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192656.png)

抢占式：windows  linux  unix等都是抢占式

![image-20200528163839918](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192700.png)



基于时间片的调度的问题：

![image-20200528162557935](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192703.png)



两种应用：

![image-20200528163037038](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192708.png)

死循环：cpu拉满-------cpu约束型（密集型）

qq：一直挂着等待别人来消息，别人不来消息就没用---------IO约束型（密集型）

IO密集型时间片越短越好，为了操作更多的IO

cpu约束型不需要IO，时间片越长越好

![image-20200528163708205](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192711.png)

但是应用没有那么绝对，没有太多的单纯的IO/cpu约束型

以上都是传统的系统调度



### 6.2完全公平调度cfs

![image-20200528164341296](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192714.png)

数字越小优先级月高  -20-----+19

目标延迟：一次调度完成后到下次被调度的时间

20ms     5个进程  每个进程运行时间位4ms

当每个进程的调度时间过小<最小粒度时，修改目标延迟   最小粒度是确定的





### 6.3相关函数

sched_yield：让出cpu

命令nice：用修改过的优先级运行程序   nice -n 2 ls 优先级+2执行ls

函数nice修改程序的优先级

getpriority：

setpriority：获得设置优先级

ioprio_get

ioprio_set：io优先级

## 7.进程间的通信IPC

Inter-Process Communication

1. socket套接字（网络、本地和路由）
2. 信号
3. 共享内存
4. 管道
5. 命名管道
6. 消息队列
7. 信号量

为什么要考虑进程间的通讯问题？进程与进程间是独立的

多进程 并发 解决冲突问题 在安全可靠的条件下并发

### 7.1file_flock

flock给某个文件加锁 非强制锁  君子协议  如果别人不看锁的话也能访问文件

用3个进程累加1-100

需要维护两个变量 当前加到多少了now  当前的和sum是多少

```c
//用ind个进程累加1-max

#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<sys/types.h>
#include<sys/wait.h>
#include<sys/file.h>
#include<fcntl.h>
#include<unistd.h>

char num_file[] = "./.num";
char lock_file[] = "./.lock";


struct Num{
    int now;
    int sum;
};

size_t get_num(struct Num *num){
    size_t read;
    FILE *f = fopen(num_file, "r");
    if(f == NULL){
        perror("num fail");
        return -1;
    }
    if((read = fread(num, 1, sizeof(struct Num), f)) < 0){
        perror("read num");
        return -1;
    }
    fclose(f);
    //printf("      read %zu\n", read);
    return read;
}

size_t set_num(struct Num *num){
    FILE *f = fopen(num_file, "wb");
    size_t write = fwrite(num, 1, sizeof(struct Num), f);
    fclose(f);
    //printf("       write %zu \n", write);
    return write;
}

void do_add(int max, int x){
    while(1){
        struct Num num;
        FILE *lock = fopen(lock_file, "w");
        flock(lock->_fileno, LOCK_EX);
        if(get_num(&num) < 0){
            fclose(lock);//一旦关闭f 锁就会被释放
            continue;
        }
        printf("<%d> : %d %d\n", x, num.now, num.sum);
        if(num.now > max){
            break;
        }
        num.sum += num.now;
        num.now++;
        set_num(&num);
        flock(lock->_fileno, LOCK_UN);
        fclose(lock);
    }
}

int main(int argc, char **argv){
    if(argc != 3){
        fprintf(stderr, "Usage : %s max ins\n", argv[0]);
        return 1;
    }

    int x = 0;
    int max = atoi(argv[1]);
    int ins = atoi(argv[2]);
    pid_t pid;
    struct Num num;
    num.now = 0;
    num.sum = 0;
    set_num(&num);

    for(int i = 0; i < ins; i++){
        if((pid = fork()) < 0){
            perror("fork");
            return 1;
        }
        if(pid == 0){
            x = i;
            break;
        }
    }

    if(pid == 0){
        do_add(max, x);
        printf("<%d> exit!\n", x);
        exit(0);
    }
    while(ins){
        wait(NULL);
        ins--;
    }
    get_num(&num);
    printf("Ans = %d\n", num.sum);
    return 0;
}

```

互斥锁/排他锁：只有一个人能用

共享锁：某些人可以用  电影院看电影一群人

文件锁是推荐锁，当没有验证锁时没有意义

Linux下有哪些锁：[https://www.cnblogs.com/TMesh/p/11730847.html](https://www.cnblogs.com/TMesh/p/11730847.html)

### 7.2共享内存SHM

每个进程的内存空间都是独立的

不同的进程访问同一块内存，需要考虑锁的问题，互斥锁

flock是文件锁，不是强制锁

相关函数：

- shmget：获取共享内存
- ftok：确定一个key，对应特定的路径
- shmat：对应上返回shm的指针
- shmdt：删掉shm

亲缘关系下的共享内存使用——配合signal

- kill给某个进程发信号
- signal当接收到特定信号执行特定任务

```c
 //亲子进程shm

#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<sys/types.h>
#include<signal.h>
char *share_memory = NULL;

void print(int sig){
    printf("<parent> : %s\n", share_memory);
}

int main(){
    pid_t pid;
    int shmid;
    key_t k = ftok(".", 198);
    if((shmid = shmget(k, 4096, IPC_CREAT | 0666)) < 0){
        perror("shmget");
        return 1;
    }

    if((share_memory = shmat(shmid, NULL, 0)) < 0){
        perror("shmat");
        return 1;
    }

    if((pid = fork()) < 0){
        perror("fork");
        return 1;
    }
    if(pid == 0){
        while(1){
            printf("<child> : ");
            scanf("%[^\n]s", share_memory);
            getchar();
            kill(getppid(), SIGUSR2);//给父进程发送信号
        }
    }else{
        while(1){
            signal(SIGUSR2, print);//接受信号并操作
        }
    }

    return 0;
}

```



非亲缘进程下的SHM

ftok获得相同的key_t即可

```c
//server.c
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<sys/types.h>
#include<signal.h>
#include<string.h>

struct Shm{
    pid_t pid;
    char buff[1024];
};

struct Shm *share_memory = NULL;

void print(){
    printf("<server> : %s\n", share_memory->buff);
    return ;
}

int main(){
    int shmid;
    key_t key = ftok(".", 2021);

    if((shmid = shmget(key, sizeof(struct Shm), IPC_CREAT | 0666)) < 0){
        perror("shmget");
        return 1;
    }
    if((share_memory = (struct Shm *)shmat(shmid, NULL, 0)) == NULL){
        perror("shmat");
        return 1;
    }
    memset(share_memory, 0, sizeof(struct Shm));
    share_memory->pid = getpid();
    while(1){
        signal(SIGUSR2, print);
    }
    return 0;
}

```

```c
//client.c
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<sys/types.h>
#include<signal.h>
#include<string.h>
struct Shm{
    pid_t pid;
    char buff[1024];
};

struct Shm *share_memory = NULL;


int main(){
    int shmid;
    key_t key = ftok(".", 2021);

    if((shmid = shmget(key, sizeof(struct Shm), IPC_CREAT | 0666)) < 0){
        perror("shmget");
        return 1;
    }
    if((share_memory = (struct Shm *)shmat(shmid, NULL, 0)) == NULL){
        perror("shmat");
        return 1;
    }
    //memset(share_memory, 0, sizeof(struct Shm));
    while(1){
        printf("<client> : ");
        scanf("%[^\n]s", share_memory->buff);
        getchar();
        if(share_memory->pid == 0){
            printf("<Error> : server not login!\n");
        }else{
            kill(share_memory->pid, SIGUSR2);
        }
    }
    return 0;
}

```

```c
//shm + mutex实现0-max的多进程累加
#include<stdio.h>
#include<string.h>
#include<sys/wait.h>
#include<sys/types.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<unistd.h>
#include<pthread.h>
struct Shm{
    int now;
    int sum;
    pthread_mutex_t mutex;
};

struct Shm *share_memory = NULL;

void do_add(int max, int x){
    while(1){
        pthread_mutex_lock(&share_memory->mutex);
        printf("<%d> : %d %d\n", x, share_memory->now, share_memory->sum);
        if(share_memory->now > max) {
            pthread_mutex_unlock(&share_memory->mutex);
            break;
        }
        share_memory->sum += share_memory->now;
        share_memory->now++;
        pthread_mutex_unlock(&share_memory->mutex);
    }
    return ;
}


int main(int argc, char **argv){
    if(argc != 3){
        fprintf(stderr, "Usage: %s max ins", argv[0]);
        return 1;
    }
    int max, ins;
    max = atoi(argv[1]);
    ins = atoi(argv[2]);
    int shmid;
    key_t key = ftok(".", 2021);
    if((shmid = shmget(key, sizeof(struct Shm), IPC_CREAT | 0666)) < 0){
        perror("shmget");
        return 1;
    }
    if((share_memory = (struct Shm *)shmat(shmid, NULL, 0)) == NULL){
        perror("shmat");
        return 1;
    }
    memset(share_memory, 0, sizeof(struct Shm));

    pthread_mutexattr_t attr;
    pthread_mutexattr_init(&attr);
    //将线程锁共享为其他进程也可以用
    //如果不pshared，虽然放在了共享内存中，但是进程不知道这个锁别人也在用
    pthread_mutexattr_setpshared(&attr, PTHREAD_PROCESS_SHARED);
    
    pthread_mutex_init(&share_memory->mutex, &attr);
    share_memory->now = 0;
    share_memory->sum = 0;
    
    int x = 0;
    pid_t pid;
    for(int i = 0; i < ins; i++){
        if((pid = fork()) < 0){
            perror("fork");
            return 1;
        }
        if(pid == 0){
            x = i;
            break;
        }
    }

    if(pid == 0){
        do_add(max, x);
        printf("<%d> exit!\n", x);
        exit(0);
    }

    while(ins){
        wait(NULL);
        ins--;
    }
    printf("Ans = %d\n", share_memory->sum);

    return 0;
}

```

### 7.3匿名管道pipe/命名管道fifo

- pipe:单向的数据通道  pipe[0]读  pipe[1]写
- mkfifo:命名管道
- popen/pclose：打开/关闭管道流

限制：

1. 单向
2. 必须是亲缘关系的进程才能用管道通信

```c
//通过管道 进行进程间的通信 子进程发 父进程收

#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<sys/types.h>
#include<signal.h>
#include<string.h>
int main(){
    pid_t pid;
    int pipefd[2];
    pipe(pipefd);
    char buff[1024] = {0};
    if((pid = fork()) < 0){
        perror("fork");
        return 1;
    }
    
    if(pid == 0){
        while(1){
            close(pipefd[0]);//写的时候关闭读
            scanf("%[^\n]s", buff);
            getchar();
            write(pipefd[1], buff, strlen(buff));
        }
    }else{
        while(1){
            close(pipefd[1]);//读的时候关闭写
            read(pipefd[0], buff, 1024);
            printf("server : %s\n", buff);
            memset(buff, 0, sizeof(buff));
        }
    }

    return 0;
}

```

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/types.h>
#include <signal.h>



int main() {
    pid_t pid;
    FILE *fp, *fp1;
    int pipefd[2];
    pipe(pipefd);
    char buff[1024] = {0};
    if ((pid = fork()) < 0) {
        perror("fork");
        return 1;
    }
    fp = fdopen(pipefd[1], "w");
    fp1 = fdopen(pipefd[0], "r");
    setbuf(fp, NULL);
    if (pid == 0) {
        while (1) {
            scanf("%[^\n]s", buff);
            getchar();
            //fwrite(buff, 1, sizeof(buff), fp);
            fprintf(fp, "%s\n", buff);
            //标准io对于文件的处理是block buffered（块缓存）
            //fflush(fp);
        }
    } else {
        while (1) {
            fscanf(fp1, "%s", buff);
            //fread(buff, 1, 1024, fp1);
            printf("server : %s\n", buff);
        }
    }
    return 0;
}

```

fread:必须读够指定的字节或者到达文件末尾 否则阻塞

> ​		On  success,  fread() and fwrite() return the number of items read or written.  This number
> ​       equals the number of bytes transferred only when size is 1.  If an error occurs, or the end
> ​       of the file is reached, the return value is a short item count (or zero).
>
>    fread()  does  not  distinguish between end-of-file and error, and callers must use feof(3)
>    and ferror(3) to determine which occurred.

read:读到的字节可以小于指定的字节  文件末尾/读管道/或者从终端上读

> ​		On success, the number of bytes read is returned (zero indicates end of file), and the file
> ​       position is advanced by this number.  It is not an error if this number is smaller than the
> ​       number  of  bytes  requested;  this may happen for example because fewer bytes are actually
> ​       available right now (maybe because we were close to end-of-file, or because we are  reading
> ​       from  a pipe, or from a terminal), or because read() was interrupted by a signal.  See also
> ​       NOTES.

popen/pclose: /home/mox/1.HaiZei/6.os/1.process/7.IPC/3.AnonymousPipe

命名管道：FIFO 

- fifo会创建文件
- fifo可以在非亲缘进程中进行通信

- mkfifo:创建

### 7.4存储映射mmap

https://blog.csdn.net/anmengdai0123/article/details/102043345

1. 使一个磁盘文件与存储空间中的一个缓冲区相映射。
2. 当从缓冲区中取数据，就相当于读文件中的相应字节。
3. 将数据存入缓冲区，则相应的字节就自动写入文件。
4. 实现进程间的通信，不同的进程操作同一个文件



- mmap:把文件打开映射到内存地址中
- munmap:取消映射

![image-20210322212824926](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210322212835.png)

```c
//将文件做一个存储映射，映射完后从映射区域读取到stdout
#include <sys/mman.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

#define handle_error(msg) \
   do { perror(msg); exit(EXIT_FAILURE); } while (0)

int main(int argc, char *argv[]){
    char *addr;
    int fd;
    struct stat sb;
    off_t offset, pa_offset;
    size_t length;
    ssize_t s;

    if (argc < 3 || argc > 4) {
       fprintf(stderr, "%s file offset [length]\n", argv[0]);
       exit(EXIT_FAILURE);
    }

    fd = open(argv[1], O_RDONLY);
    if (fd == -1)
       handle_error("open");

    if (fstat(fd, &sb) == -1)           /* To obtain file size */
       handle_error("fstat");
	
    offset = atoi(argv[2]);
    printf("PageSize = %ld\n", sysconf(_SC_PAGE_SIZE));//输出4096的整数倍
    //小于4096的都是0  大于4096的整数倍 pa_offset实际上内存要映射的起始偏移
    pa_offset = offset & ~(sysconf(_SC_PAGE_SIZE) - 1);
       /* offset for mmap() must be page aligned */
    printf("pa_offset = %ld\n", pa_offset);
    if (offset >= sb.st_size) {
       fprintf(stderr, "offset is past end of file\n");
       exit(EXIT_FAILURE);
    }

    if (argc == 4) {
       length = atoi(argv[3]);
       if (offset + length > sb.st_size)
           length = sb.st_size - offset;
               /* Can't display bytes past end of file */

    } else {    /* No length arg ==> display to end of file */
       length = sb.st_size - offset;
    }

    addr = mmap(NULL, length + offset - pa_offset, PROT_READ,
               MAP_PRIVATE, fd, pa_offset);
    if (addr == MAP_FAILED)
       handle_error("mmap");

    s = write(STDOUT_FILENO, addr + offset - pa_offset, length);
    if (s != length) {
       if (s == -1)
           handle_error("write");

       fprintf(stderr, "partial write");
       exit(EXIT_FAILURE);
    }

    munmap(addr, length + offset - pa_offset);
    close(fd);

    exit(EXIT_SUCCESS);
}

```

**<u>内存为什么要做页对齐？</u>**

页作为内存分布的基本单位，现在一般是4096

用户空间上的内存和内核上的内存都是按页划分，做好内存对齐后这样读写数据操作比较快

<img src="https://gitee.com/xsm970228/images2020.9.5/raw/master/20210323142927.png" alt="image-20210323142912341" style="zoom:67%;" />

当需要的是以offset开始的长度为len的内容时，我们真正传输的数据是本页的开始 一直到需要数据的结尾，pa_offset会对齐为此页的开始 所以要传输的真实数据的长度len也会发生改变

<img src="https://gitee.com/xsm970228/images2020.9.5/raw/master/20210323143223.png" alt="image-20210323143221037" style="zoom:67%;" />

<img src="https://gitee.com/xsm970228/images2020.9.5/raw/master/20210323143454.png" alt="image-20210323143452829" style="zoom:67%;" />



### 7.5消息队列msg

https://blog.csdn.net/yang_yulei/article/details/19772649

- msgget:获取消息队列  需要用到和shmget一样的key（ftok）通过相同的key拿到同一块内存
- msgctl:消息队列的管理
- msgsnd：向消息队列发送
- msgrcv：从消息队列接受（移除）读一个少一个

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#include <unistd.h>
#include <errno.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>

struct msgbuf {
    long mtype;
    char mtext[80];
};

static void usage(char *prog_name, char *msg){
    if (msg != NULL)
       fputs(msg, stderr);

    fprintf(stderr, "Usage: %s [options]\n", prog_name);
    fprintf(stderr, "Options are:\n");
    fprintf(stderr, "-s        send message using msgsnd()\n");
    fprintf(stderr, "-r        read message using msgrcv()\n");
    fprintf(stderr, "-t        message type (default is 1)\n");
    fprintf(stderr, "-k        message queue key (default is 1234)\n");
    exit(EXIT_FAILURE);
}

static void send_msg(int qid, int msgtype){
    struct msgbuf msg;
    time_t t;

    msg.mtype = msgtype;

    time(&t);
    snprintf(msg.mtext, sizeof(msg.mtext), "a message at %s",ctime(&t));//snprintf需要带大小

    if (msgsnd(qid, (void *) &msg, sizeof(msg.mtext),
               IPC_NOWAIT) == -1) {
       perror("msgsnd error");
       exit(EXIT_FAILURE);
    }
    printf("sent: %s\n", msg.mtext);
}

static void get_msg(int qid, int msgtype){
    struct msgbuf msg;

    if (msgrcv(qid, (void *) &msg, sizeof(msg.mtext), msgtype,
              MSG_NOERROR | IPC_NOWAIT) == -1) {
       if (errno != ENOMSG) {
           perror("msgrcv");
           exit(EXIT_FAILURE);
       }
       printf("No message available for msgrcv()\n");
    } else
       printf("message received: %s\n", msg.mtext);
}

int main(int argc, char *argv[]){
    int qid, opt;
    int mode = 0;               /* 1 = send, 2 = receive */
    int msgtype = 1;
    int msgkey = 1234;

    while ((opt = getopt(argc, argv, "srt:k:")) != -1) {//t: 指此选项后面还有参数
       switch (opt) {
       case 's':
           mode = 1;
           break;
       case 'r':
           mode = 2;
           break;
       case 't':
           msgtype = atoi(optarg);
           if (msgtype <= 0)
               usage(argv[0], "-t option must be greater than 0\n");
           break;
       case 'k':
           msgkey = atoi(optarg);
           break;
       default:
           usage(argv[0], "Unrecognized option\n");
       }
    }

    if (mode == 0)
       usage(argv[0], "must use either -s or -r option\n");

    qid = msgget(msgkey, IPC_CREAT | 0666);

    if (qid == -1) {
       perror("msgget");
       exit(EXIT_FAILURE);
    }

    if (mode == 2)
       get_msg(qid, msgtype);
    else
       send_msg(qid, msgtype);

    exit(EXIT_SUCCESS);
}

```

### 7.6信号量segment

https://blog.csdn.net/yang_yulei/article/details/19775933

- semget:获得一个信号量，通过同一个key(ftok)访问
- semctl：管理信号量集
- semop:信号量集的原子操作

10个人吃饭只有1个碗，那么等第一个人吃完了第二个才能吃，以此类推

```c

//两个进程跑起来 信号量只有一个 当1进程拿到信号量 用完了 2进程才可以用
#include <unistd.h>
#include <sys/ipc.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <sys/sem.h>

union semun
{
  int val;
  struct semid_ds *buf;
  unsigned short *arry;
};

static int sem_id = 0;
static int set_semvalue();
static void del_semvalue();
static int semaphore_p();
static int semaphore_v();

int main(int argc, char *argv[]) {
    char message = 'X';
    int i = 0;
    //创建信号量
    sem_id = semget((key_t) 1234, 1, 0666 | IPC_CREAT);
    if (argc > 1)
    {   
        //程序第一次执行初始化信号量
        if (!set_semvalue())
        {
            fprintf(stderr, "Failed to initialize semaphore\n");
            exit(EXIT_FAILURE);
        }
        //设置要输出到屏幕中的信息，即其参数的第一个字符
        message = argv[1][0];
        sleep(2);
    }

    for (i = 0; i < 10; ++i)
    {
        //进入临界区
        if (!semaphore_p())
        {
            exit(EXIT_FAILURE);
        }
        //向屏幕输出数据
        printf("After P: %c\n", message);
        //清理缓冲区
        fflush(stdout);
        sleep(10);


        if (!semaphore_v())
        {
            exit(EXIT_FAILURE);
        }
        printf("After V: %c\n", message);
        fflush(stdout);
        sleep(rand() % 2);
    }

    sleep(10);
    printf("\n%d - finished\n", getpid());

    if (argc > 1)
    {
        sleep(3);
        del_semvalue();
    }
    exit(EXIT_SUCCESS);
}

static int set_semvalue()
{
    union semun sem_union;

    sem_union.val = 1;//资源个数
    if (semctl(sem_id, 0, SETVAL, sem_union) == -1)
    {
	perror("123");
        return 0;
    }
    return 1;
}

static void del_semvalue()
{
    union semun sem_union;

    if (semctl(sem_id, 0, IPC_RMID, sem_union) == -1)
    {
        fprintf(stderr, "Failed to delete semaphore\n");
    }
}

static int semaphore_p()
{
    //对信号量做减1操作 等待P（sv）
    struct sembuf sem_b;
    sem_b.sem_num = 0;
    sem_b.sem_op = -1;//P()
    sem_b.sem_flg = SEM_UNDO;
    if (semop(sem_id, &sem_b, 1) == -1)
    {
        fprintf(stderr, "semaphore_p failed\n");
        return 0;
    }

    return 1;
}

static int semaphore_v()
{
    //这是一个释放操作，它使信号量变为可用， 即发送信号V（sv）
    struct sembuf sem_b;
    sem_b.sem_num = 0;
    sem_b.sem_op = 1; // V()
    sem_b.sem_flg = SEM_UNDO;
    if (semop(sem_id, &sem_b, 1) == -1)
    {
        fprintf(stderr, "semaphore_v failed\n");
        return 0;
    }

    return 1;
}

```

- 读者-写者问题
- 银行家问题
- 生产者-消费者问题  
- pv问题 

通过segment模拟人工客服   pv操作自带共享锁

```c
#include "../head/head.h"
#include "../head/tcp_server.h"

#define MAXSER 5
#define MAXCLI 10

static int sem_id = 0;
static int set_semvalue();
static void del_semvalue();
static int semaphore_p();
static int semaphore_v();
int total = 0;

union semnum{
    int val;
    struct semid_ds *buf;
    unsigned short *arry;
};


void *work(void *arg){
    //int *new_fd = (int *)arg;
    //上面的不行 fd全局只有一个！！！！多线程共用
    int new_fd = *(int *)arg;
    semaphore_p();
    char msg[512] = "You can say!\n";
    send(new_fd, msg, strlen(msg), 0);
    while(1){
        memset(msg, 0, sizeof(msg));
        if(recv(new_fd, msg, sizeof(msg), 0) <= 0){
            total--;
            semaphore_v();
            close(new_fd);
            return NULL;
            //exit(0);
            //不能用exit  用exit所有线程都结束
        }
        send(new_fd, msg, strlen(msg), 0);
    }

}


int main(int argc, char **argv){
    if(argc != 2){
        fprintf(stderr, "error\n");
        exit(1);
    }

    if((sem_id = semget((key_t)8731, 1, 0666 | IPC_CREAT)) < 0){
        perror("segment");
        exit(1);
    }
    
    if(!set_semvalue()){
        perror("segment");
        exit(1);
    }
    
    int server_listen, fd;
    if((server_listen = socket_create_tcp(atoi(argv[1]))) < 0){
        perror("socket_create");
        exit(1);
    }

    while(1){
        if((fd = accept(server_listen, NULL, NULL)) < 0){
            perror("accept");
            exit(1);
        }
        total++;
        printf("The %dth client!\n", total);
        send(fd, "You are Here!\n", sizeof("You are Here!\n"), 0);
        
        pthread_t tid;
        pthread_create(&tid, NULL, work, (void *)&fd);
    
    }
    sleep(10000);
    return 0;
}

static int set_semvalue(){
    union semnum sem_union;
    sem_union.val = MAXSER;

    if(semctl(sem_id, 0, SETVAL, sem_union) == -1){
        return 0;
    }
    return 1;
}

static void del_semvalue(){
    union semnum sem_union;

    if(semctl(sem_id, 0, IPC_RMID, sem_union) == -1){
        perror("segment: rmid");
    }
    return ;
}

static int semaphore_p(){
    struct sembuf sem_b;
    sem_b.sem_num = 0;
    sem_b.sem_op = -1;
    sem_b.sem_flg = SEM_UNDO;
    if(semop(sem_id, &sem_b, 1) == -1){
        perror("semop_p");
        return 0;
    }

    return 1;
}
static int semaphore_v(){
    struct sembuf sem_b;
    sem_b.sem_num = 0;
    sem_b.sem_op = 1;
    sem_b.sem_flg = SEM_UNDO;
    if(semop(sem_id, &sem_b, 1) == -1){
        perror("semop_v");
        return 0;
    }

    return 1;
}

```



# 四.I/O

unix通过文件描述符使用统一的设备接口，这种接口允许对终端、磁盘、磁带、音频甚至网络通信使用相同的IO调用

unix已经通过open、close、read、write和ioctl这5个函数为大多数设备提供了标准的访问接口

所有的设备都是由被称为特殊文件的文件表示，位于/dev目录中

io：同步 异步 直接 缓冲 阻塞 非阻塞

## 1.标准IO和文件IO

一、先来了解下什么是文件I/O和标准I/O：

1. **文件I/O：**文件I/O称之为不带缓存的IO（unbuffered I/O)。不带缓存指的是每个read，write都调用内核中的一个系统调用。也就是一般所说的低级I/O——操作系统提供的基本IO服务，与os绑定，特定于linix或unix平台。

2. **标准I/O：**标准I/O是ANSI C建立的一个标准I/O模型，是一个标准函数包和stdio.h头文件中的定义，具有一定的可移植性。标准I/O库处理很多细节。例如缓存分配，以优化长度执行I/O等。标准的I/O提供了三种类型的缓存。

   （1）全缓存：当填满标准I/O缓存后才进行实际的I/O操作。 
   （2）行缓存：当输入或输出中遇到新行符时，标准I/O库执行I/O操作。 
   （3）不带缓存：stderr就是了。

二、二者的区别

   **文件I/O** 又称为低级磁盘I/O，遵循POSIX相关标准。任何兼容POSIX标准的操作系统上都支持文件I/O。**标准I/O**被称为高级磁盘I/O，遵循ANSI C相关标准。只要开发环境中有标准I/O库，标准I/O就可以使用。（Linux 中使用的是GLIBC，它是标准C库的超集。不仅包含ANSI C中定义的函数，还包括POSIX标准中定义的函数。因此，Linux 下既可以使用标准I/O，也可以使用文件I/O）。

   通过文件I/O读写文件时，每次操作都会执行相关系统调用。这样处理的好处是直接读写实际文件，坏处是频繁的系统调用会增加系统开销，标准I/O可以看成是在文件I/O的基础上封装了缓冲机制。先读写缓冲区，必要时再访问实际文件，从而减少了系统调用的次数。

   文件I/O中用文件描述符表现一个打开的文件，可以访问不同类型的文件如普通文件、设备文件和管道文件等。而标准I/O中用FILE（流）表示一个打开的文件，通常只用来访问普通文件。

三、最后来看下他们使用的函数



|      | 标准ＩＯ                              | 文件ＩＯ(低级IO) |
| ---- | ------------------------------------- | ---------------- |
| 打开 | fopen,freopen,fdopen                  | open             |
| 关闭 | fclose                                | close            |
| 读   | getc,fgetc,getchar fgets,gets fread   | read             |
| 写   | putc,fputc,putchar fputs,puts, fwrite | write            |

标准IO与系统IO（文件IO）区别：https://blog.csdn.net/windeal3203/article/details/79083453



## 2.阻塞IO与非阻塞IO

block io

对比进程的阻塞态

当程序调用scanf函数输入时，需要等待数据的输入，这就是阻塞

- block:进程需要的资源没有，需要等待
- 挂起：调度上的挂起 原先执行现在不执行了 把它的状态信息保存下来

IO block：文件阻塞  

- 阻塞IO：自己去装一杯水 即使水龙头没水也要 等到装满水才做其他事情

- 非阻塞IO：自己装水 但是水龙头没水 先去做其他事情 不断询问水是否来了 来了就去装水 

- 同步IO：阻塞IO和非阻塞IO都是同步IO 需要自己完成装水这件事情 （即使有信号机制通知来水了 也要自己去装水）

- 异步IO：去装水但是没水 自己先做其他事情 等宿管阿姨通知已经给你装满水了 你去拿水（水不需要自己装）

  [https://www.cnblogs.com/felixzh/p/10345929.html](https://www.cnblogs.com/felixzh/p/10345929.html)


ioctl：ioctl(fd, FIONBIO, &ul) 设置为非阻塞io

```c
void make_nonblock_ioctl(int fd){
    unsigned long ul = 1;
    ioctl(fd, FIONBIO, &ul);
}
void make_block_ioctl(int fd){
    unsigned long ul = 0;
    ioctl(fd, FIONBIO, &ul);
}

```

fcntl:用于检索和修改打开的文件描述符相关的标志

```c
//直接设置O_NONBLOCK会覆盖读写等其他属性 
void make_nonblock(int fd){
    fcntl(fd, F_SETFL, O_NONBLOCK);
}

//设置为阻塞socket
void make_block(int fd){
    fcntl(fd, F_SETFL, ~O_NONBLOCK);
}

//无bug   bit mask
void make_nonblock1(int fd) {
    int flags;
    if((flags = fcntl(fd, F_GETFL)) < 0){
        return ;
    }
    flags |= O_NONBLOCK;
    fcntl(fd, F_SETFL, flags);
}

void make_block1(int fd) {
    int flags;
    if((flags = fcntl(fd, F_GETFL)) < 0){
        return ;
    }
    flags &= ~O_NONBLOCK;
    fcntl(fd, F_SETFL, flags);
}

```

## 3.缓冲IO

- non-buffered无缓冲  一般用到stderr（但并不是所有的stderr都是无缓冲）
- block-buffered块（全）缓冲  所有的文件操作都是全缓冲
- line-buffered行缓冲 printf行缓冲

块 block（磁盘存储inode等）文件io操作的基本单位

512    1024    2048	4096

如果此时的块block_size = 1024   数据1025   则分成1024 + 1   后面的这个block后面的1023被0填充补满（修整）  如果每个数据都是1025  那么会浪费block，所以我们取的bufsize一般是512 1024等 容易做块对齐

用stat + filename可以查看blocksize

![image-20210314110749932](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314110749932.png)

往./a文件中写入2M的东西 bs = 1表示无缓冲 bs = 4096表示block=4096

标准io都是缓冲io  stdin/stdout/stderr（某些情况无缓冲）



标准io有什么缺陷？两次copy

读文件时，fread有两次copy 文件->kernel->标准IO->用户空间   fwrite也是两次copy

两次copy：用户buffer--stdio buff--kernel（文件）

![image-20210314111215797](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314111215797.png)

setbuf：流缓冲操作  修改文件的缓冲机制

### 内核和缓冲IO

![image-20200409185356464](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192609.png)

写文件时，用户buffer->标准IO（提供了缓冲）->kernel（回写机制）->文件，标准IO提供了缓冲

1. 回写机制： 数据先拿到kernel层，等数据达到一定的量级或延时时间到后 kernel向磁盘写文件 除非是直接IO无回写机制  回写机制往往还有更优的效果，提高了IO效率，当数据还没写到磁盘上时，当我们需要这些数据时，可以直接从kernel中读取
2. 内核最近最常使用算法  内核会对磁盘进行预读  当预读的数据被用户用到时，会有一个正反馈，下次内核预读更多的数据

kenel层在内存中 page cache

0拷贝技术

如果下电影的话，没有这种缓冲机制，那么下一点数据就会向磁盘写数据，磁头一直被占用，那么磁盘一直被占用，不能处理其他关于此磁盘的任务

管道  socket等是在内核上的 没有落到文件上

迅雷边下边播：播的时候可能不是从磁盘上读 是从kernel端读



## 4.IO多路复用（转接）select、poll和epoll



### 4.1引出IO多路复用

> 以下内容参考了https://blog.csdn.net/SkydivingWang/article/details/74917897

我们都知道unix(like)世界里，一切皆文件，而文件是什么呢？文件就是一串二进制流而已，不管socket,还是FIFO、管道、终端，对我们来说，一切都是文件，一切都是流。在信息交换的过程中，我们都是对这些流进行数据的收发操作，简称为I/O操作(input and output)，往流中读出数据，系统调用read，写入数据，系统调用write。不过话说回来了 ，计算机里有这么多的流，我怎么知道要操作哪个流呢？对，就是文件描述符，即通常所说的fd，一个fd就是一个整数，所以，对这个整数的操作，就是对这个文件（流）的操作。我们创建一个socket,通过系统调用会返回一个文件描述符，那么剩下对socket的操作就会转化为对这个描述符的操作。

当我们处理高并发时，即有好多个fd需要我们处理时，我们该怎么做呢？我们想到的是开辟多个进程或者线程对许多fd进行处理，但是多进程并发与多线程并发又存在着下面的一些问题：

<u>**多进程并发：**</u>

- 进程数量有限制
- 代价太高——进程的销毁/切换
- 受限于cpu——一个cpu同一个时间只有一个进程在跑
- 进程间内存隔离
- 进程间通信复杂

**<u>多线程高并发：</u>**

- 受限于cpu——影响响应能力

- 阻塞——某个线程啥也不干，影响响应能力     非阻塞——死循环一直读数据，影响响应能力

  （双十一几十万人同时访问淘宝，服务器也不可能开几十万个线程，引入IO多路复用）

于是，我们引入IO多路复用，其有<u>select</u>、<u>poll</u>和<u>epoll</u>三个函数，这三个函数也会使进程阻塞，select先阻塞，有活动套接字才返回，但是和阻塞I/O不同的是，这三个函数可以同时阻塞多个I/O操作，而且可以同时对多个读操作，多个写操作的I/O函数进行检测，直到有数据可读或可写（就是监听多个socket）。select被调用后，进程会被阻塞，内核监视所有select负责的socket，当有任何一个socket的数据准备好了（比如可读），select就会返回套接字可读，我们就可以调用recv来接受数据。我们平时用到的scanf、read、recv等函数都是阻塞IO，但是他们一次只能监测一个fd，对一个fd进行操作。
**正因为阻塞I/O只能阻塞一个I/O操作，而I/O复用模型能够阻塞多个I/O操作，所以才叫做多路复用。**

### 4.2select函数——同步IO多路复用

select()函数允许一个进程监控多个文件描述符fd，当有一个或多个文件描述符就绪时（即可读可写等），程序对就绪的fd执行相应的操作（读/写等）。select()函数将分别监听三种独立的fd，可读，可写和异常情况的fd。在此函数处会发生阻塞

```c
 int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

- nfds：三种监听的fd的最大值+1

- readfds：监听的可读的文件fd集合

- writefds：监听的可写的文件fd集合

- exceptfds：监听特殊fd集合

- timeout：延时 

  ```c
  struct timeval {
                 long    tv_sec;         /* seconds */
                 long    tv_usec;        /* microseconds */
             };
  
  ```

  

通过下面这四个函数可以创建、操作集合

```c
void FD_CLR(int fd, fd_set *set);//清除监听集合的某个fd
int  FD_ISSET(int fd, fd_set *set);//判断是否在监听集合中
void FD_SET(int fd, fd_set *set);//创建监听集合
void FD_ZERO(fd_set *set);//对监听集合清0
```

select()函数会一直发生阻塞，阻塞结束的条件：

1. 一个文件描述符fd就绪
2. 调用被信号中断
3. 延时结束

返回值：

- retval>0：有retval个fd就绪
- retval=0：超出延时
- retval<0：发生错误



```c
//eg
#include <stdio.h>
#include <stdlib.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>

    
int main(void){

    fd_set rfds;
    struct timeval tv;
    int retval;

    /* Watch stdin (fd 0) to see when it has input. */
    //设置监听集合
    FD_ZERO(&rfds);
    FD_SET(0, &rfds);//0为stdin

    /* Wait up to five seconds. */

    tv.tv_sec = 5;
    tv.tv_usec = 0;

    retval = select(1, &rfds, NULL, NULL, &tv);//std放在readfds处，监听是否可读
    /* Don't rely on the value of tv now! */

    if (retval == -1)//错误
        perror("select()");
    else if (retval){//有fd就绪
        //此时数据是ready的 还在标准IO的缓冲中，并没有读
        //如果不读，程序结束后，缓冲区的东西到达内核的stdin
        printf("Data is available now.\n");
        //修改部分
        char buff[512] = {0};
        scanf("%s", buff);
        //修改部分
    }
        /* FD_ISSET(0, &rfds) will be true. */
    else//超时
        printf("No data within five seconds.\n");
     
    exit(EXIT_SUCCESS);
}
```

**<u>Tips：</u>**

在使用远程服务器时，我想输入大写的c需要按shift+c 此时网络畅通的情况下就会出现大写的C

而当网络不畅通时，就变成了先转换为中文输入法，再输入拼音c呢？

网络不通时，快捷键的组合可能会被拆开或者乱序，为什么？

> 我猜测这个有一种机制没有仔细研究过啊，就正常情况下，我们按键shift加一个字母键的时候，实际上这两个键然后按下去之后，底层处理的时候，它应该也是两个键，只是因为它的间隔时间较短，或者说它时间有重叠性，导致系统会把它识别为一个组合键。当我们的系统变得卡顿的时候呢，相当于是IO事件的响应时间变长并且不稳定，这会导致我们正常正常情况操作的。他会认为是一个组合键的，就会被系统的识别为是两个单独文件，所以导致这种现象。



使用select_connect_timeout实现定时登录

```c
//实现定时登陆
//登陆体现在connect上，而connect是阻塞的俄 没法定时
//首先要先改成非阻塞的 然后定时
//定时用select

int socket_connect_timeout(char *host, int port, long timeout){
    int sockfd;
    if((sockfd = socket(AF_INET, SOCK_STREAM, 0)) < 0){
        return -1;
    }

    struct sockaddr_in server;
    server.sin_family = AF_INET;
    server.sin_port = htons(port);
    server.sin_addr.s_addr = inet_addr(host);
    
    make_nonblock(sockfd);//设置为非阻塞socket
    
    fd_set wfds;
    FD_ZERO(&wfds);
    FD_SET(sockfd, &wfds);
    struct timeval tv;
    tv.tv_sec = 0;
    tv.tv_usec = timeout;
    if(connect(sockfd, (struct sockaddr *)&server, sizeof(server)) < 0){
        int error = -1, len = sizeof(int);
        int retval = select(sockfd + 1, NULL, &wfds, NULL, &tv);
        if(retval < 0){
            perror("select");
            close(sockfd);
            return -1;
        }else if(retval){
            //ok tcp建立连接有三次握手 第一次握手就可以写的话不一定就是成功建立tcp连接了
            //用getsockopt获取socket的状态检验tcp是否成功建立
            if(getsockopt(sockfd, SOL_SOCKET, SO_ERROR, &error, (socklen_t *)&len) < 0){
                close(sockfd);
                return -1;
            }else if(error){
                close(sockfd);
                return -1;
            }
        }else{
            //timeout
            printf("Connect Time Out!\n");
            close(sockfd);
            return -1;
        }
    }
    printf("socket connected!\n");
    make_block(sockfd);
    return sockfd;
    
}

```

```c
//eg:用select函数写一个服务器，用来接受多个客户端发来的消息并给客户端发送对应信息
#include "../head/head.h"
#include "../head/tcp_server.h"
#include "../head/common.h"
#define CLIENTSIZE 100
#define BUFSIZE 1024
//小写->大写
char ch_char(char c){
    if(c >= 'a' && c <= 'z') return c - 32;
    return c;
}

int main(int argc, char **argv){
    if(argc != 2){
        fprintf(stderr, "Usage: %s port!\n", argv[0]);
        exit(1);
    }
    int server_listen, fd;
    
    if((server_listen = socket_create_tcp(atoi(argv[1]))) < 0){//将socket bind listen封装
        perror("socket_create");
        exit(1);
    }
    
    int client[CLIENTSIZE] = {0}, max_fd;
    make_nonblock(server_listen);
    memset(client, -1, sizeof(client));
    //client[server_listen] = server_listen;
    fd_set rfds, wfds, efds;
    max_fd = server_listen;

    while(1){
        FD_ZERO(&rfds);
        FD_ZERO(&wfds);
        FD_ZERO(&efds);

        FD_SET(server_listen, &rfds);
        for(int i = 0; i < CLIENTSIZE; i++){
            if(client[i] == server_listen) continue;
            if(client[i] > 0){
                if(client[i] > max_fd) max_fd = client[i];
                FD_SET(client[i], &rfds);
            }
        }
        if(select(max_fd + 1, &rfds, NULL, NULL, NULL) < 0){
            perror("select");
            return 1;
        }
        if(FD_ISSET(server_listen, &rfds)){
            printf("New Client!\n");
            if((fd = accept(server_listen, NULL, NULL)) < 0){
                perror("accept");
                return 1;
            }
            //数组下标对用fd
            if(fd > CLIENTSIZE){
                printf("Too many clients!\n");
                close(fd);
            }else{
                make_nonblock(fd);
                if(client[fd] == -1){
                    client[fd] = fd;
                }
            }
        }
        for(int i = 0; i < CLIENTSIZE; i++){
            if(i == server_listen) continue;
            if(FD_ISSET(i, &rfds)){
                char buf[BUFSIZE] = {0};
                int retval = recv(i, buf, BUFSIZE, 0);
                if(retval <= 0){
                    printf("Logout!\n");
                    client[i] = -1;
                    close(i);
                    continue;
                }
                printf("recv : %s\n", buf);
                for(int j = 0; j < strlen(buf); j++){
                    buf[j] = ch_char(buf[j]);
                }
                send(i, buf, strlen(buf), 0);
            }
        }
        
    }
    
    return 0;
}

```



### 4.3poll函数——等待一个fd上的events

poll()函数执行与select类似的任务，等待一个fd集合中的一个fd变成就绪状态

```c
int poll(struct pollfd *fds, nfds_t nfds, int timeout);
```

- fds：在fds参数中指定要监控的文件描述符

  ```c
  struct pollfd {
                 int   fd;         /* file descriptor */ //文件描述符
                 short events;     /* requested events */ //要监听的时间 可读/写？
                 short revents;    /* returned events */   //反馈 当可读/写时会通过这个参数反馈
             };
  ```

- nfds: 要监听的fd的个数，一般就是struct pollfd结构体集合的大小

- timeout：延时ms

常用的events：

- POLLIN：可读
- POLLOUT：可写
- POLLERR：错误
- POLLHUP：挂起

阻塞结束条件和返回值与select()类似

```c
//eg:用poll函数写一个服务器，用来接受多个客户端发来的消息并给客户端发送对应信息
#include"../1.select将connect设置为非阻塞/common/head.h"
#include"../1.select将connect设置为非阻塞/common/common.h"
#include"../1.select将connect设置为非阻塞/common/tcp_server.h"
#include<poll.h>

#define POLLSIZE 100
#define BUFSIZE 1024
//小写->大写
char ch_char(char c){
    if(c >= 'a' && c <= 'z') return c - 32;
    return c;
}

int main(int argc, char **argv){
    if(argc != 2){
        fprintf(stderr, "Usage: %s port!\n", argv[0]);
        exit(1);
    }
    int server_listen, fd;
    
    if((server_listen = socket_create(atoi(argv[1]))) < 0){//将socket bind listen封装
        perror("socket_create");
        exit(1);
    }
    
    struct pollfd event_set[POLLSIZE];
    for(int i = 0; i < POLLSIZE; i++){
        event_set[i].fd = -1;
    }
    //用0号event_set监听server_listen
    event_set[0].fd = server_listen;
    event_set[0].events = POLLIN;
    
    while(1){
        //一次while循环只会加上一个client
        int retval;
        if((retval = poll(event_set, POLLSIZE, -1)) < 0){
            perror("poll");
            return 1;
        }
        //如果server_listen就绪，需要向结构体数组里加入客户端fd，以便进行监听
        if(event_set[0].revents & POLLIN){
            if((fd = accept(server_listen, NULL, NULL)) < 0){
                perror("accept");
                continue;
            }
            retval--;
            //找到最小的下标存放fd
            int i;
            for(i = 1; i < POLLSIZE; i++){
                if(event_set[i].fd < 0){
                    event_set[i].fd = fd;
                    event_set[i].events = POLLIN;//对于服务端，要对fd进行读操作
                    break;
                }    
            }
            if(i == POLLSIZE){
                printf("Too many client!\n");
                close(fd);
            }

        }
        //当有fd就绪时，要对就绪的fd进行读操作并且给客户端发送对应的大写信息
        for(int i = 1; i < POLLSIZE; i++){
            if(event_set[i].fd < 0) continue;
            if(event_set[i].revents & (POLLIN | POLLHUP | POLLERR)){
                retval--;
                char buff[BUFSIZE] = {0};
                if(recv(event_set[i].fd, buff, BUFSIZE, 0) > 0){
                    printf("Recv: %s\n", buff);
                    for(int i = 0; i < strlen(buff); i++){
                        buff[i] = ch_char(buff[i]);
                    }
                    send(event_set[i].fd, buff, strlen(buff), 0);
                    
                }else{//当recv失败时，说明这个客户端已经下线，清楚对应的event_set
                    close(event_set[i].fd);
                    event_set[i].fd = -1;
                }
            }
            if(retval < 0) break;
        }
    }

    return 0;
}

```

### 4.4epoll函数——IO事件通知

epoll()函数执行与poll函数相同的任务：监听多个文件描述符。其有两种模式：

1. Level_triggered(水平触发)：当被监控的文件描述符上有可读写事件发生时，epoll_wait()会通知处理程序去读写。如果这次没有把数据一次性全部读写完(如读写缓冲区太小)，那么下次调用 epoll_wait()时，它还会通知你在上没读写完的文件描述符上继续读写，当然如果你一直不去读写，它会一直通知你！！！如果系统中有大量你不需要读写的就绪文件描述符，而它们每次都会返回，这样会大大降低处理程序检索自己关心的就绪文件描述符的效率！！！

2. Edge_triggered(边缘触发)：当被监控的文件描述符上有可读写事件发生时，epoll_wait()会通知处理程序去读写。如果这次没有把数据全部读写完(如读写缓冲区太小)，则会发生阻塞，直到该文件描述符上出现第二次可读写事件才会通知你！！（即有新的数据可读）！这种模式比水平触发效率高，系统不会充斥大量你不关心的就绪文件描述符！！！所以我们用边缘触发时一定记得设置为非阻塞IO  

   - 用于非阻塞IO
   - 用于read和write返回EAGAIN之后

   ![image-20210802154600892](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210802154609.png)

   

我们要用到以下3个函数：

1. ```c
   int epoll_create(int size);//创建一个新的epoll实例
   //程序是磁盘中的文件在内存的镜像（实例）
   ```

2. ```c
   int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
   //该系统调用对文件描述符epfd引用的epoll（7）实例执行控制操作。 它要求对目标文件描述符fd执行操作op。
   ```

   - epfd:通过epoll_create()创建的实例

   - op:对epoll的操作

     - EPOLL_CTL_ADD：注册（添加）一个新的fd在epoll实例中
     - EPOLL_CTL_MOD：改变对应fd的event参数（比如由原来监听可读->可写）
     - EPOLL_CTL_DEL：从epoll实例中将对应的fd删除，不再监听

   - fd:要对epoll实例中的哪个实例进行操作

   - event:描述一个fd的状态

     ```c
     typedef union epoll_data {
                    void        *ptr;
                    int          fd;
                    uint32_t     u32;
                    uint64_t     u64;
                } epoll_data_t;
     
                struct epoll_event {
                    uint32_t     events;      /* Epoll events */
                    epoll_data_t data;        /* User data variable */
                };
     ```

3. ```c
   int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
   //等待epoll实例中fd集合的事件发生
   //执行后epoll实例中所有要监听的fd的状态都放在传出参数events中
   //其中events.data.fd = fd, events.events=可读/写/挂起/错误等
   ```

   - epfd:创建的epfd实例
   - events：传出参数
   - maxevents：要监听的fd的数量
   - timeout：延时

阻塞结束条件和返回值跟select类似

```c
//eg:用epoll写一个服务器
#include <sys/epoll.h>
#define MAX_EVENTS 10
#include "../1.select将connect设置为非阻塞/common/head.h"
#include "../1.select将connect设置为非阻塞//common/tcp_server.h"
#include "../1.select将connect设置为非阻塞//common/common.h"
#define BUFFSIZE 512

int main(int argc, char **argv) {
    //ev负责向epollfd里面注册
    //events数组用来接受监控的结果
    struct epoll_event ev, events[MAX_EVENTS];
    int listen_sock, conn_sock, nfds, epollfd;
    char buff[BUFFSIZE] = {0};
    if (argc != 2) exit(1);
    listen_sock = socket_create(atoi(argv[1]));
/* Code to set up listening socket, 'listen_sock',
   (socket(), bind(), listen()) omitted */
    epollfd = epoll_create1(0);//创建epoll实例，一开始size=0
    if (epollfd == -1) {
        perror("epoll_create1");
        exit(EXIT_FAILURE);
    }
//将listen_sock注册进epollfd实例
    ev.events = EPOLLIN;
    ev.data.fd = listen_sock;

    if (epoll_ctl(epollfd, EPOLL_CTL_ADD, listen_sock, &ev) == -1) {
        perror("epoll_ctl: listen_sock");
        exit(EXIT_FAILURE);
    }

    for (;;) {
        nfds = epoll_wait(epollfd, events, MAX_EVENTS, -1);

        if (nfds == -1) {
            perror("epoll_wait");
            exit(EXIT_FAILURE);
        }
		//先找是否有新加入的client
        for (int n = 0; n < nfds; ++n) {
            //来一个client往epollfd注册一个
            if (events[n].data.fd == listen_sock) {
                conn_sock = accept(listen_sock, NULL, NULL);
                if (conn_sock == -1) {
                    perror("accept");
                    exit(EXIT_FAILURE);
                }
                make_nonblock(conn_sock);
                ev.events = EPOLLIN | EPOLLET;
                ev.data.fd = conn_sock;

                if (epoll_ctl(epollfd, EPOLL_CTL_ADD, conn_sock, &ev) == -1) {
                    perror("epoll_ctl: conn_sock");
                    exit(EXIT_FAILURE);
                }
            //当epollfd监测的sockfd有数据就绪时，接受转化为大写发送    
            } else {
                //do_use_fd(events[n].data.fd);
                if (events[n].events & (EPOLLIN | EPOLLHUP | EPOLLERR)) {
                    memset(buff, 0, sizeof(buff));
                    if (recv(events[n].data.fd, buff, BUFFSIZE, 0) > 0) {
                        printf("recv: %s", buff);
                        for (int i = 0; i < strlen(buff); i++) {
                            if (buff[i] >= 'a' && buff[i] <= 'z') buff[i] -= 32;
                        }
                        send(events[n].data.fd, buff, strlen(buff), 0);
                        //当没有数据接受时，说明这个sockfd已经关闭，将这个sockfd从epollfd删除
                    } else {
                        if (epoll_ctl(epollfd, EPOLL_CTL_DEL, events[n].data.fd, NULL) < 0) {
                            perror("epoll_ctrl");
                        }
                        close(events[n].data.fd);
                        printf("Logout!\n");
                    }
                }
            }
        }
    }
    return 0;
}

```

### 4.5三个函数的比较

> 以下内容参考了https://blog.csdn.net/sinat_36629696/article/details/81186774
>
> 

1. select 的参数类型 fd_set 没有将文件描述符和事件绑定 , 只是一个文件描述符集合 , 因此 select 需要提供三个此类型的参数来分别传入和输出可读 , 可写 , 异常等事件 , 这使得 select 不能处理更多类型的事件 , 又因为内核对 fd_set 集合的在线修改 , 下次调用 select 之前必须重置 这 3 个 fd_set 集合

   - 每次调用select，都需要把fd集合从用户态拷贝到内核态，这个开销在fd很多时会很大
   - 同时每次调用select都需要在内核遍历传递进来的所有fd，这个开销在fd很多时也很大
   - select支持的文件描述符数量太小了，默认是1024

2. poll 的参数类型 pollfd 将文件描述符和事件绑定 , 任何事件都被统一处理 , 并且内核每次修改的是 pollfd 结构体的 revents 成员 , 而 events 成员保持不变 , 因此下次调用 poll 时无需重置 pollfd 类型的事件集参数

3. epoll则采用与select和poll完全不同的方式来管理用户注册的事件。它在内核中维护一个事件表，并提供了一个独立的系统调用用epoll_ctl来控制往其中添加、删除、修改事件。这样，每次epoll_wait调用都直接从该内核事件表中取得用户注册的事件，而无须反复从用户空间读入这些事件。epoll_wait系统调用的events参数仅用来返回就绪的事件，这使得应用程序索引就绪文件描述符的时间复杂度达到o(1)

   epoll 适用于连接数量多 , 但活动连接较少的情况 , 因为当活动连接较多时 , 回调函数的触发过于频繁 , 其效率未必高于 select 和 poll

![image-20200412124735754](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192627.png)



# 五.网络编程socket

![image-20210314135350188](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314135350188.png)

netstat -aptn：*Netstat*是在内核中访问网络连接状态及其相关信息的程序，它能提供TCP连接，TCP和UDP监听，进程内存管理的相关报告。

telnet IP port 用于远端测试

## 1.相关函数

1. socket

   ![image-20210314141844882](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314141844882.png)

   socket是与端口绑定的

   系统起来就是一个大楼，新复制的进程没有门，他想要实现网络通信就需要分配一个端口（安个门）

   domain(域)：ipv4  ipv6 本地 路由

   type：stream（tcp） dgram（udp）

   protocol：前面已经决定了写0就行

2. bind

   ![image-20210314142548493](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314142548493.png)

   

   绑定具体的端口号 绑定自己的IP （大型项目多个网卡）

   ![image-20210314142656551](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314142656551.png)

   ![image-20210314143006426](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314143006426.png)

   ![image-20210314143206502](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314143206502.png)

   端口号 16uint 65535

   大端高位放在低地址

   主机字节序可能是大端，可能是小端

   网络字节序肯定是大端

   所以编程时一定要作字节序转换，因为主机不知道是大端和小端

3. listen

   ![image-20210314144835291](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314144835291.png)

   将主动的socket转换为被动的socket，等待连接

   backlog的含义？backlog是运输层定义的东西

   下面accept可以处理的队列的长度，当多个客户端

   

4. accept

   ![image-20210314144935057](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314144935057.png)

   addr能够接受对方的地址 当不想接受地址时可以写NULL

   如果accept只跟一个人交互，那么其他人想连接你就连接不了了，所以accpt跟一人连接成功后会返回一个新的socket来处理跟这个人的交互，原来的socket继续等待别人连接

   比如电脑游戏中的npc  npc带你去完成任务时别人找npc就不在了吗？

5. connect

   ![image-20210314145623680](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314145623680.png)

   创建socket后主动连接别人 不需要bind

6. getpeername

   获取对端地址

   ![image-20210314145823801](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314145823801.png)

7. gethostname

   ![image-20210314145929127](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314145929127.png)

8. close 关闭socket

9. send

   ![image-20210314150007375](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314150007375.png)

10. recv

    ![image-20210314150030481](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314150030481.png)
    
11. telnet ip port 基于tcp的网络连接 可以用它来测试tcp服务端

## 2.TCP_example

![image-20210317103347149](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210317103352.png)

server.c

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<arpa/inet.h>
int main(int argc, char **argv){
    if(argc != 2){
        fprintf(stderr, "Usage: %s port\n", argv[0]);
        exit(1);
    }
    int port, server_listen;
    port = atoi(argv[1]);
    if((server_listen = socket(AF_INET, SOCK_STREAM, 0)) < 0){
        perror("socket");
        exit(1);
    }
    printf("Socket Created\n");
    struct sockaddr_in server;
    server.sin_family = AF_INET;
    server.sin_port = htons(port);
    server.sin_addr.s_addr = INADDR_ANY;
    if(bind(server_listen, (struct sockaddr *)&server, sizeof(server)) < 0){
        perror("bind");
        exit(1);
    }
    printf("Socket bind\n");
    if(listen(server_listen, 20) < 0){
        perror("server");
        exit(1);
    }
    //while有bug 当accept到一个socket时，程序会阻塞到recv  如果recv不结束
    //accpte就不会执行 另外的socket就无法连接到
    while(1){
        int sockfd;
        if((sockfd = accept(server_listen, NULL, NULL)) < 0){
            perror("accept");
            close(sockfd);
            continue;
        }
        char name[20] = {0};
        if(recv(sockfd, name, sizeof(name), 0) <= 0){
            close(sockfd);
            continue;
        }
        printf("%s\n", name);
        close(sockfd);
    }

}

```

client.c

```c

#include<stdio.h>
#include<arpa/inet.h>
#include<stdlib.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<unistd.h>

int main(int argc, char **argv){
    int sockfd, port;
    struct sockaddr_in server;
    if(argc != 3){
        fprintf(stderr, "Usage: %s ip port", argv[0]);
        exit(1);
    }
    port = atoi(argv[2]);

    server.sin_family = AF_INET;
    server.sin_port = htons(port);
    server.sin_addr.s_addr = inet_addr(argv[1]);

    if((sockfd = socket(AF_INET, SOCK_STREAM, 0)) < 0){
        perror("socket");
        exit(1);
    }
    if(connect(sockfd, (struct sockaddr *)&server, sizeof(server)) < 0){
        perror("connect");
        exit(1);
    }
    if(send(sockfd, "xsm", sizeof("xsm"), 0) < 0){
        perror("send");
        exit(1);
    }
    close(sockfd);
    return 0;
}

```

优化：while有bug 当accept到一个socket时，程序会阻塞到recv  如果recv不结束  accpte就不会执行 另外的socket就无法连接到

2.server_better.c

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<arpa/inet.h>
#include<sys/wait.h>
int main(int argc, char **argv){
    if(argc != 2){
        fprintf(stderr, "Usage: %s port\n", argv[0]);
        exit(1);
    }
    int port, server_listen;
    port = atoi(argv[1]);
    if((server_listen = socket(AF_INET, SOCK_STREAM, 0)) < 0){
        perror("socket");
        exit(1);
    }
    printf("Socket Created\n");
    struct sockaddr_in server;
    server.sin_family = AF_INET;
    server.sin_port = htons(port);
    server.sin_addr.s_addr = INADDR_ANY;
    if(bind(server_listen, (struct sockaddr *)&server, sizeof(server)) < 0){
        perror("bind");
        exit(1);
    }
    printf("Socket bind\n");
    if(listen(server_listen, 20) < 0){
        perror("server");
        exit(1);
    }
    //while有bug 当accept到一个socket时，程序会阻塞到recv  如果recv不结束
    //accpte就不会执行 另外的socket就无法连接到
    while(1){
        int sockfd;
        if((sockfd = accept(server_listen, NULL, NULL)) < 0){
            perror("accept");
            close(sockfd);
            continue;
        }
        pid_t pid;
        //注意打括号！！！
        //不加括号 父进程和子进程的pid都是0
        if((pid = fork()) < 0){
            perror("fork");
            continue;
        }
        if(pid == 0){
            close(server_listen);
            char name[20] = {0};
            if(recv(sockfd, name, sizeof(name), 0) <= 0){
                perror("recv");
                close(sockfd);
            }
            printf("name = %s\n", name);
            exit(0);
        }
    }

    return 0;

}

```

### 僵尸进程的处理

但是上述的服务端会产生大量的僵尸进程

![image-20210314171114643](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210314171114643.png)

- 僵尸进程：孩子死了 父亲活着  
-  孤儿进程：孩子活着  父亲死了 

如何解决僵尸进程？

僵尸进程太多会导致进程表里面条目满了，进而导致系统崩溃，倒是不占用系统资源。它需要等到父进程来收尸

使用`signal(SIGCHLD, SIG_IGN);`来处理僵尸进程，在父进程中加入这个就可以防止僵尸进程的产生

- SIGCHLD信号
  子进程结束时, 父进程会收到这个信号。
  如果父进程没有处理这个信号，也没有等待(wait)子进程，子进程虽然终止，但是还会在内核进程表中占有表项，这时的子进程称为僵尸进程。这种情 况我们应该避免(父进程或者忽略SIGCHILD信号，或者捕捉它，或者wait它派生的子进程，或者父进程先终止，这时子进程的终止自动由init进程 来接管)。

- SIG_ING

  忽略的意思

通过signal(SIGCHLD, SIG_IGN)通知内核对子进程的结束不关心，由内核回收。

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<arpa/inet.h>
#include<sys/wait.h>
#include<signal.h>
int main(int argc, char **argv){
    if(argc != 2){
        fprintf(stderr, "Usage: %s port\n", argv[0]);
        exit(1);
    }
    int port, server_listen;
    port = atoi(argv[1]);
    if((server_listen = socket(AF_INET, SOCK_STREAM, 0)) < 0){
        perror("socket");
        exit(1);
    }
    printf("Socket Created\n");
    struct sockaddr_in server;
    server.sin_family = AF_INET;
    server.sin_port = htons(port);
    server.sin_addr.s_addr = INADDR_ANY;
    if(bind(server_listen, (struct sockaddr *)&server, sizeof(server)) < 0){
        perror("bind");
        exit(1);
    }
    printf("Socket bind\n");
    if(listen(server_listen, 20) < 0){
        perror("server");
        exit(1);
    }
    //子进程由内核回收
    signal(SIGCHLD, SIG_IGN);

    //while有bug 当accept到一个socket时，程序会阻塞到recv  如果recv不结束
    //accpte就不会执行 另外的socket就无法连接到
    while(1){
        int sockfd;
        if((sockfd = accept(server_listen, NULL, NULL)) < 0){
            perror("accept");
            close(sockfd);
            continue;
        }
        pid_t pid;
        if((pid = fork()) < 0){
            perror("fork");
            continue;
        }
        if(pid == 0){
            close(server_listen);
            char name[20] = {0};
            if(recv(sockfd, name, sizeof(name), 0) <= 0){
                printf("--------\n");
                perror("recv");
                close(sockfd);
            }
            printf("name = %s\n", name);
            exit(0);
        }

    }

    return 0;

}

```

## 3.地址重用和延时关闭

<img src="https://gitee.com/xsm970228/images2020.9.5/raw/master/20210316211444.png" alt="image-20210316211341836" style="zoom:67%;" />

tcp四次挥手过程：

**【注意】中断连接端可以是Client端，也可以是Server端。**

假设Client端发起中断连接请求，也就是发送FIN报文。Server端接到FIN报文后，意思是说"我Client端没有数据要发给你了"，但是如果你还有数据没有发送完成，则不必急着关闭Socket，可以继续发送数据。所以你先发送ACK，"告诉Client端，你的请求我收到了，但是我还没准备好，请继续你等我的消息"。这个时候Client端就进入FIN_WAIT状态，继续等待Server端的FIN报文。当Server端确定数据已发送完成，则向Client端发送FIN报文，"告诉Client端，好了，我这边数据发完了，准备好关闭连接了"。Client端收到FIN报文后，"就知道可以关闭连接了，但是他还是不相信网络，怕Server端不知道要关闭，所以发送ACK后进入TIME_WAIT状态，如果Server端没有收到ACK则可以重传。“，Server端收到ACK后，"就知道可以断开连接了"。Client端等待了2MSL后依然没有收到回复，则证明Server端已正常关闭，那好，我Client端也可以关闭连接了。Ok，TCP连接就这样关闭了！

由上述可以看出谁先关闭谁最后会进入一个TIME_WAIT状态，当服务器断开时，最后会有一个TIME_WAIT的状态，此时端口会被一直占用，浪费资源

<img src="https://gitee.com/xsm970228/images2020.9.5/raw/master/20210316211742.png" alt="image-20210316211738083" style="zoom:67%;" />

当端口被占用了怎么办？

- 地址重用
- 延时关闭：当我们调用close（sockfd）时，主机会向对端进行tcp的四次挥手，我们为了防止端口占用可以选择暴力关闭  暴力关闭时会立马收到RST信号

1. setsockopt
2. getsockopt

setsockopt :https://blog.csdn.net/u012398613/article/details/51914596 

https://www.cnblogs.com/eeexu123/p/5275783.html

```c
//放在socket申请之后 在bind之前    
//设置暴力关闭 延时设置为0表示立刻关闭
    struct linger m_linger;
    m_linger.l_onoff = 1;
    m_linger.l_linger = 0;
    if((setsockopt(server_listen, SOL_SOCKET, SO_LINGER, &m_linger, (socklen_t)sizeof(m_linger))) < 0){
        perror("setsockopt");
        return -1;
    }

    //地址重用
    int flag = 1;
    if(setsockopt(server_listen, SOL_SOCKET, SO_REUSEADDR, &flag, sizeof(int)) < 0){
        perror("setsockopt");
        return -1;
    }

```



## 4.UDP

比tcp更简单 不需要连接 只要发送数据就可以 不过不能保证数据是否丢失 是否准确 

在使用带宽上不会受到像tcp一样的拥塞控制和流量控制

![image-20200528181333516](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140956.png)

简单 使用带宽不会有拥塞控制

丢包不会重发

tcp和udp是两套端口 8888可以被tcp/udp同时使用

- sendto：

  ![image-20200528182058523](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314141000.png)

  dest_addr发送到哪个地址

- recvfrom：

  ![image-20200528182230089](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314141002.png)

  src_addr client的地址数据

上层需要简单的封装，保证数据包顺序的准确性

```c
//server_udp.c
#include<stdio.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<unistd.h>
#include<string.h>
#include<stdlib.h>
#include<arpa/inet.h>
int main(int argc, char **argv){
    
    int port, sockfd;
    port = atoi(argv[1]);
    //SOCK_DGRAM udp
    if((sockfd = socket(AF_INET, SOCK_DGRAM, 0)) < 0){
        perror("socket");
        return -1;
    }

    struct sockaddr_in server;
    server.sin_family = AF_INET;
    server.sin_port = htons(port);
    server.sin_addr.s_addr = INADDR_ANY;

    if(bind(sockfd, (struct sockaddr *)(&server), sizeof(server)) < 0){
        perror("bind");
        return -1;
    }

    struct sockaddr_in client;
    socklen_t addrlen = sizeof(client);
    memset(&client, 0, sizeof(struct sockaddr_in));
    while(1){
        char buf[512] = {0};
        if(recvfrom(sockfd, buf, sizeof(buf), 0, (struct sockaddr *)(&client), &addrlen) > 0){
            printf("[%s] : %s\n", inet_ntoa(client.sin_addr), buf);
        }
        sendto(sockfd, buf, strlen(buf), 0, (struct sockaddr *)(&client), addrlen);
    }


    return 0;
}

```

```c
//client_udp.c
#include<stdio.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<unistd.h>
#include<string.h>
#include<stdlib.h>
#include<arpa/inet.h>
int main(int argc, char **argv){
    
    int port, sockfd;
    port = atoi(argv[2]);
    if((sockfd = socket(AF_INET, SOCK_DGRAM, 0)) < 0){
        perror("socket");
        return -1;
    }

    struct sockaddr_in server;
    server.sin_family = AF_INET;
    server.sin_port = htons(port);
    server.sin_addr.s_addr = inet_addr(argv[1]);
    socklen_t addrlen = sizeof(server);
    while(1){
        char buf[512] = {0};
        scanf("%s", buf);
        if(sendto(sockfd, buf, sizeof(buf), 0, (struct sockaddr *)(&server), addrlen) > 0){
            memset(buf, 0, sizeof(buf));
            if(recvfrom(sockfd, buf, sizeof(buf), 0, (struct sockaddr *)(&server), &addrlen) > 0){
                printf("%s\n", buf);
            }
        }
    }


    return 0;
}

```

## 5.文件传输问题

普通文件传输：

```cpp
//server.c
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<arpa/inet.h>
#include<sys/stat.h>
#include<fcntl.h>
#include<string.h>
#define BUFSIZE 4096

int main(int argc, char **argv){
    int server_listen, port;
    if(argc != 2){
        fprintf(stderr, "Usage : %s port\n", argv[0]);
        exit(1);
    }
    port = atoi(argv[1]);
    if((server_listen = socket(AF_INET, SOCK_STREAM, 0)) < 0){
        perror("socket");
        exit(1);
    }
    struct sockaddr_in server;
    server.sin_family = AF_INET;
    server.sin_port = htons(port);
    server.sin_addr.s_addr = INADDR_ANY;
    if(bind(server_listen, (const struct sockaddr *)&server, sizeof(server)) < 0){
        perror("bind");
        exit(1);
    }

    if(listen(server_listen, 20) < 0){
        perror("listen");
        exit(1);
    }

    int sockfd = accept(server_listen, NULL, NULL);
    
    char buf[BUFSIZE] = {0};
    
    FILE *fp = fopen("newfile", "wb");
    int n;
    while((n = recv(sockfd, buf, BUFSIZE, 0)) > 0){
        fwrite(buf, 1, n, fp);
        //n一定是接收到多少数据就写多少数据
        //strlen一定以\0为结尾 如果数组末尾没有\0会超出范围的写入
        //二进制形式下可能有很多\0读入 一旦按照strlen写 那么\0后边的数据就没了
        printf("n = %d, strlen(buf) = %ld\n", n, strlen(buf));
        memset(buf, 0, sizeof(buf));
    }
    
    close(server_listen);
    close(sockfd);
    fclose(fp);
    return 0;
}
```

```cpp
//client.c
#include<stdio.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<stdlib.h>
#include<fcntl.h>
#include<unistd.h>
#include<arpa/inet.h>
#include<sys/stat.h>
#include<string.h>
#define BUFSIZE 256

int main(int argc, char **argv){
    if(argc != 4){
        fprintf(stderr, "Usage : %s ip port filename\n", argv[0]);
        exit(1);
    }
    int sockfd, port, fd;
    port = atoi(argv[2]);
    if((sockfd = socket(AF_INET, SOCK_STREAM, 0)) < 0){
        perror("socket");
        exit(1);
    }
    struct sockaddr_in server;
    server.sin_family = AF_INET;
    server.sin_port = htons(port);
    server.sin_addr.s_addr = inet_addr(argv[1]);
    

    if(connect(sockfd, (struct sockaddr *)&server, sizeof(server)) < 0){
        perror("connect");
        exit(1);
    }

    FILE *fp = fopen(argv[3], "rb");
    char buf[BUFSIZE] = {0};
    int n = 0;
    while((n = fread(buf, 1, BUFSIZE, fp)) > 0){
        //\0后边有数据也需要发送！！
        send(sockfd, buf, n, 0);
        memset(buf, 0, sizeof(buf));
    }

    close(sockfd);
    fclose(fp);
    return 0;
}

```

![image-20210403093744450](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403093753.png)

当我们用TCP传输大文件时，底层tcp会将大文件切分成多个包进行传输

![image-20200402195329060](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140454.png)

由上图知不是每一次传送的packet不一定是完整的

![image-20210403094157292](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403094206.png)

刚好是一个整包，直接提交给上层

下面是粘包问题：

![image-20210403094308534](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403094445.png)

![image-20210403094330834](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403094457.png)

![image-20210403094352476](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403094503.png)

packet_t中的数据不止一个包 

![image-20210403094402503](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403094505.png)

![image-20210403094426850](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403094508.png)

![image-20210403094434247](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403094511.png)

1. 整包 offset + recv_size == packet_size
2. 拆包 offset + recv_size < packet_size
3. 粘包offset + recv_size > packet_size

```c
//recver.c
#include "./common/tcp_server.h"
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<string.h>
#include<sys/stat.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<arpa/inet.h>
#include<signal.h>

struct FileMsg{
    long size;
    char name[50];
    char buf[4096];
};


void child_do(int fd){
    struct FileMsg packet, packet_t, packet_pre;
    int packet_size = sizeof(struct FileMsg);
    size_t recv_size = 0;
    int offset, flag = 0, cnt = 0;
    long filesize;
    FILE *fp = NULL;
    size_t write_size = 0, size = 0;
    while(1){
        if(flag){
            memcpy(&packet, &packet_pre, flag);
            offset = flag;
            flag = 0;
        }
        while((recv_size = recv(fd, (void *)&packet_t, packet_size, 0)) > 0){
            //size需要统计的应该是接受的buf部分 而不是整个结构体
            //size += recv_size;
            //printf("size = %d\n", (int)size);
            if(offset + recv_size == packet_size){
                memcpy((char *)&packet + offset, &packet_t, recv_size);
                offset = 0;
                printf("整包 size = %d\n", packet_size);
                break;
            }else if(offset + recv_size < packet_size){
                memcpy((char *)&packet + offset, &packet_t, recv_size);
                offset += recv_size;
                printf("拆包 size = %d\n", offset);
                if(size >= packet.size) break;
            }else{
                memcpy((char *)&packet + offset, &packet_t, packet_size - offset);
                flag = recv_size - (packet_size - offset);
                memcpy(&packet_pre, (char *)&packet_t + packet_size - offset, flag);
                printf("粘包 size = %d\n", flag + packet_size);
                offset = 0;
                break;
            }
        }
        
        if(!cnt){
            printf("recv %s with size = %ld\n", packet.name, packet.size);
            //char name[50] = {0};
            int index = strlen(packet.name);
            packet.name[index] = '1';
            if((fp = fopen(packet.name, "wb")) == NULL){
                perror("fopen");
                break;
            }
        }
        cnt++;
        if(packet.size - size >= packet_size){
            write_size = fwrite(&packet.buf, 1, sizeof(packet.buf), fp);
            size += sizeof(packet.buf);
        }else{
            write_size = fwrite(&packet.buf, 1, packet.size - size, fp);
            size += (packet.size - size);
        }
        printf("size = %ld\n", size);
        if(size >= packet.size){
            printf("Finish : %s done, size = %ld\n", packet.name, size);
            break;
        }
    }
    fclose(fp);
    return ;
}

int main(int argc, char **argv){
    if(argc != 2){
        fprintf(stderr, "Usage : %s port\n", argv[0]);
        exit(1);
    }
    int server_listen, port, fd;
    port = atoi(argv[1]);
    if((server_listen = socket_create(port)) < 0){
        perror("socket");
        exit(1);
    }
    int pid;
    signal(SIGCHLD, SIG_IGN);
    
    while(1){
        if((fd = accept(server_listen, NULL, NULL)) < 0){
            perror("accept");
            continue;
        }
        
        if((pid = fork()) < 0){
            perror("fork");
            continue;
        }
        if(pid == 0){
            close(server_listen);
            child_do(fd);
            close(fd);
            exit(0);
        }else{
            close(fd);
        }

    }
    
    return 0;
}

```

```c
//sender.c
#include"./common/head.h"
#include"./common/tcp_client.h"
#include"./common/common.h"

struct FileMsg{
    long size;
    char name[50];
    char buff[4096];
};

int main(int argc, char **argv){
    if(argc != 3){
        fprintf(stderr, "Usage: %s ip port", argv[0]);
        return 1;
    }
    int sockfd, port;
    char buff[100] = {0};
    char name[50] = {0};
    struct FileMsg filemsg;
    port = atoi(argv[2]);
    if((sockfd = socket_connect(argv[1], port)) < 0){
        perror("socket_connect");
        return 1;
    }
    //while(1){
        scanf("%[^\n]s", buff);
        getchar();
        if(!strncmp("send ", buff, 5)){
            strcpy(name, buff + 5);
        }else{
            fprintf(stderr, "invalid cmd!\n");
            //continue;
            return 1;
        }
        FILE *fp = NULL;
        size_t size;
        if((fp = fopen(name, "rb")) == NULL){
            perror("fopen");
            //continue;
            return 1;
        }
        fseek(fp, 0L, SEEK_END);
        filemsg.size = ftell(fp);
        strcpy(filemsg.name, name);
        fseek(fp, 0L, SEEK_SET);
        while((size = fread(filemsg.buff, 1, 4096, fp))){
            send(sockfd, (void *)&filemsg, sizeof(filemsg), 0);
            memset(filemsg.buff, 0, sizeof(filemsg.buff));
        }
        fclose(fp);
    //}
    close(sockfd);

    return 0;
}

```



# 六.线程

实现并发的一种方法是多个进程通过共享内存或消息传递进行协作和同步，另一种方法就是在一个地址空间上使用多个线程

八核十二线程：八个物理核心，其中有几个核心用了超线程技术，所以八核当做十二核用

## 1.进程与线程的区别

看了一遍排在前面的答案，类似”**进程是资源分配的最小单位，线程是CPU调度的最小单位“**这样的回答感觉太抽象，都不太容易让人理解。

做个简单的比喻：进程=火车，线程=车厢

- 线程在进程下行进（单纯的车厢无法运行）
- 一个进程可以包含多个线程（一辆火车可以有多个车厢）
- 不同进程间数据很难共享（一辆火车上的乘客很难换到另外一辆火车，比如站点换乘）
- 同一进程下不同线程间数据很易共享（A车厢换到B车厢很容易）
- 进程要比线程消耗更多的计算机资源（采用多列火车相比多个车厢更耗资源）
- 进程间不会相互影响，一个线程挂掉将导致整个进程挂掉（一列火车不会影响到另外一列火车，但是如果一列火车上中间的一节车厢着火了，将影响到所有车厢）
- 进程可以拓展到多机，进程最多适合多核（不同火车可以开在多个轨道上，同一火车的车厢不能在行进的不同的轨道上）
- 进程使用的内存地址可以上锁，即一个线程使用某些共享内存时，其他线程必须等它结束，才能使用这一块内存。（比如火车上的洗手间）－"互斥锁"
- 进程使用的内存地址可以限定使用量（比如火车上的餐厅，最多只允许多少人进入，如果满了需要在门口等，等有人出来了才能进去）－“信号量”

https://blog.csdn.net/whui19890911/article/details/8662850

线程是轻量级的进程

线程可以共享内存空间，进程间都是独立的

进程不能进行资源共享，安全但是切换进程代价大

线程可以进行资源共享，如果多个线程访问一片内存空间会出问题

1个cpu只能处理一个进程 将一个进程分解成多个线程，分布在多个cpu---并发

## 2.相关函数

**pthread_create：创建线程**

```c
#include<stdio.h>
#include<pthread.h>

void *print(void *arg){
    printf("%s\n", (char *)arg);
    return NULL;
}

int main(){
    pthread_t tid;
    char msg[1024] = {0};
    scanf("%s", msg);
    pthread_create(&tid, NULL, print, (void *)msg);
    return 0;
}

```

![image-20210316152102613](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210316152102613.png)

为什么没有输出呢？

> The new thread terminates in one of the following ways:
>
>    * It  calls  pthread_exit(3),  specifying an exit status value that is available to another
>      thread in the same process that calls pthread_join(3).
>    * It returns from start_routine().  This is equivalent to calling pthread_exit(3) with  the
>      value supplied in the return statement.
>    * It is canceled (see pthread_cancel(3)).
>    * Any  of  the  threads  in the process calls exit(3), or the main thread performs a return
>      from main().  This causes the termination of all threads in the process.
>
> 新线程以下列方式之一终止：
>
> ​    *它调用pthread_exit（3），指定退出状态值，该值可在调用pthread_join（3）的同一进程中的另一个线程使用。
>
> ​    *它从start_routine（）返回。 这等效于使用return语句中提供的值调用pthread_exit（3）。
>
> ​    *它被取消（请参阅pthread_cancel（3））。
>
> ​    *进程中的任何线程都调用exit（3），或者主线程从main（）返回。 这导致进程中所有线程的终止。

线程还没开始执行任务，主函数就return 0;了，直接将线程干掉

```c
#include<stdio.h>
#include<pthread.h>
#include<unistd.h>
void *print(void *arg){
    printf("%s\n", (char *)arg);
    return NULL;
}

int main(){
    pthread_t tid;
    char msg[1024] = {0};
    scanf("%s", msg);
    pthread_create(&tid, NULL, print, (void *)msg);
    sleep(1);//如果不加sleep 那么主函数退出直接将线程杀死 无输出
    return 0;
}

```

想要传多个参数可以封装结构体

```c
#include<stdio.h>
#include<pthread.h>
#include<unistd.h>
#include<string.h>
struct Msg{
    int age;
    char name[20];
};

void *print(void *arg){
    struct Msg *tmp = (struct Msg *)arg;
    printf("%s\n", tmp->name);
    printf("%d\n", tmp->age);
    return NULL;
}

int main(){
    pthread_t tid;
    struct Msg msg;
    msg.age = 18;
    strcpy(msg.name, "xsm");
    pthread_create(&tid, NULL, print, (void *)&msg);
    sleep(1);//如果不加sleep 那么主函数退出直接将线程杀死 无输出
    return 0;
}


```

**pthread_join():等待特定的线程退出，并传出线程结束状态**

**pthread_exit():以特定码退出线程**

```c
#include<stdio.h>
#include<pthread.h>
#include<unistd.h>
#include<string.h>
#include<stdlib.h>
struct Msg{
    int age;
    char name[20];
};

int retval;//如果不是全局变量 retval在函数执行完被释放 返回状态吗找不到了

void *print(void *arg){
    struct Msg *tmp = (struct Msg *)arg;
    printf("%s\n", tmp->name);
    printf("%d\n", tmp->age);
    retval = 3;
    //exit(3);//不能加 任何一个线程调用exit 所有线程都退出 包括主线程
    pthread_exit((void *)&retval);
}

int main(){
    int *status;
    pthread_t tid;
    struct Msg msg;
    msg.age = 18;
    strcpy(msg.name, "xsm");
    pthread_create(&tid, NULL, print, (void *)&msg);
    
    pthread_join(tid, (void *)&status);
    printf("retval = %d\n", *status);
    return 0;
}
```

exit一旦调用，不管程序是多进程还是多线程 全部结束 exit是将控制权交给系统而return是交给调用这个函数的函数

## 3.线程同步

同步就是协同步调，按预定的先后次序进行运行。如：你说完，我再说。这里的同步千万不要理解成那个同时进行，应是指协同、协助、互相配合。线程同步是指多线程通过特定的设置（如互斥量，事件对象，临界区）来控制线程之间的执行顺序（即所谓的同步）也可以说是在线程之间通过同步建立起执行顺序的关系，如果没有同步，那线程之间是各自运行各自的！

线程互斥是指对于共享的进程系统资源，在各单个线程访问时的排它性。当有若干个线程都要使用某一共享资源时，任何时刻最多只允许一个线程去使用，其它要使用该资源的线程必须等待，直到占用资源者释放该资源。线程互斥可以看成是一种特殊的线程同步（下文统称为同步）。

### 3.1互斥锁mutex

当没有相关函数 的man手册时`sudo apt install manpages-de manpages-de-dev glibc-doc manpages-posix-dev manpages-posix`

- pthread_mutex_init：初始化锁
- pthread_mutex_trylock：尝试锁
- pthread_mutex_lock：上锁
- pthread_mutex_unlock：解锁
- pthread_mutex_destroy：销毁锁



- pthread_mutexattr_init：涉及锁属性
- pthread_mutex_setshared：线程互斥锁设置进程间的共享

mutex是个君子锁 如果不查看是否上锁而直接访问也是可以的

```c
//shm + mutex实现0-max的多进程累加
#include<stdio.h>
#include<string.h>
#include<sys/wait.h>
#include<sys/types.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<unistd.h>
#include<pthread.h>
struct Shm{
    int now;
    int sum;
    pthread_mutex_t mutex;
};

struct Shm *share_memory = NULL;

void do_add(int max, int x){
    while(1){
        pthread_mutex_lock(&share_memory->mutex);
        printf("<%d> : %d %d\n", x, share_memory->now, share_memory->sum);
        if(share_memory->now > max) {
            pthread_mutex_unlock(&share_memory->mutex);
            break;
        }
        share_memory->sum += share_memory->now;
        share_memory->now++;
        pthread_mutex_unlock(&share_memory->mutex);
    }
    return ;
}


int main(int argc, char **argv){
    if(argc != 3){
        fprintf(stderr, "Usage: %s max ins", argv[0]);
        return 1;
    }
    int max, ins;
    max = atoi(argv[1]);
    ins = atoi(argv[2]);
    int shmid;
    key_t key = ftok(".", 2021);
    if((shmid = shmget(key, sizeof(struct Shm), IPC_CREAT | 0666)) < 0){
        perror("shmget");
        return 1;
    }
    if((share_memory = (struct Shm *)shmat(shmid, NULL, 0)) == NULL){
        perror("shmat");
        return 1;
    }
    memset(share_memory, 0, sizeof(struct Shm));

    pthread_mutexattr_t attr;
    pthread_mutexattr_init(&attr);
    //将线程锁共享为其他进程也可以用
    //如果不pshared，虽然放在了共享内存中，但是进程不知道这个锁别人也在用
    pthread_mutexattr_setpshared(&attr, PTHREAD_PROCESS_SHARED);
    
    pthread_mutex_init(&share_memory->mutex, &attr);
    share_memory->now = 0;
    share_memory->sum = 0;
    
    int x = 0;
    pid_t pid;
    for(int i = 0; i < ins; i++){
        if((pid = fork()) < 0){
            perror("fork");
            return 1;
        }
        if(pid == 0){
            x = i;
            break;
        }
    }

    if(pid == 0){
        do_add(max, x);
        printf("<%d> exit!\n", x);
        exit(0);
    }

    while(ins){
        wait(NULL);
        ins--;
    }
    printf("Ans = %d\n", share_memory->sum);

    return 0;
}

```

死锁：https://blog.csdn.net/hd12370/article/details/82814348

mutex的底层实现：原子操作 https://www.zhihu.com/question/332113890

各种锁：[https://www.cnblogs.com/TMesh/p/11730847.html](https://www.cnblogs.com/TMesh/p/11730847.html)

1. 自旋锁（spinlock）：不能加锁时自己主动多次询问
2. 互斥锁（mutex）：不能加锁时阻塞
3. 读写锁：读共享写互斥
4. RCU锁：read-copy-update
5. 递归锁和非递归锁：mutex默认非递归 一个进程/线程多次加锁，递归可以，非递归死锁

### 3.2条件变量cond

类似信号的东西

信号是单对单 条件变量可以给多个发送

需要跟mutex配合使用

- pthread_cond_broadcast:重启所有等待cond的线程（发送好多个信号）
- pthread_cond_signal:重启一个等待cond的线程（发送一个信号）
- pthread_cond_destroy:销毁cond
- pthread_cond_init:初始化cond
- pthread_cond_wait:自动unlock mutex后等待cond信号到来 cond信号到来后自动lock mutex使用前需要先加锁

example：

client发送数据后发送信号提示server端接受  server端接到信号后输出

server：

1. pthread_mutex_lock
2. pthread_cond_wait
3. printf
4. pthread_mutex_unlock

client:

1. pthread_mutex_lock
2. scanf
3. signal
4. pthread_mutex_unlock

server起来后lock mutex了，client还能拿到mutex锁吗？

可以拿到，因为pthread_cond_wait时会先解锁

ps:server端wait时解锁了 此时client拿到锁 scanf后发送信号在解锁 server端拿到信号加锁，但是server端在拿信号加锁的同时client端也在抢下一次循环的锁，可能server端竞争不过client端

```c
//server
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<sys/types.h>
#include<signal.h>
#include<string.h>
#include<pthread.h>
struct Shm{
    pthread_mutex_t mutex;
    pthread_cond_t cond;
    char buff[1024];
};

struct Shm *share_memory = NULL;


int main(){
    int shmid;
    key_t key = ftok(".", 2021);

    if((shmid = shmget(key, sizeof(struct Shm), IPC_CREAT | 0666)) < 0){
        perror("shmget");
        return 1;
    }
    if((share_memory = (struct Shm *)shmat(shmid, NULL, 0)) == NULL){
        perror("shmat");
        return 1;
    }
    memset(share_memory, 0, sizeof(struct Shm));
    
    pthread_mutexattr_t m_attr;
    pthread_condattr_t c_attr;
    
    pthread_mutexattr_init(&m_attr);
    pthread_condattr_init(&c_attr);

    pthread_mutexattr_setpshared(&m_attr, PTHREAD_PROCESS_SHARED);
    pthread_condattr_setpshared(&c_attr, PTHREAD_PROCESS_SHARED);

    pthread_mutex_init(&share_memory->mutex, &m_attr);
    pthread_cond_init(&share_memory->cond, &c_attr);

    while(1){
        
        pthread_mutex_lock(&share_memory->mutex);
        printf("after mutex lock\n");
        pthread_cond_wait(&share_memory->cond, &share_memory->mutex);
        printf("After cond wait\n");
        printf("<server> : %s\n", share_memory->buff);
        pthread_mutex_unlock(&share_memory->mutex);
    }

    return 0;
}

```

```c
//client
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<sys/types.h>
#include<signal.h>
#include<string.h>
#include<pthread.h>

struct Shm{
    pthread_mutex_t mutex;
    pthread_cond_t cond;
    char buff[1024];
};

struct Shm *share_memory = NULL;


int main(){
    int shmid;
    key_t key = ftok(".", 2021);


    if((shmid = shmget(key, sizeof(struct Shm), IPC_CREAT | 0666)) < 0){
        perror("shmget");
        return 1;
    }
    if((share_memory = (struct Shm *)shmat(shmid, NULL, 0)) == NULL){
        perror("shmat");
        return 1;
    }
    //memset(share_memory, 0, sizeof(struct Shm));
    while(1){
        printf("before lock\n");
        pthread_mutex_lock(&share_memory->mutex);
        printf("<client> : ");
        scanf("%[^\n]s", share_memory->buff);
        getchar();
        pthread_cond_signal(&share_memory->cond);
        pthread_mutex_unlock(&share_memory->mutex);
        usleep(1000);
    }
    return 0;
}

```

## 4.线程池

之前的每来一个任务新创建一个线程，类似公司每有一个任务我就重新招聘一个人，用完了踢走，不合理

线程池：来任务了就进任务队列，每个线程认领一个任务，做完任务之后再拿下一个任务，直至全部任务完成

![image-20210326104656056](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210326104658.png)

- 可能会有多个线程抢一个任务：需要做互斥
- 当有任务来时需要告知所有人：条件变量

```c
#include"./common/head.h"
#include"./common/tcp_server.h"
#include"./common/color.h"
#include"./common/common.h"

#define MAXTASK 100
#define MAXTHREAD 20
//小写->大写字母
char ch_char(char c){
    if(c >= 'a' && c <= 'z') return c - 32;
    return c;
}
//任务队列
typedef struct {
    int sum;
    int *fd;
    int head, tail;
    pthread_mutex_t mutex;
    pthread_cond_t cond;
}TaskQueue;
//初始化
void TaskQueueInit(TaskQueue *queue, int sum){
    queue->sum = sum;
    queue->fd = calloc(sum, sizeof(int));
    queue->head = queue->tail = 0;
    pthread_mutex_init(&queue->mutex, NULL);
    pthread_cond_init(&queue->cond, NULL);
}

void TaskQueuePush(TaskQueue *queue, int fd){
    pthread_mutex_lock(&queue->mutex);
    queue->fd[queue->tail] = fd;
    printf(GREEN"<TaskPush>"NONE ": %d\n", fd);
    if(++queue->tail == queue->sum){
        queue->tail = 0;
    }
    pthread_cond_signal(&queue->cond);
    //用signal而不用broadcast  避免鲸群效应
    pthread_mutex_unlock(&queue->mutex);
}

int TaskQueuePop(TaskQueue *queue){
    pthread_mutex_lock(&queue->mutex);
    //只能用while，不能用if 惊群效应？？
    while(queue->tail == queue->head){
        pthread_cond_wait(&queue->cond, &queue->mutex);
    }
    int fd = queue->fd[queue->head];
    printf(GREEN"<TaskPop>"NONE ": %d\n", fd);
    if(++queue->head == queue->sum){
        queue->head = 0;
    }
    pthread_mutex_unlock(&queue->mutex);
    return fd;
}
void do_echo(int fd){
    char buf[512] = {0}, ch;
    int index = 0;
    while(1){
    
        if(recv(fd, &ch, 1, 0) <= 0){
            printf(RED"<Logout>"NONE": %d\n", fd);
            break;
        }
        if(index < sizeof(buf)) buf[index++] = ch_char(ch);
        if(ch == '\n'){
            send(fd, buf, index, 0);
            index =  0;
            continue;
        }
    }
}

//一共20个线程，每当有一个fd连入，分配一个线程给此fd处理任务
void *thread_run(void  *arg){
    pthread_t tid = pthread_self();//获取线程id
    pthread_detach(tid);//将此线程标记为分离 线程结束后程序自动回收资源 类似僵尸进程的处理

    TaskQueue *queue = (TaskQueue *)arg;
    while(1){
        int fd = TaskQueuePop(queue);
        do_echo(fd);
    }
}

int main(int argc, char **argv){
    if(argc != 2){
        fprintf(stderr, "Usage : %s port\n", argv[0]);
        exit(1);
    }
    int port, server_listen, fd;
    port = atoi(argv[1]);

    if((server_listen = socket_create(port)) < 0){
        perror("socket_create");
        exit(1);
    }
    TaskQueue queue;
    TaskQueueInit(&queue, MAXTASK);
    pthread_t *tid = calloc(MAXTHREAD, sizeof(pthread_t));
    for(int i = 0; i < MAXTHREAD; i++){
        pthread_create(&tid[i], NULL, thread_run, (void *)&queue);
    }

    while(1){
        if((fd = accept(server_listen, NULL, NULL)) < 0){
            perror("accept");
            exit(1);
        }
        printf(BLUE"<login>"NONE ": %d\n", fd);
        TaskQueuePush(&queue, fd);
        //do sth
    }

    return 0;
}

```

**鲸群效应：**

举一个很简单的例子，当你往一群鸽子中间扔一块食物，虽然最终只有一个鸽子抢到食物，但所有鸽子都会被惊动来争夺，没有抢到食物的鸽子只好回去继续睡觉， 等待下一块食物到来。这样，每扔一块食物，都会惊动所有的鸽子，即为惊群。对于操作系统来说，多个进程/线程在等待同一资源是，也会产生类似的效果，其结 果就是每当资源可用，所有的进程/线程都来竞争资源，造成的后果：

- 1）系统对用户进程/线程频繁的做无效的调度、上下文切换，系统系能大打折扣。
- 2）为了确保只有一个线程得到资源，用户必须对资源操作进行加锁保护，进一步加大了系统开销。

但是对于线程池呢？

一个基本的线程池框架是基于生产者和消费者模型的。生产者往队列里面添加任务，而消费者从队列中取任务并进行执行。一般来说，消费时间比较长，一般有许多个消费者。当许多个消费者同时在等待任务队列的时候，也就发生了“惊群效应”。

# 七.终端编程

终端中 比如more等 按q会直接退出，不需要按'\n'  即不需要行缓冲

- tcgetattr
- tcsetattr

ICANON：传统模式下需要敲击回车   注意设置c_cc数组



将tty设置成直接IO

1. 获取原终端属性
2. 重新设置终端属性~ICANON
3. 操作完成后还原原终端属性

```c
//需要敲击回车
//echo $? 查看上一条命令的返回值
#include<stdio.h>
#define Q "Do you want another try?"

int getresponse();

int main(){
    int response;
    response = getresponse();
    return response;
}

int getresponse(){
    printf("%s", Q);
    printf("(y/n) :");
    switch(getchar()){
        case 'y':
        case 'Y': return 1;
        case 'n':
        case 'N': return 0;
        default: return 0;
    }
}


```

```c
//不需要按‘\n’ 没有定时
//echo $? 查看上一条命令的返回值
#include<stdio.h>
#define Q "Do you want another try?"
#include<termios.h>
int getresponse();

void tty_mode(int);
void setcrmode();

int main(){
    int response;
    tty_mode(0);
    setcrmode();
    response = getresponse();
    tty_mode(1);
    printf("return value = %d\n", response);
    return response;
}

int getresponse(){
    printf("%s", Q);
    printf("(y/n) :");
    switch(getchar()){
        case 'y':
        case 'Y': return 1;
        case 'n':
        case 'N': return 0;
        default: return 0;
    }
}

void setcrmode(){
    struct termios ttystate;
    tcgetattr(0, &ttystate);
    ttystate.c_lflag &= ~ICANON;//非标准输入 不需要键入回车
    ttystate.c_lflag &= ~ECHO;//不显示输入的字符
    ttystate.c_cc[VMIN] = 1;//接受几个字符的输入就刷新输入缓存区
    //ttystate.c_cc[VTIME] = 1;//当输入第一个字符后，在等10s
    tcsetattr(0, TCSANOW, &ttystate);
}

void tty_mode(int how){
    //how = 1；还原  how = 0；保存
    static struct termios orginal_mode;
    if(how == 0){
        tcgetattr(0, &orginal_mode);
    }else{
        tcsetattr(0, TCSANOW, &orginal_mode);
    }
}


```

```c
//使用select实现计时
//echo $? 查看上一条命令的返回值
#include<stdio.h>
#define Q "Do you want another try?"
#include<termios.h>
#include<fcntl.h>
#include<sys/select.h>
#include<ctype.h>
#include<string.h>
#include<time.h>

#define TIMEOUT 5
int get_response();
void tty_mode(int);
void set_cr_noecho_mode();
void set_nonblock_mode();
int get_ok_char();
int main(){
    int response;
    tty_mode(0);
    set_cr_noecho_mode();
    response = get_response();
    tty_mode(1);
    printf("\nreturn value = %d\n", response);
    return response;
}

int get_response(){
    printf("%s (y/n)?", Q);
    fflush(stdout);
    struct timeval tv;
    a1:tv.tv_sec = TIMEOUT;
    tv.tv_usec = 0;
    fd_set set;
    FD_ZERO(&set);
    FD_SET(0, &set);//
    int ret =  select(1, &set, NULL, NULL, &tv);
    if(ret == 1){
        if(FD_ISSET(0,&set)){
            while(1){
                int input = tolower(getchar());//大写转小写
                switch(input){
                    case 'y': return 1;
                    case 'n': return 0;
                    default: goto a1;//输入错误时重新输入goto 非y  n 
                }
            }
        }
    }
    return 2;
}



//将终端设置为非阻塞
void set_nonblock_mode(){
    int termflags;
    termflags = fcntl(0, F_GETFL);//fcntl操作文件描述符fd
    termflags |= O_NONBLOCK;
    fcntl(0, F_SETFL, termflags);
}

//设置终端属性
void set_cr_noecho_mode(){
    struct termios ttystate;
    tcgetattr(0, &ttystate);
    ttystate.c_lflag &= ~ICANON;//非标准输入 不需要键入回车
    ttystate.c_lflag &= ~ECHO;//不显示输入的字符
    ttystate.c_cc[VMIN] = 1;//接受几个字符的输入就刷新输入缓存区
    //ttystate.c_cc[VTIME] = 1;
    tcsetattr(0, TCSANOW, &ttystate);
}
//保存还原终端属性
void tty_mode(int how){
    //how = 1；还原  how = 0；保存
    static struct termios orginal_mode;
    if(how == 0){
        tcgetattr(0, &orginal_mode);
    }else{
        tcsetattr(0, TCSANOW, &orginal_mode);
    }
}

```



# 八.文件系统

[https://www.linuxprobe.com/linux-system-structure.html](https://www.linuxprobe.com/linux-system-structure.html)

[https://blog.csdn.net/github_37882837/article/details/90672881](https://blog.csdn.net/github_37882837/article/details/90672881)

[https://www.cnblogs.com/bellkosmos/p/detail_of_linux_file_system.html](https://www.cnblogs.com/bellkosmos/p/detail_of_linux_file_system.html)

- superblock: 存放文件系统的属性 inode表和dateblock的大小等
- inode表：inode号——inode具体内容  还有文件的权限和属性信息等
- dateblock：真正的数据存放的地方 （如果是目录文件存放的是目录下的文件名——inode号，找文件找到inode号后在inode表中读取具体内容）

# 九.内存管理

虚拟内存技术：[https://www.cnblogs.com/zhenbianshu/p/10300769.html](https://www.cnblogs.com/zhenbianshu/p/10300769.html)

page cache：从外部存储介质中加载数据到内存中，这个过程是比较耗时的，因为外部存储介质读写性能毫秒级。为了减少外部存储设备的读写，Linux内核提供了Page cache。最常见的操作，每次读取文件时，数据都会被放入页面缓存中，以避免后续读取时所进行昂贵的磁盘访问。同样，当写入文件时，数据被重新放置在缓存中，被标记为脏页，定期的更新到存储设备上，以提高读写性能。

[https://blog.csdn.net/qq_27347991/article/details/80958198](https://blog.csdn.net/qq_27347991/article/details/80958198)

刷脏页的概念：[https://www.icode9.com/content-3-814084.html](https://www.icode9.com/content-3-814084.html)



# 十.定时器

[https://blog.csdn.net/lxmky/article/details/7669296](https://blog.csdn.net/lxmky/article/details/7669296)

进程是程序在内存中的镜像（实例）

用户态：无法控制硬件  可以通过系统调用调用内核态

内核态：可以控制硬件 进行IO操作

time ./a.out会有三个时间

- realtime：程序运行的真是时间
- usrtime：用户态上的运行时间
- systime：内核态的时间

> 在 多处理器的系统上，一个进程如果有 多个线程或者有 多个子进程可能导致real time比CPU time（user + sys time）要小，这是因为不同的线程或进程可以并行执行。换句话说，这里的并行是指真正的并行，不是在单CPU上的多线程并发，如果系统只有一个CPU（单核），那么即使多线程，也不会使得real < user + sys。
> 举个例子，一个纯计算任务，没有系统调用（即没有耗费sys time，只有user time），采用单线程需要执行的时间为8s。那么在一个四核的机器上，采用并行算法，用4个核一起算，理想上如果完全并行，加速比为4，2s内就能完成，那么从开始到结束，real time是2s。user time呢？应该是2s * 4 = 8s. 所以此时会出现real < sys + user的情况。

## 1.sleep()/usleep()

其中sleep精度是1秒，usleep精度是1微妙，具体代码就不写了。使用这种方法缺点比较明显，在Linux系统中，sleep类函数不能保证精度，尤其在系统负载比较大时，sleep一般都会有超时现象。

## 2.signal+alarm

精度为s级， sleep的底层实现

用下面的三个函数实现sleep

- alarm 
- pause
- signal

```c
#include<stdio.h>
#include<unistd.h>
#include<signal.h>
void print(){
    printf("It's time to get up\n");
}
int main(){
    signal(14, print);//注册alarm信号  当有alarm信号时实现print函数
    alarm(10);
    pause();//没有pause()程序直接结束 不会等到alarm发出SIGALRM信号
    return 0;
}

```

内核收到一个时间脉冲，会遍历一遍所有进程的计时器  更新

## 3.RTC机制

RTC机制利用系统硬件提供的Real Time Clock机制，通过读取RTC硬件/dev/rtc，通过ioctl()设置RTC频率

这种方式比较方便，利用了系统硬件提供的RTC，精度可调，而且非常高。

```cpp
#include <stdio.h>
#include <linux/rtc.h>
#include <sys/ioctl.h>
#include <sys/time.h>
#include <sys/types.h>
#include <fcntl.h>
#include <unistd.h>
#include <errno.h>
#include <stdlib.h>
 
int main(int argc, char* argv[])
{
        unsigned long i = 0;
        unsigned long data = 0;
        int retval = 0;
        int fd = open ("/dev/rtc", O_RDONLY);
 
        if(fd < 0)
        {
                perror("open");
                exit(errno);
        }
 
        /*Set the freq as 4Hz*/
        if(ioctl(fd, RTC_IRQP_SET, 1) < 0)
        {
                perror("ioctl(RTC_IRQP_SET)");
                close(fd);
                exit(errno);
        }
        /* Enable periodic interrupts */
        if(ioctl(fd, RTC_PIE_ON, 0) < 0)
        {
                perror("ioctl(RTC_PIE_ON)");
                close(fd);
                exit(errno);
        }
 
        for(i = 0; i < 100; i++)
        {
                if(read(fd, &data, sizeof(unsigned long)) < 0)
                {
                        perror("read");
                        close(fd);
                        exit(errno);
 
                }
                printf("timer\n");
        }
        /* Disable periodic interrupts */
        ioctl(fd, RTC_PIE_OFF, 0);
        close(fd);
 
        return 0;
}
```

## 4.select（）

过使用select()，来设置定时器；原理利用select()方法的第5个参数，第一个参数设置为0，三个文件描述符集都设置为NULL，第5个参数为时间结构体

```c
#include <sys/time.h>
#include <sys/select.h>
#include <time.h>
#include <stdio.h>
 
/*seconds: the seconds; mseconds: the micro seconds*/
void setTimer(int seconds, int mseconds)
{
        struct timeval temp;
 
        temp.tv_sec = seconds;
        temp.tv_usec = mseconds;
 
        select(0, NULL, NULL, NULL, &temp);
        //printf("timer\n");
 
        return ;
}
 
int main()
{
        int i;
 
        for(i = 0 ; i < 100; i++){
            setTimer(1, 0);
            printf("hello\n");
        }
                
 
        return 0;
}

```

## 5.间隔计时器

设置间隔计时器  interval timer

- getitimer
- setitimer



三个间隔计时器：

- **TIMER_REAL**：实时递减，并在到期时发送**SIGALRM**。
- **ITIMER_VIRTUAL**：仅在进程执行时递减，并在到期时传递**SIGVTALRM**。
- **ITIMER_PROF**：在进程执行时和系统代表进程执行时都递减。与**ITIMER_VIRTUAL**结合使用，此计时器通常用于分析应用程序在用户和内核空间中花费的时间。**SIGPROF**在到期时交付。

```c
#include<stdio.h>
#include<sys/time.h>
#include<unistd.h>
#include<signal.h>

void print(){
    printf("Recv a signal\n");
}


int main(){
    struct itimerval itm;
    signal(14, print);
    itm.it_interval.tv_sec = 1;//之后的值
    itm.it_interval.tv_usec = 0;
    itm.it_value.tv_sec = 3;//当前值
    itm.it_value.tv_usec = 0;
    setitimer(ITIMER_REAL, &itm, NULL);//实时递减
    
    while(1){
        sleep(10);//itimer发送的alarm信号会将sleep中的pause跳出
        //程序运行开始后3s打印一次 之后每隔1s打印一次
        printf("After 10 seconds!\n") ;  
    }

    return 0;
}

```

# small project

路径/home/mox/1.HaiZei/6.os/5.project

<u>**v1.0**</u>

1. tcp流程图

   ![image-20210317103347149](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210317103352.png)

2. server端

   - accept到一个链接就创建一个子进程处理，父进程一直accept
   - 子进程不停的接受数据放在msg中，收到数据就打印出来
   - 为了防止server端先断开连接而使端口处在TIME_WAIT状态，使用延时关闭（0s）和端口复用

3. client端

   建立tcp链接后不停的发送数据

<u>**v2.0**</u>

将v1.0代码进行封装

- head：放头文件
- src：放源文件
- bin：放编译好的可执行文件
- makefile：使用make进行编译

<u>**v3.0**</u>

用线程代替进程来执行任务

- server：当有新用户连接 打印"New client login!"并向对端发送"You Are Here"表示你已经连接 之后等待接受client发送过来的数据，接收到数据打印并将接收到的数据转化为大写后回传
- client: 当连接成功后接受server发送的"You Are Here"并打印，发送数据后接受server回传的大写数据并打印

bug:

```cpp
pthread_t tid;
    while(1){
        struct sockaddr_in client;
        socklen_t addrlen = sizeof(client);
        if((sockfd = accept(server_listen, (struct sockaddr *)(&client), &addrlen)) < 0){
            perror("accept");
            close(sockfd);
            continue;
        }
        printf("New client login!\n");
        pthread_create(&tid, NULL, work, (void *)&sockfd);
    }

```

tid只有一个，当多个客户端连接时只有一个客户端会工作

多个client会有竞争

<u>**v4.0**</u>

用数组处理多个客户端的竞争问题

一个程序最多能处理的线程是1024

```c
#define MAXCLIENT 512//一次进程处理最大的线程1024

struct Client{
    int flag;
    int fd;
    pthread_t tid;
};

struct Client *client;

//寻找最小的可用的客户端下标
int find_sub(){
    for(int i = 0; i < MAXCLIENT; i++){
        if(client[i].flag == 0){
            return i;
        }
    }
    return -1;
}

		int sub;
        if((sub = find_sub()) < 0){
            fprintf(stderr, "Client Full\n");
            close(sockfd);
            continue;
        }
        client[sub].flag = 1;
        client[sub].fd = sockfd;
        pthread_create(&client[sub].tid, NULL, work, (void *)&sub);


```

# 广域聊天室

路径`/home/mox/1.HaiZei/6.os/7.chatroom`

## 广域聊天室整体要求

### client端

> 执行./client
>
> 请使用配置文件配置服务器监听端口等
>
> Server_ip = 192.168.1.40
>
> Server_port=8731
>
> My_name=xxxx
>
> Log_FIle=./chat.log

1. 假设A用户想给B用户发送私聊信息，可以发送`@B 这是来自A的私聊信息`
2. 所有公聊信息，服务器端会转发给Client端
3. 将`Client`收到的所有聊天信息，保存在`${Log_File}`中
4. 使用`tail -f ${Log_File}`查看文件，获取聊天信息，发送和接受独立
5. 由服务器发送到本地的数据是一个结构体，`Client`端自行解析

```c
struct Message{
    char from[20];
    int flag;//若flag为1则为私聊信息，0为公聊信息，2则为服务器的通知信息或客户端验证信息，3为客户端下线信息
    char message[1024];
};
```

### server端

> 执行./server
>
> 使用配置文件，将服务器监听端口，客户端监听端口等都写到配置文件
>
> Server_port=8731

1. Server将在`${Server_port}`上进行监听,等待用户上该端口上接受线，并在该端口上接收用户输出信息
2. Server每收到一条私聊信息，需要将消息转发给其他所有在线的用户
3. 如果用户发送的信息是一条私聊信息，则只转发给目标用户
4. 所有转发给用户的信息都将使用结构体Message进行封装
5. 私聊信息中所指定的用户不存在或已经下线，需要通知信息告知
6. 请选用合理的数据结构，储存用户信息
7. 支持100个以上的在线用户
8. 在`Client`上线时，发送欢迎信息，告知当前所有在线人数等
9. 需要考虑当两个用户在某一时刻同时上线的情况

## v1.0

```c
//发送的信息包 from为用户名 flag标记信息类型 message为信息内容
struct Msg{
    char from[20];
    int flag;
    char message[512];
};
//收到的信息包 因为下面将接收数据recv进行了打包 所以需要靠retval判断接收数据是否成功
struct RecvMsg{
    struct Msg msg;
    int retval;
};

//解析conf文件 从path拿到对应的key的value值
char *get_value(char *path, char *key);
//从fd接收数据
struct RecvMsg chat_recv(int fd);
//将msg发送到fd
int chat_send(struct Msg msg, int fd);



```

server端：

1. 创建socket连接，等待连接
2. 建立连接后先收一个是否建立连接的包 判断用户名是否被占用 如果被占用 回复类型3的数据断开连接 如果没被占用 回复类型2的信息说明新的client可以上线
3. 如果没被占用 寻找client数组最小下标记录信息 
4. 开辟线程一直接收并打印信息

```c
//服务端需要记录客户信息 name  是否在线 线程tid 建立的连接fd
struct User{
    char name[20];
    int online;
    pthread_t tid;
    int fd;
};

//判断用户名是否在线
bool check_online(char *name);
//寻找最小online为0的下标
int find_sub();
```

client端：

1. 建立socket连接
2. 连接成功后发送数据类型为2的客户端验证信息 等待接受服务端的通知信息
3. 如果通知信息类型为3 拒绝连接 关闭sockfd并退出
4. 如果通知类型是2不是3 则成功建立连接 
5. 开辟新的进程用于发送自己的信息
6. 主进程等待子进程结束

bug：

![image-20210402112450957](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210402112935.png)

1111和xsm上线 下线时的名称都是xsm ？？？？

```c
//server mian
while(1){
        if((fd = accept(server_listen, NULL, NULL)) < 0){
            perror("accept");
            continue;
        }
        
        recvmsg = chat_recv(fd);
        if(recvmsg.retval < 0){
            close(fd);
            continue;
        } 
        if(check_online(recvmsg.msg.from)){
            //拒绝连接
            msg.flag = 3;
            strcpy(msg.message, "You have Already Logined!");
            chat_send(msg, fd);
            close(fd);
            printf(YELLOW"D :" NONE"%s have been used!\n", recvmsg.msg.from);
            continue;
            
        }

        msg.flag = 2;
        strcpy(msg.message, "Welcome to this chatroom!");
        chat_send(msg, fd);
        int sub;
        sub = find_sub();
        client[sub].online = 1;
        client[sub].fd = fd;
        strcpy(client[sub].name, recvmsg.msg.from);
        pthread_create(&client[sub].tid, NULL, work, (void *)&sub);
        

    }

    return 0;
}
//线程处理函数
void *work(void *arg){
    int *sub = (int *)arg;
    int client_fd = client[*sub].fd;
    struct RecvMsg rmsg;
    printf(GREEN"Login : "NONE"%s\n", client[*sub].name);
    while(1){
        rmsg = chat_recv(client_fd);
        if(rmsg.retval < 0){
            printf(PINK"Logout : %s\n"NONE, client[*sub].name);
            close(client_fd);
            client[*sub].online = 0;
            return NULL;
        }
        printf(BLUE"%s"NONE": %s\n", rmsg.msg.from, rmsg.msg.message);
    }
    //printf(BLUE"%s login!\n"NONE, recvmsg.msg.from);
    return NULL;
}



```

线程处理函数的sub地址指向主函数中的sub  而每次有新的客户上线 则主函数中的sub值就会改变 而线程处理函数中一直用的是地址 所以它指向的地址的取值是一直改变的

解决：线程处理函数中将主函数中的值copy一份 而不是直接用同一个地址

```c
void *work(void *arg){
    int sub = *(int *)arg;
    int client_fd = client[sub].fd;
    struct RecvMsg rmsg;
    printf(GREEN"Login : "NONE"%s\n", client[sub].name);
    while(1){
        rmsg = chat_recv(client_fd);
        if(rmsg.retval < 0){
            printf(PINK"Logout : %s\n"NONE, client[sub].name);
            close(client_fd);
            client[sub].online = 0;
            return NULL;
        }
        printf(BLUE"%s"NONE": %s\n", rmsg.msg.from, rmsg.msg.message);
    }
    //printf(BLUE"%s login!\n"NONE, recvmsg.msg.from);
    return NULL;
}
```



# END





# 足球游戏

CS结构

client不处理数据，接受service端的数据并重绘（UI）

server端处理数据

使用udp协议



球场：

- 宽度65
- 长度105

![image-20200530200904603](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192722.png)



![image-20200530210811192](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192730.png)



![image-20200531142142255](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192725.png)

命令别名gcc = gcc -lnsurses

![image-20200531142559955](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192736.png)

先在.bashrc的文件下添加相应的命令  在source一下   

alias

![image-20200630182646934](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192738.png)

![image-20200630182819971](/home/mox/图片/Typora/image-20200630182819971.png)







BIOS硬件及配置信息

boot loader虚拟文件系统 跑内核

内核跑起来第一个进程init   不断的复制子进程





# Ctag的使用--追溯函数原型

```shell
sudo apt install ctags


ctags -I __THROW -I __attribute_pure__ -I __nonnull -I __attribute__ --file-scope=yes --langmap=c:+.h --languages=c,c++ --links=yes --c-kinds=+p --c++-kinds=+p --fields=+iaS --extra=+q -f ~/.vim/systags /usr/include/* /usr/include/x86_64-linux-gnu/sys/* /usr/include/x86_64-linux-gnu/bits/* /usr/include/arpa/* 

//在~/.vimrc的最后一行添加
set tags+=~/.vim/systags

//在项目目录下   生成索引文件tags
ctags -R

ctrl+]  追溯
ctrl+o/t 返回/切换
Tlist

```

![image-20200606190336931](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192748.png)



# IO模型大总结

![image-20200607140352125](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192755.png)

多进程  最高开1024个进程

- 优点 内存隔离
- 缺点 进程损耗大 最多1024个进程

收数据  解码   计算  重新编码  发送数据



![image-20200607140740237](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192752.png)

多线程

- 内存间共享 通信方便
- 线程开销小
- 线程与主程序独立
- 线程安全问题  资源竞争  多个线程访问一个资源
- 调试不方便



![image-20200607141047774](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192801.png)

单线程IO多路复用

reactor反应堆模式----------epoll

- 没有将cpu的效率完全使用出来
- 可以实现上万的并发



![image-20200607141515886](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192805.png)

带线程池的reactor



![image-20200607141944550](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192809.png)

主从模式reactor

根据cpu核心个数布局从反应堆个数，最大化利用cpu



![image-20200607142328196](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192816.png)

带线程池的主从reactor

现阶段企业用的最多的



# C10K--一个cpu实现1w个连接

1w个连接不是1w个客户，一个客户可能创建多个连接

poll + nonblock很轻松就可以实现

主从反应堆模式实现C100K

# cJson

![image-20200705142655850](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192822.png)

https://github.com/DaveGamble/cJSON#copying-the-source