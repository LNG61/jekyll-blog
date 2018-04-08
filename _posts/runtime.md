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
     uintptr_t nonpointer : 1; 
     uintptr_t has_assoc : 1; // hasAssociatedObjects
     uintptr_t has_cxx_dtor : 1; 
     uintptr_t shiftcls : 44;
     uintptr_t magic : 6;
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
#了解union

fastpath(x) x为真的可能性更大
slowpath(x) x为假的可能性更大
使用改两个指令的方式，编译器在编译过程中，会将可能性更大的代码紧跟着起面的代码，从而减少指令跳转带来的性能上的下降。 
