# Android Learning Notes 

## Java

#### Static class与non-static class的区别

* 内部静态类不需要有指向外部类的引用。但非静态内部类需要持有对外部类的引用。

* 非静态内部类能够访问外部类的静态和非静态成员。静态类不能访问外部类的非静态成员。他只能访问外部类的静态成员。
I
* 一个非静态内部类不能脱离外部类实体被创建，一个非静态内部类可以访问外部类的数据和方法，因为他就在外部类里面。

#### Java反射机制

JAVA反射机制是在运行状态中, 对于任意一个类, 都能够知道这个类的所有属性和方法; 对于任意一个对象, 都能够调用它的任意一个方法和属性; 这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制.

主要作用有三：

* 运行时取得类的方法和字段的相关信息。

* 创建某个类的新实例(.newInstance())

* 取得字段引用直接获取和设置对象字段，无论访问修饰符是什么。

用处如下：

* 观察或操作应用程序的运行时行为。

* 调试或测试程序，因为可以直接访问方法、构造函数和成员字段。

* 通过名字调用不知道的方法并使用该信息来创建对象和调用方法。

#### Java多态的实现原理

当JVM执行Java字节码时，类型信息会存储在方法区中，为了优化对象的调用方法的速度，方法区的类型信息会增加一个指针，该指针指向一个记录该类方法的方法表，方法表中的每一个项都是对应方法的指针。

#### Java对象的内存布局

* 对象头（Header）

**Mark Word** 第一部分用于存储对象自身的运行时数据，如hashCode、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID、偏向时间戳等

对象的另一部分是**类型指针（klass）**，即对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是那个类的实例

**对象头在32位系统上占用8B，64位系统上占16B。 无论是32位系统还是64位系统，对象都采用8字节对齐。Java在64位模式下开启指针压缩，比32位模式下，头部会大4B（mark区域变位8B,kclass区域被压缩），如果没有开启指针压缩，头部会大8B（mark和kclass都是8B）**


* 实例数据（Instance Data）


* 对齐填充（Padding）

#### 并发编程的三大概念：

* 原子性

* 有序性

* 可见性

只有简单的读取、赋值（而且必须是将数字赋值给某个变量，变量之间的相互赋值不是原子操作）才是原子操作。

当一个共享变量被volatile修饰时，它会保证修改的值会立即被更新到主存，当有其他线程需要读取时，它会去内存中读取新值。

#### 指令重排序（Instruction Reorder）

一般来说，处理器为了提高程序运行效率，可能会对输入代码进行优化，它不保证程序中各个语句的执行先后顺序同代码中的顺序一致，但是它会保证程序最终执行结果和代码顺序执行的结果是一致的。

指令重排序不会影响单个线程的执行，但是会影响到线程并发执行的正确性。

happens-before原则（先行发生原则）：

* 程序次序规则：一个线程内，按照代码顺序，书写在前面的操作先行发生于书写在后面的操作

* 锁定规则：一个unLock操作先行发生于后面对同一个锁的lock操作

* volatile变量规则：对一个变量的写操作先行发生于后面对这个变量的读操作

* 传递规则：如果操作A先行发生于操作B，而操作B又先行发生于操作C，则可以得出操作A先行发生于操作C

* 线程启动规则：Thread对象的start()方法先行发生于此线程的每个一个动作

* 线程中断规则：对线程interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生

* 线程终结规则：线程中所有的操作都先行发生于线程的终止检测，我们可以通过Thread.join()方法结束、Thread.isAlive()的返回值手段检测到线程已经终止执行

* 对象终结规则：一个对象的初始化完成先行发生于他的finalize()方法的开始

volatile不能确保原子性，可以通过synchronized或lock，进行加锁，来保证操作的原子性。也可以通过AtomicInteger。

### Java中的锁

#### 公平锁和非公平锁

**公平锁是指多个线程在等待同一个锁时，必须按照申请锁的先后顺序来一次获得锁**。

* 公平锁的好处是等待锁的线程不会饿死，但是整体效率相对低一些；

* 非公平锁的好处是整体效率相对高一些，但是有些线程可能会饿死或者说很早就在等待锁，但要等很久才会获得锁。

* 其中的原因是公平锁是严格按照请求所的顺序来排队获得锁的，而非公平锁时可以抢占的，即如果在某个时刻有线程需要获取锁，而这个时候刚好锁可用，那么这个线程会直接抢占，而这时阻塞在等待队列的线程则不会被唤醒。

* 公平锁可以使用new ReentrantLock(true)实现。

#### 自旋锁

**许多应用上，共享数据的锁定状态只会持续很短的一段时间，为了这段时间取挂起和恢复现场并不值得。为了让线程等待，我们只需让线程执行一个忙循环（自旋），这项技术就是所谓的自旋锁。**

* 自旋等待不能代替阻塞。自旋等待本身虽然避免了线程切换的开销，但它是要占用处理器时间的，因此，如果锁被占用的时间很短，自旋当代的效果就会非常好，反之，如果锁被占用的时间很长，那么自旋的线程只会拜拜浪费处理器资源。

* 因此，自旋等待的时间必须要有一定的限度，如果自旋超过了限定次数（默认是10次，可以使用-XX:PreBlockSpin来更改）没有成功获得锁，就应当使用传统的方式去挂起线程了。

* 自旋锁在JDK1.4.2中引入，使用-XX:+UseSpinning来开启。JDK6中已经变为默认开启，并且引入了自适应的自旋锁。自适应意味着自旋的时间不在固定了，而是由前一次在同一个锁上的自旋时间及锁的拥有者的状态来决定。

* 自旋是在轻量级锁中使用的，在重量级锁中，线程不使用自旋。

#### 锁消除

**锁消除是虚拟机JIT在运行时，对一些代码上要求同步，但是被检测到不可能存在共享数据竞争的锁进行消除。**

锁消除的主要判断依据是来源于逃逸分析的数据支持，如果判断在一段代码中，堆上的所有数据都不会逃逸出去从而能被其他线程访问到，那就可以把他们当做栈上数据对待，认为他们是线程私有的，同步加锁自然就无需进行。

#### 锁粗化

如果一些列的联系操作都是同一个对象反复加上和解锁，甚至加锁操作是出现在循环体中的，那么即使没有线程竞争，频繁地进行互斥操作会有性能损失，为了提高性能，把加锁同步的范围扩展到整个操作序列的外部，即扩展到第一个操作之前和最后一个操作之后，这样的一个锁范围扩展的操作就称之为锁粗化。

#### 可重入锁

**可重入锁，也叫做递归锁，指的是同一线程外层函数获得锁之后 ，内层递归函数仍然有获取该锁的代码，但不受影响。**

在JAVA环境下 ReentrantLock 和synchronized 都是可重入锁。**可重入锁最大的作用是避免死锁。**

#### 类锁和对象锁

##### 类锁

在方法上加上static synchronized的锁，或者synchronized(xxx.class)的锁。如下代码中的method1和method2：

对象锁：参考method4, method5,method6.

```java
public class LockStrategy {     
    public Object object1 = new Object();      

    public static synchronized void method1(){

    }     

    public void method2(){         
        synchronized(LockStrategy.class){

        }     
    }      

    public synchronized void method4(){

    }     

    public void method5()     {         
        synchronized(this){

        }     
    }     

    public void method6()     {         
        synchronized(object1){

        }     
    } 

}
```

#### 偏向锁、轻量级锁和重量级锁

synchronized的偏向锁、轻量级锁以及重量级锁是通过Java对象头实现的。

**偏向锁是JDK6中引入的一项锁优化，它的目的是消除数据在无竞争情况下的同步原语，进一步提高程序的运行性能。**

偏向锁会偏向于第一个获得它的线程，如果在接下来的执行过程中，该锁没有被其他的线程获取，则持有偏向锁的线程将永远不需要同步。大多数情况下，锁不仅不存在多线程竞争，而且总是由同一线程多次获得，为了让线程获得锁的代价更低而引入了偏向锁。

Java对象的内存布局分为：对象头、实例数据和对其填充，而对象头又可以分为”Mark Word”和类型指针klass。”Mark Word”是关键，默认情况下，其存储对象的HashCode、分代年龄和锁标记位。

整个synchronized锁流程如下：

* 检测Mark Word里面是不是当前线程的ID，如果是，表示当前线程处于偏向锁

* 如果不是，则使用CAS将当前线程的ID替换Mard Word，如果成功则表示当前线程获得偏向锁，置偏向标志位1

* 如果失败，则说明发生竞争，撤销偏向锁，进而升级为轻量级锁。

* 当前线程使用CAS将对象头的Mark Word替换为锁记录指针，如果成功，当前线程获得锁

* 如果失败，表示其他线程竞争锁，当前线程便尝试使用自旋来获取锁。

* 如果自旋成功则依然处于轻量级状态。

* 如果自旋失败，则升级为重量级锁。

#### 悲观锁和乐观锁

**悲观锁**：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作。

**乐观锁**：假定不会发生并发冲突，只在提交操作时检测是否违反数据完整性。（使用版本号或者时间戳来配合实现）

#### 共享锁和排它锁

**共享锁**：如果事务T对数据A加上共享锁后，则其他事务只能对A再加共享锁，不能加排它锁。获准共享锁的事务只能读数据，不能修改数据。

**排它锁**：如果事务T对数据A加上排它锁后，则其他事务不能再对A加任何类型的锁。获得排它锁的事务即能读数据又能修改数据。

