# ......
用于记录使用各种工具过程中的经验

## linux 安装opencv4.4.0+cuda+cuDNN
### 第一次安装OpenCV3.4.0失败

安装cuda倒没什么问题，注意wget下载cuda_.run文件的时候最后会出现“核心已转储”的错误（不知道此时的下载的。run包还能不能用）,weget下载deb包安装就没问题。还有，安装系统的时候，分配给/的挂载空间大一点，第一次分配了20G,安个cuda就没了。

安装opencv会用到以下链接
https://blog.csdn.net/qq_39839546/article/details/107427546
https://blog.csdn.net/u014613745/article/details/78310916
https://blog.csdn.net/qq_17783559/article/details/105078293?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param
https://blog.csdn.net/qq_36327203/article/details/79372152

不过最终还是没能成功装上opencv-cuda版，虽然opencv3.4.0太老了不支持cuda11.0这种理由不太可信，但姑且也只能这么想了。下次换个高版本的opencv试试。

### 第二次安装OpenCV4.4.0成功

和第一次过程差不多，不过这次找到了个极为靠谱的教程。教程中有一个问题，就是安装cuDNN的时候，需要把cudnn_version.h内的宏定义复制到cudnn.h中，这样使用cmake配置编译opencv的时候才能找到对应版本的cuDNN。高版本的cuDNN的版本信息在cudnn_version.h内，和网络上大部分教程不同。

完美符合我的需求，安装成功，最后的darnet也运行成功。

https://my.oschina.net/u/4283198/blog/4684264

下面这个可能有用，我就是受这个启发把cudnn_version.h的内容复制到cudnn.h的。

https://www.cnblogs.com/phillee/p/11173316.html

## gitbook+腾讯云轻量服务器搭建博客

最初的博客方案为常见的：github+github做图床+PigGo+hexo方案，再加一个域名即可正常使用。由于原有博客的更新频率太低，不符合现阶段的使用需求，于是我想将其改为类似于Gitbook的组织方式，以章节区分不同的内容主题，并且在不同的内容主题下分节写入内容。这次使用的方案原定其实是腾讯轻量服务器+Github图床+Hugo+Gitbook+Nginx的方案，但实际上由于Hugo主题好看的不是很多，而且对Hugo模板的需求较小，于是就精简了腾讯轻量服务器+Gitbook+Nginx的方案。

其硬件和软件的配置如下：

**硬件：**

腾讯云轻量服务器 2核CPU 2G内存 40G磁盘

**软件：**

系统：Linux VM-24-2-centos 4.18.0-348.7.1.el8_5.x86_64

Nvm：0.33.2

Nodejs：10.14.1

Gitbook: 3.2.3

Nginx：1.14.1

和nodejs、npm挂钩的东西都很容易有版本问题，而且不容易查找，所以在安装时需要仔细核对各项软件的版本。以下简要说明构建博客的各过程：

### 安装各项软件

- nvm安装：

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```

或者

```bash
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.37.2/install.sh | bash
```

- 安装nodejs:

```bash
nvm install 10.14.1 # 安装对应版本nodejs
nvm alias default v10.14.1 # 设置默认nodejs版本
nvm use 10.14.1 # 使用1.14.1版本
```

- 安装gitbook:

```bash
npm install gitbook-cli -g
```

- 安装nginx:

```bash
yum -y install  nginx # 安装nginx
systemctl start nginx # 开启nginx服务
```

### 创建本地Gitbook网页

将准备好的Gitbook仓库clone到本地，其文件结构为：

```bash
├── About # 自定义文件夹
├── Algorithm # 自定义文件夹
├── book.json # Gitbook配置文件
├── Notes # 自定义文件夹
├── Project # 自定义文件夹
├── README.md # Gitbook首页
├── Research # 自定义文件夹
└── SUMMARY.md # Gitbook目录配置文件
```

其中book.json如下：

```json
{
    "title": "Junhui Book",
    "description": "自行、自信、智性",
    "author": "Junhui Luo",
    "output.name": "site",
    "language": "zh-hans",
    "gitbook": "3.2.3",
    "styles": {       
        "website": "./style/website.css"
    },
    "structure": {   
        "readme": "README.md"
    },
    "links": {      
        "sidebar": {
            "君山": "/"
        }
    },
    "plugins": [   
        "anchors",
        "auto-scroll-table", 
    "chapter-fold", 
    "expandable-chapters-small",
    "toggle-chapters",
        "advanced-emoji",  
        "code", 
        "favicon", 
        "fontsettings",                    
        "klipse",  
        "-livereload", 
        "-lunr", 
        "pageview-count",
        "page-toc-button", 
        "popup", 
        "sharing-plus",
        "-sharing", 
        "splitter", 
        "-search",
        "search-pro", 
        "tbfed-pagefooter",
        "hide-element"
    ],
    "pluginsConfig": {   
        "hide-element": {
            "elements": [".gitbook-link"]
        },
        "theme-default": {
            "showLevel": true
        },
        "code": {
            "copyButtons": true
        },
        "tbfed-pagefooter": {
            "copyright": "Copyright © JunShan 2022",
            "modify_label": "本书发布时间：",
            "modify_format": "YYYY-MM-DD HH:mm:ss"
        },
        "page-toc-button": {
            "maxTocDepth": 2,
            "minTocSize": 2
        },
        "sharing": {
            "douban": false,
            "facebook": false,
            "google": false,
            "hatenaBookmark": false,
            "instapaper": false,
            "line": false,
            "linkedin": false,
            "messenger": false,
            "pocket": false,
            "qq": true,
            "qzone": true,
            "stumbleupon": false,
            "twitter": false,
            "viber": false,
            "vk": false,
            "weibo": true,
            "whatsapp": false,
            "all": [
                "weibo","qq", "qzone", "douban", "facebook","google",
                              "linkedin","twitter","whatsapp"
            ]
        },
        "anchor-navigation-ex": {
            "showLevel": true
        },
        "favicon":{
            "shortcut": "",
            "bookmark": ""
        }
    }
}
```

SUMMARY.md如下：

```markdown
# Summary

