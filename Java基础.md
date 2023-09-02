# jdk、jre、jvm的区别
- jdk：jre + 开发工具【编译、打包】
- jre：java runtime environment【jvm、java核心类库】
- jvm：java解释器将字节码解释成特定的机器码交由不同的机器进行执行，从而达到跨平台的效果
# Java和C++
## 相同点
- 都是面向对象
- 封装、继承和多态
## 不同点
- Java不支持指针
- Java的类只能单继承，接口可以多继承；C++的类可以多继承
- Java有GC，不需要程序员手动释放内存
# Oracle JDK和Open JDK
## Oracle JDK
- 版本3年发布一次
- 不完全开源
- 更加稳定
- 性能更高
- 不会为版本提供长期支持
- 通过二进制代码许可协议获取许可
## Open JDK
- 版本3个月发布一次
- 完全开源
- 不稳定
- 通过GPL v2许可协议获取许可
# 基本数据类型
## 数值型
- byte
- short
- int
- long
- float
- double
## 字符型
- char
## 布尔型
- boolean
##
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171744c434465b69~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.awebp)
# 引用数据类型
- 类
- 接口
- 数组
# 访问修饰符
## private
- 同一个类中可见
- 可以修饰变量、方法、内部类
- 不能修饰外部类
## default
- 缺省
- 同一个包中可见
## protected
- 同一个包、所有子类可见
- 可以修饰变量、方法、内部类
- 不能修饰外部类
## public
- 所有类可见
- 可以修饰变量、方法、类、接口
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171744c433bcfd38~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.awebp)
# final、finally、finalize
## final
- 修饰变量、方法、类
- 被修饰的变量不可以被改变，不可改变的是变量引用，引用指向的内容是可以改变的
- 被修饰的方法不可以被重写
- 被修饰的类不可以被继承
## finally
- 用于try-catch代码块，无论是否发生异常，finally中的代码一定会执行
## finalize
- 属于Object类的一个方法
- 由gc调用
# this、super
## this
- 代表对象本身的指针
- 需要放在构造函数的第一行
- 不能用于static
## super
- 代表对象父类的指针
- 需要放在构造函数的第一行
- 不能用于static
- this和super不能同时出现在一个构造函数中，因为this调用其他构造函数，其他构造函数必然会调用super【隐式或显示】，编译器无法通过
# static
- 独立于对象，优于对象存在
- 静态代码块优化程序性能
- 类加载时，按照静态代码块的顺序执行，且只执行一次
- 类加载时，分配存储空间，创建对象时不会重新分配，但可以赋值
- 可以修饰变量、方法、代码块、静态内部类、静态导包
- 静态只能访问静态
- 非静态可以访问非静态和静态
# 面向对象三大特性
## 封装
-  属性私有化，对外提供访问接口
-  属性的方法灵活控制属性的读取和修改，防止错误修改属性，有助于封装实现细节
## 继承
- 将已有的类作为基础定义新类
- 子类可以增加新的数据和方法
- 子类拥有父类非private修饰的属性和方法
- 子类可以重写父类的方法
## 多态
- 父类或接口定义的引用变量可以指向子类或具体实现类的实例对象
### 编译时多态
- 方法的重载
### 运行时多态
- 父类的引用指向子类的实例
- 必须满足：继承、重写、向上转型
- 被引用对象的类型决定调用谁的成员方法
# 面向对象五大基本原则
## 单一职责原则-SRP
- 类的功能必须单一
## 开放封闭原则-OCP
- 类对于扩展是开放的，对于修改时封闭的
## 里式替换原则-LSP
- 子类可以替换父类出现在父类能够出现的地方
## 依赖倒置原则-DIP
- 高层不依赖低层，两者都应该依赖抽象
## 接口分离原则-ISP
- 设计时采用多个接口优于采用一个通用的接口
# 抽象类和接口
## 抽象类
- 定义时使用abstract关键字
- 子类使用extends关键字继承
- 子类不是抽象类的话，必须实现父类的所有抽象方法
- 可以有构造函数
- 可以被任意修饰符修饰
- 只能单继承
## 接口
- 定义时使用interface关键字
- 子类使用implents关键字实现
- 子类必须实现接口的所有方法
- 不能有构造函数
- 不能被private和protected修饰
- 可以多实现
- 属性默认是static和final
## 相同点
- 都不能实例化
- 位于顶层，被继承和实现
- 都有抽象方法
## 对象引用和对象实例
- 对象实例通过new创建后，存储在堆内存
- 对象引用创建后存储在栈内存
- 对象引用可以指向0或1个对象
- 对象实例可以被无数对象引用指向
# 变量和方法
## 成员变量和局部变量
### 成员变量
- 在类内部，方法外部定义
- 整个类有效
- 存储在堆内存
- 随对象的创建而存在，随对象的回收而消失
- 有默认的初始值
### 局部变量
- 在类的方法内部定义
- 方法内有效
- 存储在栈内存
- 方法调用完自动释放
- 没有默认初始值，使用前必须赋值
## 静态变量和实例变量
### 静态变量
- static修饰
- 所有类对象共享
- 类加载时分配内存空间
### 实例变量
- 创建对象时分配内存空间
## 定义一个无参不做事的构造函数
- 子类和父类，父类定义了有参的构造函数，子类在初始化时，会隐式调用父类的无参构造函数，编译报错
- 未定义构造函数时，会有默认的无参构造函数
- 定义了构造函数，默认的无参构造函数就没了
## 构造函数的作用
- 完成对类对象的初始化
## 构造函数的特性
- 方法名和类名一样
- 无返回值，但不能用void声明
- 创建对象时自动执行，无需调用
# 内部类
## 成员内部类
- 定义在类内部的非静态类
- 可以访问外部类的所有变量和方法
- 创建方式：外部类实例.new 内部类()
```java
public class OuterClass {
    private static int a = 1;
    private int b = 2;
    class InnerClass {
        public void test() {
            System.out.println("..." + a);
            System.out.println("..." + b);
        }
    }
}

OuterClass outer = new OuterClass();
OuterClass.InnerClass inner = outer.new InnerClass();
inner.test();
```
## 静态内部类
- 定义在类内部的静态类
- 只能访问外部类的静态变量和静态方法
- 创建方式：new 外部类.内部类()
```java
public class OuterClass {
    private static int a = 1;
    private int b = 2;
    static InnerClass {
        public void test() {
            System.out.println("..." + a);
        }
    }
}
OuterClass.InnerClass inner = new OuterClass.InnerClass();
inner.test();
```
## 局部内部类
- 定义在方法内部的类
- 实例方法内定义的类，可以访问外部类的所有变量和方法
- 静态方法内定义的类，只能访问外部类的静态变量和静态方法
- 创建方式：在方法内创建 new 内部类()
```java
public class OuterClass {
    private static int a = 1;
    private int b = 2;
    public void test1() {
        class InnerClass {
            public void test() {
                System.out.println("..." + a);
                System.out.println("..." + b);
            }
        }
        InnerClass inner = new InnerClass();
        inner.test();
    }
    public static void tese2() {
        class InnerClass {
            public void test() {
                System.out.println("..." + a);
            }
        }
        InnerClass inner = new InnerClass();
        inner.test();
    }
}
```
## 匿名内部类
- 在方法中无名称的内部类
- 必须继承已有类或实现接口
- 不能定义静态变量和静态方法
- 使用外部方法中的变量必须声明为final
- 创建方式：new 类/接口 {}
```java
public class OuterClass {
    public void test(final int i) {
        public final int a = 5;
        new A {
            @Override
            public void fun(int x) {
                int y = x + i;
                System.out.println("..." + y);
            }
        }.fun(1);
        new B {
            @Override
            public void fun(int y) {
                int z = y + a;
                System.out.println("..." + z);
            }
        }.fun(1);
    }
}
public class A {
    public void fun(int x) {
        System.out.println("..." + x);
    }
}
public interface B {
    void fun(int y);
}
```
## 内部类的优点
- 内部类可以访问私有数据
- 内部类不能被同一个包下的其他类访问
- 匿名内部类很方便实现回调
# 重载和重写
## 重载
- 同一个类
- 参数类型、参数个数、参数顺序不同
- 不能通过返回值类型和修饰符类型判断
- 编译时多态
- 构造函数可以被重载
## 重写
- 父子类
- 方法的信息全一样，除了实现不一样
- 子类返回值类型小于等于父类
- 子类抛出的异常小于等于父类
- 子类修饰符类型大于等于父类
# ==、equals、hashCode
## ==
- 基本数据类型比较的是值
- 引用类型比较的是内存地址
## equals
- 类没有重写equals方法，等价于==
- 类重写了equals方法，判断引用对象的内容是否一样
## hashCode
- 获取哈希码（散列码）
- 哈希码的作用是确定对象在哈希表中的索引位置
## 为什么重写equals时，都会重写hashCode
- hashCode可以加快判断速度
- 每次都用equals判断内容是否一致很耗时
# 值传递和引用传递
## 值传递
- java只有值传递
- 可以改变引用对象参数的属性
- 方法不可以修改值传递的变量值
- 方法修改的只是参数的拷贝
- 方法不能让对象参数引用新的对象
- 方法不能改变基本数据类型的参数
## 引用传递
- 方法可以修改引用传递的变量值
# IO
## IO流
- InputStream：字节输入流
- OutputStream：字节输出流
- InputReader：字符输入流
- OutputWriter：字符输出流
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171744c4799a7a74~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.awebp)
## BIO、NIO、AIO
![](https://zhuanlan.zhihu.com/p/419345478)
### BIO
- 同步阻塞IO
- 一个线程处理一个请求
![](https://pic3.zhimg.com/80/v2-bc76c5646097f2be6cf6fb7b8ad76b16_1440w.webp)
### NIO【JDK1.4】
- 同步非阻塞IO
- 一个线程处理多个请求
- Channel、Selector、Buffer
- 多路复用
![](https://pic2.zhimg.com/80/v2-5d1e9dabeb52ab1241dcf779e63c4a79_1440w.webp)
### AIO【JDK1.7】
- 异步非阻塞IO
- 基于事件和回调机制
# 反射
## 定义
- 在运行状态，对于任意一个类，都可以知道类的所有属性和方法
- 在运行状态，对于任意一个对象，都可以调用对象的所有方法
## 使用场景
- spring框架
- 动态代理
### 实现反射的三种方法
- new对象
- 全限定路径
- 类名
```java
@Data
public class Test {
    private int a;
}
public class Main {
    public static void main(String[] args) throws ClassNotFoundException {
        // 1
        Test test = new Test();
        Class classObj1 = test.getClass();
        // 2
        Class classObj2 = Class.forName("*.*.*.Test");
        // 3
        Class classObj3 = Test.class;
    }
}
```
# String、StringBuilder、StringBuffer
## String
- 不可继承
- 常量池优化
- 不可修改
- 线程安全
## StringBuffer
- 可以修改
- 加锁同步，线程安全
## StringBuilder
- 可以修改
- 线程不安全