#### 读写锁

**读写锁是一个资源能够被多个读线程访问，或者被一个写线程访问但不能同时存在读线程。Java当中的读写锁通过ReentrantReadWriteLock实现。**

#### 互斥锁

**所谓互斥锁就是指一次最多只能有一个线程持有的锁。在JDK中synchronized和JUC的Lock就是互斥锁。**

#### 无锁

要保证现场安全，并不是一定就要进行同步，两者没有因果关系。同步只是保证共享数据争用时的正确性的手段，如果一个方法本来就不涉及共享数据，那它自然就无须任何同步措施去保证正确性，因此会有一些代码天生就是线程安全的。

* 无状态编程。无状态代码有一些共同的特征：不依赖于存储在对上的数据和公用的系统资源、用到的状态量都由参数中传入、不调用非无状态的方法等。可以参考Servlet。

* 线程本地存储。可以参考ThreadLocal

* volatile

* CAS

* 协程：在单线程里实现多任务的调度，并在单线程里维持多个任务间的切换。

### Map 类集合 K/V 能不能存储 null 值的情况

集合类 Key Value Super 说明

Hashtable 不允许为 null 不允许为 null Dictionary 线程安全

ConcurrentHashMap 不允许为 null 不允许为 null AbstractMap 线程局部安全

TreeMap 不允许为 null 允许为 null AbstractMap 线程不安全

HashMap 允许为 null 允许为 null AbstractMap 线程不安全

### RxJava


#### Operator

##### Creating Observable

* create

* just

* from

* defer

...

##### Transforming Observable

* map

* flatMap

* groupBy

...

##### Filtering Observable

* debounce

* distinct

* filter

...

##### Combining Observable

* zip

* merge

* startWith 

...

##### Error Handling Operator

**Catch**

* onErrorReturn

* onErrorResumeNext

* onExceptionResumeNext 

**Retry**

#### Schedulers



## Android

### 五种布局： FrameLayout 、 LinearLayout 、 AbsoluteLayout 、 RelativeLayout 、 TableLayout 全都继承自ViewGroup，各自特点及绘制效率对比。

#### FrameLayout(框架布局)

此布局是五种布局中最简单的布局，Android中并没有对child view的摆布进行控制，这个布局中所有的控件都会默认出现在视图的左上角，我们可以使用`android:layout_margin`，`android:layout_gravity`等属性去控制子控件相对布局的位置。

#### LinearLayout(线性布局)

此布局有一个需要格外注意的属性:android:orientation="horizontal|vertical"。

* 当`android:orientation="horizontal`时，说明你希望将水平方向的布局交给**LinearLayout** ，其子元素的`android:layout_gravity="right|left"` 等控制水平方向的gravity值都是被忽略的，此时**LinearLayout**中的子元素都是默认的按照水平从左向右来排，我们可以用`android:layout_gravity="top|bottom"`等gravity值来控制垂直展示。

* 反之，可以知道当`android:orientation="vertical`时，**LinearLayout**对其子元素展示上的的处理方式。

#### AbsoluteLayout(绝对布局)

可以放置多个控件，并且可以自己定义控件的x,y位置

#### RelativeLayout(相对布局)

这个布局也是相对自由的布局，Android 对该布局的child view的 水平layout垂直layout做了解析，由此我们可以FrameLayout的基础上使用标签或者Java代码对垂直方向 以及 水平方向 布局中的views进行任意的控制.

相关属性：

```
　　android:layout_centerInParent="true|false"
　　android:layout_centerHorizontal="true|false"
　　android:layout_alignParentRight="true|false"

```

#### TableLayout(表格布局)

将子元素的位置分配到行或列中，一个TableLayout由许多的TableRow组成

### Activity生命周期

* 启动Activity:

`onCreate()—>onStart()—>onResume()`，Activity进入运行状态。


* Activity退居后台:

当前Activity转到新的Activity界面或按Home键回到主屏：
onPause()—>onStop()，进入停滞状态。


* Activity返回前台:

onRestart()—>onStart()—>onResume()，再次回到运行状态。


* Activity退居后台，且系统内存不足，
系统会杀死这个后台状态的Activity（此时这个Activity引用仍然处在任务栈中，只是这个时候引用指向的对象已经为null），若再次回到这个Activity,则会走onCreate()–>onStart()—>onResume()(将重新走一次Activity的初始化生命周期)


* 锁屏：onPause()->onStop()


* 解锁：onStart()->onResume()


### 通过Acitivty的xml标签来改变任务栈的默认行为

使用`android:launchMode="standard|singleInstance|singleTask|singleTop"`来控制Acivity任务栈。

任务栈是一种后进先出的结构。

位于栈顶的Activity处于焦点状态,当按下back按钮的时候,栈内的Activity会一个一个的出栈,并且调用其onDestory()方法。

如果栈内没有Activity,那么系统就会回收这个栈,**每个APP默认只有一个栈,以APP的包名来命名.**

* standard : 

标准模式,每次启动Activity都会创建一个新的Activity实例,并且将其压入任务栈栈顶,而不管这个Activity是否已经存在。Activity的启动三回调(onCreate()->onStart()->onResume())都会执行。


* singleTop : 

**栈顶复用模式**.这种模式下,如果新Activity已经位于任务栈的栈顶,那么此Activity不会被重新创建,所以它的启动三回调就不会执行,同时Activity的onNewIntent()方法会被回调.如果Activity已经存在但是不在栈顶,那么作用与standard模式一样.

* singleTask: 

**栈内复用模式**.创建这样的Activity的时候,系统会先确认它所需任务栈已经创建,否则先创建任务栈.然后放入Activity,如果栈中已经有一个Activity实例,那么这个Activity就会被调到栈顶,onNewIntent(),并且singleTask会清理在当前Activity上面的所有Activity.(clear top)

* singleInstance : 

**加强版的singleTask模式**,这种模式的Activity只能单独位于一个任务栈内,由于栈内复用的特性,后续请求均不会创建新的Activity,除非这个独特的任务栈被系统销毁了


**Activity的堆栈管理以ActivityRecord为单位,所有的ActivityRecord都放在一个List里面.可以认为一个ActivityRecord就是一个Activity栈**

### Activity缓存方法

##### onSaveInstanceState (Bundle outState)

当某个activity变得"容易"被系统销毁时，该activity的onSaveInstanceState就会被执行，除非该activity是被用户主动销毁的

onSaveInstanceState方法会在什么时候被执行，有这么几种情况：

* 当用户按下HOME键时。
    
这是显而易见的，系统不知道你按下HOME后要运行多少其他的程序，自然也不知道activity A是否会被销毁，故系统会调用onSaveInstanceState，让用户有机会保存某些非永久性的数据。以下几种情况的分析都遵循该原则

* 长按HOME键，选择运行其他的程序时。

* 按下电源按键（关闭屏幕显示）时。

* 从activity A中启动一个新的activity时。

* 屏幕方向切换时，例如从竖屏切换到横屏时。（如果不指定configchange属性）
在屏幕切换之前，系统会销毁activity A，在屏幕切换之后系统又会自动地创建activity A，所以onSaveInstanceState一定会被执行

**总而言之，onSaveInstanceState的调用遵循一个重要原则，即当系统“未经你许可”时销毁了你的activity，则onSaveInstanceState会被系统调用，这是系统的责任，因为它必须要提供一个机会让你保存你的数据（当然你不保存那就随便你了）**

另外，需要注意的几点：

* 布局中的每一个View默认实现了onSaveInstanceState()方法，这样的话，这个UI的任何改变都会自动地存储和在activity重新创建的时候自动地恢复。但是这种情况只有在你为这个UI提供了唯一的ID之后才起作用，如果没有提供ID，app将不会存储它的状态。

* 由于默认的onSaveInstanceState()方法的实现帮助UI存储它的状态，所以如果你需要覆盖这个方法去存储额外的状态信息，你应该在执行任何代码之前都调用父类的onSaveInstanceState()方法（super.onSaveInstanceState()）。
既然有现成的可用，那么我们到底还要不要自己实现onSaveInstanceState()?这得看情况了，如果你自己的派生类中有变量影响到UI，或你程序的行为，当然就要把这个变量也保存了，那么就需要自己实现，否则就不需要。

* 由于onSaveInstanceState()方法调用的不确定性，你应该只使用这个方法去记录activity的瞬间状态（UI的状态）。不应该用这个方法去存储持久化数据。当用户离开这个activity的时候应该在onPause()方法中存储持久化数据（例如应该被存储到数据库中的数据）

* onSaveInstanceState()如果被调用，这个方法会在onStop()前被触发，但系统并不保证是否在onPause()之前或者之后触发。

##### onRestoreInstanceState (Bundle outState) 

至于onRestoreInstanceState方法，需要注意的是

* onSaveInstanceState方法和onRestoreInstanceState方法“不一定”是成对的被调用的

* onRestoreInstanceState被调用的前提是，activity A“确实”被系统销毁了，而如果仅仅是停留在有这种可能性的情况下，则该方法不会被调用

* 另外，onRestoreInstanceState的bundle参数也会传递到onCreate方法中，你也可以选择在onCreate方法中做数据还原。

* onRestoreInstanceState在onstart之后执行

### View的生命周期

#### Creation（创建）

* Constructors（构造函数）：有一种形式的构造函数会在View在代码中被创建时调用，另一种形式的构造函数会在View从layout加载出来时被调用。
第二种形式的构造函数会解析和应用layout文件中定义的任何属性。

