---
published: false
---
```
typedef struct objc_Class *Class;
typedef struct objc_object *id;

union isa_t{
   Class cls;  // 指向的类cls
   uintptr_t bits;
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