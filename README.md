# 初学rust指针笔记 #

rust指针 （pointer）是一个包含内存地址的变量的通用概念。这个地址引用，或 “指向”（points at）一些其他数据。总体分为三类，引用，智能指针，裸指针

+ 引用

    + 引用（reference）像一个指针，因为它是一个地址，我们可以由此访问储存于该地址的属于其他变量的数据

    + 与指针不同，引用确保指向某个特定类型的有效值

    + 与使用 & 引用相反的操作是 解引用（dereferencing），它使用解引用运算符 *

    + &s1 语法让我们创建一个 指向 值 s1 的引用，但是并不拥有它

    + 我们将创建一个引用的行为称为 借用（borrowing）

    + 引用以 & 符号为标志并借用了他们所指向的值。除了引用数据没有任何其他特殊功能。它们也没有任何额外开销，所以应用得最多

    + rust的每一个引用都有其生命周期，什么时候需要使用声明周期：当无法判断相关引用的生命周期大小，例如相关if语句，结构体等等，可以把生命周期看成和引用相绑定的参数

    + slice是一个特殊的引用，slice 允许你引用集合中一段连续的元素序列，而不用引用整个集合

    + 在任意给定时间，要么 只能有一个可变引用，要么 只能有多个不可变引用

    + 引用必须总是有效的，避免悬垂引用，可用生命周期保证
    

    - 相关方法
    
        - pub const fn as_ptr(self) -> *const T

            - 将裸指针返回到切片的缓冲区

            - 这等效于将 self 强制转换为 *const T，但类型安全性更高
    
        - pub const fn as_mut_ptr(&mut self) -> *mut T

            - 返回指向切片缓冲区的不安全可变指针

            - 调用者必须确保切片比该函数返回的指针有效，否则它将最终指向垃圾

            - 修改此切片引用的容器可能会导致重新分配其缓冲区，这也将使指向它的任何指针无效

        - pub unsafe fn from_raw_parts<'a, T>(data: *const T, len: usize) -> &'a [T]

            - 根据指针和长度形成切片

            - len 参数是 元素 的数量，而不是字节数

        - pub unsafe fn from_raw_parts_mut<'a, T>(data: *mut T, len: usize) -> &'a mut [T]
            
            - 执行与 from_raw_parts 相同的功能，除了返回可变切片

        
    - str相关方法

        - pub const fn as_bytes(&self) -> &[u8]ⓘ

            - 将字符串切片转换为字节切片。 要将字节切片切回为字符串切片，请使用 from_utf8 函数
            
        - pub unsafe fn as_bytes_mut(&mut self) -> &mut [u8]ⓘ

            - 将可变字符串片段转换为可变字节片段。

+ 智能指针

    + 普通引用和智能指针的一个额外的区别是引用是一类只借用数据的指针；相反，在大部分情况下，智能指针 拥有 他们指向的数据

    + 智能指针通常使用结构体实现。智能指针区别于常规结构体的显著特性在于其实现了 Deref 和 Drop trait

    + Deref trait 允许智能指针结构体实例表现的像引用一样，这样就可以编写既用于引用、又用于智能指针的代码

    + Drop trait 允许我们自定义当智能指针离开作用域时运行的代码
 
    + 由于 Rust 的所有权，也就是说 Box<Point>，永远都是持有一份自己的 Point 对象；因此我们想要引用别的数据时，其实也就是将引用的变量赋值给 Box 就能形成类似的效果

    + Deref 强制转换将这样一个类型的引用转换为另一个类型的引用，可实现自动解引用

    + Box, Rc, RefCell, Weak, Mutex, Rwlock, Arc, String, Vec
    
    
    - 相关方法

        
    
        - 

+ 裸指针 Pointer

    + 裸指针与引用和智能指针的区别在于

        + 允许忽略借用规则，可以同时拥有不可变和可变的指针，或多个指向相同位置的可变指针

        + 不保证指向有效的内存

        + 允许为空
         
        + 不能实现任何自动清理功能
    
    
    - 相关方法

        - pub const unsafe fn copy_from(self, src: *const T, count: usize)
    
            - 将 count * size_of<T> 字节从 src 复制到 self。 源和目标可能会重叠
        
        - pub const unsafe fn copy_to(self, dest: *mut T, count: usize)

            - 将 count * size_of<T> 字节从 self 复制到 dest。 源和目标可能会重叠
        
        - pub unsafe fn write_bytes(self, val: u8, count: usize)
    
            - 在指定的指针上调用 memset，将 self 开始的 count * size_of::<T>() 内存字节设置为 val
        
        - pub const unsafe fn write(self, val: T)

            -用给定值覆盖存储位置，而无需读取或丢弃旧值
        
        - pub const unsafe fn read(self) -> T

            - 从 self 读取值而不移动它。 这将使 self 中的内存保持不变
    
        - pub unsafe fn write_volatile(self, val: T)

            - 使用给定值对存储单元执行易失性写操作，而无需读取或丢弃旧值

            - 易失性操作旨在作用于 I/O 存储器，并保证编译器不会在其他易失性操作中对易失性操作进行清除或重新排序
    
        - pub unsafe fn read_volatile(self) -> T

            - 对 self 的值进行易失性读取，而无需移动它。这将使 self 中的内存保持不变

            - 易失性操作旨在作用于 I/O 存储器，并保证编译器不会在其他易失性操作中对易失性操作进行清除或重新排序
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
        
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
        

