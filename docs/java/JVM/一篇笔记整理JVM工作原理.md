## 首先要了解的

### >>数据类型

Java虚拟机中，数据类型可以分为两类：**基本类型和引用类型**。

基本类型的变量保存原始值，即：他代表的值就是数值本身；而引用类型的变量保存引用值。

“引用值”代表了某个对象的引用，而不是对象本身，对象本身存放在这个引用值所表示的地址的位置。

基本类型包括：**byte,short,int,long,char,float,double,Boolean,returnAddress**

引用类型包括：**类类型，接口类型和数组。**

### >>堆与栈

栈是运行时的单位，而堆是存储的单位。

**栈解决程序的运行问题，即程序如何执行，或者说如何处理数据；堆解决的是数据存储的问题，即数据怎么**

**放、放在哪儿。**

在Java中一个线程就会相应有一个线程栈与之对应，这点很容易理解，因为不同的线程执行逻辑有所不同

因此需要一个独立的线程栈。而堆则是所有线程共享的。栈因为是运行单位，因此里面存储的信息都是跟当

线程（或程序）相关信息的。包括局部变量、程序运行状态、方法返回值等等；而堆只负责存储对象信息。

**堆中存的是对象。栈中存的是基本数据类型和堆中对象的引用。**

**>>Java中的参数传递时传值呢？还是传引用？**

要说明这个问题，先要明确两点：

1. 不要试图与C进行类比，Java中没有指针的概念

2. **程序运行永远都是在栈中进行的，因而参数传递时，只存在传递基本类型和对象引用的问题。**不会直接

传对象本身。

明确以上两点后。Java在方法调用传递参数时，因为没有指针，所以它都是进行传值调用（这点可以参考C的

传值调用）。因此，很多书里面都说Java是进行传值调用，这点没有问题，而且也简化的C中复杂性。

堆和栈中，栈是程序运行最根本的东西。**程序运行可以没有堆，但是不能没有栈。**而堆是为栈进行数据存储

服务，说白了**堆就是一块共享的内存**。不过，**正是因为堆和栈的分离的思想，才使得Java的垃圾回收成为可**

**能。**

**Java中，栈的大小通过-Xss来设置，当栈中存储数据比较多时，需要适当调大这个值，否则会出现**

**java.lang.StackOverflowError异常。常见的出现这个异常的是无法返回的递归，因为此时栈中保存的信息都是**

**方法返回的记录点。**

### >>Java对象的大小