* onFinishInflate()：该方法当View及其子View从XML文件中加载完成后会被调用。

#### Layout（布局）

* onMeasure(int, int)：该方法在计算当前View及其所有子View尺寸大小需求时会被调用。

* onLayout(boolean, int, int, int, int)：该方法在当前View需要为其子View分配尺寸和位置时会被调用。

* onSizeChanged(int, int, int, int)：该方法在当前View尺寸变化时被调用。

#### Drawing（绘制）

* onDraw(android.graphics.Canvas)：该方法在当前View需要呈现其内容时被调用。

#### Event processing（事件处理）

* onKeyDown(int, KeyEvent)：该方法在一个物理按键事件发生时被调用。

* onKeyUp(int, KeyEvent)：该方法在一个物理按键弹起事件发生时被调用。

* onTrackballEvent(MotionEvent)：该方法在一个轨迹球运动事件发生时被调用。

* onTouchEvent(MotionEvent)：该方法在一个触摸屏幕运动事件发生时被调用。

#### Focus（聚焦）

* onFocusChanged(boolean, int, android.graphics.Rect)：该方法在当前View获得或失去焦点时被调用。

* onWindowFocusChanged(boolean)：该方法在包含当前View的window获得或失去焦点时被调用。

#### Attaching（附上）

* onAttachedToWindow()：该方法在当前View被附到一个window上时被调用。

* onDetachedFromWindow()：该方法在当前View从一个window上分离时被调用。

* onVisibilityChanged(View, int)：该方法在当前View或其祖先的可见性改变时被调用。

* onWindowVisibilityChanged(int)：该方法在包含当前View的window可见性改变时被调用。

```
[改变可见性] --> 构造View() --> onFinishInflate() --> onAttachedToWindow() --> onMeasure() --> onSizeChanged() --> onLayout() --> onDraw() --> onDetackedFromWindow()
```

### 为什么在Service中创建子线程而不是Activity中

* 这是因为Activity很难对Thread进行控制，当Activity被销毁之后，就没有任何其它的办法可以再重新获取到之前创建的子线程的实例。

* 而且在一个Activity中创建的子线程，另一个Activity无法对其进行操作。但是Service就不同了，所有的Activity都可以与Service进行关联，然后可以很方便地操作其中的方法，即使Activity被销毁了，之后只要重新与Service建立关联，就又能够获取到原有的Service中Binder的实例。因此，使用Service来处理后台任务，Activity就可以放心地finish，完全不需要担心无法对后台任务进行控制的情况。

### Intent的使用方法，可以传递哪些数据类型

* Intent/Bundle支持传递基本类型的数据和基本类型的数组数据，以及String/CharSequence类型的数据和String/CharSequence类型的数组数据。

* 要让非基本类型和非String/CharSequence类型的数据通过Intent/Bundle来进行传输，我们就需要在数据类型中**实现Parcelable接口或是Serializable接口**。

### Service的两种启动方法，有什么区别

* 在Context中通过public boolean bindService(Intent service,ServiceConnection conn,int flags) 方法来进行Service与Context的关联并启动，并且Service的生命周期依附于Context。

* 通过public ComponentName startService(Intent service)方法去启动一个Service，此时Service的生命周期与启动它的Context无关。

* 要注意的是，whatever，都需要在xml里注册你的Service，就像这样:
`
<service android:name=".packnameName.youServiceName"
        android:enabled="true" />
    `

### 广播(Broadcast Receiver)的两种动态注册和静态注册有什么区别。

* 静态注册：在AndroidManifest.xml文件中进行注册，当App退出后，Receiver仍然可以接收到广播并且进行相应的处理

* 动态注册：在代码中动态注册，当App退出后，也就没办法再接受广播了

### 目前能否保证service不被杀死

* Service设置成START_STICKY

kill 后会被重启（等待5秒左右），重传Intent，保持与重启前一样

* 提升service优先级

在AndroidManifest.xml文件中对于intent-filter可以通过android:priority = "1000"这个属性设置最高优先级，1000是最高值，如果数字越小则优先级越低，同时适用于广播。
**目前看来，priority这个属性貌似只适用于broadcast，对于Service来说可能无效**

* 提升service进程优先级

Android中的进程是托管的，当系统进程空间紧张的时候，会依照优先级自动进行进程的回收，当service运行在低内存的环境时，将会kill掉一些存在的进程。因此进程的优先级将会很重要，可以在startForeground()使用startForeground()将service放到前台状态。这样在低内存时被kill的几率会低一些。
**如果在极度极度低内存的压力下，该service还是会被kill掉，并且不一定会restart()**

* onDestroy方法里重启service

service +broadcast  方式，就是当service走onDestory()的时候，发送一个自定义的广播，当收到广播的时候，重新启动service
也可以直接在onDestroy()里startService
**当使用类似QQ管家等第三方应用或是在setting里-应用-强制停止时，APP进程可能就直接被干掉了，onDestroy方法都进不来，所以还是无法保证**

* 监听系统广播判断Service状态

通过系统的一些广播，比如：手机重启、界面唤醒、应用状态改变等等监听并捕获到，然后判断我们的Service是否还存活，别忘记加权限
**这也能算是一种措施，不过感觉监听多了会导致Service很混乱，带来诸多不便**

* 在JNI层,用C代码fork一个进程出来

这样产生的进程,会被系统认为是两个不同的进程.但是Android5.0之后可能不行

* root之后放到system/app变成系统级应用

* 大招: 放一个像素在前台(手机QQ)

### 动画有哪两类，各有什么特点？三种动画的区别

* tween 补间动画。通过指定View的初末状态和变化时间、方式，对View的内容完成一系列的图形变换来实现动画效果。
Alpha
Scale
Translate
Rotate。

* frame 帧动画
AnimationDrawable 控制
animation-list xml布局

* PropertyAnimation 属性动画

### Android的数据存储形式

* SQLite：SQLite是一个轻量级的数据库，支持基本的SQL语法，是常被采用的一种数据存储方式。
Android为此数据库提供了一个名为SQLiteDatabase的类，封装了一些操作数据库的api

* SharedPreference： 除SQLite数据库外，另一种常用的数据存储方式，其本质就是一个xml文件，常用于存储较简单的参数设置。

* File： 即常说的文件（I/O）存储方法，常用语存储大数量的数据，但是缺点是更新数据将是一件困难的事情。

* ContentProvider: Android系统中能实现所有应用程序共享的一种数据存储方式，由于数据通常在各应用间的是互相私密的，所以此存储方式较少使用，但是其又是必不可少的一种存储方式。例如音频，视频，图片和通讯录，一般都可以采用此种方式进行存储。每个Content Provider都会对外提供一个公共的URI（包装成Uri对象），如果应用程序有数据需要共享时，就需要使用Content Provider为这些数据定义一个URI，然后其他的应用程序就通过Content Provider传入这个URI来对数据进行操作。

### 如何判断应用被强杀

在Application中定义一个static常量，赋值为－1，在欢迎界面改为0，如果被强杀，application重新初始化，在父类Activity判断该常量的值。

* 应用被强杀如何解决

如果在每一个Activity的onCreate里判断是否被强杀，冗余了，封装到Activity的父类中，如果被强杀，跳转回主界面，如果没有被强杀，执行Activity的初始化操作，给主界面传递intent参数，主界面会调用onNewIntent方法，在onNewIntent跳转到欢迎页面，重新来一遍流程。

### Json有什么优劣势。

### 怎样退出终止App

### Asset目录与res目录的区别。

* res 目录下面有很多文件，例如 drawable,mipmap,raw 等。res 下面除了 raw 文件不会被压缩外，其余文件都会被压缩。

* 同时 res目录下的文件可以通过R 文件访问。Asset 也是用来存储资源，但是 asset 文件内容只能通过路径或者 AssetManager 读取。 

### Android怎么加速启动Activity

分两种情况

* 启动应用 ：Application 的构造方法，onCreate 方法中不要进行耗时操作，数据预读取(例如 init 数据) 放在异步中操作

* 启动普通的Activity：A 启动B 时不要在 A 的 onPause 中执行耗时操作。因为 B 的 onResume 方法必须等待 A 的 onPause 执行完成后才能运行

### Android内存优化方法：
ListView优化，及时关闭资源，图片缓存等等。

### Android中弱引用与软引用的应用场景。

### Bitmap的四种属性，与每种属性队形的大小。

### View与View Group分类。自定义View过程：onMeasure()、onLayout()、onDraw()。

### 如何自定义控件：

* 自定义属性的声明和获取
* 分析需要的自定义属性
* 在res/values/attrs.xml定义声明
* 在layout文件中进行使用
* 在View的构造方法中进行获取

### Android长连接，怎么处理心跳机制。

### View树绘制流程

* 测量onMeasure

* 布局onLayout(ViewGroup)

* 绘制onDraw

* onTouchEvent

* onInterceptTouchEvent(ViewGroup)




### 下拉刷新实现原理

### Context区别

* Activity和Service以及Application的Context是不一样的,Activity继承自ContextThemeWraper.其他的继承自ContextWrapper

* 每一个Activity和Service以及Application的Context都是一个新的ContextImpl对象

* getApplication()用来获取Application实例的，但是这个方法只有在Activity和Service中才能调用的到。那么也许在绝大多数情况下我们都是在Activity或者Service中使用Application的，但是如果在一些其它的场景，比如BroadcastReceiver中也想获得Application的实例，这时就可以借助getApplicationContext()方法，getApplicationContext()比getApplication()方法的作用域会更广一些，任何一个Context的实例，只要调用getApplicationContext()方法都可以拿到我们的Application对象。

