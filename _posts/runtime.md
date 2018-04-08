---
published: false
---
```
typedef struct objc_Class *Class;
typedef struct objc_object *id;
typedef struct method_t *Method;
typedef struct ivar_t *Ivar;
typedef struct category_t *Category;
typedef struct property_t *objc_property_t;

#define _OBJC_TAG_MASK 1UL // taggedPointer标记

union isa_t{
   Class cls;  // 指向的类cls
   uintptr_t bits;
   struct{
     uintptr_t nonpointer : 1;  // 用于标记是否支持优化的 isa 指针，其字面含义意思是 isa 的内容不再是类的指针了，而是包含了更多信息，比如引用计数，析构状态，被其他 weak 变量引用情况。
     uintptr_t has_assoc : 1; // 表示该对象是否包含 associated object，如果没有，则析构时会更快
     uintptr_t has_cxx_dtor : 1; // 表示该对象是否有 C++ 或 ARC 的析构函数，如果没有，则析构时更块
     uintptr_t shiftcls : 33; // 类的指针
     uintptr_t magic : 6; // 固定值为 0xd2，用于在调试时分辨对象是否未完成初始化。
     uintptr_t weakly_referenced : 1; // 表示该对象是否有过 weak 对象，如果没有，则析构时更快
     uintptr_t deallocating : 1; // 表示该对象是否正在析构
     uintptr_t has_sidetable_rc : 1 // 表示该对象的引用计数值是否过大无法存储在 isa 指针
     uintptr_t extra_rc : 19; // 存储引用计数值减一后的结果
   };
};

struct objc_object{
   isa_t isa; // isa指针
};
struct objc_class : objc_object{
   Class superclass; // 父类
   cache_t cache;
   class_data_bits_t bits;
};

/*
什么是TaggedPointer?
Tagged Pointer专门用来存储小的对象，例如NSNumber和NSDate。
Tagged Pointer指针的值不再是地址了，而是真正的值。所以，实际上它不再是一个对象了，它只是一个披着对象皮的普通变量而已。所以，它的内存并不存储在堆中，也不需要 malloc 和 free。
在内存读取上有着 3 倍的效率，创建时比以前快 106 倍。
*/
int main(int argc, char * argv[])
{
    @autoreleasepool {
        NSNumber *number1 = @1;
        NSNumber *number2 = @2;
        NSNumber *number3 = @3;
        NSNumber *numberFFFF = @(0xFFFF);
        
        NSLog(@"number1 pointer is %p", number1);
        NSLog(@"number2 pointer is %p", number2);
        NSLog(@"number3 pointer is %p", number3);
        NSLog(@"numberffff pointer is %p", numberFFFF);
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```


#了解union
fastpath(x) x为真的可能性更大
slowpath(x) x为假的可能性更大
使用改两个指令的方式，编译器在编译过程中，会将可能性更大的代码紧跟着起面的代码，从而减少指令跳转带来的性能上的下降。 