基本数据的类型的大小是固定的，[Java基本数据类型与位运算](https://yq.aliyun.com/go/articleRenderRedirect?url=http%3A%2F%2Fwww.cnblogs.com%2Fbinyue%2Fp%2F5177749.html)，这里就不多说了。对于非基本类型的Java对象，其大小就值得商榷。

在Java中，**一个空Object对象的大小是8byte**，这个大小只是保存堆中一个没有任何属性的对象的大小。看

下面语句：

| 1 | Object ob = new Object(); | 
|----|----|
这样在程序中完成了一个Java对象的生命，但是**它所占的空间为：4byte+8byte**。**4byte是上面部分所说的**

**Java栈中保存引用的所需要的空间**。而那8byte则是Java堆中对象的信息。因为所有的Java非基本类型的对象都

需要默认继承Object对象，因此不论什么样的Java对象，其大小都必须是大于8byte。

有了Object对象的大小，我们就可以计算其他对象的大小了。

| 12345 | Class NewObject {int count;boolean flag;Object ob;} | 
|----|----|
其大小为：

**空对象大小(8byte)+int大小(4byte)+Boolean大小(1byte)+空Object引用的大小(4byte)=17byte。**

但是因为Java在对对象内存分配时**都是以8的整数倍来分**，因此大于17byte的最接

近8的整数倍的是24，因此此**对象的大小为24byte**。

这里需要注意一下基本类型的包装类型的大小。因为这种包装类型已经成为对象了，因此需要把他们作为对

象来看待。包装类型的大小至少是12byte（声明一个空Object至少需要的空间），而且12byte没有包含任何有

效信息，同时，因为Java对象大小是8的整数倍，因此**一个基本类型包装类的大小至少是16byte**。这个内存占

用是很恐怖的，它是使用基本类型的N倍（N>2），有些类型的内存占用更是夸张（随便想下就知道了）。

因此，**可能的话应尽量少使用包装类**。在JDK5.0以后，因为加入了自动类型装换，因此，Java虚拟机会在存储方

面进行相应的优化。

### >>引用类型

对象引用类型分为**强引用、软引用、弱引用和虚引用**。

**强引用:**就是我们一般声明对象时虚拟机生成的引用，强引用环境下，垃圾回收时需要严格判断当前对象是否

被强引用，如果被强引用，则不会被垃圾回收

**软引用:**软引用一般被做为缓存来使用。与强引用的区别是，软引用在垃圾回收时，虚拟机会根据当前系统的剩

余内存来决定是否对软引用进行回收。如果剩余内存比较紧张，则虚拟机会回收软引用所引用的空间；如果剩

余内存相对富裕，则不会进行回收。换句话说，虚拟机在发生OutOfMemory时，肯定是没有软引用存在的。

**弱引用:**弱引用与软引用类似，都是作为缓存来使用。但与软引用不同，**弱引用在进行垃圾回收时，是一定会被**

**回收掉的，因此其生命周期只存在于一个垃圾回收周期内。**

强引用不用说，我们系统一般在使用时都是用的强引用。而“软引用”和“弱引用”比较少见。他们一般被

作为缓存使用，而且一般是在内存大小比较受限的情况下做为缓存。因为如果内存足够大的话，可以直接使用

强引用作为缓存即可，同时可控性更高。因而，他们常见的是被使用在桌面应用系统的缓存。

 

## JVM的生命周期

### 一、首先分析两个概念

**JVM实例和JVM执行引擎实例**

（1）JVM实例对应了一个独立运行的java程序，它是**进程级别**。

（2）JVM执行引擎实例则对应了属于用户运行程序的线程，它是**线程级别的**。

### 二、JVM的生命周期

**（1）JVM实例的诞生**

当启动一个Java程序时，一个JVM实例就产生了，任何一个拥有public static void main(String[] args)函数的**class都可以作为JVM实例运行的起点**。

**（2）JVM实例的运行 main()作为该程序初始线程的起点，任何其他线程均由该线程启动。**

JVM内部有两种线程：**守护线程和非守护线程，main()属于非守护线程，守护线程通常由JVM自己使用，java程序也可以标明自己创建的线程是守护线程。** 

**（3）JVM实例的消亡**

当程序中的所有非守护线程都终止时，JVM才退出；若安全管理器允许，程序也可以使用Runtime类或者System.exit()来退出。

 

## JVM的体系结构

### ![图片](https://uploader.shimo.im/f/GOEqDShXla0VfnrN.png!thumbnail)

### 一、JVM的内部体系结构分为三部分，

**（1）类装载器（ClassLoader）子系统**

作用: 用来装载.class文件

**（2）执行引擎**

作用:执行字节码，或者执行本地方法

**（3）运行时数据区**

方法区，堆，java栈，PC寄存器，本地方法栈

 

 

## JVM类加载器

### ![图片](/images/jvm2.png)

### 一、 JVM将整个类加载过程划分为了三个步骤：

**（1）装载**

装载过程负责找到二进制字节码并加载至JVM中，JVM通过类名、类所在的包名通过ClassLoader来完成类的加载，同样，也采用以上三个元素来标识一个被加载了的类：类名+包名+ClassLoader实例ID。

**（2）链接**

链接过程负责对二进制字节码的格式进行校验、初始化装载类中的静态变量以及解析类中调用的接口、类。

在完成了校验后，JVM初始化类中的静态变量，并将其值赋为默认值。

最后一步为对类中的所有属性、方法进行验证，以确保其需要调用的属性、方法存在，以及具备应的权限（例如public、private域权限等），会造成NoSuchMethodError、NoSuchFieldError等错误信息。

**（3）初始化**

初始化过程即为执行类中的静态初始化代码、构造器代码以及静态属性的初始化，在四种情况下初始化过程会被触发执行：

调用了new；反射调用了类中的方法；子类调用了初始化；JVM启动过程中指定的初始化类。

### 二、JVM两种类装载器包括：启动类装载器和用户自定义类装载器，

启动类装载器是JVM实现的一部分，用户自定义类装载器则是Java程序的一部分，必须是ClassLoader类的子类。

 主要分为以下几类：

**(1) Bootstrap ClassLoader**

这是JVM的根ClassLoader，它是用C++实现的，JVM启动时初始化此ClassLoader，并由此ClassLoader完成**$JAVA_HOME中jre/lib/rt.jar（Sun JDK的实现）中所有class文件的加载**，这个jar中包含了java规范定义的所有接口以及实现。

**(2) Extension ClassLoader**

JVM用此classloader来加载扩展功能的一些jar包

**(3) System ClassLoader**

JVM用此classloader来加载启动参数中指定的Classpath中的jar包以及目录，在Sun JDK中ClassLoader对应的类名为AppClassLoader。

**(4) User-Defined ClassLoader**

User-DefinedClassLoader是Java开发人员继承ClassLoader抽象类自行实现的ClassLoader，基于自定义的ClassLoader可用于加载非Classpath中的jar以及目录

### 
### 三、ClassLoader抽象类提供了几个关键的方法：

**（1）loadClass**

此方法负责加载指定名字的类，ClassLoader的实现方法为先从已经加载的类中寻找，如没有则继续从parent ClassLoader中寻找，如仍然没找到，则从System ClassLoader中寻找，最后再调用findClass方法来寻找，如要改变类的加载顺序，则可覆盖此方法

**（2）findLoadedClass**

此方法负责从当前ClassLoader实例对象的缓存中寻找已加载的类，调用的为native的方法。

**（3） findClass**

此方法直接抛出ClassNotFoundException，因此需要通过覆盖loadClass或此方法来以自定义的方式加载相应的类。

**(4) findSystemClass**

此方法负责从System ClassLoader中寻找类，如未找到，则继续从Bootstrap ClassLoader中寻找，如仍然为找到，则返回null。

**(5)defineClass**** **

此方法负责将二进制的字节码转换为Class对象

**(6) resolveClass**

此方法负责完成Class对象的链接，如已链接过，则会直接返回。

### 
### 四、简单的classLoader例子

| 123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051 | /** 重写ClassLoader类的findClass方法，将一个字节数组转换为 Class 类的实例*/public Class<?> findClass(String name) throws ClassNotFoundException {byte[] b = null;try {b = loadClassData(AutoClassLoader.FormatClassName(name));} catch (Exception e) {e.printStackTrace();}return defineClass(name, b, 0, b.length); }/** 将指定路径的.class文件转换成字节数组*/private byte[] loadClassData(String filepath) throws Exception {int n =0;BufferedInputStream br = new BufferedInputStream(new FileInputStream(new File(filepath)));ByteArrayOutputStream bos= new ByteArrayOutputStream();while((n=br.read())!=-1){bos.write(n);}br.close();return bos.toByteArray();}/** 格式化文件所对应的路径*/public static String FormatClassName(String name){ FILEPATH= DEAFAULTDIR + name+".class";return FILEPATH;} /** main方法测试*/public static void main(String[] args) throws Exception { AutoClassLoader acl = new AutoClassLoader();Class c = acl.findClass("testClass");Object obj = c.newInstance();Method m = c.getMethod("getName",new Class[]{String.class ,int.class});m.invoke(obj,"你好",123);System.out.println(c.getName());System.out.println(c.getClassLoader());System.out.println(c.getClassLoader().getParent());} | 
|----|----|
 

  

## JVM执行引擎

### 一、JVM通过执行引擎来完成字节码的执行，在执行过程中JVM采用的是自己的一套指令系统，

每个线程在创建后，都会产生一个程序计数器（pc）和栈（Stack），其中程序计数器中存放了下一条将要执行的指令，

Stack中存放**Stack Frame，栈帧**，表示的为当前正在执行的方法，每个方法的执行都会产生Stack Frame，Stack Frame中存放了**传递给方法的参数、方法内的局部变量以及操作数栈**，

操作数栈用于存放指令运算的中间结果，指令负责从操作数栈中弹出参与运算的操作数，指令执行完毕后再将计算结果压回到操作数栈，当方法执行完毕后则从Stack中弹出，继续其他方法的执行。

在执行方法时JVM提供了**invokestatic、invokevirtual、invokeinterface和invokespecial**四种指令来执行

（1）invokestatic：调用类的static方法

（2） invokevirtual： 调用对象实例的方法

（3） invokeinterface：将属性定义为接口来进行调用

（4） invokespecial： JVM对于初始化对象（Java构造器的方法为：<init>）以及调用对象实例中的私有方法时。

### 二、反射机制是Java的亮点之一，基于反射可动态调用某对象实例中对应的方法、访问查看对象的属性等，

而无需在编写代码时就确定需要创建的对象，这使得Java可以实现很灵活的实现对象的调用，代码示例如下：

Class actionClass=Class.forName(外部实现类);

Method method=actionClass.getMethod(“execute”,null);

Object action=actionClass.newInstance();

method.invoke(action,null);

反射的关键：**要实现动态的调用，最明显的方法就是动态的生成字节码，加载到JVM中并执行。**

**（1）Class actionClass=Class.forName(外部实现类);**

调用本地方法，使用调用者所在的ClassLoader来加载创建出Class对象；

**（2）Method method=actionClass.getMethod(“execute”,null);**

校验此Class是否为public类型的，以确定类的执行权限，如不是public类型的，则直接抛出

SecurityException；

调用privateGetDeclaredMethods来获取到此Class中所有的方法，在privateGetDeclaredMethods对此Class中所有的方法的集合做了缓存，在第一次时会调用本地方法去获取；

扫描方法集合列表中是否有相同方法名以及参数类型的方法，如有则复制生成一个新的Method对象返回；

如没有则继续扫描父类、父接口中是否有此方法，如仍然没找到方法则抛出NoSuchMethodException；

**（3） Object action=actionClass.newInstance();**

第一步：校验此Class是否为public类型，如权限不足则直接抛出SecurityException；

第二步：如没有缓存的构造器对象，则调用本地方法获取到构造器，并复制生成一个新的构造器对象，放入缓存，如没有空构造器则抛出InstantiationException；

第三步：校验构造器对象的权限；

第四步：执行构造器对象的newInstance方法；构造器对象的newInstance方法判断是否有缓存的ConstructorAccessor对象，如果没有则调用sun.reflect.ReflectionFactory生成新的ConstructorAccessor对象；

第五步：sun.reflect.ReflectionFactory判断是否需要调用本地代码，可通过sun.reflect.noInflation=true来设置为不调用本地代码，在不调用本地代码的情况下，就转交给MethodAccessorGenerator来处理了；

第六步：MethodAccessorGenerator中的generate方法根据Java Class格式规范生成字节码，字节码中包括了ConstructorAccessor对象需要的newInstance方法，此newInstance方法对应的指令为invokespecial，所需的参数则从外部压入，生成的Constructor类的名字以：sun/reflect/GeneratedSerializationConstructorAccessor或sun/reflect/GeneratedConstructorAccessor开头，后面跟随一个累计创建的对象的次数；

第七步：在生成了字节码后将其加载到当前的ClassLoader中，并实例化，完成ConstructorAccessor对象的创建过程，并将此对象放入构造器对象的缓存中；

最后一步：执行获取的constructorAccessor.newInstance，这步和标准的方法调用没有任何区别。

**（4） method.invoke(action,null);**

这步执行的过程和上一步基本类似，只是在生成字节码时生成的方法改为了invoke，其调用的目标改为了传入的对象的方法，同时生成的类名改为了：sun/reflect/GeneratedMethodAccessor。

注：但是getMethod是非常耗性能的，一方面是权限的校验，另外一方面所有方法的扫描以及Method对象的复制，因此在使用反射调用多的系统中应缓存getMethod返回的Method对象

### 2、执行技术

主要的执行技术有:解释，即时编译，自适应优化、芯片级直接执行

**（1）解释属于第一代JVM，**

**（2）即时编译JIT属于第二代JVM，**

**（3）自适应优化（目前Sun的HotspotJVM采用这种技术）则吸取第一代JVM和第二代JVM的经验，采用两者结合的方式**

**（4）自适应优化：开始对所有的代码都采取解释执行的方式，并监视代码执行情况，然后对那些经常调用的方法启动一个后台线程，将其编译为本地代码，并进行仔细优化。若方法不再频繁使用，则取消编译过的代码，仍对其进行解释执行。**

 

 

## JVM运行时数据区

### **一、JVM在运行时将数据划分为了6个区域来存储，而不仅仅是大家熟知的Heap区域，这6个区域图示如下：**

**第一块： PC寄存器**

PC寄存器是用于存储每个线程下一步将执行的JVM指令，如该方法为native的，则PC寄存器中不存储任何信息。

**第二块：JVM栈**

JVM栈是线程私有的，每个线程创建的同时都会创建JVM栈，JVM栈中存放的为当前线程中局部基本类型的变量（java中定义的八种基本类型：boolean、char、byte、short、int、long、float、double）、部分的返回结果以及Stack Frame，非基本类型的对象在JVM栈上仅存放一个指向堆上的地址。

![图片](/images/jvm3.jpg)

**第三块：堆（Heap）**

Heap是大家最为熟悉的区域，它是JVM用来存储对象实例以及数组值的区域，可以认为Java中所有通过new创建的对象的内存都在此分配，Heap中的对象的内存需要等待GC进行回收。

### 
### JVM将Heap分为New Generation和Old Generation（或Tenured Generation）两块来进行管理：

![图片](/images/jvm4.jpg)

**新生代旧生代**

**（1）New Generation**

**又称为新生代，程序中新建的对象都将分配到新生代中，新生代又由Eden Space和两块Survivor Space构成，可通过-Xmn参数来指定其大小**

**（2） Old Generation**

**又称为旧生代，用于存放程序中经过几次垃圾回收还存活的对象，例如缓存的对象等，旧生代所占用的内存大小即为-Xmx指定的大小减去-Xmn指定的大小。**

**PermGen space**

**PermGen space的全称是Permanent Generation space,是指内存的永久保存区域OutOfMemoryError: PermGen space从表面上看就是内存益出，解决方法也一定是加大内存。说说为什么会内存益出：这一部分用于存放Class和Meta的信息,Class在被 Load的时候被放入PermGen space区域，它和和存放Instance的Heap区域不同,GC(Garbage Collection)不会在主程序运行期对PermGen space进行清理，所以如果你的APP会LOAD很多CLASS的话,就很可能出现PermGen space错误。这种错误常见在web服务器对JSP进行pre compile的时候。 如果你的WEB APP下都用了大量的第三方jar, 其大小 超过了jvm默认的大小(4M)那么就会产生此错误信息了。包括下面的数据：**

 

>1.Class的节本信息
>Package Name
>Super class package name
>Class or interface
>Type modifiers
>Super inferface package name
>2.其它信息
>The constant pool for the type 
>Field information 
>Method information

 

**对堆的解释：**

（1）堆是JVM中所有线程共享的，因此在其上进行对象内存的分配均需要进行加锁，这也导致了new对象的开销是比较大的。

（2）鉴于上面的原因，Sun Hotspot JVM为了提升对象内存分配的效率，对于所创建的线程都会分配一块独立的空间，这块空间又称为TLAB（Thread Local Allocation Buffer），其大小由JVM根据运行的情况计算而得，在TLAB上分配对象时不需要加锁，因此JVM在给线程的对象分配内存时会尽量的在TLAB上分配，在这种情况下JVM中分配对象内存的性能和C基本是一样高效的，但如果对象过大的话则仍然是直接使用堆空间分配。

（3）TLAB仅作用于新生代的Eden Space，因此在编写Java程序时，通常多个小的对象比大的对象分配起来更加高效，

但这种方法同时也带来了两个问题，一是空间的浪费，二是对象内存的回收上仍然没法做到像Stack那么高效，同时也会增加回收时的资源的消耗，可通过在启动参数上增加-XX:+PrintTLAB来查看TLAB这块的使用情况。

**第四块：方法区域（Method Area）**

（1）方法区域存放了所加载的类的信息（名称、修饰符等）、类中的静态变量、类中定义为final类型的常量、类中的Field信息、类中的方法信息，当开发人员在程序中通过Class对象中的getName、isInterface等方法来获取信息时，这些数据都来源于方法区域，可见方法区域的重要性，同样，方法区域也是全局共享的，在一定的条件下它也会被GC，当方法区域需要使用的内存超过其允许的大小时，会抛出OutOfMemory的错误信息。

**（2）在Sun JDK中这块区域对应的为Permanet Generation，又称为持久代，默认为64M，可通过-XX:PermSize以及-XX:MaxPermSize来指定其大小。**

**sunJDK的方法区可能会抛出****java.lang.OutOfMemoryError: PermGen space异常****。**

**Java 8中已经放弃了对永久代的分配，使用Native Memory来进行管理。**

**第五块：运行时常量池（Runtime Constant Pool）**

类似C中的符号表，存放的为类中的固定的常量信息、方法和Field的引用信息等，其空间从方法区域中分配。

![图片](/images/jvm5.png)

 

**第六块：本地方法堆栈（Native Method Stacks）**

JVM采用本地方法堆栈来支持native方法的执行，此区域用于存储每个native方法调用的状态。

![图片](/images/jvm6.png)

 

 

 

## 基本垃圾回收算法

### 按照基本回收策略分

**引用计数（Reference Counting）:**

比较古老的回收算法。原理是**此对象有一个引用，即增加一个计数，删除一个引用则减少一个计数**。垃圾回收时，只收集计数为0的对象。

此算法最致命的是**无法处理循环引用**的问题。

**标记-清除（Mark-Sweep）:**

此算法执行分两阶段。**第一阶段从引用根节点开始标记所有被引用的对象，第二阶段遍历整个堆，把未标记的**

**对象清除。**此算法**需要暂停整个应用，同时，会产生内存碎片**。

**复制（Copying）:**

此算法把内存空间划为两个相等的区域，每次只使用其中一个区域。垃圾回收时，遍历当前使用区域，把正在

使用中的对象复制到另外一个区域中。次算法每次只处理正在使用中的对象，因此复制成本比较小，**同时复制**

**过去以后还能进行相应的内存整理，不会出现“碎片”问题**。当然，此算法的缺点也是很明显的，就是**需要两**

**倍内存空间**。

**标记-整理（Mark-Compact）:**

**此算法结合了“标记-清除”和“复制”两个算法的优点。**也是分两阶段，第一阶段从根节点开始标记所有被引

用对象，第二阶段遍历整个堆，把清除未标记对象并且把存活对象“压缩”到堆的其中一块，按顺序排放。此

算法避免了“标记-清除”的碎片问题，同时也避免了“复制”算法的空间问题。

### 按分区对待的方式分

**增量收集（Incremental Collecting）****:**

**实时垃圾回收算法，即：在应用进行的同时进行垃圾回收。**不知道什么

原因JDK5.0中的收集器**没有使用这种算法**的。

**分代收集（Generational Collecting）:**

基于对对象生命周期分析后得出的垃圾回收算法。把对象**分为年青**

**代、年老代、持久代，对不同生命周期的对象使用不同的算法（上述方式中的一个）进行回收**。现在的垃圾回

收器（从J2SE1.2开始）都是使用此算法的。

### 
### 按系统线程分

**串行收集:**

串行收集使用单线程处理所有垃圾回收工作，因为无需多线程交互，实现容易，而且效率比较高。但

是，其局限性也比较明显，**即无法使用多处理器的优势，所以此收集适合单处理器机器**。当然，此收集器也可

以用在小数据量（100M左右）情况下的多处理器机器上。

**并行收集:**

并行收集使用多线程处理垃圾回收工作，因而速度快，效率高。**而且理论上CPU数目越多，越能体现**

**出并行收集器的优势。**

**并发收集:**

相对于串行收集和并行收集而言，前面两个在进行垃圾回收工作时，需要暂停整个运行环境，而只有

垃圾回收程序在运行，因此，系统在垃圾回收时会有明显的暂停，而且暂停时间会因为堆越大而越长。

 

 

## JVM垃圾回收

### 一、 JVM中自动的对象内存回收机制称为：GC（Garbage Collection）

**1、 GC的基本原理：**

为将内存中不再被使用的对象进行回收，GC中用于回收内存中不被使用的对象的方法称为收集器，由于GC需要消耗一些资源和时间的，Java在对对象的生命周期特征进行分析后，在V 1.2以上的版本采用了分代的方式来进行对象的收集，即按照新生代、旧生代的方式来对对象进行收集，以尽可能的缩短GC对应用造成的暂停

**（1）对新生代的对象的收集称为minor GC，**

**（2）对旧生代的对象的收集称为Major GC，**

**（3）程序中主动调用System.gc()强制执行的GC为Full GC，**

### 二、 JVM中自动内存回收机制

**（1）引用计数收集器**

原理：

引用计数是标识Heap中对象状态最明显的一种方法，引用计数的方法简单来说就是对每一个对象都提供一个关联的引用计数，以此来标识该对象是否被使用，当这个计数为零时，说明这个对象已经不再被使用了。

优点：

引用计数的好处是可以不用暂停应用，当计数变为零时，即可将此对象的内存空间回收，但它需要给每个对象附加一个关联引用计数

缺点：

并且引用计数无法解决循环引用的问题，因此JVM并没有采用引用计数。

**（2）跟踪收集器**

原理：

跟踪收集器的方法为停止应用的工作，然后开始跟踪对象，跟踪时从对象根开始沿着引用跟踪，直到检查完所有的对象。

根对象的来源主要有三种：

1.被加载的类的常量池中的对象引用

2.传到本地方法中，没有被本地方法“释放”的对象引用

3.虚拟机运行时数据区中从垃圾收集器的堆中分配的部分

存在问题：

跟踪收集器采用的均为扫描的方法，但JVM将Heap分为了新生代和旧生代，在进行minor GC时需要扫描是否有旧生代引用了新生代中的对象，但又不可能每次minor GC都扫描整个旧生代中的对象，因此JVM采用了一种称为卡片标记（Card Marking）的算法来避免这种现象。

**（3）卡片标记算法**

**卡片标记的算法为将旧生代以某个大小（例如512字节）进行划分，划分出来的每个区域称为卡片，JVM采用卡表维护卡的状态，每张卡片在卡表中占用一个字节的标识（有些JVM实现可能会不同），当Java代码执行过程中发现旧生代的对象引用或释放了对于新生代对象的引用时，就相应的修改卡表中卡的状态，每次Minor GC只需扫描卡表中标识为脏状态的卡中的对象即可，**图示如下：

 ![图片](/images/jvm7.jpg)

1、跟踪收集器在扫描时最重要的是要根据这些对象是否被引用来标识其状态

2、JVM中将对象的引用分为了四种类型，不同的对象引用类型会造成GC采用不同的方法进行回收：

（1）强引用：默认情况下，对象采用的均为强引用

（这个对象的实例没有其他对象引用，GC时才会被回收）

（2）软引用：软引用是Java中提供的一种比较适合于缓存场景的应用

（只有在内存不够用的情况下才会被GC）

（3）弱引用：在GC时一定会被GC回收

（4）虚引用：由于虚引用只是用来得知对象是否被GC

 

 

## 问题

### 你能不能谈谈，java GC是在什么时候，对什么东西，做了什么事情？

—>程序员不能具体控制时间，系统在不可预测的时间调用System.gc()函数的时候；当然可以通过调优，用NewRatio控制newObject和oldObject的比例，用MaxTenuringThreshold 控制进入oldObject的次数，使得oldObject 存储空间延迟达到full gc,从而使得计时器引发gc时间延迟OOM的时间延迟，以延长对象生存期。

超出了作用域或引用计数为空的对象；从gc root开始搜索找不到的对象，而且经过一次标记、清理，仍然没有复活的对象。

删除不使用的对象，回收内存空间；运行默认的finalize,当然程序员想立刻调用就用dipose调用以释放资源如文件句柄，JVM用from survivor、to survivor对它进行标记清理，对象序列化后也可以使它复活。


