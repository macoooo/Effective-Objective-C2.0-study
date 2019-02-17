
* 多用字面量语法，少用与之等价的方法
```
NSString *string = nil;
    NSArray *array = [NSArray arrayWithObjects:@"dsds",string,@"fdf", nil];
    //NSArray *array2 = @[@1,@2,string];//如果用字面量，数组有这种有空对象的元素，编译时会直接报错
    NSLog(@"%@",array);
    //NSLog(@"%@", array2);
```
打印结果为
```
2019-01-13 19:17:24.017733+0800 委托模式[1321:25214] (
    dsds
)
//如果array中有为空的对象，截止到那个对象之前获取数组
```
* 在类的头文件中尽量少引入其他头文件  
“向前声明” @class EOCEmployer: 在不需要知道EOCEmployer类的全部细节，只需要知道有一个类名叫EOCEmployer的类的情况下  
* 多用类型常量，少用#define预处理指令  
 单元内用static const  

```
static const NSTimeInterval kAnimationDuration = 0.3;//  
命名方法：若常量局限于某“编译单元”之内，在前面字母加k,  
若常量在类之外可见，则通常以类名为前缀。
```
需放在“全局符号表”的变量用extern const
```
   //in the header file声明
    extern NSString *const EOCStringConstant;
    //in the implementation file定义
    NSString *const EOCStringConstant = @"VALUE";
```
* 用枚举表示状态，选项，状态码

```
typedef NS_OPTIONS(NSUInteger, UIViewAutoresizing) {
    UIViewAutoresizingNone                 = 0,
    UIViewAutoresizingFlexibleLeftMargin   = 1 << 0,
    UIViewAutoresizingFlexibleWidth        = 1 << 1,
    UIViewAutoresizingFlexibleRightMargin  = 1 << 2,
    UIViewAutoresizingFlexibleTopMargin    = 1 << 3,
    UIViewAutoresizingFlexibleHeight       = 1 << 4,
    UIViewAutoresizingFlexibleBottomMargin = 1 << 5
};
用NS_ENUM(<#...#>);
    NS_OPTIONS(<#_type#>, <#_name#>)宏来定义枚举类型，并指明其底层数据类型。  
    这样做可以确保枚举是用开发者所选的底层数据类型实现出来的，而不会采用编译器所选的类型
```
- [ ] NS_ENUM 和 NS_OPTIONS的区别：  
凡是需要以按位或操作来组合的枚举都应使用NS_OPTIONS定义。若是枚举不需要互相组合，则应使用NS_ENUM来定义。

```
typedef NS_ENUM(NSInteger, UITableViewCellStyle) {
    UITableViewCellStyleDefault,
    UITableViewCellStyleValue1,
    UITableViewCellStyleValue2,
    UITableViewCellStyleSubtitle
};
NS_ENUM 的第一个参数是用于存储的新类型的类型。
在64位环境下，UITableViewCellStyle 和NSInteger  
一样有8bytes长。你要保证你给出的所有值能被该类型
容纳，否则就会产生错误。第二个参数是新类型的名字 
。大括号里面和以前一样，是你要定义的各种值。  
位掩码用 NS_OPTIONS 宏
```
在处理枚举类型的switch语句中不要实现default分支。这样的话，加入新枚举之后，编译器就会提示开发者，switch语句并未处理所有枚举。  
* **理解“属性”这一概念**  
如果使用属性@property会“自动合成”，这个过程由编译器在编译期执行，除了生成方法代码外，编译器还要自动向类中添加适当类型的实例变量，并且在属性名前加下划线，以此作为实例变量的名字。    
在编译访问属性的代码时，即使编译器发现没有定义的存取方法时，也不会报错，因为它相信会在运行期找到。
- [ ] 属性特质：  

1. 原子性：atomic
2. 同步锁  
3. 属性是结构体对象，有方法，编译器会向类中添加实例变量  
4. 内存管理语义（属性关键字）  
5. atomic 与nonatomic的区别
* 在对象内部尽量直接访问实例变量
1. 在对象内部读取数据时，应该直接通过实例变量来读，而写入数据时，则应通过属性来写。
2. 在初始化方法及dealloc方法中，总是应该直接通过实例变量来读写数据。
* 理解“对象等同性”这一概念  
1. isEqual与hash方法
2. 相同的对象必须具有相同的哈希码，但是两个哈希码相同的对象却未必相同。
* 以类族模式隐藏实现细节
1. 子类应该继承自类族中的抽象基类
2. 子类应该定义自己的数据存储方式。
3. 子类应当覆写超类文档中指明需要覆写的方法。