* [概要介绍](README.md)
* [算法拾遗](Algorithm/README.md)
    * [力扣](Algorithm/leetcode/README.md)
    * [洛谷](Algorithm/luogu/README.md)
    * [PTA](Algorithm/luogu/README.md)
* [学术漫途](Research/README.md)
    * [每周总结](Research/WeelySummary.md)
* [项目实战](Project/README.md)
* [记录与方法论](Notes/README.md)
    * [统计学习方法](Notes/StatisticalMachineLearning.md)
    * [C和指针](Notes/C和指针.md)
    * [C专家编程](Notes/C专家编程.md)
    * [Learning-Algebra-Done-Right-3E](Notes/Learning-Algebra-Done-Right-3E.md)
    * [Github使用命令](Notes/Github使用命令.md)
    * [开发经验](Notes/Others.md)
* [关于我](About/README.md)

```

之后输入如下命令：

```bash
gitbook install # 将book.json中的插件安装至node_modules文件夹
gitbook init # 初始化文件夹
gitbook build # 构建gitbook文件
```

输出的html文件保存在_book文件中，将其复制到nginx前端代理目录下：

```bash
rm -rf   /usr/share/nginx/html/*
cp -r  _book/* /usr/share/nginx/html
```

如此即可通过服务器的公共ip访问gitbook。

### Gitbook博客的工作流程

```mermaid
graph LR
A(本地撰写博客)-->B(上传至Github)
B--检测到Github仓库更新-->C(服务器端更新本地目录)
C-->D(重新构建Gitbook并发布)
```

为使得服务器端的工作自动化需要编写脚本如下：

```bash
#!/bin/sh
export LANG=en_US.UTF-8
source /etc/profile

paths=/home/lighthouse
dirs=("Book")
log_path=$paths/git_log.txt
mail_address="2479694366@qq.com"
echo "begin at: `date`" > $log_path

for i in ${dirs[*]};do
cur_path="$paths/$i"
cd $cur_path
echo $cur_path
echo -e "\n******* $i *************" >> $log_path
git status >> $log_path
echo "---- remote status ---------" >> $log_path
git remote show origin >> $log_path
done

flag=1
for line in `cat $log_path`
do
if echo $line |  egrep -i  "可快进|已过时|FAST-FORWARDABLE|OUT-OF-DATE"
then
flag=0
break
fi
done
if (($flag == 0))
then
mail -s "github更新完成，详情请查看附件" -a $log_path $mail_address < $log_path
cd /home/lighthouse/Book
git config pull.rebas false
git pull
/root/.nvm/versions/node/v10.14.1/bin/gitbook build
rm -rf   /usr/share/nginx/html/*
cp -r  /home/lighthouse/Book/_book/* /usr/share/nginx/html
fi
```

随后设置系统定时任务时期每1小时检测远程仓库是否更新：

```bash
vim /etc/crontab
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
0    *    *    *    *  cd /home/lighthouse/Book & bash book.sh  
```

