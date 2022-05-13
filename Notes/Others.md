# ......
用于记录使用各种工具过程中的经验

## 第一次安装OpenCV3.4.0失败

安装cuda倒没什么问题，注意wget下载cuda_.run文件的时候最后会出现“核心已转储”的错误（不知道此时的下载的。run包还能不能用）,weget下载deb包安装就没问题。还有，安装系统的时候，分配给/的挂载空间大一点，第一次分配了20G,安个cuda就没了。

<!--more-->

安装opencv会用到以下链接
https://blog.csdn.net/qq_39839546/article/details/107427546
https://blog.csdn.net/u014613745/article/details/78310916
https://blog.csdn.net/qq_17783559/article/details/105078293?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param
https://blog.csdn.net/qq_36327203/article/details/79372152

不过最终还是没能成功装上opencv-cuda版，虽然opencv3.4.0太老了不支持cuda11.0这种理由不太可信，但姑且也只能这么想了。下次换个高版本的opencv试试。

## 第二次安装OpenCV4.4.0成功

和第一次过程差不多，不过这次找到了个极为靠谱的教程。教程中有一个问题，就是安装cuDNN的时候，需要把cudnn_version.h内的宏定义复制到cudnn.h中，这样使用cmake配置编译opencv的时候才能找到对应版本的cuDNN。高版本的cuDNN的版本信息在cudnn_version.h内，和网络上大部分教程不同。

完美符合我的需求，安装成功，最后的darnet也运行成功。

https://my.oschina.net/u/4283198/blog/4684264

下面这个可能有用，我就是受这个启发把cudnn_version.h的内容复制到cudnn.h的。

https://www.cnblogs.com/phillee/p/11173316.html

