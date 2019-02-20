
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
*  理解“对象等同性”这一概念  
1. isEqual与hash方法
2. 相同的对象必须具有相同的哈希码，但是两个哈希码相同的对象却未必相同。
*  以类族模式隐藏实现细节
1. 子类应该继承自类族中的抽象基类
2. 子类应该定义自己的数据存储方式。
3. 子类应当覆写超类文档中指明需要覆写的方法。
* 用前缀避免命名空间冲突
1. @class 类名 向前声明
2. 若自己所开发的程序库中用到了第三方库，则应为其中的名称加上前缀
* 提供全能初始化方法

##  内存管理的思考方式 
引用计数式内存管理的思考方式就是思考ARC所引起的变化
* 自己生成的对象,自己所持有。
* 非自己生成的对象，
自己也能持有。
* 自己持有的对象不再需要时释放。
* 非自己持有的对象无法释放。  
## 所有权修饰符  
Objective-C编程中为了处理对象，可将变量类型定义为id类型或各种对象类型.  
所谓对象类型就是指向NSObject这样的Objective-C类的指针，例如“NSObject *".
id类型用于隐藏对象类型的类名部分，相当于c语育中常用的“void *"。  
ARC有效时，id类型和对象类型同C语言其他类型不同，其类型上必须附加所有权修饰符
所有权修饰符--共有4种。
* _ strong 修饰符
* _ weak 修饰符
* __unsafe_ unretained 修饰符
* _autoreleasing 修饰符  


### _ _ strong 修饰符  

__strong 修饰符是id类型和对象类型默认的所有权修饰符。也就是说，以下源代码中的id
变量，实际上被附加了所有权修饰符。
```
id obj = [(NSobject alloc] init];
```

id和对象类型在没有明确指定所有权修饰符时，默认为__strong修饰符。上面的源代码与以
下相同。

```
id__ strong obj = [ [NSObject alloc] init];
```

该源代码在ARC无效时又该如何表述呢?
```
ARC无败*/
id obj = [(NSobject alloc] init];
```

此源代码明确指定了c语言的变量的作用域。ARC无效时,该原代码可记如下，

```
1 ARC无效*/
{
id obj = [(NSobject alloc] init];
[obj releasel];
}
```

为了释放生成并持有的对象，增加了调用release方法的代码。该源代码进行的动作同先前
ARC有效时的动作完全一样。  
如此源代码所示，附有__strong 修饰符的变量obi在超出其变量作用域时，即在该变量被废弃时，会释放其被赋予的对象。  
如“strong” 这个名称所示，_ stong 修饰符表示对对象的“强引用”。持有强引用的变量在
超出其作用域时被废弃，随着强引用的失效，引用的对象会随之释放。
下面关注一下源代码中关于对象的所有者的部分。

```
{
id __strong obj = [[Nsobject alloc] init];
此源代码就是之前自己生成并持有对象的源代码，该对象的所有者如下:
//自己生成并持有对象
id strong obj = [NSobject alloc] init];
/*
*因为变量obj为强引用，
*所以自己持有对象
*/
}
* 因为变量obj超出其作用域，强引用失效，所以自动地释放自己持有的对象。
对象的所有者不存在，因此废弃该对象。
```
此处，对象的所有者和对象的生存周期是明确的。那么，在取得非自己生成并持有的对象
又会如何呢?

```
id __strong obj=[NSMutableArray array];
```

在NSMutableAray类的array类方法的源代码中取得非自已生成并持有的对象，具体

```
{
/*
★取得非自己生成并持有的对象
id __strong obj = [NSMutableArray array];
/*
★因为变量obj为强引用，
★所以自己持有对象
★/
} /*
★因为变量obj超出其作用域，强引用失效，:
★所以自动地释放自己持有的对象
```
当然附有__strong修饰符的变量之间可以相互赋值

```
id __strong objo = [[NSObject al1oc] init];
id __strong objl=[ [NSObject alloc] init];
id __strong obj2 = nil;
obj0 = objl;
obj2 = objo;
obj1 = nil;
obj0= nil;
obj2 = nil;

```

