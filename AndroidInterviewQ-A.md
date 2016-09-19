# Android Interview Questions

------

### Describe architecture of android framework.
Android operating system comprise of different software components arranges in stack. Different components of android operating system are: 

* Linux kernel

Bottom layer of android operating system is Linux kernel.
Android is built on top of Linux 2.6 Kernel and few architectural changes made by Google.
Linux Kernel provides the basic system functionality such as process management, memory management and device management Linux kernel also provides array of device drivers which make our task easier while interfacing the android with peripheral devices.

* Libraries

On the top of Linux Kernel another layer called libraries is present.
It provides the different libraries useful for well functioning of android operating system.
Libraries are java libraries build specific for android operating system.
Some of the libraries are listed below: 

**SQLite**	It is used to access data published by content providers and includes SQLite database management classes

**SSL**	It is used to provide internet security

**OpenGL**	It is used to provide Java interface to the OpenGL/ES 3D graphics rendering API.
Media framework	It is used to provides different media codecs which allow the recording and playback of different media formats

**WebKit**	It is the browser engine used to display internet content or HTML content

* Android Runtime

It is third component of the android architecture and placed in second layer from bottom
It provides most important part of android called Dalvik Virtual Machine.
Dalvik Virtual Machine is similar to JVM but only difference is that it is designed and optimized for Android.  Dalvik Virtual machine uses core functions of Linux such as memory management and multithreading and enables each android app to run its own process.

* Application framework

It is 2nd topmost component in android operating system stack Android applications directly interacts with application framework.  Application framework manages the basic functions of android device such as resource management, voice call management etc. Important blocks of Application framework are :

**Activity Manager** is used to manage complete activity life cycle of applications

**Content Providers** is used to manage the data sharing between two applications

**Telephony Manager** is used to manages all voice calls
 
**Location Manager** is used to manage the Locations obtained using GPS or cell tower

**Resource Manager** is used to manage the different types of resources used in android app

* Applications

Applications created by third party users or developer will be installed on application layer.

------

### Why DVM why not JVM?
 
Conceptually, there is little difference from an application level between a DVM and a JVM. Architecturally, there is a major difference between the register-based DVM and the stack-based JVM.

Both use a VM code model. DVM is Register based which is designed to run on low memory, uses its own byte code and runs .dex file. JVM is Stack based which uses java byte code and runs .class file having JIT.  Java source code is compiled by the Java compiler into .class files. Then the dx (dexer) tool, part of the Android SDK processes the .class files into a file format called DEX that contains Dalvik bytecode. The dx tool eliminate all the redundant information that is present in the classes. In DEX all the classes of the application are packed into one file. DVM has been designed so that a device can run multiple instances of the VM efficiently.  stack-based machines must use instructions to load data on the stack and manipulate that data, and, thus, require more instructions than register machines to implement the same high level code, but the instructions in a register machine must encode the source and destination registers and, therefore, tend to be larger.

Google developed Android and chose DVM for several reasons. 

* First, there were licensing issues with most JVMs. 

* Next, the DVM should be more efficient in terms of memory usage and performance on a register-based machine. DVM is also supposed to be more efficient when running multiple instances of the DVM. Applications are given their own instance. Hence, multiple active applications require multiple DVM instances. Like most Java implementations, the DVM has an automatic garbage collector.

------

### Which layer does DVM sits?
Every android application runs in DVM. To run an application it requires memory, process, threads and other resources. But all these resources are under control of kernel. So DVM has to interact with driver layer for memory and thread management, it sits just above driver layer (that is.. it sits in **library layer**)

------

### Explain life cycle of activity?
* onCreate():

Called when the activity is first created. This is where you should do all of your normal static set up: create views, bind data to lists, etc. This method also provides you with a Bundle containing the activity's previously frozen state, if there was one. Always followed by onStart().

* onRestart():

Called after your activity has been stopped, prior to it being started again. Always followed by onStart()

* onStart():

Called when the activity is becoming visible to the user. Followed by onResume() if the activity comes to the foreground, or onStop() if it becomes hidden.

* onResume():

Called when the activity will start interacting with the user. At this point your activity is at the top of the activity stack, with user input going to it. Always followed by onPause().

* onPause ():

Called as part of the activity lifecycle when an activity is going into the background, but has not (yet) been killed. The counterpart to onResume(). When activity B is launched in front of activity A, this callback will be invoked on A. B will not be created until A's onPause() returns, so be sure to not do anything lengthy here.

* onStop():

Called when you are no longer visible to the user. You will next receive either onRestart(), onDestroy(), or nothing, depending on later user activity.

Note that this method may never be called, in low memory situations where the system does not have enough memory to keep your activity's process running after its onPause() method is called.

* onDestroy():

The final call you receive before your activity is destroyed. This can happen either because the activity is finishing (someone called finish() on it, or because the system is temporarily destroying this instance of the activity to save space. You can distinguish between these two scenarios with the isFinishing() method.

**When the Activity first time loads the events are called as below:**

onCreate()

onStart()
 
onResume()

**When you click on Phone button the Activity goes to the background and the below events are called:**

onPause()

onStop()

**Exit the phone dialer and the below events will be called:**

onRestart()

onStart()

onResume()

**When you click the back button OR try to finish() the activity the events are called as below:**

onPause()

onStop()

onDestroy()

**Activity States**

The Android OS uses a priority queue to assist in managing activities running on the device. Based on the state a particular Android activity is in, it will be assigned a certain priority within the OS. This priority system helps Android identify activities that are no longer in use, allowing the OS to reclaim memory and resources. The following diagram illustrates the states an activity can go through, during its lifetime:

These states can be broken into three main groups as follows:

* Active or Running - Activities are considered active or running if they are in the foreground, also known as the top of the activity stack. This is considered the highest priority activity in the Android Activity stack, and as such will only be killed by the OS in extreme situations, such as if the activity tries to use more memory than is available on the device as this could cause the UI to become unresponsive.

* Paused - When the device goes to sleep, or an activity is still visible but partially hidden by a new, non-full-sized or transparent activity, the activity is considered paused. Paused activities are still alive, that is, they maintain all state and member information, and remain attached to the window manager. This is considered to be the second highest priority activity in the Android Activity stack and, as such, will only be killed by the OS if killing this activity will satisfy the resource requirements needed to keep the Active/Running Activity stable and responsive.

* Stopped - Activities that are completely obscured by another activity are considered stopped or in the background. Stopped activities still try to retain their state and member information for as long as possible, but stopped activities are considered to be the lowest priority of the three states and, as such, the OS will kill activities in this state first to satisfy the resource requirements of higher priority activities.

Sample activity to understand the life cycle
```java
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
public class MainActivity extends Activity {
    String tag = "LifeCycleEvents";
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       setContentView(R.layout.main);
       Log.d(tag, "In the onCreate() event");
    }
    public void onStart()
    {
       super.onStart();
       Log.d(tag, "In the onStart() event");
    }
    public void onRestart()
    {
       super.onRestart();
       Log.d(tag, "In the onRestart() event");
    }
    public void onResume()
    {
       super.onResume();
       Log.d(tag, "In the onResume() event");
    }
    public void onPause()
    {
       super.onPause();
       Log.d(tag, "In the onPause() event");
    }
    public void onStop()
    {
       super.onStop();
       Log.d(tag, "In the onStop() event");
    }
    public void onDestroy()
    {
       super.onDestroy();
       Log.d(tag, "In the onDestroy() event");
    }
}
```

------

### If I have a broadcast receiver that updates my UI frequently, then where should I register that broadcast receiver in my activity life cycle functions?
**onStart()** function will be called just before your activity will be made visible to the user. So any thing that affects UI has to be registered in onStart() function.

------

### Can I save all my database tables updations in onStop() of activity? If not explain why and also explain where should I save db tables?
No, because onStop() may not be called in some situations.
In case of low memory or configuration changes, there is a chance that android may force-kill your application before reaching onStop(). onPause() is the only function that will be called without fail before killing the application. so save all persistent data like DB tables in onPause() only.
Note : We can't save all database tables in onSaveInstanceState, because that function will not be called if user presses back button.

------

### What will happen if I remove super.oncreate() from oncreate() function of activity? What is the purpose of super.oncreate() ?
Every Activity you make is started through a sequence of method calls. onCreate() is the first of these calls.  Each and every one of your Activities extends android.app.Activity either directly or by subclassing another subclass of Activity.  In Java, when you inherit from a class, you can override its methods to run your own code in them. A very common example of this is the overriding of the toString() method when extending java.lang.Object.  When we override a method, we have the option of completely replacing the method in our class, or of extending the existing parent class' method. By calling super.onCreate(savedInstanceState), you tell the Dalvik VM to run your code in addition to the existing code in the onCreate() of the parent class. If you leave out this line, then only your code is run. The existing code is ignored completely.

However, you must include this super call in your method, because if you don't then the onCreate() code in Activity is never run, and your app will run into all sorts of problem like having no Context assigned to the Activity (though you'll hit a SuperNotCalledException before you have a chance to figure out that you have no context).

