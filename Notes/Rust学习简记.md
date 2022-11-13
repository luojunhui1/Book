# Rust 学习简记

## 语言学习

主要还是在[rust官方社区](https://rustwiki.org/)中进行学习。

### 入门指南

rust是一门静态语言，通过toml文件来实现工程的依赖控制，官方提供的主要构建工具有rustc和cargo，一般推荐使用cargo来进行项目的构建和依赖控制，rustc的功能还是比较初级，只有编译应该。cargo构建项目会自动给你建一个本地git仓库（当然这其实是可以设置的），主要有以下的常见命令：

```bash
cargo new "project name"
cargo check # 检查代码能否通过编译
cargo build # 可以增加--release选项来优化编译项目
cargo run # 同时构建和运行项目
```

### 通用编程概念

#### 变量和可变性

- rust中创建的变量默认是不可变的，若要使的变量可变需要加入`mut`关键字；
- rust的存在变量遮蔽机制，激发遮蔽机制的关键字是`let`，遮蔽也具有生命周期；
- rust存在常量类型，其机制与作用域与C++类似，**必须说明数据类型**，约定写法格式为`THREE_HOURS_IN_SECONDS`。

#### 数据类型

- 标量类型，包括整形、浮点型、布尔值和字符
- 符合类型，包括元组和数组，元组不要求元素的类型一致但长度不可变，数组要求类型一致而且长度也不可变。元组支持常见的拆装操作，使用`.`来访问元组的元素；数组可以使用`let x:[ type or value; number]`来初始化，通过下标来访问数组元素会进行越界检查；

#### 函数

- rust的函数需要说明参数的类型

- rust中参数的返回值依靠表达式来返回，而不是像c++或python那样通过return语句显示返回。这里拓展出rust的语句（statement）是不返回值的，但是表达式返回值，所以可以这样写：

  ```rust
  let y = {
          let x = 3;
          x + 1
      };
  ```

#### 控制流

- while和if语句的condition不使用`()`包裹，并且`{}`应该是不能省略；
- loop中的break后可以接表达式用于返回值。

### 认识所有权

#### 所有权

实际上rust中的所有权类似于C++中的作用域，但是所有权在实际实现上有着这样几个明显的特征：

- 当将一个具有`drop trait`的变量，实际上也就是这个变量存储在堆上，原封不动地赋值给另一个变量时，原变量失效，对其进行操作将会报错，也就是原变量的所有权转移到了新的变量上，这是一种很保守的策略，文档中说是为了避免多个变量指向同一块堆内存导致释放资源时的释放顺序和释放时机难以确定的问题，实际上的一个变量就只有一份所有权，避免了这样的问题；
- 但是，若变量具有`copy trait`，也即变量存储在栈上，或者手动使用`clone`函数，那么赋值操作确实会将变量的内容再复制一遍；
- 这样的机制也影响到函数传参，当一个具有`drop trait`形参（parameter）传入到函数中，这个参数的所有权转移到函数内，并且会在函数的末尾执行`drop`函数，除非在函数的返回值中再将这个值返回到上一级的函数空间中，这样就会将变量的所有权再次转移回去。

#### 引用

rust中也存在引用，当使用引用来进行传参时就不用再将变量的所有权击鼓传花传来传去，但是引用的使用也有非常严苛的限制：

- 首先，引用默认是不可以改变引用值的，类似于C++中的`const int *`；
- 其次，为了避免数据竞争，也就是避免出现读脏数据、脏写这样的会破环数据同一性的问题，rust不允许一个变量可变引用与其他类型的任何引用同时存在，这样会甚至会无法通过编译；
- rust不允许垂悬引用的存在，即引用必须是有效的；

#### 切片

rust实现了类似于python的切片语法，其中切片的本质是对源数据的引用，它在设计上可能是针对字符串的，不过也可以用于其他的数组类型。但是在对于被字符串直接定义的变量，如：

```rust
let str = "rust tremendous"
```

str其实是一个不可变的引用，也可以被视作一个切片，这点与C++遥相呼应，在C++的这种情形中，变量str其实是一个内容不可变的指针，也可以被视作一个数组。rust没有指针，但是其引用却明显带有指针的意味。



### 使用结构体组织相关联的数据

####   结构体定义与使用

看到这就初次体会到了rust语言中对于堆内存的保守策略会带来的不方便，这意味着当我们使用一个已有变量对其他变量进行初始化或更新操作时，我们得时刻注意原变量我们是否还需要。

结构体的定义很普通，但是实例化的路径有以下方式：

- ```rust
  // 对每个成员单独实例化
  let user1 = User {
          email: String::from("someone@example.com"),
          username: String::from("someusername123"),
          active: true,
          sign_in_count: 1,
      };
  ```

- ```rust
  // 变量名与结构体成员名相同时可省略'成员名:'
  let user1 = User {
          email,
          username,
          active: true,
          sign_in_count: 1,
      }
  ```

  

- ```rust
  // 自定义部分成员，其他成员使用已有结构体实例进行实例化，但需额外注意，这种方法实际上也是相当于将user2中的成员变量赋值给user1中的成员，所有权的变化也符合之前所讨论的规律
  let user2 = User {
          email: String::from("another@example.com"),
          ..user1
      };
  ```

- ```rust
  // 使用元组创建
  let user2 = (String::from("someone@example.com"), String::from("someusername123"), true, 1)
  ```

#### 结构体的“方法”

  实际上与结构体相关的"方法"有两种，一种是与结构体相关的，参数中带有`&self`的，被称为`方法（method）`，而另一种不做用于结构体实例上，被称为“函数”，如结构体的构造函数等，前者通过implement语义来实现方法，使用`.`运算符来调用，而后者可以直接在结构体中实现，使用`::`操作符来调用。

 

### 枚举和模式匹配

#### 枚举的定义

rust也具有枚举类型`enum`，但是相比于传统C++中的枚举，它有这样两个明显的特点：

- 枚举可以指定的数据类型，如定义IP枚举可以绑定不同的结构体：

  ```rust
  struct IPv4_struct{
      // snip
  }
  
  struct IPv6_struct{
      // snip
  }
  
  enum IP{
      IPv4: IPv4_struct,
      IPv6: IPv6_struct
  }
  ```

  这样做的好处是为统一层次的枚举型增加了除了语义上，但也在结构上的区分性，这在有些场合是非常适用的；

- rust中有一个常见的枚举`Option<T>`，其包含两种枚举型，分别为`None`和`Some(T)`。这个枚举型的产生是因为rust中限制空置`null`的泛滥的设计思路，当一个值可能为null时，那么它必定要被`Option`类型包装，当我们使用这样一个经过了一层包装的变量时，我们又无法不考虑到它为`null`的情形，这在一定程度上确实抑制了由于`null`值导致程序崩溃的可能。

#### match控制流

rust中的match控制流类似于C++中的`switch case`，但是不同之处在于它使用`,`来分隔不同的分支而且不需要使用`break`来显示退出分支控制流，它使用`other`来替换C++中的关键字`default`，并且它还支持一种类似于`lambda`函数的写法，即：

```rust
match x {
            None => None,
            Some(i) => Some(i + 1),
        }
```

这段代码使用i来绑定Some中包含的值，并且在后续使用了这个值。

rust在设计上提供了一个语法糖用于降低只有很少的分支的时候，写`match`分支的复杂性，那就是`if let`语句，如下：

```rust
let mut count = 0;
if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
```

当let语句成立时，这段代码就进入`if`后的语句，也可以在后面加上`else`语句，`else`对应着分支中的`other`分支。

除了上面这些match的用法外，还存在`卫语句（Guard）`和`绑定`等用法，前者可以对分支添加一个if的卫语句以确保只有当当前的分支符合某特定条件时才执行后续语句，后者则确保只有当匹配对象的值符合一定条件时才执行后续语句，例子如下：

```rust

let pair = (2, -2);
println!("Tell me about {:?}", pair);
match pair {
    (x, y) if x == y => println!("These are twins"),
    // ^ `if` 条件部分是一个卫语句
    (x, y) if x + y == 0 => println!("Antimatter, kaboom!"),
    (x, _) if x % 2 == 1 => println!("The first one is odd"),
    _ => println!("No correlation..."),
}

fn age() -> u32 {
    15
}

match age() {
        0             => println!("I haven't celebrated my first birthday yet"),
        // 可以直接匹配（`match`） 1 ..= 12，但那样的话孩子会是几岁？
        // 相反，在 1 ..= 12 分支中绑定匹配值到 `n` 。现在年龄就可以读取了。
        n @ 1  ..= 12 => println!("I'm a child of age {:?}", n),
        n @ 13 ..= 19 => println!("I'm a teen of age {:?}", n),
        // 不符合上面的范围。返回结果。
        n             => println!("I'm an old person of age {:?}", n),
    }
```

### 闭包

rust中提供了类似于lambda表达式的写法，不过在无法自动推导出类型的时候需要指定类型，这种闭包可以被复制给一个`闭包类型`的变量，在后续中也可以使用这个变量执行功能。

```rust
// 通过闭包和函数分别实现自增。
// 译注：下面这行是使用函数的实现
fn  function            (i: i32) -> i32 { i + 1 }

// 闭包是匿名的，这里我们将它们绑定到引用。
// 类型标注和函数的一样，不过类型标注和使用 `{}` 来围住函数体都是可选的。
// 这些匿名函数（nameless function）被赋值给合适地命名的变量。
let closure_annotated = |i: i32| -> i32 { i + 1 };
let closure_inferred  = |i     |          i + 1  ;

// 译注：将闭包绑定到引用的说法可能不准。
// 据[语言参考](https://doc.rust-lang.org/beta/reference/types.html#closure-types)
// 闭包表达式产生的类型就是 “闭包类型”，不属于引用类型，而且确实无法对上面两个
// `closure_xxx` 变量解引用。

let i = 1;
// 调用函数和闭包。
println!("function: {}", function(i));
println!("closure_annotated: {}", closure_annotated(i));
println!("closure_inferred: {}", closure_inferred(i));

// 没有参数的闭包，返回一个 `i32` 类型。
// 返回类型是自动推导的。
let one = || 1;
println!("closure returning one: {}", one());
```

闭包或自动捕获闭包体中所用到的变量，并根据对于变量的捕获类型，自动创建不同类型的闭包类型，捕获的类型有三种，分别为：

- 通过引用：`&T`（可读）
- 通过可变引用：`&mut T`（可读、可修改）
- 通过值：`T`（可读、可修改、可消耗）

这三种捕获类型从上至下明显闭包会对变量获取越来越大的控制权,rust会选择闭包中控制权最大的一种捕获类型来表示闭包的类型：

- `Fn`：表示捕获方式为通过引用的闭包
- `FnMut`：表示捕获方式为通过可变引用的闭包
- `FnOnce`：表示捕获方式为通过值的闭包

当闭包被定义后，编译器其实创建了一个匿名类型的结构体，用以存储闭包捕获的变量，但是如果闭包作为函数参数，这个结构体的类型就是未知的，这时候需要根据实际情况指明实现的到底是`Fn`\\`FnMut`\\`FnOnce`中的哪一个，如：

```rust
fn apply<F>(f: F) where
    F: FnOnce() {
    f();
}
```

当闭包或函数作为输出函数时，由于rus他不允许返回泛型，并且需要在闭包前加入move关键字，这表明对应的闭包的所有捕获都是通过值进行的，否则闭包内获取的只是无效的引用：

```rust
fn create_fn() -> impl Fn() {
    let text = "Fn".to_owned();

    move || println!("This is a: {}", text)
}
```

## 实战经验

- 首先，我将学习开源项目https://github.com/Nukesor/pueue，并以此为基础开始熟悉Rust工程；

  
