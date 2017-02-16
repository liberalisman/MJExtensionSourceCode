# MJExtensionSourceCode

主要是针对MJExtension的源代码写一写自己的理解。请多多指教。
### 结构

1.**``NSObjecte+MJKeyValue``**提供的字典和模型互相转换的核心代码和逻辑.

2.**``NSObject+MJProperty``**提供了字典key值和value值与模型属性的相对对应关系的配置.最常见写在模型中的方法``mj_setupReplacedKeyFromPropertyName``和``mj_setupObjectClassInArray``

3.**``NSObject+MJCoding``**主要是模型的归档和解码.外界实现比较方法,主要是一个宏,这个宏其实是代替两个方法.

4.**``NSObject+MJClass``**提供了遍历属性类型父类的方法.以及模型和字典互转过程中需要转化的白名单和黑名单.

5.**``MJProperty``**包装模型中每个属性,包括属性的类型,属性的名字,父类.储存和取出属性对应的值.

6.**``MJPropertyKey``**模型对应的key值,以及他的对应的类型(字典还是数组)

7.**``MJPropertyType``**属性对应的类型,这个里面比较细:比如这个属性是一个基本类型,还是一个foundation类,还是另外一个模型类型等等.

8.**``MJFoundation``**这两个类分别代表遍历某个类是不是**``Foudation``**框架下

9.**``MJExtensionConst``**一些基本的配置信息的宏,比如断言,错误,日志输出等等.

### 主要逻辑的说明
1.从字典转模型基本方法 

**- (instancetype)mj_setKeyValues:(id)keyValues context:(NSManagedObjectContext *)context**

开始捋顺逻辑:
 
![](http://okhqmtd8q.bkt.clouddn.com/MJExtension%E6%BA%90%E7%A0%81%E8%A7%A3%E8%AF%BB-01.jpg)

### 代码中需要学习的部分
1.对于经常用到的方法或者语句可以抽成宏或者内联方法,方便使用.

2.runtime获取属性的应用是方法的关键.

3.对于**``objc_setAssociatedObject``**和**``objc_getAssociatedObject``**动态增加属性方法的使用.

4.将 key值 和 模型属性 的转换桥梁以动态属性的方式写在自己的类里面.对于模型的侵入性很小.

5.将每一个模型中属性的包装.然后NSObject中根据之后包装进行处理,转换成想要的样子

### runTime
1.获取所有属性
``class_copyPropertyList``说明：使用class_copyPropertyList并不会获取无@property声明的成员变
2. 获取属性名``property_getName``
3.描述一个属性``objc_property_t``
4.获取所有属性特性``property_copyAttributeList``
5. 获取属性特性描述字符串``property_getAttributes``,获取的结果类似是``T@"NSDictionary",C,N,V_dict1``

**注意获取的结果解释:**
 属性类型  name值：T  value：变化
 编码类型  name值：C(copy) &(strong) W(weak)空(assign) 等 value：无
 非/原子性 name值：空(atomic) N(Nonatomic)  value：无
 变量名称  name值：V  value：变化

```
    unsigned int outPut = 0;
    objc_property_t *prots = class_copyPropertyList([TestMadal class], &outPut);

    for (int i = 0; i<outPut; i++)
    {
        objc_property_t property = prots[i];
        const char *t = property_getName(property);
        const char *attrs = property_getAttributes(property);
        NSLog(@"%s---%s",t,attrs);
    }
```

