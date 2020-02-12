# 在Ubuntu18.04 server上安装Conda，并使之能多用户共用

只为单个用户安装conda其实是挺简单的操作，但是想要安装一份conda，而让它在多个用户之间共用，就有点复杂了。

本文主要是由[这篇文章](https://medium.com/@pjptech/installing-anaconda-for-multiple-users-650b2a6666c6)翻译过来的，其结尾提供了另一种实现方式。

## 安装Conda

1. 在[官网](https://www.anaconda.com/distribution/#linux)下载安装脚本。

以sudo运行以下命令：

2. 创建一个名为anaconda的用户

        adduser anconda

3. 创建一个文件夹用于安装anaconda

        mkdir /opt/anaconda

4. 修改anaconda安装脚本的执行权限，使之能以sudo权限运行
    
        chmod +x Anaconda3-5.0.1-Linux-x86_64.sh

5. 运行安装脚本:脚本运行时会让你选择安装路径，输入第三步里你创建的那个文件夹的路径

        ./Anaconda3-5.0.1-Linux-x86_64.sh

6. 修改/opt/anaconda的所有者

        chown -R anaconda:anaconda /opt/anaconda

7. 移除'group' 和 'others'对/opt/anaconda的写入权限，这样就只有anaconda这个用户（以及root）能够对这个文件夹写入了，也就是只有anaconda能够在这个路径创建新环境、安装新的包。

        chmod -R go-w /opt/anaconda

8. 现在，我们要给予'group'和'others'读入和执行权限，让他们可以使用conda的环境以及库。

        chmod -R go+rX /opt/anaconda
    
现在，conda已经安装好、可以使用了

## 创建共享环境

使用如下命令创建一个名为shared_env的共享的环境, 这个环境所有的用户都是可以使用的。

    su anaconda
    conda create -n shared_env package1 package2
    
## 创建用户私有的环境
如果你想创建一个私有的环境，使用以下命令，这个环境一般会被创建在你的/home/user/.conda/envs下（假设user是你的用户名）

    /opt/anaconda/bin/conda create -n my_env package1 package2