---
layout: post
title: "Rails 常量加载机制"
date: 2020-04-09 09:12:45 +0800
comments: true
categories: Rails
---

## 0x1 基本介绍

首先，在编写Rails应用时，代码会预加载：通过约定类定义所在的文件名与类名一致映射，实现自动加载

Rails 通过config.cache_classes参数来设置常见加载的模式，主要有以下两种形式：

- Kernel#require(一般用于生产环境，只加载一次)
- Kernel#load（一般用于开发环境）

除了加载的方式不同，在`config.cache_classes = false`时，Rails还会启用Reloader中间件
在代码发生变更时，通过`remove_constant`/`const_missing`等方法实现么常量、模块热替换

```ruby
# railties-4.0.13/lib/rails/application.rb:384
unless config.cache_classes
    middleware.use ::ActionDispatch::Reloader, lambda { app.reload_dependencies? }
end
```

下面，本文逐步解析下Ruby及Rails下的常量加载机制

## 0x2 常量刷新机载

Ruby中常见的常量：

- 模块 module
- 类 class
- 自定义常量

其中，既然module和class在Ruby中本质就是常量的话，类和模块定义的嵌套创建的命名空间也是常量了

Ruby 的常量嵌套从内向外展开，嵌套通过Module.nesting方法审查

```ruby
module X
  module Y
    def self.test
      puts Module.nesting
    end
  end
end
X::Y.test

module A
  module B
     def self.test
      puts Module.nesting
    end
  end
end
A::B.test

module X::Y
  module A::B
    def self.test
      puts Module.nesting
    end
  end
end
A::B.test

# >>
#[X::Y, X]
#[A::B, A]
#[A::B, X::Y]
```

从上面的例子看出，嵌套中的类和模块的名称与所在的命名空间没有必然联系

嵌套是解释器维护的一个内部堆栈，根据下述规则修改：
1. 执行 class 关键字后面的定义体时，类对象入栈；执行完毕后出栈。
2. 执行 module 关键字后面的定义体时，模块对象入栈；执行完毕后出栈。
3. 执行 class << object 打开的单例类时，类对象入栈；执行完毕后出栈。
4. 调用 `instance_eval` 时如果传入字符串参数，接收者的单例类入栈求值的代码所在的嵌套层次。
5. 调用 `class_eval` 或 `module_eval` 时如果传入字符串参数，接收者入栈求值的代码所在的嵌套层次.
6. 顶层代码中由 Kernel#load 解释嵌套是空的，除非调用 load 时把第二个参数设为真值；如果是这样，Ruby 会创建一个匿名模块，将其入栈。

定义类和模块的本质是为常量赋值

```ruby
class C
end
# => 本质：在Object中创建一个常量C，并将一个类对象存储进去

class Project < ApplicationRecord
end
＃ => 本质：Project = Class.new(ApplicationRecord)


module Admin
end
# => 本质：Admin = Module.new

Admin.name # => "Admin"
```

常量赋值的一条特殊规则：如果被赋值的对象是匿名类或模块，Ruby 会把对象的名称设为常量的名称。

自此之后常量和实例发生的事情无关紧要。例如，可以把常量删除，类对象可以赋值给其他常量，或者不再存储于常量中，等等。名称一旦设定就不会再变。

## 0x3 常量解析
### 0x31 映射
当常量存储在模块中，常量就会和类和模块中的常量表关联映射（类似哈希表）

```ruby
module Colors
    RED = '0xff0000'
end
```

解析模块定义体时，会在Colors常量中的常量表新建一条映射，把"RED"映射到字符串"0xff0000"


### 0x32 Ruby下的解析

相对常量、绝对常量、限定常量

```ruby
Billing::Invoice #此时，Billing为相对常量，Invoice为限定常量
::Billing::Invoice #此时，Billing为绝对常量（顶层常量）在Object中查找
```

相对常量解析：
在代码中的特定位置，假如使用 cref 表示嵌套中的第一个元素，如果没有嵌套，则表示 Object。

1. 嵌套不为空，在嵌套元素中按元素顺序查找，元素祖先忽略不记
2. 未果，向上回溯，进去cref的祖先链
3. 未果，当cref为module时，进入Object查找常量
4. 未果，在cref上调用const_missing，默认抛出NameError异常，可覆写

限定常量解析：
上面例子 Invoice 由 Billing 限定，解析算法如下

1. 在 Billing 及其祖先中查找 Invoice 常量
2. 未果，调用 Billing 的const_missing方法，默认抛出NameError异常

但Rails 的自动加载机制没有仿照这个算法，查找的起点是要自动加载的常量名称和限定的类或模块对象

如果缺失限定常量，Rails 不会在父级命名空间中查找。

但是有一点要留意：缺失常量时，Rails 不知道它是相对引用还是限定引用。

如果类或模块的父级命名空间中没有缺失的常量，Rails 假定引用的是相对常量。否则是限定常量。

还有在Rails开发环境中，常量时惰性加载的。遇到不存在的常量再触发`const_missing`使用Rails的自动加载机制

但在生产环境中，预先把所有 autoload 目录下的文件都加载过了。没有触发`const_missing`使用Ruby本身的常量查找

## 0x4 加载机制
config.cache_classes 设为 false 时，Rails 会重新自动加载常量

在应用运行的过程中，如果相关的逻辑有变，会重新加载代码。为此，Rails 会监控下述文件：

- config/routes.rb
- 本地化文件
- autoload_paths 中的 Ruby 文件
- db/schema.rb 和 db/structure.sql

如果这些文件中的内容有变，有个中间件会发现，然后重新加载代码。

主要原理：
1. 先覆写 `const_missing` 方法，按需去load对应依赖
2. 监听文件变化，自动加载机制会记录自动加载的常量
3. 检测到发生变更，重新加载机制使用 Module#remove_const 方法把它们从相应的类和模块中删除
4. 这样，运行代码时那些常量就变成未知了，从而按需重新加载文件。

但是，因为类之间的依赖极难处理。Rails默认reloader模块经常比较极端，不止重新加载有变化的代码，而是重载一切

### 0x41 Ruby Module#autoload 的缺陷

Module#autoload 是Ruby 提供的惰性常量加载机制，可以遍历应用树调用autoload把文件名和常规的常量名对应起来

但是，Module#autoload 只能使用 require 加载文件，因此无法重新加载。

不仅如此，它使用的还仅是 require 关键字，而不是 Kernel#require 方法。

因此，删除文件后，它无法移除声明。如果使用 Module#remove_const 把常量删除了，不会触发 Module#autoload

综上，在Rails的常量自动加载机制中使用了覆写Module#const_missing 的方式来实现

Rails(ActiveSupport) 中的会根据触发 const_missing 的常量名称来猜测并尝试加载对应的文件, 以加载 Auth::User 为例:

```ruby
# demo/role.rb
module Demo
  class Role
    User
  end
end

# demo/user.rb
module Demo
  class User
    "class Demo::User loaded"
  end
end

# app/models/auth/user.rb
module Auth
  class User
  end
end
```

Demo::Role 找不到 User 常量, 触发 `const_missing(const_name)`, 此处 `const_name == 'User'`

ActiveSupport 中先拼接出来一个查询的起点 "#{Demo::Role.name}::#{const_name}", 即 Demo::Role::User

首先尝试查找 autoload_paths 下的 demo/role/user.rb, 没找到

然后往上走一层, 尝试查找 autoload_paths 下的 demo/user.rb, 找到了