* Activity在创建的时候会new一个ContextImpl对象并在attach方法中关联它，Application和Service也差不多。ContextWrapper的方法内部都是转调ContextImpl的方法

* 创建对话框传入Application的Context是不可以的

* 尽管Application、Activity、Service都有自己的ContextImpl，并且每个ContextImpl都有自己的mResources成员，但是由于它们的mResources成员都来自于唯一的ResourcesManager实例，所以它们看似不同的mResources其实都指向的是同一块内存

* Context的数量等于Activity的个数 + Service的个数 + 1，这个1为Application

### IntentService的使用场景与特点。

IntentService是Service的子类，是一个异步的，会自动停止的服务，很好解决了传统的Service中处理完耗时操作忘记停止并销毁Service的问题

* 优点：

一方面不需要自己去new Thread
另一方面不需要考虑在什么时候关闭该Service

onStartCommand中回调了onStart，onStart中通过mServiceHandler发送消息到该handler的handleMessage中去。最后handleMessage中回调onHandleIntent(intent)。

### 图片缓存

查看每个应用程序最高可用内存：
    int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);  
    Log.d("TAG", "Max memory is " + maxMemory + "KB");  


### Gradle

构建工具、Groovy语法、Java
Jar包里面只有代码，aar里面不光有代码还包括代码还包括资源文件，比如 drawable 文件，xml 资源文件。对于一些不常变动的 Android Library，我们可以直接引用 aar，加快编译速度

### Android 内存泄漏总结

内存管理的目的就是让我们在开发中怎么有效的避免我们的应用出现内存泄漏的问题。内存泄漏大家都不陌生了，简单粗俗的讲，就是该被释放的对象没有释放，一直被某个或某些实例所持有却不再被使用导致 GC 不能回收。

### Java 内存分配策略

Java 程序运行时的内存分配策略有三种,分别是静态分配,栈式分配,和堆式分配，对应的，三种存储策略使用的内存空间主要分别是静态存储区（也称方法区）、栈区和堆区。


* 静态存储区（方法区）：主要存放静态数据、全局 static 数据和常量。这块内存在程序编译时就已经分配好，并且在程序整个运行期间都存在。

* 栈区 ：当方法被执行时，方法体内的局部变量（其中包括基础数据类型、对象的引用）都在栈上创建，并在方法执行结束时这些局部变量所持有的内存将会自动被释放。因为栈内存分配运算内置于处理器的指令集中，效率很高，但是分配的内存容量有限。

* 堆区 ： 又称动态内存分配，通常就是指在程序运行时直接 new 出来的内存，也就是对象的实例。这部分内存在不使用时将会由 Java 垃圾回收器来负责回收。

### 栈与堆的区别：

在方法体内定义的（局部变量）一些基本类型的变量和对象的引用变量都是在方法的栈内存中分配的。当在一段方法块中定义一个变量时，Java 就会在栈中为该变量分配内存空间，当超过该变量的作用域后，该变量也就无效了，分配给它的内存空间也将被释放掉，该内存空间可以被重新使用。
堆内存用来存放所有由 new 创建的对象（包括该对象其中的所有成员变量）和数组。在堆中分配的内存，将由 Java 垃圾回收器来自动管理。在堆中产生了一个数组或者对象后，还可以在栈中定义一个特殊的变量，这个变量的取值等于数组或者对象在堆内存中的首地址，这个特殊的变量就是我们上面说的引用变量。我们可以通过这个引用变量来访问堆中的对象或者数组。

* 结论：

**局部变量的基本数据类型和引用存储于栈中，引用的对象实体存储于堆中。—— 因为它们属于方法中的变量，生命周期随方法而结束。
成员变量全部存储与堆中（包括基本数据类型，引用和引用的对象实体）—— 因为它们属于类，类对象终究是要被new出来使用的。**

### Java是如何管理内存

为了更好理解 GC 的工作原理，我们可以将对象考虑为有向图的顶点，将引用关系考虑为图的有向边，有向边从引用者指向被引对象。另外，每个线程对象可以作为一个图的起始顶点，例如大多程序从 main 进程开始执行，那么该图就是以 main 进程顶点开始的一棵根树。在这个有向图中，根顶点可达的对象都是有效对象，GC将不回收这些对象。如果某个对象 (连通子图)与这个根顶点不可达(注意，该图为有向图)，那么我们认为这个(这些)对象不再被引用，可以被 GC 回收。

Java使用有向图的方式进行内存管理，**可以消除引用循环的问题**，例如有三个对象，相互引用，只要它们和根进程不可达的，那么GC也是可以回收它们的。这种方式的优点是管理内存的精度很高，但是效率较低。**另外一种常用的内存管理技术是使用计数器**，例如COM模型采用计数器方式管理构件，它与有向图相比，精度行低(很难处理循环引用的问题)，但执行效率很高。

### 什么是Java中的内存泄露

在Java中，内存泄漏就是存在一些被分配的对象，这些对象有下面两个特点，

* 首先，这些对象是可达的，即在有向图中，存在通路可以与其相连；

* 其次，这些对象是无用的，即程序以后不会再使用这些对象。

如果对象满足这两个条件，这些对象就可以判定为Java中的内存泄漏，这些对象不会被GC所回收，然而它却占用内存。

在C++中，内存泄漏的范围更大一些。有些对象被分配了内存空间，然后却不可达，由于C++中没有GC，这些内存将永远收不回来。在Java中，这些不可达的对象都由GC负责回收，因此程序员不需要考虑这部分的内存泄露。

通过分析，我们得知，对于C++，程序员需要自己管理边和顶点，而对于Java程序员只需要管理边就可以了(不需要管理顶点的释放)。通过这种方式，Java提高了编程的效率。

因此，通过以上分析，我们知道**在Java中也有内存泄漏，但范围比C++要小一些**。因为Java从语言上保证，任何对象都是可达的，所有的不可达对象都由GC管理。
对于程序员来说，GC基本是透明的，不可见的。虽然，我们只有几个函数可以访问GC，例如运行GC的函数System.gc()，但是根据Java语言规范定义， **该函数不保证JVM的垃圾收集器一定会执行**。因为，不同的JVM实现者可能使用不同的算法管理GC。通常，**GC的线程的优先级别较低**。JVM调用GC的策略也有很多种，有的是内存使用到达一定程度时，GC才开始工作，也有定时执行的，有的是平缓执行GC，有的是中断式执行GC。但通常来说，我们不需要关心这些。除非在一些特定的场合，GC的执行影响应用程序的性能，例如对于基于Web的实时系统，如网络游戏等，用户不希望GC突然中断应用程序执行而进行垃圾回收，那么我们需要调整GC的参数，让GC能够通过平缓的方式释放内存，例如将垃圾回收分解为一系列的小步骤执行，Sun提供的HotSpot JVM就支持这一特性。


### Java中的内存泄漏

* Java内存泄漏引起的原因

* 静态集合类引起内存泄漏：
像HashMap、Vector等的使用最容易出现内存泄露，这些静态变量的生命周期和应用程序一致，他们所引用的所有的对象Object也不能被释放，因为他们也将一直被Vector等引用着。

```
static Vector v = new Vector(10);
for (int i = 1; i<100; i++)
{
    Object o = new Object();
    v.add(o);
    o = null;
}
```

在这个例子中，循环申请Object 对象，并将所申请的对象放入一个Vector 中，如果仅仅释放引用本身（o=null），那么Vector 仍然引用该对象，所以这个对象对GC 来说是不可回收的。因此，如果对象加入到Vector 后，还必须从Vector 中删除，最简单的方法就是将Vector对象设置为null。

* 当集合里面的对象属性被修改后，再调用remove()方法时不起作用。

```
public static void main(String[] args)
{
    Set<Person> set = new HashSet<Person>();
    Person p1 = new Person("唐僧","pwd1",25);
    Person p2 = new Person("孙悟空","pwd2",26);
    Person p3 = new Person("猪八戒","pwd3",27);
    set.add(p1);
    set.add(p2);
    set.add(p3);
    System.out.println("总共有:"+set.size()+" 个元素!"); //结果：总共有:3 个元素!
    p3.setAge(2); //修改p3的年龄,此时p3元素对应的hashcode值发生改变

    set.remove(p3); //此时remove不掉，造成内存泄漏

    set.add(p3); //重新添加，居然添加成功
    System.out.println("总共有:"+set.size()+" 个元素!"); //结果：总共有:4 个元素!
    for (Person person : set)
    {
        System.out.println(person);
    }
}

```

* 监听器
在java 编程中，我们都需要和监听器打交道，通常一个应用当中会用到很多监听器，我们会调用一个控件的诸如addXXXListener()等方法来增加监听器，但往往在释放对象的时候却没有记住去删除这些监听器，从而增加了内存泄漏的机会。

* 各种连接
比如数据库连接（dataSourse.getConnection()），网络连接(socket)和io连接，除非其显式的调用了其close（）方法将其连接关闭，否则是不会自动被GC 回收的。对于Resultset 和Statement 对象可以不进行显式回收，但Connection 一定要显式回收，因为Connection 在任何时候都无法自动回收，而Connection一旦回收，Resultset 和Statement 对象就会立即为NULL。但是如果使用连接池，情况就不一样了，除了要显式地关闭连接，还必须显式地关闭Resultset Statement 对象（关闭其中一个，另外一个也会关闭），否则就会造成大量的Statement 对象无法释放，从而引起内存泄漏。这种情况下一般都会在try里面去的连接，在finally里面释放连接。