In short, Android's own classes can be incredibly complex. The code in the framework classes handles stuff like UI drawing, house cleaning and maintaining the Activity and application lifecycles. super calls allow developers to run this complex code behind the scenes, while still providing a good level of abstraction for our own apps.

------

### Show me how does intent-filter of main activity looks like? What is the action and what is the category?

```xml
<intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
```            
**action.MAIN**  says this activity is the main activity (starting point) for this application. 

**category.LAUNCHER**  says this activity should appear in the home screen's launcher.

------

### What is the importance of version code and version name attributes in manifest file?
It tells your applications version number and name. It will be used when you want to update your app in google play store.  Version no and name will be useful when you upload some application to play store and wanted to update it. When you are upgrading your application then you can increment the version number so that users of your application will get notification on their phones about the latest updates available.

------

### Can one application have more than one manifest file?
You usually would require more than one manifest file when you have more than one build type or product flavors.  Gradle merges all manifest files into a single manifest file. The merging priority is

* Product flavors and build types specific manifest files.

* Main manifest file for the application.

* Library manifest files.

Imagine the case where you have a free version and a paid version of your app. The root manifest file will have the segments shared by both the versions. The paid version would have the part specific for it, like checking license.

------

### Can I create activity without xml file?
Yes, it is possible. You can create resources with code also, with out having your xml files.
For eg: below code will work without setting xml file as the layout for your activity.
```java
public class Example extends Activity {
    @Override
    public void onCreate(Bundle savedInstanceState) 
    {
        super.onCreate(savedInstanceState);

        LinearLayout layout = new LinearLayout(this);
        // Define the LinearLayout's characteristics
        layout.setGravity(Gravity.CENTER);
        layout.setOrientation(LinearLayout.VERTICAL);

        // Set generic layout parameters
        LayoutParams params = new LayoutParams(LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT);

        Button button = new Button(this);
        button.setText("Button!");
        layout.addView(button, params); // Modify this

        myView custom = new myView(this);
        layout.addView(custom, params); // Of course, this too

        setContentView(layout);
    }
}
```
------

### Give me two examples of configuration changes in phone?
Configuration changes include: rotating the phone, having virtual keypad on, and changing language settings in settings.

------

### What is the mandatory life cycle function that will get called in case of configuration changes?
In case of low memory or configuration changes, android will call both **onPause()** and **onSaveInstanceState()** with out fail. Exception: there is one exceptional case, that is if programmer is handling configuration changes, then in that case it will not call those functions.

------

### If some heavy images are downloaded from internet in onCreate() of an Activity, then where to save them in case of configuration changes (rotating the phone)?
onSaveInstanceState() function has to be used for saving small objects (transient states). If we want to save large objects use **onRetainNonConfigurationInstance()** function. Or else we can make that image as static, so that the image will be loaded only once.

onSaveInstanceState() function has to be used for saving small objects, not for heavy objects.

If you want to save heavy images on phone rotation, then use any of below techniques:

1. If you want to save large objects use onRetainNonConfigurationInstance() function.
2. Or else we can make that image as static, so that the image will be loaded only once. Meaning: On downloading an image from network, make it pointed by a static variable. If user rotates the phone, since android kills that activity and recreates it, just put a if condition check if that static variable is not null, then only download again. As you know static variables will be created only once, it will not download again.

------

### What is the difference between this context and getApplicationContext() ? which one to use when?
This points to current context, application context points to entire process. if your context is of entire life time of process then use app context, else this.

This pointer always points to current class object, app context will point to entire process. there is only one app context. if you want to use some control whose life time is through out your application life time then go for app context, else use this pointer (activity context).

**Application context** is associated with the application and will always be same throughout the life of application -- it does not change. So if you are using Toast, you can use application context or even activity context (both) because toast can be displayed from anywhere with in your application and is not attached to a specific window. But there are many exceptions, one exception is when you need to use or pass the activity context.

**Activity context** is associated with to the activity and can be destroyed if the activity is destroyed -- there may be multiple activities (more than likely) with a single application. And sometimes you absolutely need the activity context handle. For example, should you launch a new activity, you need to use activity context in its Intent so that the new launching activity is connected to the current activity in terms of activity stack. However, you may use application's context too to launch a new activity but then you need to set flag Intent.FLAG_ACTIVITY_NEW_TASK in intent to treat it as a new task.

Let's consider some cases:

MainActivity.this refers to the MainActivity context which extends Activity class but the base class (activity) also extends Context class, so it can be used to offer activity context.

getBaseContext() offers activity context.

getApplication() offers application context.

getApplicationContext() also offers application context.

------

### Every application will have by default one thread? True or false?
True, always any given application will have default one main thread.  Default android will allocate one main thread called as (UI thread) to every process or application.

------

### What is ANR (application not responding)? What is the reason for this problem and what is the solution for that problem?
ANR - will occur if we are doing any other heavy functionality along with UI in single Main Thread.If two heavy functionalities happen in single thread, it will delay response to user actions, which may irritate user, and hence stop your process.
Solution - Run only UI components in Main Thread.

------

### Main thread will have a looper , true or false?
Only handler threads will have loopers, but we can prepare normal threads looper also.

Main Thread is a handler thread, so it will have looper enabled. Normal threads looper will be in disabled mode, where as handler threads will have their loopers enabled. but if we want we can prepare loopers for normal threads also.

------

### By default a given process will have how many threads? Who will create those threads?
One main thread created by android system,Since every process requires a thread to run with CPU, by default android system will create one main thread for every application

------

### What will happen if I remove oncreate() & onstart() from activity? Will it run?
Nothing will happen it will run perfectly, It is a standard, that programmer has to implement corresponding life cycles methods of activity based on the functionality supported. But one can skip those functions if programmer wish to.

------

### Can I have an activity without UI?
The answer is yes, it's possible. Activities don't have to have a UI. It's mentioned in the documentation.
To e.g. display a Toast from an Activity without layout you would define the activity in your manifest like so:
```xml
<activity
    android:name=".MainActivity"
    android:theme="@android:style/Theme.NoDisplay">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```
The code would look like this:
```java
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Toast.makeText(this, "I'm alive", Toast.LENGTH_LONG).show();
        finish();
    }
}
```

------

### What is the difference between intent, sticky intent, pending intent?
* intent is a message passing mechanism between components of android, except for Content Provider. You can use intent to start any component.

* Sticky Intent Sticks with android, for future broadcast listeners. For example if BATTERY_LOW event occurs then that intent will be stick with android so that if any future user requested for BATTER_LOW, it will be fired; 

* Pending Intent  If you want some one to perform any Intent operation at future point of time on behalf of you, then we will use Pending Intent. Eg: Booking a ticket at late night when your application is not running. In this scenario we will create a pending intent to start a service which can book tickets at late night and hand it over to Alarm Manager to fire it at that time.

------

### What is the difference between implicit intent and explicit intent, give one example?
* Implicit Intents do not directly specify the Android components which should be called , it only specifies action to be performed.An Uri can be used with the implicit intent to specify data type.  For example

```java
Intent intent = new Intent(ACTION_VIEW,Uri.parse("http://www.google.com");
```
this will cause web browser to open a webpage .Android system searches for all components which are registered for the specific action and the data type.If many components are found then the user can select which component to use..

* Explicit intents are used in the application itself wherein one activity can switch to other activty.
For example:
```java
Intent intent = new Intent(this,Target.class);
```
this causes switching of activity from current context to the target activity. Explicit Intents can also be used to pass data to other activity using putExtra method and retrieved by target activity by getIntent().getExtras() methods.

------

### How many components are there in an intent?
Intent can have:
* action 
* data and its type
* category
* extras
* Target Component name 
* some extra flags

------

### Can I give more than one action in a given intent?
No. Intent can have 0 or maximum one action.
Intent can have maximum one action, but it is not mandatory. some times it can have 0 actions also (in case of explicit intent). So an intent can have 0-1 actions.

------

### Can I give more than one category in a given intent?
Yes. You can have 0 or n number of categories in intent
Unlike actions, an intent can choose to have 0 or n number of categories.

------

### Create an intent to start calling a number “12345” ?
```java
Intent in = new Intent();  
in.setAction(Intent.ACTION_CALL); 
in.setData(Uri.parse("tel:12345")); 
startActivity(in);
```

------

### What is the importance of putextras in intent? How is it different from setData() ? any way both are passing data only , so in that case what is the difference?
* setData() - is to pass data on which to take action. 
* putExtra() - is to send extra information about this intent. 

Eg: if one is starting an activity to perform ACTION_CALL, then he has to set number in setData(). this function will contain on which data target component has to take action. if one want to pass extra details also, then only use putExtra().

------

### What is the difference between Thread, AsyncTask and Service?
* Service is like an Activity but has no interface. Probably if you want to fetch the weather for example you won't create a blank activity for it, for this you will use a Service.

* Thread is a Thread, probably you already know it from other part. You need to know that you cannot update UI from a Thread. You need to use a Handler for this, but read further.

* AsyncTask is an intelligent Thread that is advised to be used. Intelligent as it can help with it's methods, and there are two methods that run on UI thread, which is good to update UI components.

I am using Services, AsyncTasks frequently. Thread less, or not at all, as I can do almost everything with AsyncTask.

