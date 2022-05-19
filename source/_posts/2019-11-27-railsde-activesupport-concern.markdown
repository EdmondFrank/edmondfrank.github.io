---
layout: post
title: "Railsçš„ActiveSupport::Concern"
date: 2019-11-27 18:47:49 +0800
comments: true
categories: Ruby
---

### 1）简单内部类
```ruby
module ActiveSupport::Concern
  .....
  class MultipleIncludedBlocks < StandardError #:nodoc:
    def initialize
      super "Cannot define multiple 'included' blocks for a Concern"
    end
  end
end
```
ActiveSupport::Concern简单定义了一个内部的错误类型，
这个自定义错误类型主要用于提醒我们在扩展了ActiveSupport::Concern的模块中

只能够显式调用模块方法ActiveSupport::Concern::included一次，

第二次调用的话就会抛出这个自定义的错误类型。

### 2）设置“胎记”
我们前面说过扩展了ActiveSupport::Concern的模块我把它简称为依赖模块，那我们怎么知道一个模块是不是依赖模块呢？答案就在这个类方法。
```ruby
module ActiveSupport::Concern
  .....
  def self.extended(base) #:nodoc:
    base.instance_variable_set(:@_dependencies, [])
  end
end
```
写过Ruby的应该都知道，这个是当一个模块被扩展(extend)之后就会被调用的一个回调方法，

并以扩展它的模块做为参数(base)传入该回调方法。

当一个模块扩展了ActiveSupport::Concern之后就会在模块内部设置一个实例变量

@_dependencies并设置为空数组，我们姑且把它看做是ActiveSupport::Concern的“胎记”，

后面我们会利用这个“胎记”所包含的依赖项，优雅地增强我们的终端模块。

### 3) 收集并扩展相关方法
在分析后面的方法之前我们先来简单看一下的原理。

我们都知道在Ruby里面如何定义一个类，并且定义相关的类方法，和实例方法

（我们只需要用class关键字就能够很容易做到这一点），但不知道大家是否了解还有这样一种方式？
```ruby
instance_block = Proc.new do
  def instance_method
    puts "I am instance method"
  end

  def self.class_method_in_instance_block
    puts "I am class method in instance block"
  end
end

class_block = Proc.new do
  def class_method
    puts "I am class method"
  end
end


Example = Class.new

Example.class_eval(&instance_block)
ClassModule = Module.new
ClassModule.module_eval(&class_block)
Example.extend(ClassModule)

Example.class_method
Example.class_method_in_instance_block
Example.new.instance_method
```
运行结果
> 
I am class method 
I am class method in instance block 
I am instance method

运行结果有点意思，下面简单来分析一下:
我们以代码块的方式收集了类方法class_method，class_method_in_instance_block，

以及实例方法instance_method。然后我们创建一个空的类Example，

用Class#class_eval方法来打开类，在类的上下文环境下运行块instance_block，

其实这就相当于我们在类上下文中运行相应的语句，这样就能够得到Example#instance_method,

Example::class_method_in_instance_block这两个方法了。
另外，我们从已有的知识中了解到，可以通过Class#extend扩展模块的方式来获得类方法。

为此我们可以把class_block这个代码块包裹在模块ClassModule中，

最后我们只需要扩展(extend)这个模块就可以得到相应的类方法Example#class_method了。

ActiveSupport::Concern其实就是这种黑科技，

这里我简单把接下来的过程分为两部分

1）收集方法。

2）对收集的方法进行功能扩展

a. 方法收集
来看看依赖模块如何收集方法的？
```ruby
module ActiveSupport::Concern
  .....
  def included(base = nil, &block)
    if base.nil?
      raise MultipleIncludedBlocks if instance_variable_defined?(:@_included_block)

      @_included_block = block
    else
      super
    end
  end
end
```
首先我们来看ActiveSupport::Concern#included，

当ActiveSupport::Concern被扩展之后这个included方法就会变成相应模块的类方法了。

咦，这不是一个模块被包含之后才会被调用的回调函数吗？没错，为了不影响它原来的功能rails团队采用了个巧妙的做法，

当我们在扩展了ActiveSupport::Concern的模块的上下文中显式调用included方法，

并且不带任何参数而只传入代码块的情况下，便会在模块内部设置一个@_included_block实例变量来接收这个代码块，

换句话说这个实例变量就是我们将来需要在终端模块上下文运行的代码块。

而在其他情况下则通过super关键字来调用原始版本的included方法。

这样既增强了included方法又不影响原始方法的使用。

另外我们也注意到了，在一个模块里面included方法只能被显式调用一次，

否则会抛出MultipleIncludedBlocks这个自定义的错误，这便是前面定义的内部类的应用场景。

OK，收集完需要在模块上下文运行的代码，我们接下来要收集类方法了。
```ruby
module ActiveSupport::Concern
  .....
  def class_methods(&class_methods_module_definition)
    mod = const_defined?(:ClassMethods, false) ?
      const_get(:ClassMethods) :
      const_set(:ClassMethods, Module.new)

    mod.module_eval(&class_methods_module_definition)
  end
end
```
其实这个东西跟前面的included方法原理差不多，也是通过代码块来收集方法，只是有一点点不同，