* 内部类和外部模块的引用
内部类的引用是比较容易遗忘的一种，而且一旦没释放可能导致一系列的后继类对象没有释放。此外程序员还要小心外部模块不经意的引用，例如程序员A 负责A 模块，调用了B 模块的一个方法如：
public void registerMsg(Object b);
这种调用就要非常小心了，传入了一个对象，很可能模块B就保持了对该对象的引用，这时候就需要注意模块B 是否提供相应的操作去除引用。

* 单例模式
不正确使用单例模式是引起内存泄漏的一个常见问题，单例对象在初始化后将在JVM的整个生命周期中存在（以静态变量的方式），如果单例对象持有外部的引用，那么这个对象将不能被JVM正常回收，导致内存泄漏，考虑下面的例子：

```
class A{
    public A(){
        B.getInstance().setA(this);
    }
....
}
//B类采用单例模式
class B{
    private A a;
    private static B instance=new B();
    public B(){}
    public static B getInstance(){
        return instance;
    }
    public void setA(A a){
        this.a=a;
    }
    //getter...
} 

```

显然B采用singleton模式，它持有一个A对象的引用，而这个A类的对象将不能被回收。想象下如果A是个比较复杂的对象或者集合类型会发生什么情况

### Android中常见的内存泄漏汇总

* 集合类泄漏

* 单例造成的内存泄漏

* 匿名内部类/非静态内部类和异步线程

* 非静态内部类创建静态实例造成的内存泄漏

* 匿名内部类

* Handler 造成的内存泄漏

**综述，即推荐使用静态内部类 + WeakReference 这种方式。每次使用前注意判空。**

Java对引用的分类有 Strong reference, SoftReference, WeakReference, PhatomReference 四种

**如果只是想避免OutOfMemory异常的发生，则可以使用软引用。如果对于应用的性能更在意，想尽快回收一些占用内存比较大的对象，则可以使用弱引用。**

**如果该对象可能会经常使用的，就尽量用软引用。如果该对象不被使用的可能性更大些，就可以用弱引用。**

### 尽量避免使用 static 成员变量

不要在类初始时初始化静态成员。可以考虑lazy初始化。
架构设计上要思考是否真的有必要这样做，尽量避免。如果架构需要这么设计，那么此对象的生命周期你有责任管理起来

### 避免 override finalize()

* finalize 方法被执行的时间不确定，不能依赖与它来释放紧缺的资源。时间不确定的原因是：
虚拟机调用GC的时间不确定
Finalize daemon线程被调度到的时间不确定

* finalize 方法只会被执行一次，即使对象被复活，如果已经执行过了 finalize 方法，再次被 GC 时也不会再执行了，原因是：
含有 finalize 方法的 object 是在 new 的时候由虚拟机生成了一个 finalize reference 在来引用到该Object的，而在 finalize 方法执行的时候，该 object 所对应的 finalize Reference 会被释放掉，即使在这个时候把该 object 复活(即用强引用引用住该 object )，再第二次被 GC 的时候由于没有了 finalize reference 与之对应，所以 finalize 方法不会再执行。

* 含有Finalize方法的object需要至少经过两轮GC才有可能被释放。

### 资源未关闭造成的内存泄漏

### 一些不良代码造成的内存压力

比如：

* Bitmap 没调用 recycle()方法，对于 Bitmap 对象在不使用时,我们应该先调用 recycle() 释放内存，然后才它设置为 null. 因为加载 Bitmap 对象的内存空间，一部分是 java 的，一部分 C 的（因为 Bitmap 分配的底层是通过 JNI 调用的 )。 而这个 recyle() 就是针对 C 部分的内存释放。

* 构造 Adapter 时，没有使用缓存的 convertView ,每次都在创建新的 converView。这里推荐使用 ViewHolder。

### 总结

* 对 Activity 等组件的引用应该控制在 Activity 的生命周期之内； 如果不能就考虑使用 getApplicationContext 或者 getApplication，以避免 Activity 被外部长生命周期的对象引用而泄露。

* 尽量不要在静态变量或者静态内部类中使用非静态外部成员变量（包括context )，即使要使用，也要考虑适时把外部成员变量置空；也可以在内部类中使用弱引用来引用外部类的变量。

* 对于生命周期比Activity长的内部类对象，并且内部类中使用了外部类的成员变量，可以这样做避免内存泄漏：

**将内部类改为静态内部类**

**静态内部类中使用弱引用来引用外部类的成员变量**

* Handler 的持有的引用对象最好使用弱引用，资源释放时也可以清空 Handler 里面的消息。比如在 Activity onStop 或者 onDestroy 的时候，取消掉该 Handler 对象的 Message和 Runnable.

* 在 Java 的实现过程中，也要考虑其对象释放，最好的方法是在不使用某对象时，显式地将此对象赋值为 null，比如使用完Bitmap 后先调用 recycle()，再赋为null,清空对图片等资源有直接引用或者间接引用的数组（使用 array.clear() ; array = null）等，最好遵循谁创建谁释放的原则。

* 正确关闭资源，对于使用了BraodcastReceiver，ContentObserver，File，游标 Cursor，Stream，Bitmap等资源的使用，应该在Activity销毁时及时关闭或者注销。

* 保持对对象生命周期的敏感，特别注意单例、静态对象、全局性集合等的生命周期。

            
### Handler、Looper、Message、MessageQueue


#### Looper(先分析这个是因为能够引出四者的关系)

在Looper中，维持一个Thread对象以及MessageQueue，通过Looper的构造函数我们可以知道:

```
private Looper(boolean quitAllowed) {
    mQueue = new MessageQueue(quitAllowed);//传入的参数代表这个Queue是否能够被退出
    mThread = Thread.currentThread();
}

```
Looper在构造函数里干了两件事情：

* 将线程对象指向了创建Looper的线程

* 创建了一个新的MessageQueue

##### looper.loop()（在当前线程启动一个Message loop机制，此段代码将直接分析出Looper、Handler、Message、MessageQueue的关系）


```
public static void loop() {
    final Looper me = myLooper();//获得当前线程绑定的Looper
    if (me == null) {
        throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
    }
    final MessageQueue queue = me.mQueue;//获得与Looper绑定的MessageQueue

    // Make sure the identity of this thread is that of the local process,
    // and keep track of what that identity token actually is.
    Binder.clearCallingIdentity();
    final long ident = Binder.clearCallingIdentity();

    //进入死循环，不断地去取对象，分发对象到Handler中消费
    for (;;) {
        Message msg = queue.next(); // 不断的取下一个Message对象，在这里可能会造成堵塞。
        if (msg == null) {
            // No message indicates that the message queue is quitting.
            return;
        }

        // This must be in a local variable, in case a UI event sets the logger
        Printer logging = me.mLogging;
        if (logging != null) {
            logging.println(">>>>> Dispatching to " + msg.target + " " +
                    msg.callback + ": " + msg.what);
        }

        //在这里，开始分发Message了
        //至于这个target是神马？什么时候被赋值的？ 
        //我们一会分析Handler的时候就会讲到
        msg.target.dispatchMessage(msg);

        if (logging != null) {
            logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
        }

        // Make sure that during the course of dispatching the
        // identity of the thread wasn't corrupted.
        final long newIdent = Binder.clearCallingIdentity();
        if (ident != newIdent) {
            Log.wtf(TAG, "Thread identity changed from 0x"
                    + Long.toHexString(ident) + " to 0x"
                    + Long.toHexString(newIdent) + " while dispatching to "
                    + msg.target.getClass().getName() + " "
                    + msg.callback + " what=" + msg.what);
        }

        //当分发完Message之后，当然要标记将该Message标记为 *正在使用* 啦
        msg.recycleUnchecked();
    }
}

```

这段最重要的代码中涉及到了四个对象,他们与彼此的关系如下：

* MessageQueue：装食物的容器

* Message：被装的食物

* Handler（msg.target实际上就是Handler）：食物的消费者

* Looper：负责分发食物的人


##### looper.prepare()（在当前线程关联一个Looper对象）

```
private static void prepare(boolean quitAllowed) {
    if (sThreadLocal.get() != null) {
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    //在当前线程绑定一个Looper
    sThreadLocal.set(new Looper(quitAllowed));
}
```

#### Handler

* Looper.loop()死循环中的msg.target是什么时候被赋值的？

```
public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
    //引用Handler中的MessageQueue
    //这个MessageQueue就是创建Looper时被创建的MessageQueue
    MessageQueue queue = mQueue;

    if (queue == null) {
        RuntimeException e = new RuntimeException(
                this + " sendMessageAtTime() called with no mQueue");
        Log.w("Looper", e.getMessage(), e);
        return false;
    }
    //将新来的Message加入到MessageQueue中
    return enqueueMessage(queue, msg, uptimeMillis);
}

private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
    //显而易见，大写加粗的赋值啊！
    msg.target = this;
    if (mAsynchronous) {
        msg.setAsynchronous(true);
    }
    return queue.enqueueMessage(msg, uptimeMillis);
}

```

* handler.handleMessage(msg)在什么时候被回调的？

```
public void dispatchMessage(Message msg) {
    if (msg.callback != null) {
        handleCallback(msg);
    } else {
        if (mCallback != null) {
            if (mCallback.handleMessage(msg)) {
                return;
            }
        }
        handleMessage(msg);
    }
}
```