```
id__ strong obj0 = [[NSObject alloe] initl;
/*对象A
obj0持有对象A的强引用
*/
id_ strong obj1”lINSObject al1oc] initl;
/*对象B
objl持有对象B的强引用
*/
id __strong obj2 = nil;
/*
obj2不持有任何对象
*/
obj0 = obj1;
/* obj0持有由obj1赋值的对象B的强引用
* 因为obj0被赋值，所以原先持有的对对象A的强引用失效。
* 对象A的所有者不存在，因此废弃对象A。
* 此时，持有对象B的强引用的变量为
* obj0和obj1。
*/
obj2 = obj0;
/*
* obj2持有由obj0赋值的对象B的强引用
* 此时，持有对象B的强引用的变量为
。obj0, obj1和obj2
*/
obj1 = nil;
/*因为ni2被赋予了ob1,所以对对象B的强引用失效。
.此时，持有对象B的强引用的变量为
* obj0和obj2。
*/
obj0 = nil;
/*
* 因为nil被赋予obj0,所以对对象B的强引用失效。
* 此时，持有对象B的强引用的变量为
* obj2。
*/
obj2 = nil;
/*
因为nil被赋予obi2,所以对对象B的强引用失效。
:对象B的所有者不存在，因此废弃对象B。
*/
```
### weak修饰符  
看起来好像通过__strong修饰符编译器就能够完美地进行内存管理。但是遗饿的是，仅通
__strong 修饰符是不能解决有些重大问题的。  

这里提到的重大问题就是引用计数式内存管理中必然会发生的“**循环引用**”的问题。

```
@interface Test : NSobject
id __strong obj_ ;
一(void) setobject: (id __strong)obj;
@end
@ imp1 ementation Test
一(id) init {
self = [super init];
return self;
}
- (void)setobjet:(id __strong)obj {
obj_ = obj;
}
gend
```

以下为循环引用。
```
id testo = [[Test alloc] init],
id test1 = [[Test alloc] init];
[test0 setobject:test1];
[testl setobject:test0];
}1
```

为便于理解，下面写出了生成并持有对象的状态。

```
{
id test0 = [[Test alloc] init]; /*对象A*/
/*   
test0 持有Test对象A的强引用
*/
id test1 = [[Test alloc] init]; /* 对象B */
/*
* test1 持有Test对象B的强引用
*/
[test0 setobject:test1];
/*
Test对象A的obj_成员变量持有Test对象B的强引用。
★此时，持有Test对象B的强引用的变量为
* Test对象A的obj_ 和test1。
*/
[test1 setobject:test0] ;
/*
* Test 对象B的obj_成员变量持有 Test对象A的强引用。
*此时，持有Test对象A的强引用的变量为
* Test对象B的obj_ 和test0。

*/
}
/*
★
因为testo变量超出其作用域，强引用失效，
★
所以自动释放Test对象A。
★
因为testl变量超出其作用域，强引用失效，
*所以自动释放Test对象B。
★
*此时，持有Test对象A的强引用的变量为
★
Test对象B的obj_。
★
★
此时，持有Test对象B的强引用的变量为
*Test对象A的obj_。
★
★
发生内存泄漏!
*/
```
循环引用容易发生内存泄漏。所谓**内存泄漏**就是应当废弃的对象在超出其生存周期后继续存在.  

此代码的本意是赋予变量testO的对象A和赋予变量test1的对象B在超出其变量作用域时1
释放，即在对象不被任何变量持有的状态下予以废弃。但是,循环引用使得对象不能被再次废弃。  

像下面这种情况，虽然只有一个对象，但在该对象持有其自身时，也会发生循环引用(有
引用)。如图1-19所示。

```
id test = [[Test alloc] init] ;
[test setobject:test] ;
```

那么__weak修饰符就可以避免循环引用  

```
@interface Test : NSobject
id __weak obj_ ;
一(void) setobject: (id __strong)obj;
@end
```
weak修饰符还有另一 优点。在持有某对象的弱引用时，若该对象被废弃，则此弱引用自动失效且处于nil被赋值的状态( 空弱应用)。  
如以下代码所示。

```
id __weak obj1 = ni1;
{
id _ strong bj0一{[NSObject alloc] init];
objl = obj0;
NSLog(@"A: @"，obj1);
}
NSLog(@"B: @"，obj1);
```

此源代码执行结果如下:
A: <Nsobject: 0x753e180>
B: (null)  

下面我们来确认一下对象的持有情况，看看为什么会得到这样的执行结果。

```
id weak obj1 = nil;
{
/*
* 自己生成并持 有对象
*/
id strong obj0 = [[NSObject alloc] init];
/*
，因为obj0变量为强引用，
，所以自己持有对象
*/
obj1 = obj0;
/*
obj1 变量持有对象的弱引用
*/
NSLog(@"A: 8@", obj1) ;
/*
, 输出obj1变量持有的弱引用的对象
*/
}
/* 因为obj0变量超出其作用域，强引用失效，
所以自动释放自己持有的对象。;
●因为对象无持有者，所以废弃该对象。
◆废弃对象的同时,
持有该对象弱引用的obj1变量的弱引用失效，nil 赋值给obj1。
*/
NSLog(@*B: 8@", obj1) ;
/*
*输出赋值给obj1变量中的nil
*/
```

像这样，使用_ weak 修饰符可避免循环引用。通过检查附有_ weak 修饰符的变量是否为
nil,可以判断被赋值的对象是否已废弃。