------

### Can I have a service without thread?
Service is a component that performs some operation in the background with out having UI. But it doesn't mean that it will have separate thread to do it. By default if programmer doesn't provide any thread for the service, then it runs in Main UI thread. Since it is not good practice to run Activity and Services in single thread, it is suggestible to have separate thread for the service, except in few cases like where a given application is not having any activity in it.

------

### If I want to touch ui from another thread can I touch directly? What will happen if I do so?
On the Android platform, applications operate, by default, on one thread.  This thread is called the UI thread.  It is often called that because this single thread displays the user interface and listens for events that occur when the user interacts with the app.

Developers quickly learn that if code running on that thread hogs that single thread and prevents user interaction (for more than 5 seconds), it causes Android to throw up the infamous Android Not Responsive (ANR) error.

So how do you prevent ANR?  Your application must create other threads and put long running work on non-UI threads.  There are options on how to accomplish the creation of alternate threads.  You can create and start your own java.lang.Thread.  You can create and start an AsyncTask – Android’s own thread simplification mechanism.  The non-UI thread then handles long running processing – like downloading a file – while the UI thread sticks to displaying the UI and reacting to user events.  Life seems good again.

However, there is a problem in paradise.  Unfortunately, the user interface (UI) cannot be updated by non-UI threads.  For example, after successfully downloading a file, a separate (non-UI) thread can’t show an AlertDialog, update a TextView widget, otherwise make a UI change to indicate the file has been successfully downloaded.  If you attempt to update the UI from a non-UI thread, the application will compile, but you get a **CalledFromWrongThreadException** thrown from the point your non-UI thread attempts to make the UI change.  As the exception message will inform you, **“Only the original thread that created a view hierarchy can touch its views.”**

This seems like a catch-22 situation.  If you put long running work on the UI thread, you can get ANR errors.  If you have multiple threads and put long running work on the non-UI threads, those non-UI threads can’t inform the user of what is happening.

Well, as it turns out, there are several ways to have non-UI threads request updates to the UI through the UI thread.  In fact, in the next posts, I plan to show you five ways to have the non-UI thread send UI update requests to be executed on the UI thread.

* Use runOnUiThread( ) method call
* Use post( ) method call
* Use the Handler framework
* Use a Broadcasts and BroadcastReceiver (optionally with LocalBroadcastManager)
* Use an AsyncTask’s onProgressUpdate( ) method

As with all options, there are considerations when making a selection from this list.  Much depends on your design decisions about how/where the non-UI thread is created and launched.

------

### How to achieve inter thread communication?
That depends on the nature of the communication.

Is it duplex (ie A talks to B and B talks to A)?

Is it communication of data or communication of completion?
and so on.

The simplest and most advisable form of inter-thread communication is simply to wait for the completion of other threads. That's most easily done by using Future:

```java
ExecutorService exec = Executors.newFixedThreadPool(50);
final Future f = exec.submit(task1);
exec.submit(new Runnable() {
    @Override
    public void run() {
        f.get();
        // do stuff
    }
});
```
The second task won't execute until the first completes.

------

### What is the difference between thread and handler thread?
* The same: you can both execute task asynchronously without blocking your current code,

* The difference: Imagine you have a Runnable r = new Runnable{...}

When you use new Thread(r).start(), you actually created a new thread an run task asynchronously.
When you use new Handler().post(r) (or Message), you added the Runnable object to Looper and execute the code later in same thread.
A Thread, generally MainThread or UIThread contains a Looper. When MainThread runs, it will loop the Looper and execute Runnable one by one.

* When Thread is preferred:

When you're doing a heavy work like network communication, or decoding large bitmap files, a new thread is preferred. If a lot of thread is needed, maybe ExecutorService is preferred further.

* When Handler is preferred:

When your want to update UI objects (like TextView's text) from other thread, it is necessary that UI objects could only be updated in UI Thread. Also, when you just want to run some light code later(like delay for 300ms) you can use Handler because it's lighter and faster.

------

### What will happen if I bind a service from a broad cast receiver, is there any problem?
One should not bind a service from Broadcast receiver. The reason is broadcast receivers are light weight components, where it has to finish its functionality within not more than 10 seconds maximum. Else android may forcefully kill your receiver. Binding (establishing connection to) a service may take more than 10 seconds in some worst cases, that's why android won't allow it.

Rules for Broadcast Receivers:

* Broadcast receivers will not have any UI(mostly) and it will have only background logic.

* Broadcast receivers will have the maximum time limit of 10 sec to finish its functionality otherwise it will crash.

* You should not do long running operations or asynchronous operations in the receiver.  Example: 

    a. Preparing SD card. 

    b. Uploading / Downloading files from internet. 

    c. Creating DB files. 

    d. Binding to services

* Do not show dialog to the user in broadcast receiver. (this is asynchronous operation)

* You can use "toast" or "Notifications".

* Don’t write any heavy functionalities.

------

### How to start a service with foreground priority?
Generally services will run in background, which is of 3rd priority. if you feel that the service is critical for user, then you can increase its priority by making it foreground service. Use function startForeground (int id, Notification notification), in onCreate() of your service to make this service as foreground service.
Foreground services will be treated with highest priority, so android will ensure it will not kill these services even in case of low memory situations. Eg: MP3 player service is a foreground service.

------

### If I start an activity with implicit intent, and there is no matching intent-filter then what will happen?
It will throw run time exception - **activityNotFoundException**, and crashes if it is not handled properly.
for startActivity(intent), if there are no matching target components or activities, then it will throw run time exception - ActivityNotFoundException, and will crash the program if this exception is not handled.

------

### If I send a broad cast with implicit intent, and there is no matching intent-filter then what will happen?
Nothing will happen, but it will not launch any receiver.
Unlike startActivity() and startService(); sendBroadcast() will not throw any run time exception. If there are no target components available for this broadcast it will keep quiet. It is because in case of activity and service, action is yet to be performed but in case of broadcastReceiver action is already over and we are just informing it to every one.

------

### Write a broadcast receiver that gets triggered once phone boot is done?
```xml
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"></uses-permission>    
<receiver android:name="BroadcastReceiver">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED"></action>

        </intent-filter>
    </receiver>
```

------

### Can I start a content provider using an intent?
Intents will be used to communicate or start components of android. Only exception is content provider. You can start an activity, service, and a broadcast receiver using intent but not content provider. For content provider we have to use content resolver to communicate.

------

### Can I have extras in my intent-filter?
No, Intent-filter tag doesn't have extra tag at all.
intent-filter tag will not have extra tag. it has only action, category, and data tags. extra tag will not be used while performing tests for intent resolution.

------

### I want to start something in the background in my activity, should I use thread or should I start service? Why?
It always depends on the requirement. if the background functionality is related or tightly coupled with activity, then use thread. but if you want some thing to do in background irrespective of whether activity is alive or not, then go for service-with thread.

------

### If I create a thread in my activity and if I stop my activity what will happen to that thread, will it be alive or dead?
It will be alive, but its priority will be least compared to thread in a service.
Once a thread is created, it will run independently of who has created until the work given to that thread is finished. but in case of low memory scenarios a thread created by activity and which is running in the background will have more susceptibility of being killed by android system. More worse is android can not recreate the thread which was killed because of low memory. but if that thread was in service, then chances of getting killed by android due to low memory may be less than previous situation. Even if android system kills the thread, again it will start the service when memory resources are available and service can re-initiate that killed thread.

------

### How many ways are there to kill a service?
One can stop a service using 
* stopSelf() 
* stopService(). 

There are some situations where android may also kill running services in case if it requires memory and phone is running out of memory(low memory).

------

### If I want my service to allow binding, then what is the function that I need to implement in my service class?  
OnBind(Intent) method need to implement.

------

### What is the use of content provider? Will it support shared preferences?
Content Provider is used to share an application's data with other applications. Right now there is no support for shared preferences with content provider.

------

### Can one application access other app database directly?
Content provider is a mechanism to access apps data by out side world. even if data is stored in internal memory of app, it is possible to access it through this component.

Note: Directly with out using content provider, accessing database is not possible.

------

### What is the extension of your shared preference file? Where is it stored?
shared preference files by default will be stored in internal memory of app. shared preference files extension is **.xml**. It is in the file structure of **data/data/preferences**. But this will not be visible to outside applications and phone users. they can't see what is available in apps internal memory allocated to it.

------

### What is the default shared preference file name of an activity?
```xml
<Activityname>.xml
```
By default if programmer don't give any name to shared preference, then activity name will be taken as shared preference file with .xml extension.

------

### What is the difference between task, process, application, and thread?
* Process every instance of an application being executed is called as process. You execute same application two times, it will create 2 processes.

* Application is generalized term of a process. 

* Task can have one or more applications in it. 
eg: task of sending a message : we will start with message application, lets say i want to attach a photo to my message, then i will open gallery application also. In this case my task has got two applications, messaging and gallery application.

* Thread  theoretically thread is a light weight process, or part of process.Practically thread is dispatch-able unit to CPU, and it is internal part of a process. With out a thread in your program it is difficult to execute your application. That's why by default every process will have at least one thread created by Operating system.

------

### Does android support multi tasking? If yes explain how to start a new task when you are already running one task?
Android supports multitasking at app level also. That means you can create apps with multitasking capability. One way to achieve multitasking is press home button on current task which will move it to background and then you can start new task from launcher.Another way is use FLAG_NEW_TASK in the intent, when you are starting a new activity.
Note: User can press and hold home button to see all the starting activities of recent tasks he has visited. From there also user can switch to other tasks by selecting any of those activities.

------

### What is the mechanism used by android for Inter-process-communication? How binder is different from serialization? How serialization differ from parcel?
IPC means Inter process communication : Where two applications or processes will communicate with each other by passing some data between them.
Since android is meant for embedded and small devices, we should not use serialization for IPC, rather we can use BINDERs which internally uses parcels. parcel is a sort of light weight serialization by using shared memory concept.

There are many differences between Binder IPC and Serialization IPC:
1. Serialization is very heavy to use in embedded devices, communication will be very slow.
2. Binders uses Parcels to make IPC very fast.
3. Binders internally uses Shared memory concept which uses less memory while sharing data between two processes.

Bottom line : Binders uses less memory, and quite fast as it uses parcels. Serialization is very heavy , takes time to send and receive data, and also it takes more memory compared to binders.

Note : To pass data between activities, services, and receivers use only Bundles. Don't go for either serialization or binders.  Binders are specifically used only for binder services where 2 processes will communicate.

------

### If I have one application with activity, service, and contentprovider.  Then when I run this program how many process, threads will be created? Is it possible to run these components in more than one process?
One process, one Thread, Yes it is possible to run in more than one process.

Before loading an application into RAM, a process will be created with a thread by default. Even though in Linux, an application will be given only one process to run all its components, it is quite possible that components of a given application can run in more than one process provided both processes have same Linux user id.

------

### What is looper, message queue, and a Handler? Where do you need these components?
* A Looper is a message handling loop: it reads and processes items from a MessageQueue. The Looper class is usually used in conjunction with a HandlerThread (a subclass of Thread).

* A Handler is a utility class that facilitates interacting with a Looper—mainly by posting messages and Runnable objects to the thread's MessageQueue. When a Handler is created, it is bound to a specific Looper (and associated thread and message queue).

In typical usage, you create and start a HandlerThread, then create a Handler object (or objects) by which other threads can interact with the HandlerThread instance. The Handler must be created while running on the HandlerThread, although once created there is no restriction on what threads can use the Handler's scheduling methods (post(Runnable), etc.)

The main thread (UI thread) in an Android application is set up as a looper thread before your application is created.

------

### Can I send a message from thread-a to thread-b, if thread-b didn’t prepare its looper?
if thread-a wants to send a message to thread-b, then thread-b's looper should be prepared to retrieve message send by others.it is also possible with HandlerThread to have inter-thread communication.

------

### How to avoid synchronization problems in threads?
To avoid synchronized problems with multi threaded programs, use synchronized block or synchronized methods, which ever suits better.

------

### What is the difference between synchronized block(statement) and synchronized methods?
A synchronized method uses the method receiver as a lock (i.e. this for non static methods, and the enclosing class for static methods).  Synchronized blocks uses the expression as a lock.  So the following two methods are equivalent from locking prospective:
```java
synchronized void mymethod() { ... }
void mymethod() {
  synchronized (this) { ... }
}
```
For static methods, the class will be locked:
```java
class MyClass {
  synchronized static mystatic() { ... }

  static mystaticeq() {
    syncrhonized (MyClass.class) { ... }
  }
}
```
For synchronized blocks, you can use any non-null object as a lock:
```java
synchronized (mymap) {
  mymap.put(..., ...);
}
```
For synchronized methods, the lock will be held throughout the method scope, while in the synchronized block, the lock is held only during that block scope (otherwise known as critical section). In practice, the JVM is permitted to optimize by removing some operations out of the synchronized block execution if it can prove that it can be done safely.

------

### If I want to create a service with one worker thread how to achieve it?
If you want  a service with one thread, then go for IntentService, or a normal service with a thread in onCreate().
onCreate will be called only once, so if you create a thread there, then it will become a service with single thread.
But make sure that from onStartCommand you are queuing up the requests to that thread using some handlers.
Even if you use IntentService also, that will also create only one thread in that service. Using IntentService is a good option to go for a service with single thread.

------

### What will happen if you start a service that does heavy functionality without starting a thread?
Since android gives only one thread per application, default all activities will run in that thread. but service also performs long running operations in the background, it is not suggestible to run service also in the same thread which may hang your activities which lead to ANR.

------

### What is the UI response time limit in android. (I.E with in how many seconds main thread has to respond for user actions?)
5 sec

If user touches your activity, and if your program doesn't respond to user events with in 5 seconds, android will not tolerate it and it force closes your application. this error is called as ANR (Application Not Responding).

------

### What is the time limit of a broadcast receiver, what will happen if it crosses that time limit?
10 sec

Broadcast Receiver is a component that responds to system wide broadcast announcements. It acts as gateway to other components of our application. It is not supposed to do long running operation, it has to do its operation in 10 seconds maximum.

------

### What is the difference between sendbroadcast(), sendorderedbroadcast(), sendstickybroadcast() ?
* sendbroadcast() - normal broadcast, but we can set priority as well.

* sendorderedbroadcast() - we can set priority, and set result. can block broadcasts as well.
In the ordered broadcast you can predict the order of broadcast receiver using priority in the intent filter.
    * If the priority is same for two receivers then order cannot be predicted.

    * In the ordered broadcast you can also pass data between two receivers.

    * You can also abort the broadcast anywhere in between the receivers.

* sendstickybroadcast() - intent passed with this will be stick for future users who are registering through code (dynamic receivers).
When somebody sends a sticky broadcast using send stickyBroadcast(in); then that broadcast will be available for the future users who are using dynamic receivers.
This broadcast will be available for only Dynamic Broadcast rx who are coming in future.
Eg for stickybroadcast is - BATTERY LOW.

------

### If I startactivityforresult() and the child activity gets crashed what is the result code obtained by the parent?
In case of crash, parent activity will get RESULT_CANCELLED.

------

### In case of low memory if android closes a service forcefully, then will it restart automatically or user has to start it?
If android has stopped service with out user's knowledge, then it is the responsibility of android to restart it. But this rule will not be applicable if programmer returns START_NOT_STICKY from onStartCommand().

------

### What are the various return values of onstartcommand() , and when to use what?
* START_STICKY - in case if android stops our service forcefully, then restart service by sending intent null. 

* START_NOT_STICKY - in case if android stops our service forcefully, then don't restart service, until user restarts it. 

* START_REDELIVER_INTENT - in case if android stops our service forcefully, then restart service by re-sending the intent.

------

### Let’s say my service supports both starting a service and binding a service, and currently two persons have started my service and one person is binding to my service. After 5 minutes person who bound to my service, unbinds it.And other person stops my service, now is my service running in memory or got moved out from memory?
Service is dead and moved out of memory

Even if one client says stopService(), then service will be dead and moved out of memory if there are no binded connections are no other startService() requests pending to be processed.

------

### What is empty process and what is its priority? When android will use this concept?
Empty process - is an application which user is frequently visiting and closing. (Which resides in memory even after killing).

To launch frequently visited app fastly, even after destroying that app, it will be still in memory so that loading time will be reduced and user feels more comfortable. That is called as empty process.

its priority is 5 (Empty_Process). this is the last and least priority of an application or a process.

When android device goes out of memory, to reclaim some memory android will start killing all the processes starting with least priority process. Empty process is the most susceptible process to be killed in case of low memory.

------

### How does android achieves seamlessness. What is the meaning of seamlessness?
Seamlessness means un-interrupted flow of an application. No matter what is happening to your application, user should not feel that disturbance. these disturbances may happen in case of low memory, and configuration changes where android will and recreate the activity. but these changes should not be visible to the user and should not disturb user. this can be done by handling these conditions in onSaveInstantnceState() in all activities.

------

### What is the difference between menus and dialogs?
Menus are designed using xml, because menus will not change so frequently. You can build menus using code also when ever required. But it is not recommended to completely build menus using code.
Dialogs are built using code because dialog content will frequently change.

------

### How many kinds of menus are there in android?
* Options Menu - triggered when user presses menu hard key. This is deprecated starting from android 3.0. In place of options menu migrate to action bars.

* Sub Menu - Menu with in a menu (only one level of sub menu is allowed). 

* Context Menu - Triggered when user press and hold on some view which was registered to show context menu. 

------

### How many kinds of dialogues are there in android?
* AlertDialog - This is the most common form of any dialog, which will contain title, text, and maximum 3 buttons.  There in this 4 types of it - **normal alert dialog**, **items alert dialog**, **single choice**, & **multi choice alert dialog**. 

* ProgressDialog. - This is to show progress bar in the dialog box.

* DatePickerDialog - This is to show date to pick.

* Time picker dialog - This is to show time to pick.

------

### What is the difference between httpget() and httppost() methods, when to use what?
* httpget() - use it when we want to get some information from an URL of a server.
* httppost() - use it when we want to post some infromation to a server mentioned by URL from mobile.

eg: Use HttpGet, if you want to get google.com page. Use HttpGet if you want to get all employee details from server.

eg: Use HttpPost, if you want to post some data like posting email to gmail server, or posting a blog to blog server.

Note:

In an HTTP GET request, key/value pairs are specified in the URL:
```
http://server/something?value1=foo&value2=bar.
```
In an HTTP POST request, key/value pairs are sent as part of the HTTP request after the headers. For example:
```
 POST /something HTTP/1.1
 Host: server
 Content-Length: 21
 Content-Type: application/x-www-form-urlencoded

 value1=foo&value2=bar
```
The HTTP specification differentiates POST and GET in terms of their intent:

GET is idempotent: it is for obtaining a resource, without changing anything on the server. As a consequence it should be perfectly safe to resubmit a GET request.

POST is not: it is for updating information on the server. It can therefore not be assumed that it is safe to re-submit the request which is why most browsers ask for confirmation when you hit refresh on a POST request.

In terms of security, no difference. POST is more obscure, perhaps, but that's a very different thing. Security needs to be added at another layer, for example SSL.

------

### If I want to secure my preference file from other activities in my app, then should I use getPreferences() or getSharedPreferences()?
Use getPreferences(), but its not guaranteed to be protected as it will be stored with the name of Activity.

* getPreferences(0) - will open or create a preference file without giving a name specifically. By default preference file name will be the current Activity name. if some one knows this, any once can access getPreference() file using that activity name. 

* getSharedPreferences("name",0) - will open or create a preference file with the given name. If you want to create a preference file with a specific name then use this function. If you want multiple preference files for you activity, then also you can use this function.

Note: preferences file will be with .xml extension stored in data/data/preferences folder.

------

### I want to store huge structured data in my app that is private to my application, now should I use preferences [or] files [or] sqlite [or] content provider?
Use sqlite.

* Content Providers
ContentProvider is a scheme/interface for providing data to other apps; it doesn't directly involve the actual storage of the data itself, although generally they will be backed by a database. 

Consider the structured data added to the device from application1 is not accessible to another application2 present in the same device but the profile photo added to the device by application1 is available to the application2 running in the same device

Consider android device as a city, the applications in it are the houses in the city, people in the houses(application) are the data. Now content provider is like an broker in the city(android device). This broker provide access for the people in the city for finding different houses referring as the content provider in the android device provide access for the data in the device for different applications.

* Shared Preferences

Shared Preferences is very handy in such scenarios where I can use its feature to share my data in a xml file Which physically exists in the Android app installed in my phone which is not destroyed even if the app is closed. Here we can save user preferences data of the current application.

* File Storage

In Android we can use the device storage space to store the data in it for the applications. The type of data involves things such as a text file, image file, video file, audio file etc.
As seen in the figure as we can see that there are two places we can do this. One way is to write the raw files into primary /secondary storage. Another way is to write the cache files into the primary/secondary storage.

* SQLite

Sqlite is used to store more structured data locally in a mobile where the android app is running. Structured data involves as of which shown in the figure like a student’s information in the form of rows and columns.
Sqlite is very helpful in storing complex and large data which can be downloaded once and can be used again and again until the application is running. When the application is closed the sqlite database is also destroyed.

* Network connection refers to storing data on the cloud.

------

### My application has only a service, and my service performs heavy lift functionality to connect to internet and fetch data, now should I create a thread or not? If so why?
No need to create a new thread in Service as it is not required in this scenario. Because service runs in the main thread. Since our app doesn't have any activities, so its OK to run service in main thread. ANR error will occur only if activities and services runs in same thread.

------

### When a new activity comes on top of your activity, what is the life cycle function that gets executed.
When new activity comes on top of an existing activity, then existing activity will move to invisible state by calling onPause() -> then -> onStop. If top activity is transparent or doesn't occupy complete screen, then below activity's onStop() will not be called.

------

### When a dialog is displayed on top of your activity, is your activity in foreground state or visible state?
When a dialog comes on top of an existing activity, then existing activity will move to partially invisible state by calling onPause().

------

### When your activity is in stopped state, is it still in memory or not?
when onStop() is called, then activity is still in memory and all its states and variables are intact.

------

### I started messaging app –> composer activity -> gallery  -> camera -> press home button. Now which state camera activity is in?
Stopped state.

------

### Continuation to above question, now If I launch gmail application will it create a new task or is it part of old messaging task?
New task.

------

### Can I have more than one application in a given task? Can I have more than one process in a given task?
Apps and process is same. Task is a collection of one or more applications. App means process, so task can contain multiple  processes.

------

### Do all the activities and services of my application run in a single process?
Activities, services and other components of an applications runs in single process. there can be some situations where we can allow a component of application to run in a different process. but this is possible only if user id of those two different processes are same.

------

### Do all components of my application run in same thread?
Each application will have one process and one main thread created by system, by default. So by default all the components of an android application runs in Main thread (UI thread)

------

### How to access progress bar from a service?
If you want to touch UI from service, trigger a dynamically registered receiver in activity from service. And update UI from that dynamic receiver with in that activity. All UI controlling has to be done in activity to reduce side effects. if a services wants to touch UI, send a broadcast from service which triggers a receiver in activity which is dynamically registered in activity for communication. from that receiver you can touch UI since it is inner class of it.

------

### What is the difference between intent and intent-filter?
* intent : is a message passing mechanism between components of android, except for content provider.  you can use intents to pass data from one component to other component.  you can also use intents to start one component from other component. eg: you can start an activity by using intents.

* intent-filter : tells about the capabilities of that component.  it tells what kind of implicit intents that component can handle.  intent-filters are counter parts for intents.

------

### What is the difference between content-provider and content-resolver?
Content provider is used to share private data with other applications, where as content-resolver communicates from client end with content provider.
DB, Files, preferences are stored in private memory of application which cannot be accessed by outside applications.content provider is used to share this private data with other applications.
Where as contentresolver is used to communicate with content provider from client application. 
As of now there is no support for shared preferences with content provider.

------

### What is the difference between cursor & contentvalues ?
ContentValues is a name value pair, used to insert or update values into database tables. ContentValues object will be passed to SQLiteDataBase objects insert() and update() functions. 
Where as Cursor is a temporary buffer area which stores results from a SQLiteDataBase query.

------

### What is Bundle? What does it contain in oncreate() of your activity?
Bundle contain previous savedInstantceState

Bundle is a data holder, which holds data to be passed between activities.
In case of forceful closing of an activity, android will save all its UI states and transient states so that they can be used to restore the activity's states later. This saved states will be passed via Bundle in onCreate() function to restore its states once android recreates a killed activity. 
EG: this will happen in case of low memory or configuration changes like rotating the phone.

onSaveInstanceState():
This function will be called by Android before “onPause” or after “onPause” if Android is forcefully killing your activity. In this function we have to save all your activity states.

onRestoreInstanceState():
This function will be called after “onStart”.

123.                        How to get contact number from contacts content provider?
Use ContactsContract.Contacts.URI and CommonDataKinds.Phone.CONTENT_URI, then request it through content resolver
First get the all basic details from basic table of contacts
```java
Cursor c1 = this.getContentResolver().query( ContactsContract.Contacts.CONTENT_URI, null, null,null, null);
c1.moveToNext(); //move to first row
String id = c1.getString(c1.getColumnIndex(Contacts._ID)); //get its id
//now based on that id, you can retrive phone details from other table.
Cursor cur = this.getContentResolver().
                    		query(CommonDataKinds.Phone.CONTENT_URI, 
                    		null, 
                    		CommonDataKinds.Phone.CONTACT_ID +" = ?", 
                    		new String[]{id}, null);
cur.moveToNext();
String number = cur.getString(cur.getColumnIndex(                       				CommonDataKinds.Phone.NUMBER));
```

------

### What is the difference between linear layout and relative layout?
linear layout - arranges child element in either vertical or horizontal fashion. 
Relative layout - arranges child elements in relative to each other.
Important properties of Relative Layout:
* android:below
* android:above
* android:toRightof
* android:toLeftof

------

### What's difference between gravity and layout_gravity?

Their names should help:

* android:gravity sets the gravity of the content of the View its used on.

* android:layout_gravity sets the gravity of the View or Layout in its parent.

------

### What is adapterview? How many adapter views are available in android?
Any view that takes input from adapter is called as adapter view. eg:listview, gallery, spinner, gridview, etc..

any view that takes input from adapter is called as adapter view. eg:listview, gallery, spinner, gridview, etc..

------

### Can I give cursor to an array adapter?
No, array adapter takes - lists (arraylists); 
cursor adapter takes - cursor which is returned from database tables upon querying.

------

### What is custom adapter, when should I use it. what are the mandatory functions I need to implement in custom adapter?
getView, getItem, getItemId, and getCount are the mandatory functions that have to be implemented in Custom Adapter.

------

### What is the class that I need to extend to create my own adapter?
To create a own custom adapter, one can extend BaseAdapter, or can extend any of the existing concrete adapters like ArrayAdapter, SimpleCursorAdatper etc.. 

Note: we can also extend or implement other adapter interfaces, but it is not so useful. Generally extending BaseAdapter is enough to create our own custom adapter.

------

### What is the android compilation and execution process/ cycle?
.java file -- will be given to -- java compiler -- to generate -- .class file.
all .class files -- will be given to -- dx tool -- to generate single -- dex file
dex file -- will be given to -- dvm -- to generate -- final machine code.
final machine code -- will be given to -- CPU -- to execute.

------

### What is adb? What is the command to install one application using adb command prompt?  
Android debug bridge (ADB)
  * tool used to communicate to emulator/ devices from eclipse or command prompt.
  * its a client-server program

There are 3 components in it:
1. a client running on development machine. -> you can invoke this by issueing    
```
adb shell command.
```
(adt/ ddms also starts adb client)

2. a server [runs as a b.g process] on development machine.
   this establishes comm'on channel between client - adb daemon running on emulator/ device.

3. adb-daemon that runs on emulator/ device.
   adb -> is located in sdk/platform-tools

How is this connection established?
1. once you issue adb command, it starts adb client,
   then searches if server process if exists or not.

2. once server started, it will start listening --> on port 5037 --> for commands from [adb client]
   It will start connections from client - to - adb daemons.

How server detects adb-daemons?
   a. server will check ports 5555 -to- 5585, if it finds any port then it will set up connection.

   Note: consoles will take up even ports, adb daemons will take up odd ports.

   eg: Emulator 1, console: 5554
       Emulator 1, adb: 5555
       Emulator 2, console: 5556
       Emulator 2, adb: 5557 ...

   it is multi connection (Means any client can talk to any daemon)

------

### What is the difference between started service and binded service?
* Started service - is used to do some long running operation in the back ground. it will be alive in memory even if the person who started it is no longer in memory. either it itself can stop or some other also can stop it. generally services will not have UI. it is used to some back ground functionalities like sending SMS, downloading files, etc..   

* Binded service- is like a client server architecture where clients can request to binded service to execute some function and return the result. started services will not return results generally. since data flow happens from service to client, there should be communication channel in the case of binded services.

------

### How to achieve bind service using IPC?
1. create a service & implement onCreate(), onBind(), onUnbind(), onDestroy()

2. create .aidl file with interface functions.

3. implement auto generated Binder stub class in service.

4. return object to this stub class from onBind()

------

### If I want to listen to my phone locations, what all the things I need to use? Is it better to use network providers or gps providers?
There are 3 location providers in Android.

They are:

* gps –> (GPS, AGPS): Name of the GPS location provider. This provider determines location using satellites. Depending on conditions, this provider may take a while to return a location fix. Requires the permission android.permission.ACCESS_FINE_LOCATION.

* network –> (AGPS, CellID, WiFi MACID): Name of the network location provider. This provider determines location based on availability of cell tower and WiFi access points. Results are retrieved by means of a network lookup. Requires either of the permissions android.permission.ACCESS_COARSE_LOCATION or android.permission.ACCESS_FINE_LOCATION.

* passive –> (CellID, WiFi MACID): A special location provider for receiving locations without actually initiating a location fix. This provider can be used to passively receive location updates when other applications or services request them without actually requesting the locations yourself. This provider will return locations generated by other providers. Requires the permission android.permission.ACCESS_FINE_LOCATION, although if the GPS is not enabled this provider might only return coarse fixes. This is what Android calls these location providers, however, the underlying technologies to make this stuff work is mapped to the specific set of hardware and telco provided capabilities (network service).

The best way is to use the “network” or “passive” provider first, and then fallback on “gps”, and depending on the task, switch between providers. This covers all cases, and provides a lowest common denominator service (in the worst case) and great service (in the best case).

------

### If I want to start some heavy weight functionalities that takes lot of battery power like starting animation or starting camera, should I do it in oncreate() or onstart() or onresume() of my activity? And where should I disable it?
Since heavy weight functions take too much of battery power, better do it just before your activity is ready to take user events. so do it in onResume().

------

### What is the life cycle of your application process?
In most cases, every Android application runs in its own Linux process. This process is created for the application when some of its code needs to be run, and will remain running until it is no longer needed and the system needs to reclaim its memory for use by other applications.

An unusual and fundamental feature of Android is that an application process's lifetime is not directly controlled by the application itself. Instead, it is determined by the system through a combination of the parts of the application that the system knows are running, how important these things are to the user, and how much overall memory is available in the system.

It is important that application developers understand how different application components (in particular Activity, Service, and BroadcastReceiver) impact the lifetime of the application's process. Not using these components correctly can result in the system killing the application's process while it is doing important work.

A common example of a process life-cycle bug is a BroadcastReceiver that starts a thread when it receives an Intent in its BroadcastReceiver.onReceive() method, and then returns from the function. Once it returns, the system considers the BroadcastReceiver to be no longer active, and thus, its hosting process no longer needed (unless other application components are active in it). So, the system may kill the process at any time to reclaim memory, and in doing so, it terminates the spawned thread running in the process. The solution to this problem is to start a Service from the BroadcastReceiver, so the system knows that there is still active work being done in the process.

To determine which processes should be killed when low on memory, Android places each process into an "importance hierarchy" based on the components running in them and the state of those components. These process types are (in order of importance):

* **A foreground process** is one that is required for what the user is currently doing. Various application components can cause its containing process to be considered foreground in different ways. A process is considered to be in the foreground if any of the following conditions hold:
    * It is running an Activity at the top of the screen that the user is interacting with (its onResume() method has been called).
    
    * It has a BroadcastReceiver that is currently running (its BroadcastReceiver.onReceive() method is executing).

    * It has a Service that is currently executing code in one of its callbacks (Service.onCreate(), Service.onStart(), or Service.onDestroy()).

There will only ever be a few such processes in the system, and these will only be killed as a last resort if memory is so low that not even these processes can continue to run. Generally, at this point, the device has reached a memory paging state, so this action is required in order to keep the user interface responsive.

* **A visible process** is one holding an Activity that is visible to the user on-screen but not in the foreground (its onPause() method has been called). This may occur, for example, if the foreground Activity is displayed as a dialog that allows the previous Activity to be seen behind it. Such a process is considered extremely important and will not be killed unless doing so is required to keep all foreground processes running.

* **A service process** is one holding a Service that has been started with the startService() method. Though these processes are not directly visible to the user, they are generally doing things that the user cares about (such as background mp3 playback or background network data upload or download), so the system will always keep such processes running unless there is not enough memory to retain all foreground and visible process.

* **A background process** is one holding an Activity that is not currently visible to the user (its onStop() method has been called). These processes have no direct impact on the user experience. Provided they implement their Activity life-cycle correctly (see Activity for more details), the system can kill such processes at any time to reclaim memory for one of the three previous processes types. Usually there are many of these processes running, so they are kept in an LRU list to ensure the process that was most recently seen by the user is the last to be killed when running low on memory.

* **An empty process** is one that doesn't hold any active application components. The only reason to keep such a process around is as a cache to improve startup time the next time a component of its application needs to run. As such, the system will often kill these processes in order to balance overall system resources between these empty cached processes and the underlying kernel caches.

When deciding how to classify a process, the system will base its decision on the most important level found among all the components currently active in the process. See the Activity, Service, and BroadcastReceiver documentation for more detail on how each of these components contribute to the overall life-cycle of a process. The documentation for each of these classes describes in more detail how they impact the overall life-cycle of their application.

A process's priority may also be increased based on other dependencies a process has to it. For example, if process A has bound to a Service with the Context.BIND_AUTO_CREATE flag or is using a ContentProvider in process B, then process B's classification will always be at least as important as process A's.

------

### What is the difference between viewgroup and layout?
viewgroup is invisible container, and abstract class. Layouts are more concrete form of view groups. view groups derive from views, and layouts derive from view groups.  Eg of layouts are 1.framelayout, 2.relative layout, 3.linear layout, etc..

------

### Describe the key event flow in android?
Key events will flow like this: **Android system -> Activity -> Layout -> View -> programmer**. First priority will be given programmer written logic, if programmer has written some thing to handle some key events it will be execute first. From there based on the return statement it will decide to flow back that key event to parent or not. eg: if programmer choose to block some key say KEYCODE_0, then for that key code he can say return true; which will block that event and will be destroyed in programmers function only.

------

### How many levels of security available in android?
Android supports 2 levels of security for applications. one at operating system level or kernel level. other is using **permission**  tags in app level.

------

### How to achive security to your service programmatically in such a way that your service should not get triggered from outside applications?
If you don't want to expose your service to outside apps, 3 ways are there. 
1. Don't give intent-fiiter tag, so that outsiders can't specify intent action to trigger your service. 

2. User exported="false" in service tag, so that outside world can't trigger it. 

3. User local service manager.

------

### What are the sequence of tests done to map intent with an intent-filter?
When programmer starts a component by using an intent, to trigger appropriate component android will perform 3 tests.

1. action test : the action string of intent should match with at least one action string of intent filter.

2. data test : data and data type of intent should match with that of intent filter.

3. category test : all the categories of intent should be there in intent filter.  note: In case of implicit intent starting an activity, then default category will be automatically added to intent.  

If there is any component in manifest file with an intent filter which passes all the 3 tests, then android will trigger that component. Else not.

If there are multiple components satisfying that intent, and if it is not a broadcast receiver, then it will ask user to make a decision.

------

### If I want to notify something to the user from broadcast receiver, should I use dialogs or notifications? Why?
Broadcast receivers are light weight components, which has to finish off its functionality with in time limit of 10 seconds. if we show dialog to user, there is a chance that user may take more than 10 seconds to respond to it by chance. in that case receiver may crash or may not be available to take dialog input from user. So it is always a standard and good practice to use notifications from a receiver.

------

### If I want to broadcast BATTERY_LOW action, should I use sendbroadcast() or sendstickybroadcast? Why?
use sendStickyBroadCast(), because logically this broadcast has to be available for future users.
We have to use sendStickyBroadCast() because, logically if battery went down, then this information has to be available for applications which may run after some time in future.

------

### If I want to set an alarm to trigger after two days, how should I implement it? assume that I may switch off the phone in between.
Use AlarmManager and call set() to set after 2 days. If phone switched off, all alarms will be dead.So, before switching off maintain all alarms details in Database and recreate all alarms after switching on.

------

### What is sleep mode? What will happened to CPU once screen light goes off?
Sleep mode - means CPU will be sleeping and will not accept any command except from RIL(Radio Interface Layer) and alarms. CPU will go to sleep mode with in fraction of seconds after LCD is turned off.

There is small difference in power consumption when you compare a phone with laptops.
Laptops most of the times runs on direct power through charger. (Mostly we carry charge with laptop).  Mobiles mostly run on battery power. (Rarely we carry charger with us).  By keeping this in mind, android has designed in a such a way that, phone will not consume battery power if user is not interacting with the phone. This is to increase battery back up time for user.  Thats why when LCD screen power is off (that means user is not interacting with phone), with in fraction of seconds CPU will also go to a mode (sleep mode) where it does minimal work and saves battery power. When CPU is in sleep mode, it accepts commands only from RIL (radio interface layer) which is basically sms and call functions, and alarms. Other than this CPU will stop executing other applications functions.  

Note : If any application wants CPU time for its emergency work when CPU is in sleep mode, then it can request CPU time by using WAKE LOCKS.
For eg : MP3 application has to keep playing songs in its service, even though user has turned off LCD screen. That means MP3 application's service has requested CPU time by using WAKE LOCKS.

------

### How many kinds of wake locks are available, which one to use when?
There are 4 types of wake locks available in android power manager driver:

1. PARTIAL_WAKE_LOCK - use it when you need only CPU to be on. eg : while traveling playing mp3 audio songs, which requires only cpu to be on.

2. SCREEN_DIM_WAKE_LOCK - use it when you need CPU and Screen in dim mode

3. SCREEN_BRIGHT_WAKE_LOCK - use it when you need CPU and  SCREEN in Bright mode. eg : while traveling if we want to watch a movie in the phone, then both cpu & screen should be on. 

4. FULL_WAKE_LOCK - use it when you need all SCREEN, CPU, Keypad in bright and on. eg : If user is playing a game, then cpu, screen lights, and keypads lights should be on.  Because to play a game, user might be using keyboard also to navigate or to move the elements in the game.

------

### Why android follows single threaded ui mode? How other threads can manipulate ui views?
Because synchronization is costly compared to single threaded model.
using synchronization also it is possible to manipulate UI from other threads. But Android doesn't follow that design, because it is very costly in terms of CPU time over head if we use synchronization. So all UI updates has to go through Main Thread (or UI thread) only.

------

### What is the purpose of SQLiteOpenHelper?
SQLiteOpenHelper is to make it easy to manage create or upgrade of database tables easily without loosing sensible data. table creating and upgrading has to be done carefully because if program crashes in between while creating or upgrading tables, it should not loose previous data. To reduce the burden on the programmer, these sensible data states will maintained by SQLiteOpenHelper.

------

### What is the procedure to upgrade database after first release?
If you want to upgrade the database of your existing application, which is released into android play store, then follow below steps. 
1. Create new version number for your latest database 
2. Based on some condition pass this latest version number to SQLiteOpenHelper constructor function. 
3. then what ever the updations you want to make to database, do it in onUpgrade() of SQLiteOpenHelper class.

------

### Show with one example where memory leak possibility in Android?
```java
class MyActivity extends Activity
{
    private static Drawable sBackground;

    @Override
    protected void onCreate(Bundle state) {
      super.onCreate(state);

      TextView label = new TextView(this);
      label.setText("Leaks are bad");

      if (sBackground == null) {
          sBackground = getDrawable(R.drawable.large_bitmap);
      }
      
      label.setBackgroundDrawable(sBackground);
      setContentView(label);
    }
}
```
Where static drawables are bound with process life cycle, which is pointing to label, which pointing to activity context. This is kind of dead lock situation where Android can't clean activity's resources because activity's pointer is held by label which can't be cleaned because it is pointed by process global data (static variables) drawable.

------

### If I want to write one application for both phones and tablets, what should I use in my UI?
* Use fragments where ever it is possible 
* Use DPs instead of pixels 
* Use xhdpi images as well.
* Avoid giving margins based on pixels, rather use margins with respect to parent
* Use SPs for font sizes.
* Use 9 patch images always.

------

### What is R.java file? What does it contain?
It is automatic generated file, which have pointers to all resources used in the application.

All resources located in res folder are mostly .xml files, which will not be understood by java compiler. So aapt (android application packaging) tool will convert all those xml files and other resources into a java file, which has identification numbers(pointers) to all those resources. if we want to access any resource from the code, then we can access through this R.java file. full form is Resource.java file.

------

### What does .apk  file contains?
contains single .dex file, zipped resources, other non java library files.

APK - Application Package file. It is a file format used to distribute and install android applications. .apk will contain single .dex file, zipped resources, other non java library files (c/c++). .dex file will internally contains converted .class files. other wise .apk will not contains .class files.

### Difference between px, dp, dip and sp on Android?

##### px
Pixels - corresponds to actual pixels on the screen.
##### in 
Inches - based on the physical size of the screen.1 Inch = 2.54 centimeters
##### mm 
Millimeters - based on the physical size of the screen.
##### pt 
Points - 1/72 of an inch based on the physical size of the screen.
##### dp or dip 
Density-independent Pixels - an abstract unit that is based on the physical density of the screen. These units are relative to a **160 dpi screen**, so one dp is one pixel on a 160 dpi screen. The ratio of dp-to-pixel will change with the screen density, but not necessarily in direct proportion. Note: The compiler accepts both "dip" and "dp", though "dp" is more consistent with "sp".
##### sp 
Scale-independent Pixels - this is like the dp unit, but it is also scaled by the user's font size preference. It is recommend you use this unit when specifying font sizes, so they will be adjusted for both the screen density and user's preference.

------

### What is a ContentProvider and what is it typically used for?
A ContentProvider manages access to a structured set of data. It encapsulates the data and provide mechanisms for defining data security. ContentProvider is the standard interface that connects data in one process with code running in another process.

------

### Under what condition could the code sample below crash your application? How would you modify the code to avoid this potential problem? Explain your answer.
```java
Intent sendIntent = new Intent();
sendIntent.setAction(Intent.ACTION_SEND);
sendIntent.putExtra(Intent.EXTRA_TEXT, textMessage);
sendIntent.setType(HTTP.PLAIN_TEXT_TYPE); // "text/plain" MIME type
startActivity(sendIntent);
```
An implicit intent specifies an action that can invoke any app on the device able to perform the action. Using an implicit intent is useful when your app cannot perform the action, but other apps probably can. If there is more than one application registered that can handle this request, the user will be prompted to select which one to use.  However, it is possible that there are no applications that can handle your intent. In this case, your application will crash when you invoke startActivity(). To avoid this, before calling startActivity() you should first verify that there is at least one application registered in the system that can handle the intent. To do this use resolveActivity() on your intent object:
```java
    // Verify that there are applications registered to handle this intent
    // (resolveActivity returns null if none are registered)
    if (sendIntent.resolveActivity(getPackageManager()) != null) {
        startActivity(sendIntent);
    } 
```

------

### The last callback in the lifecycle of an activity is onDestroy(). The system calls this method on your activity as the final signal that your activity instance is being completely removed from the system memory. Usually, the system will call onPause() and onStop() before calling onDestroy(). Describe a scenario, though, where onPause() and onStop() would not be invoked.
onPause() and onStop() will not be invoked if finish() is called from within the onCreate() method. This might occur, for example, if you detect an error during onCreate() and call finish() as a result. In such a case, though, any cleanup you expected to be done in onPause() and onStop() will not be executed.  Although onDestroy() is the last callback in the lifecycle of an activity, it is worth mentioning that this callback may not always be called and should not be relied upon to destroy resources. It is better have the resources created in onStart() and onResume(), and have them destroyed in onStop() and onPause, respectively.

------

### Which of the code snippets below is the correct way to check if a Compass sensor is present on the system? Explain your answer.
Answer 1: 
```java
    PackageManager m = getPackageManager();
    if (!m.hasSystemFeature(PackageManager.FEATURE_SENSOR_COMPASS)) {
        // This device does not have a compass, turn off the compass feature
    }
```
Answer 2:
```java
    SensorManager m = getSensorManager();
    if (!m.hasSystemFeature(SensorManager.FEATURE_SENSOR_COMPASS)) {
        // This device does not have a compass, turn off the compass feature
    }
```
Answer 3:
```java
    Sensor s = getSensor();
    if (!s.hasSystemFeature(Sensor.FEATURE_SENSOR_COMPASS)) {
        // This device does not have a compass, turn off the compass feature
    }
```
The correct answer is Answer 1, the version that uses PackageManager.  SensorManager and Sensor are part of Android Sensor Framework and are used for direct access and acquisition of raw sensor data. These classes do not provide any method like hasSystemFeature() which is used for evaluation of system capabilities.  Android defines feature IDs, in the form of ENUMs, for any hardware or software feature that may be available on a device. For instance, the feature ID for the compass sensor is FEATURE_SENSOR_COMPASS.  If your application cannot work without a specific feature being available on the system, you can prevent users from installing your app with a **uses-feature** element in your app’s manifest file to specify a non-negotiable dependency.However, if you just want to disable specific elements of your application when a feature is missing, you can use the PackageManager class. PackageManager is used for retrieving various kinds of information related to the application packages that are currently installed on the device.  

-------

### Describe three common use cases for using an Intent.
Common use cases for using an Intent include:
* To start an activity: You can start a new instance of an Activity by passing an Intent to startActivity() method.
* To start a service: You can start a service to perform a one-time operation (such as download a file) by passing an Intent to startService().
* To deliver a broadcast: You can deliver a broadcast to other apps by passing an Intent to sendBroadcast(), sendOrderedBroadcast(), or sendStickyBroadcast().

-------

### Suppose that you are starting a service in an Activity as follows:
```java
Intent service = new Intent(context, MyService.class);             
startService(service);
```
### where MyService accesses a remote server via an Internet connection.  If the Activity is showing an animation that indicates some kind of progress, what issue might you encounter and how could you address it?

Responses from a remote service via the Internet can often take some time, either due to networking latencies, or load on the remote server, or the amount of time it takes for the remote service to process and respond to the request.  As a result, if such a delay occurs, the animation in the activity (and even worse, the entire UI thread) could be blocked and could appear to the user to be “frozen” while the client waits for a response from the service. This is because the service is started on the main application thread (or UI thread) in the Activity.  The problem can (and should) be avoided by relegating any such remote requests to a background thread or, when feasible, using an an asynchronous response mechanism.  Note well: Accessing the network from the UI thread throws a runtime exception in newer Android versions which causes the app to crash.

-------

### There are four Java classes related to the use of sensors on the Android platform. List them and explain the purpose of each.
The four Java classes related to the use of sensors on the Android platform areL
* Sensor: Provides methods to identify which capabilities are available for a specific sensor.
* SensorManager: Provides methods for registering sensor event listeners and calibrating sensors.
* SensorEvent: Provides raw sensor data, including information regarding accuracy.
* SensorEventListener: Interface that defines callback methods that will receive sensor event notifications.

-------

### Normally, in the process of carrying out a screen reorientation, the Android platform tears down the foreground activity and recreates it, restoring each of the view values in the activity’s layout.In an app you’re working on, you notice that a view’s value is not being restored after screen reorientation. What could be a likely cause of the problem that you should verify, at a minimum, about that particular view?

You should verify that it has a valid id. In order for the Android system to restore the state of the views in your activity, each view must have a unique ID, supplied by the android:id attribute.

-------

### What is DDMS? Describe some of its capabilities.
DDMS is the **Dalvik Debug Monitor Server** that ships with Android. It provides a wide array of debugging features including:
* port-forwarding services
* screen capture
* thread and heap information
* network traffic tracking
* incoming call and SMS spoofing
* simulating network state, speed, and latency
* location data spoofing

-------

### What is the relationship between the life cycle of an AsyncTask and an Activity? What problems can this result in? How can these problems be avoided?
An AsyncTask is not tied to the life cycle of the Activity that contains it. So, for example, if you start an AsyncTask inside an Activity and the user rotates the device, the Activity will be destroyed (and a new Activity instance will be created) but the AsyncTask will not die but instead goes on living until it completes.  Then, when the AsyncTask does complete, rather than updating the UI of the new Activity, it updates the former instance of the Activity (i.e., the one in which it was created but that is not displayed anymore!). This can lead to an Exception (of the type java.lang.IllegalArgumentException: View not attached to window manager if you use, for instance, findViewById to retrieve a view inside the Activity).  There’s also the potential for this to result in a memory leak since the AsyncTask maintains a reference to the Activty, which prevents the Activity from being garbage collected as long as the AsyncTask remains alive.  For these reasons, using AsyncTasks for long-running background tasks is generally a bad idea . Rather, for long-running background tasks, a different mechanism (such as a service) should be employed.

-------

### What is an Intent? Can it be used to provide data to a ContentProvider? Why or why not?
The Intent object is a common mechanism for starting new activity and transferring data from one activity to another. However, you cannot start a ContentProvider using an Intent.  When you want to access data in a ContentProvider, you must instead use the ContentResolver object in your application’s Context to communicate with the provider as a client. The ContentResolver object communicates with the provider object, an instance of a class that implements ContentProvider. The provider object receives data requests from clients, performs the requested action, and returns the results.

-------

### What is the difference between a fragment and an activity? Explain the relationship between the two.
An activity is typically a single, focused operation that a user can perform (such as dial a number, take a picture, send an email, view a map, etc.). Yet at the same time, there is nothing that precludes a developer from creating an activity that is arbitrarily complex.  Activity implementations can optionally make use of the Fragment class for purposes such as producing more modular code, building more sophisticated user interfaces for larger screens, helping scale applications between small and large screens, and so on. Multiple fragments can be combined within a single activity and, conversely, the same fragment can often be reused across multiple activities. This structure is largely intended to foster code reuse and facilitate economies of scale.  A fragment is essentially a modular section of an activity, with its own lifecycle and input events, and which can be added or removed at will. It is important to remember, though, that a fragment’s lifecycle is directly affected by its host activity’s lifecycle; i.e., when the activity is paused, so are all fragments in it, and when the activity is destroyed, so are all of its fragments.

-------

### What is difference between Serializable and Parcelable ? Which is best approach in Android ?
Serializable is a standard Java interface. You simply mark a class Serializable by implementing the interface, and Java will automatically serialize it in certain situations.  Parcelable is an Android specific interface where you implement the serialization yourself. It was created to be far more efficient than Serializable, and to get around some problems with the default Java serialization scheme.

-------

### What are “launch modes”? What are the two mechanisms by which they can be defined? What specific types of launch modes are supported?
A “launch mode” is the way in which a new instance of an activity is to be associated with the current task.  Launch modes may be defined using one of two mechanisms: 

* Manifest file. When declaring an activity in a manifest file, you can specify how the activity should associate with tasks when it starts. Supported values include:

**standard** (default). Multiple instances of the activity class can be instantiated and multiple instances can be added to the same task or different tasks. This is the common mode for most of the activities.

**singleTop**. The difference from standard is, if an instance of the activity already exists at the top of the current task and the system routes the intent to this activity, no new instance will be created because it will fire off an onNewIntent() method instead of creating a new object.

**singleTask**. A new task will always be created and a new instance will be pushed to the task as the root. However, if any activity instance exists in any tasks, the system routes the intent to that activity instance through the onNewIntent() method call. In this mode, activity instances can be pushed to the same task. This mode is useful for activities that act as the entry points.

**singleInstance**. Same as singleTask, except that the no activities instance can be pushed into the same task of the singleInstance’s. Accordingly, the activity with launch mode is always in a single activity instance task. This is a very specialized mode and should only be used in applications that are implemented entirely as one activity.

* Intent flags. Calls to startActivity() can include a flag in the Intent that declares if and how the new activity should be associated with the current task. Supported values include:

**FLAG_ACTIVITY_NEW_TASK**. Same as singleTask value in Manifest file (see above).

**FLAG_ACTIVITY_SINGLE_TOP**. Same as singleTop value in Manifest file (see above).

**FLAG_ACTIVITY_CLEAR_TOP**. If the activity being started is already running in the current task, then instead of launching a new instance of that activity, all of the other activities on top of it are destroyed and this intent is delivered to the resumed instance of the activity (now on top), through onNewIntent(). There is no corresponding value in the Manifest file that produces this behavior.

-------

### What is the difference between Service and IntentService? How is each used?
Service is the base class for Android services that can be extended to create any service. A class that directly extends Service runs on the main thread so it will block the UI (if there is one) and should therefore either be used only for short tasks or should make use of other threads for longer tasks.

IntentService is a subclass of Service that handles asynchronous requests (expressed as “Intents”) on demand. Clients send requests through startService(Intent) calls. The service is started as needed, handles each Intent in turn using a worker thread, and stops itself when it runs out of work. Writing an IntentService can be quite simple; just extend the IntentService class and override the onHandleIntent(Intent intent) method where you can manage all incoming requests.