#### 总结：

* 当我们调用handler.sendMessage(msg)方法发送一个Message时，实际上这个Message是发送到与当前线程绑定的一个MessageQueue中，然后与当前线程绑定的Looper将会不断的从MessageQueue中取出新的Message，调用msg.target.dispathMessage(msg)方法将消息分发到与Message绑定的handler.handleMessage()方法中。

* 一个Thread对应多个Handler

* 一个Thread对应一个Looper和MessageQueue，Handler与Thread共享Looper和MessageQueue。

* Message只是消息的载体，将会被发送到与线程绑定的唯一的MessageQueue中，并且被与线程绑定的唯一的Looper分发，被与其自身绑定的Handler消费。


### Android推送服务的几种实现方式

#### 轮询

客户端定期询问服务器有没有新的消息, 这样服务器不用管客户端的地址是什么, 客户端来问, 直接告诉它就行.

* 实时性不高

* 频繁轮询很费电量流量

#### SMS通知

* 成本太高

#### 长连接


### Android图片中的三级缓存

#### 为什么要使用三级缓存

* 如今的 Android App 经常会需要网络交互，通过网络获取图片是再正常不过的事了

* 假如每次启动的时候都从网络拉取图片的话，势必会消耗很多流量。在当前的状况下，对于非wifi用户来说，流量还是很贵的，一个很耗流量的应用，其用户数量级肯定要受到影响

* 特别是，当我们想要重复浏览一些图片时，如果每一次浏览都需要通过网络获取，流量的浪费可想而知

* 所以提出三级缓存策略，通过网络、本地、内存三级缓存图片，来减少不必要的网络交互，避免浪费流量

#### 什么是三级缓存

* 网络加载，不优先加载，速度慢，浪费流量

* 本地缓存，次优先加载，速度快

* 内存缓存，优先加载，速度最快

#### 三级缓存原理

* 首次加载 Android App 时，肯定要通过网络交互来获取图片，之后我们可以将图片保存至本地SD卡和内存中

* 之后运行 App 时，优先访问内存中的图片缓存，若内存中没有，则加载本地SD卡中的图片

* 总之，只在初次访问新内容时，才通过网络获取图片资源


### 不容作用域的变量类型

* 函数成员变量 - 尽在函数内部有效

* 类成员变量 - 尽在对象内部有效

* 线程局部存储(TSL)变量 - 在本线程内的任何对象内保持一致

* 静态变量 - 在本进程内的任何对象内保持一致

* 跨进程通信(IPC)变量 - 一般使用Binder进行定义， 在所有进程内保持一致

### Android开机过程

* BootLoader引导,然后加载Linux内核.

* 0号进程init启动.加载init.rc配置文件,配置文件有个命令启动了zygote进程

* zygote开始fork出SystemServer进程

* SystemServer加载各种JNI库,然后init1,init2方法,init2方法中开启了新线程ServerThread.

* 在SystemServer中会创建一个socket客户端，后续AMS（ActivityManagerService）会通过此客户端和zygote通信

* ServerThread的run方法中开启了AMS,还孵化新进程ServiceManager,加载注册了一系列的服务,最后一句话进入loop 死循环

* run方法的SystemReady调用resumeTopActivityLocked打开锁屏界面



### Android性能优化

#### 合理管理内存

* 节制的使用Service

如果应用程序需要使用Service来执行后台任务的话，只有当任务正在执行的时候才应该让Service运行起来。当启动一个Service时，系统会倾向于将这个Service所依赖的进程进行保留，系统可以在LRUcache当中缓存的进程数量也会减少，导致切换程序的时候耗费更多性能。我们可以使用IntentService，当后台任务执行结束后会自动停止，避免了Service的内存泄漏。

* 当界面不可见时释放内存

当用户打开了另外一个程序，我们的程序界面已经不可见的时候，我们应当将所有和界面相关的资源进行释放。重写Activity的onTrimMemory()方法，然后在这个方法中监听TRIM_MEMORY_UI_HIDDEN这个级别，一旦触发说明用户离开了程序，此时就可以进行资源释放操作了。

* 当内存紧张时释放内存

onTrimMemory()方法还有很多种其他类型的回调，可以在手机内存降低的时候及时通知我们，我们应该根据回调中传入的级别来去决定如何释放应用程序的资源。

* 避免在Bitmap上浪费内存

读取一个Bitmap图片的时候，千万不要去加载不需要的分辨率。可以压缩图片等操作。

* 是有优化过的数据集合

Android提供了一系列优化过后的数据集合工具类，如SparseArray、SparseBooleanArray、LongSparseArray，使用这些API可以让我们的程序更加高效。HashMap工具类会相对比较低效，因为它需要为每一个键值对都提供一个对象入口，而SparseArray就避免掉了基本数据类型转换成对象数据类型的时间。

* 知晓内存的开支情况

使用枚举通常会比使用静态常量消耗两倍以上的内存，尽可能不使用枚举
任何一个Java类，包括匿名类、内部类，都要占用大概500字节的内存空间
任何一个类的实例要消耗12-16字节的内存开支，因此频繁创建实例也是会在一定程序上影响内存的
使用HashMap时，即使你只设置了一个基本数据类型的键，比如说int，但是也会按照对象的大小来分配内存，大概是32字节，而不是4字节，因此最好使用优化后的数据集合

* 谨慎使用抽象编程

在Android使用抽象编程会带来额外的内存开支，因为抽象的编程方法需要编写额外的代码，虽然这些代码根本执行不到，但是也要映射到内存中，不仅占用了更多的内存，在执行效率上也会有所降低。所以需要合理的使用抽象编程。

* 尽量避免使用依赖注入框架

使用依赖注入框架貌似看上去把findViewById()这一类的繁琐操作去掉了，但是这些框架为了要搜寻代码中的注解，通常都需要经历较长的初始化过程，并且将一些你用不到的对象也一并加载到内存中。这些用不到的对象会一直站用着内存空间，可能很久之后才会得到释放，所以可能多敲几行代码是更好的选择。

* 使用多个进程

谨慎使用，多数应用程序不该在多个进程中运行的，一旦使用不当，它甚至会增加额外的内存而不是帮我们节省内存。这个技巧比较适用于哪些需要在后台去完成一项独立的任务，和前台是完全可以区分开的场景。比如音乐播放，关闭软件，已经完全由Service来控制音乐播放了，系统仍然会将许多UI方面的内存进行保留。在这种场景下就非常适合使用两个进程，一个用于UI展示，另一个用于在后台持续的播放音乐。关于实现多进程，只需要在Manifast文件的应用程序组件声明一个android:process属性就可以了。进程名可以自定义，但是之前要加个冒号，表示该进程是一个当前应用程序的私有进程。

#### 分析内存的使用情况

系统不可能将所有的内存都分配给我们的应用程序，每个程序都会有可使用的内存上限，被称为堆大小。不同的手机堆大小不同，如下代码可以获得堆大小：

```
ActivityManager manager = (ActivityManager)getSystemService(Context.ACTIVITY_SERVICE);
int heapSize = manager.getMemoryClass();

```

结果以MB为单位进行返回，我们开发时应用程序的内存不能超过这个限制，否则会出现OOM。

* Android的GC操作

Android系统会在适当的时机触发GC操作，一旦进行GC操作，就会将一些不再使用的对象进行回收。GC操作会从一个叫做Roots的对象开始检查，所有它可以访问到的对象就说明还在使用当中，应该进行保留，而其他的对系那个就表示已经不再被使用了。

* Android中内存泄漏

* 高性能编码优化

都是一些微优化，在性能方面看不出有什么显著的提升的。使用合适的算法和数据结构是优化程序性能的最主要手段。

* 避免创建不必要的对象

不必要的对象我们应该避免创建：

如果有需要拼接的字符串，那么可以优先考虑使用StringBuffer或者StringBuilder来进行拼接，而不是加号连接符

在没有特殊原因的情况下，尽量使用基本数据类型来代替封装数据类型，int比Integer要更加有效，其它数据类型也是一样。

当一个方法的返回值是String的时候，通常需要去判断一下这个String的作用是什么，如果明确知道调用方会将返回的String再进行拼接操作的话，可以考虑返回一个StringBuffer对象来代替，因为这样可以将一个对象的引用进行返回，而返回String的话就是创建了一个短生命周期的临时对象。

基本数据类型的数组也要优于对象数据类型的数组。另外两个平行的数组要比一个封装好的对象数组更加高效，举个例子，Foo[]和Bar[]这样的数组，使用起来要比Custom(Foo,Bar)[]这样的一个数组高效的多。

尽可能地少创建临时对象，越少的对象意味着越少的GC操作。

* 静态优于抽象

如果你并不需要访问一个对象中的某些字段，只是想调用它的某些方法来去完成一项通用的功能，那么可以将这个方法设置成静态方法，调用速度提升15%-20%，同时也不用为了调用这个方法去专门创建对象了，也不用担心调用这个方法后是否会改变对象的状态(静态方法无法访问非静态字段)。

* 对常量使用static final修饰符

```

static int intVal = 42;  
static String strVal = "Hello, world!"; 

```  

这样，定义类就不需要方法了，因为所有的常量都会在dex文件的初始化器当中进行初始化。当我们调用intVal时可以直接指向42的值，而调用strVal会用一种相对轻量级的字符串常量方式，而不是字段搜寻的方式。
这种优化方式只对基本数据类型以及String类型的常量有效，对于其他数据类型的常量是无效的。

