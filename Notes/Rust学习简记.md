# Rust 学习简记

## 语言学习（https://rustwiki.org/）

主要还是在rust官方社区中进行学习。

### 入门指南

rust是一门静态语言，通过toml文件来实现工程的以来控制，官方提供的主要构建工具有rustc和cargo，一般推荐使用cargo来进行项目的构建和依赖控制，rustc的功能还是比较初级，只有编译应该。cargo构建项目会自动给你建一个本地git仓库（当然这其实是可以设置的），主要有以下的常见命令：

>cargo new "project name"
cargo check # 检查代码能否通过编译
cargo build # 可以增加--release选项来优化编译项目
cargo run # 同时构建和运行项目

### 通用编程概念

#### 变量和可变性

- rust中创建的变量默认是不可变的，若要使的变量可变需要加入`mut`关键字；
- rust的存在变量遮蔽机制，激发遮蔽机制的关键字是`let`，遮蔽也具有生命周期；
- rust存在常量类型，其机制与作用域与C++类似，**必须说明数据类型**，约定写法格式为`THREE_HOURS_IN_SECONDS`。

#### 数据类型

## 实战经验

