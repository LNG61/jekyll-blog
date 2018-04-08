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
     uintptr_t shiftcls : 44; // 类的指针
     uintptr_t magic : 6; // 固定值为 0xd2，用于在调试时分辨对象是否未完成初始化。
     uintptr_t weakly_referenced : 1; // 是否弱引用
     uintptr_t deallocating : 1;
     uintptr_t extra_rc : 8;
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
```

#什么是TaggedPointer和nonpointer?
TaggedPointer:有些对象如果支持使用 TaggedPointer，苹果会直接将其指针值作为引用计数返回；如果当前设备是 64 位环境并且使用 Objective-C 2.0，那么“一些”对象会使用其 isa 指针的一部分空间来存储它的引用计数；否则 Runtime 会使用一张散列表来管理引用计数。
nonpointer:
#了解union

fastpath(x) x为真的可能性更大
slowpath(x) x为假的可能性更大
使用改两个指令的方式，编译器在编译过程中，会将可能性更大的代码紧跟着起面的代码，从而减少指令跳转带来的性能上的下降。 