* 使用增强型for循环语法

```
static class Counter {  
    int mCount;  
}  
  
Counter[] mArray = ...  
  
public void zero() {  
    int sum = 0;  
    for (int i = 0; i < mArray.length; ++i) {  
        sum += mArray[i].mCount;  
    }  
}  
  
public void one() {  
    int sum = 0;  
    Counter[] localArray = mArray;  
    int len = localArray.length;  
    for (int i = 0; i < len; ++i) {  
        sum += localArray[i].mCount;  
    }  
}  
  
public void two() {  
    int sum = 0;  
    for (Counter a : mArray) {  
        sum += a.mCount;  
    }  
}  

```
zero()最慢，每次都要计算mArray的长度，one()相对快得多，two()fangfa在没有JIT(Just In Time Compiler)的设备上是运行最快的，而在有JIT的设备上运行效率和one()方法不相上下，需要注意这种写法需要JDK1.5之后才支持。
Tips:ArrayList手写的循环比增强型for循环更快，其他的集合没有这种情况。因此默认情况下使用增强型for循环，而遍历ArrayList使用传统的循环方式。

* 使用系统封装好的API

* 避免在内部调用Getters/Setters方法

**在Android中，字段搜寻比方法调用效率高得多，**我们直接访问某个字段可能要比通过getters方法来去访问这个字段快3到7倍。但是编写代码还是要按照面向对象思维的，我们应该在能优化的地方进行优化，比如避免在内部调用getters/setters方法。

##### 布局优化技巧

* 重用布局文件

标签可以允许在一个布局当中引入另一个布局，那么比如说我们程序的所有界面都有一个公共的部分，这个时候最好的做法就是将这个公共的部分提取到一个独立的布局中，然后每个界面的布局文件当中来引用这个公共的布局。

Tips:如果我们要在标签中覆写layout属性，必须要将layout_width和layout_height这两个属性也进行覆写，否则覆写xiaoguo将不会生效。

标签是作为标签的一种辅助扩展来使用的，它的主要作用是为了防止在引用布局文件时引用文件时产生多余的布局嵌套。布局嵌套越多，解析起来就越耗时，性能就越差。因此编写布局文件时应该让嵌套的层数越少越好。
举例：比如在LinearLayout里边使用一个布局。里边又有一个LinearLayout，那么其实就存在了多余的布局嵌套，使用merge可以解决这个问题。

* 仅在需要时才加载布局

某个布局当中的元素不是一起显示出来的，普通情况下只显示部分常用的元素，而那些不常用的元素只有在用户进行特定操作时才会显示出来。
举例：填信息时不是需要全部填的，有一个添加更多字段的选项，当用户需要添加其他信息的时候，才将另外的元素显示到界面上。用VISIBLE性能表现一般，可以用ViewStub。**ViewStub也是View的一种，但是没有大小，没有绘制功能，也不参与布局，资源消耗非常低，可以认为完全不影响性能。**

```
<ViewStub   
        android:id="@+id/view_stub"  
        android:layout="@layout/profile_extra"  
        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        />  

public void onMoreClick() {  
    ViewStub viewStub = (ViewStub) findViewById(R.id.view_stub);  
    if (viewStub != null) {  
        View inflatedView = viewStub.inflate();  
        editExtra1 = (EditText) inflatedView.findViewById(R.id.edit_extra1);  
        editExtra2 = (EditText) inflatedView.findViewById(R.id.edit_extra2);
        editExtra3 = (EditText) inflatedView.findViewById(R.id.edit_extra3);
    }
}

```

tips： **ViewStub所加载的布局是不可以使用标签的**，因此这有可能导致加载出来的布局存在着多余的嵌套结构。

### RxJava

#### RxJava 到底是什么

一个词：异步

> a library for composing asynchronous and event-based programs using observable sequences for the Java VM

> 随着程序逻辑变得越来越复杂，RxJava依然能够保持简洁。

#### 扩展的观察者模式

#### RxJava四个基本概念:

* Observable (可观察者，即被观察者)

* Observer (观察者)

* subscribe (订阅)

* 事件

#### Scheduler - 线程控制器

* Schedulers.immediate(): 直接在当前线程运行，相当于不指定线程。这是默认的 Scheduler。

* Schedulers.newThread(): 总是启用新线程，并在新线程执行操作。

* Schedulers.io(): I/O 操作（读写文件、读写数据库、网络信息交互等）所使用的 Scheduler。行为模式和 newThread() 差不多，区别在于 io() 的内部实现是是用一个无数量上限的线程池，可以重用空闲的线程，因此多数情况下 io() 比 newThread() 更有效率。不要把计算工作放在 io() 中，可以避免创建不必要的线程。

Schedulers.computation(): 计算所使用的 Scheduler。这个计算指的是 CPU 密集型计算，即不会被 I/O 等操作限制性能的操作，例如图形的计算。这个 Scheduler 使用的固定的线程池，大小为 CPU 核数。不要把 I/O 操作放在 computation() 中，否则 I/O 操作的等待时间会浪费 CPU。
另外， 

Android 还有一个专用的 AndroidSchedulers.mainThread()，它指定的操作将在 Android 主线程运行。

subscribeOn(): 指定 subscribe() 所发生的线程，即 Observable.OnSubscribe 被激活时所处的线程。或者叫做事件产生的线程。 * observeOn(): 指定 Subscriber 所运行在的线程。或者叫做事件消费的线程。










  
		
## 计算机网络

### HTTP协议

* HTTP构建于TCP/IP协议之上，默认端口号是80

* HTTP是无连接无状态的







## 设计模式

### 常见的面向对象设计原则

#### **单一职责原则** SRP 一个类应该仅有一个引起它变化的原因。

#### **开放关闭原则** OCP 一个类应该对外扩展开放，对修改关闭。

#### **里氏替换原则** LSP 子类型能够替换掉它们的父类型。

#### **依赖倒置原则** DIP 要依赖于抽象，不要依赖于具体类，要做到依赖倒置，应该做到：

* 高层模块不应该依赖底层模块，二者都应该依赖于抽象。

* 抽象不应该依赖于具体实现，具体实现应该依赖于抽象。

#### **接口隔离原则** ISP 不应该强迫客户依赖于他们不用的方法。

#### **最少知识原则** LKP 只和你的朋友谈话。

#### 其他原则

* 面向接口编程

* 优先使用组合，而非继承

* 一个类需要的数据应该隐藏在类的内部

* 类之间应该零耦合，或者只有传导耦合，换句话说，类之间要么没关系，要么只使用另一个类的接口提供的操作

* 在水平方向上尽可能统一地分布系统功能

### 单例模式

#### 懒汉式，线程不安全

```
public class Singleton {
    private static Singleton instance;
    private Singleton (){}
    public static Singleton getInstance() {
     if (instance == null) {
         instance = new Singleton();
     }
     return instance;
    }
}

```

#### 懒汉式，线程安全

* 不高效

* 每次调用都进行同步操作

```
public static synchronized Singleton getInstance() {
    if (instance == null) {
        instance = new Singleton();
    }
    return instance;
}

```

#### 双重检验锁

* instance = new Singleton()这句，这并非是一个原子操作

```
public static Singleton getSingleton() {
    if (instance == null) {                         //Single Checked
        synchronized (Singleton.class) {
            if (instance == null) {                 //Double Checked
                instance = new Singleton();
            }
        }
    }
    return instance ;
}

```

#### 解决指令重排序问题的双重检验锁

```
private volatile static Singleton instance;
private Singleton (){}

public static Singleton getSingleton() {
    if (instance == null) {                         //Single Checked
        synchronized (Singleton.class) {
            if (instance == null) {                 //Double Checked
                instance = new Singleton();
            }
        }
    }
    return instance ;
}

```
#### 饿汉式 static final field

```
// 饿汉式 static final field,不能懒加载
public class TaoSingleton {
    private static TaoSingleton sInstance = new TaoSingleton();
    private TaoSingleton() {

    }

    public static TaoSingleton getInstance() {
        return sInstance;
    }
}

```

#### 静态内部类

```
public class TaoSingleton {
    private static class SingletonHolder {
        private static final TaoSingleton INSTANCE = new TaoSingleton();
    }

    private TaoSingleton() {

    }

    public static TaoSingleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}

```

#### 枚举

```
public enum TaoSingleton {
    INSTANCE;
}

```

### Builder模式

#### 定义

将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

#### 模式的使用场景

* 相同的方法，不同的执行顺序，产生不同的事件结果时；

* 多个部件或零件，都可以装配到一个对象中，但是产生的运行结果又不相同时；

* 产品类非常复杂，或者产品类中的调用顺序不同产生了不同的效能，这个时候使用建造者模式非常合适；

#### 简单实例

```
public class Student {

    private int id;
    private String name;
    private String passwd;
    private String sex;
    private String address;

    // 构造器尽量缩小范围
    private Student() {
    }

    // 构造器尽量缩小范围
    private Student(Student origin) {
        // 拷贝一份
         this.id = origin.id;
        this.name = origin.name;
        this.passwd = origin.passwd;
        this.sex = origin.sex;
        this.address = origin.address;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getPasswd() {
        return passwd;
    }

    public String getSex() {
        return sex;
    }

    public String getAddress() {
        return address;
    }

    /**
     * Student的创建完全依靠Student.Builder，使用一种方法链的方式来创建
     *
     */
    public static class Builder {

        private Student target;

        public Builder() {
            target = new Student();
        }

        public Builder address(int id) {
            target.id = id;
            return this;
        }

        public Builder name(String name) {
            target.name = name;
            return this;
        }

        public Builder password(String passwd) {
            target.passwd = passwd;
            return this;
        }

        public Builder sex(String sex) {
            target.sex = sex;
            return this;
        }

        public Builder address(String address) {
            target.address = address;
            return this;
        }

        public Student build() {
            return new Student(target);
        }
        
    }

}

```