我们需要把这个收集到的代码块包裹到一个模块中，以后再扩展这个模块。

首先通过const_defined?来判断常量ClassMethods是否存在，

该方法的第二个参数false表示只从当前模块查找该常量，而不会从祖先链中去查找。

如果没有则以ClassMethods为名定义一个模块，

然后以Module#module_eval方法打开该模块并在模块的上下文运行我们所接收的代码块class_methods_module_definition。

这样模块ClassMethods就会包含对应的方法了。

在以后的日子里我们只需要扩展ClassMethods，该模块里面的方法就能成为目标模块的类方法了。

b. 功能增强
收集完相关功能之后可以来看如何增强我们的终端模块了。在分析代码之前，

先来认识一下append_features，我们需要知道的是它会在模块被包含的时候调用，并且它会在included回调方法之前被调用。

接下来我们看看扩展功能的源代码，它应该是ActiveSupport::Concern里面最复杂的方法了。
```ruby
module ActiveSupport::Concern
  ....
  def append_features(base)
    if base.instance_variable_defined?(:@_dependencies)
      base.instance_variable_get(:@_dependencies) << self
      return false
    else
      return false if base < self
      @_dependencies.each { |dep| base.include(dep) }
      super
      base.extend const_get(:ClassMethods) if const_defined?(:ClassMethods)
      base.class_eval(&@_included_block) if instance_variable_defined?(:@_included_block)
    end
  end
end
```
现在我们可以看到之前设置“胎记”起作用了，设置了“胎记”的依赖模块也可以被其他的依赖模块所包含，

但他们并不会进行相应的功能扩展，他们会做的只是在“胎记”列表@_dependencies里面添加对应的依赖选项。

然后返回false。这也是我们第一个条件分支的逻辑。
当我们的依赖模块终于被终端模块（不含“胎记”的模块）包含的时候我们程序便可以走else分支的逻辑。

如果该依赖模块已经在终端模块的祖先链中的话则表明这个模块已经在终端模块增强过了，我们就没必要做重复工作，直接返回false。

否则的话继续执行后面的代码，接下来这个语句有点意思
@_dependencies.each { |dep| base.include(dep) }

我们会以终端模块的身份去包含当前依赖模块的@_dependencies列表里面的所有模块，

这个时候我们@_dependencies的模块又会进入各自相应的append_features方法，并且都会走else分支，

然后查看各自@_dependencies接下来又会以终端模块的身份再去包含列表里的那些模块，

以此类推。这递归的过程就像是链式反应，这样就能保证不管各个依赖模块之间的依赖关系如何，

我们的终端模块都不用太过在意了，反正最后都会被我们终端模块给包含掉。

接下来调用super关键字来调用原始的append_features方法，保证了原始的功能。

毕竟我们这里做的是对原始功能加强，而并不是要复写掉原始功能。

最后我们在每一个依赖模块内部都会执行大家所熟悉的语句
base.extend const_get(:ClassMethods) if const_defined?(:ClassMethods)
base.class_eval(&@_included_block) if instance_variable_defined?(:@_included_block)

最最最后，咱们的终端模块就会具备我们预先定义好的类方法，实例方法。

并且可以直接运行一些预先定义好需要在模块上下文运行的类方法了。

### 优雅的Ruby
源码分析暂时告一段落，为了让我们对ActiveSupport::Concern印象更加深刻一些。最后我用一个简单的例子来展示一下这个库的优雅之处
```ruby
require 'active_support'

module A
  extend ActiveSupport::Concern

  included do
    def number1
      "number1 method"
    end
  end

  class_methods do
    def active1
      "active1 class method"
    end
  end
end

module B
  extend ActiveSupport::Concern
  include A

  included do
    def number2
      "number2 method"
    end
  end

  class_methods do
    def active2
      "active2 class method"
    end
  end
end

module C
  extend ActiveSupport::Concern
  include B

  included do
    def number3
      "number3 method"
    end

    def number2
      "number2 in C"
    end
  end

  class_methods do
    def active3
      "active3 class method"
    end

    def active2
      "active2 in C"
    end
  end
end

class Example
  include C
end

p "#{A.instance_variable_get("@_dependencies")}"
p "#{B.instance_variable_get("@_dependencies")}"
p "#{C.instance_variable_get("@_dependencies")}"

puts Example.active3
puts Example.active2
puts Example.active1

puts Example.new.number3
puts Example.new.number2
puts Example.new.number1
```
最后的打印结果是 
>
"[]" "[A]" "[B]" 
active3 class method 
active2 in C 
active1 class method 
number3 method 
number2 in C 
number1 method

利用ActiveSupport::Concern我们可以用少量的代码，优雅地定义我们的扩展模块，并在需要的时候进行功能增强。

在该例子中我通过打印“胎记”可以知道依赖模块之间的依赖关系，

而且他们之间的依赖关系终端模块根本不会在意，它只需要包含一个依赖模块，

便可以得到有依赖关系其他依赖模块中所定义的功能了。

另外，我在模块C中做了些手脚，复写了它所依赖的模块B中所收集的方法，

是为了展示我们所收集方法在各个有依赖关系模块中的优先级关系。