Student并不是直接new出来的，对其构造器进行了处理使其可访问范围尽可能的小，只让它通过Student.Builder来构建自己，在Student.Builder中提供了一种类set的方法链的方式来设置值，然后在最后的build()方法的时候会返回一个Student对象，现在要创建一个Student对象，代码如下：

```
Student s=new Student.Builder().name("CC").password("qwerty").sex("男").address("银河系第二旋臂").build();
```

#### Android源码中的模式实现

在Android源码中，我们最常用到的Builder模式就是AlertDialog.Builder， 使用该Builder来构建复杂的AlertDialog对象。简单示例如下 :

//显示基本的AlertDialog  
```
private void showDialog(Context context) {  
    AlertDialog.Builder builder = new AlertDialog.Builder(context);  
    builder.setIcon(R.drawable.icon);  
    builder.setTitle("Title");  
    builder.setMessage("Message");  
    builder.setPositiveButton("Button1",  
            new DialogInterface.OnClickListener() {  
                public void onClick(DialogInterface dialog, int whichButton) {  
                    setTitle("点击了对话框上的Button1");  
                }  
            });  
    builder.setNeutralButton("Button2",  
            new DialogInterface.OnClickListener() {  
                public void onClick(DialogInterface dialog, int whichButton) {  
                    setTitle("点击了对话框上的Button2");  
                }  
            });  
    builder.setNegativeButton("Button3",  
            new DialogInterface.OnClickListener() {  
                public void onClick(DialogInterface dialog, int whichButton) {  
                    setTitle("点击了对话框上的Button3");  
                }  
            });  
    builder.create().show();  // 构建AlertDialog， 并且显示
} 

```


#### 优点

* 良好的封装性， 使用建造者模式可以使客户端不必知道产品内部组成的细节；

* 建造者独立，容易扩展；

* 在对象创建过程中会使用到系统中的一些其它对象，这些对象在产品对象的创建过程中不易得到。

#### 缺点

* 会产生多余的Builder对象以及Director对象，消耗内存；

* 对象的构建过程暴露。

### 原型模式

#### 定义

用原型实例指定创建对象的种类，并通过拷贝这些原型创建新的对象。

#### 模式的使用场景

* 类初始化需要消化非常多的资源，这个资源包括数据、硬件资源等，通过原型拷贝避免这些消耗；

* 通过 new 产生一个对象需要非常繁琐的数据准备或访问权限，则可以使用原型模式；

* 一个对象需要提供给其他对象访问，而且各个调用者可能都需要修改其值时，可以考虑使用原型模式拷贝多个对象供调用者使用，即保护性拷贝。

#### 简单实例

```
class Prototype implements Cloneable {  
    public Prototype clone(){  
        Prototype prototype = null;  
        try{  
            prototype = (Prototype)super.clone();  
        }catch(CloneNotSupportedException e){  
            e.printStackTrace();  
        }  
        return prototype;   
    }  
}  
  
class ConcretePrototype extends Prototype{  
    public void show(){  
        System.out.println("原型模式实现类");  
    }  
}  
  
public class Client {  
    public static void main(String[] args){  
        ConcretePrototype cp = new ConcretePrototype();  
        for(int i=0; i< 10; i++){  
            ConcretePrototype clonecp = (ConcretePrototype)cp.clone();  
            clonecp.show();  
        }  
    }  
}  

```

#### Android源码中的模式实现

Intent中使用了原型模式

```
Uri uri = Uri.parse("smsto:0800000123");    
Intent shareIntent = new Intent(Intent.ACTION_SENDTO, uri);    
shareIntent.putExtra("sms_body", "The SMS text");    

Intent intent = (Intent)shareIntent.clone() ;
startActivity(intent);
```

#### 优缺点

##### 优点 

原型模式是在内存二进制流的拷贝，要比直接 new 一个对象性能好很多，特别是要在一个循环体内产生大量的对象时，原型模式可以更好地体现其优点。

##### 缺点

这既是它的优点也是缺点，直接在内存中拷贝，构造函数是不会执行的，在实际开发当中应该注意这个潜在的问题。优点就是减少了约束，缺点也是减少了约束，需要大家在实际应用时考虑。

#### Tips

* 使用原型模式复制对象不会调用类的构造方法, clone方法直接无视构造方法的权限，所以，单例模式与原型模式是冲突的

* 深拷贝与浅拷贝: Object类的clone方法只会拷贝对象中的基本的数据类型，对于数组、容器对象、引用对象等都不会拷贝，这就是浅拷贝。如果要实现深拷贝，必须将原型模式中的数组、容器对象、引用对象等另行拷贝


### 工厂模式

#### 简单工厂模式(工厂方法模式的一种特例)

建立一个工厂（一个函数或一个类方法）来制造新的对象

```

// Product Class
public class BMW320 {  
    public BMW320(){  
        System.out.println("制造-->BMW320");  
    }  
}  
  
public class BMW523 {  
    public BMW523(){  
        System.out.println("制造-->BMW523");  
    }  
}  

// Factory Class
public class Factory {  
    public BMW createBMW(int type) {  
        switch (type) {  
          
        case 320:  
            return new BMW320();  
  
        case 523:  
            return new BMW523();  
  
        default:  
            break;  
        }  
        return null;  
    }  
}  

// Customer Class
public class Customer {  
    public static void main(String[] args) {  
        Factory factory = new Factory();  
        BMW bmw320 = factory.createBMW(320);  
        BMW bmw523 = factory.createBMW(523);  
    }  
}
  
```

#### 工厂方法模式

工厂方法模式去掉了简单工厂模式中工厂方法的静态属性，使得它可以被子类继承。这样在简单工厂模式里集中在工厂方法上的压力可以由工厂方法模式里不同的工厂子类来分担。 

```

// Product class
abstract class BMW {  
    public BMW(){  
          
    }  
}  
public class BMW320 extends BMW {  
    public BMW320() {  
        System.out.println("制造-->BMW320");  
    }  
}  
public class BMW523 extends BMW{  
    public BMW523(){  
        System.out.println("制造-->BMW523");  
    }  
}

// Factory class
interface FactoryBMW {  
    BMW createBMW();  
}  
  
public class FactoryBMW320 implements FactoryBMW{  
  
    @Override  
    public BMW320 createBMW() {  
  
        return new BMW320();  
    }  
  
}  
public class FactoryBMW523 implements FactoryBMW {  
    @Override  
    public BMW523 createBMW() {  
  
        return new BMW523();  
    }  
}  


// Customer class
public class Customer {  
    public static void main(String[] args) {  
        FactoryBMW320 factoryBMW320 = new FactoryBMW320();  
        BMW320 bmw320 = factoryBMW320.createBMW();  
  
        FactoryBMW523 factoryBMW523 = new FactoryBMW523();  
        BMW523 bmw523 = factoryBMW523.createBMW();  
    }  
} 


```

#### 抽象工厂模式

抽象工厂模式是工厂方法模式的升级版本，他用来创建一组相关或者相互依赖的对象

```
// Product class

//发动机以及型号    
public interface Engine {    
  
}    
public class EngineA extends Engine{    
    public EngineA(){    
        System.out.println("制造-->EngineA");    
    }    
}    
public class EngineBextends Engine{    
    public EngineB(){    
        System.out.println("制造-->EngineB");    
    }    
}    
  
//空调以及型号    
public interface Aircondition {    
  
}    
public class AirconditionA extends Aircondition{    
    public AirconditionA(){    
        System.out.println("制造-->AirconditionA");    
    }    
}    
public class AirconditionB extends Aircondition{    
    public AirconditionB(){    
        System.out.println("制造-->AirconditionB");    
    }    
}   


```

// Factory class

//创建工厂的接口    
public interface AbstractFactory {    
    //制造发动机  
    public Engine createEngine();  
    //制造空调   
    public Aircondition createAircondition();   
}    
  
  
//为宝马320系列生产配件    
public class FactoryBMW320 implements AbstractFactory{    
        
    @Override    
    public Engine createEngine() {      
        return new EngineA();    
    }    
    @Override    
    public Aircondition createAircondition() {    
        return new AirconditionA();    
    }    
}    
//宝马523系列  
public class FactoryBMW523 implements AbstractFactory {    
    
     @Override    
    public Engine createEngine() {      
        return new EngineB();    
    }    
    @Override    
    public Aircondition createAircondition() {    
        return new AirconditionB();    
    }    
  
  
}   

// Customer class

public class Customer {    
    public static void main(String[] args){    
        //生产宝马320系列配件  
        FactoryBMW320 factoryBMW320 = new FactoryBMW320();    
        factoryBMW320.createEngine();  
        factoryBMW320.createAircondition();  
            
        //生产宝马523系列配件    
        FactoryBMW523 factoryBMW523 = new FactoryBMW523();    
        factoryBMW320.createEngine();  
        factoryBMW320.createAircondition();  
    }    
}  

```









  
		




