# 灰太狼的博客
Activity生命周期(第一部分)
简单一下流程（目前还无法上传图片，这个周之内会把图片上传上来，关于activity生命周期的一个流程图）

Activity生命周期最主要的一些方法，启动后依次执行：onCreate –> onStart –> onResume –> onPause –> onStop –> onDestroy
借用网上的一些资料来解释一下这几个主要的生命周期：

1. void onCreate(Bundle savedInstanceState) 
当Activity被第首次加载时执行。我们新启动一个程序的时候其主窗体的onCreate事件就会被执行。如果Activity被销毁后（onDestroy后），再重新加载进Task时，其onCreate事件也会被重新执行。注意这里的参数 savedInstanceState（Bundle类型是一个键值对集合，大家可以看成是.Net中的Dictionary）是一个很有用的设计，由于前面已经说到的手机应用的特殊性，一个Activity很可能被强制交换到后台（交换到后台就是指该窗体不再对用户可见，但实际上又还是存在于某个Task中的，比如一个新的Activity压入了当前的Task从而“遮盖”住了当前的 Activity，或者用户按了Home键回到桌面，又或者其他重要事件发生导致新的Activity出现在当前Activity之上，比如来电界面），而如果此后用户在一段时间内没有重新查看该窗体（Android通过长按Home键可以选择最近运行的6个程序，或者用户直接再次点击程序的运行图标，如果窗体所在的Task和进程没有被系统销毁，则不用重新加载，直接重新显示Task顶部的Activity，这就称之为重新查看某个程序的窗体），该窗体连同其所在的 Task和Process则可能已经被系统自动销毁了，此时如果再次查看该窗体，则要重新执行 onCreate事件初始化窗体。而这个时候我们可能希望用户继续上次打开该窗体时的操作状态进行操作，而不是一切从头开始。例如用户在编辑短信时突然来电，接完电话后用户又去做了一些其他的事情，比如保存来电号码到联系人，而没有立即回到短信编辑界面，导致了短信编辑界面被销毁，当用户重新进入短信程序时他可能希望继续上次的编辑。这种情况我们就可以覆写Activity的void onSaveInstanceState(Bundle outState)事件，通过向outState中写入一些我们需要在窗体销毁前保存的状态或信息，这样在窗体重新执行onCreate的时候，则会通过 savedInstanceState将之前保存的信息传递进来，此时我们就可以有选择的利用这些信息来初始化窗体，而不是一切从头开始。 

2. void onStart()   activity变为在屏幕上对用户可见时调用。
onCreate事件之后执行。或者当前窗体被交换到后台后，在用户重新查看窗体前已经过去了一段时间，窗体已经执行了onStop事件，但是窗体和其所在进程并没有被销毁，用户再次重新查看窗体时会执行onRestart事件，之后会跳过onCreate事件，直接执行窗体的onStart事件。 

3. void onResume()   activity开始与用户交互时调用（无论是启动还是重新启动一个活动，该方法总是被调用的）。
onStart事件之后执行。或者当前窗体被交换到后台后，在用户重新查看窗体时，窗体还没有被销毁，也没有执行过onStop事件（窗体还继续存在于Task中），则会跳过窗体的onCreate和onStart事件，直接执行onResume事件。 

4. void onPause()   activity被暂停或收回cpu和其他资源时调用，该方法用于保存活动状态的，也是保护现场，压栈吧！
窗体被交换到后台时执行。 

5. void onStop()    activity被停止并转为不可见阶段及后续的生命周期事件时调用。
onPause事件之后执行。如果一段时间内用户还没有重新查看该窗体，则该窗体的onStop事件将会被执行；或者用户直接按了Back键，将该窗体从当前Task中移除，也会执行该窗体的onStop事件。 

6. void onRestart()   重新启动activity时调用。该活动仍在栈中，而不是启动新的活动。
onStop事件执行后，如果窗体和其所在的进程没有被系统销毁，此时用户又重新查看该窗体，则会执行窗体的onRestart事件，onRestart事件后会跳过窗体的onCreate事件直接执行onStart事件。 

7. void onDestroy()   activity被完全从系统内存中移除时调用，该方法被调用可能是因为有人直接调用onFinish()方法或者系统决定停止该活动以释放资源！
Activity被销毁的时候执行。在窗体的onStop事件之后，如果没有再次查看该窗体，Activity则会被销毁。

很多人也都已经知道以上方法与执行顺序，但是Activity还有其他方法，如onContentChanged， onPostCreate， onPostResume， onConfigurationChanged， onSaveInstanceState，onRestoreInstanceState；大家是不是想试一下关于这些生命周期的执行顺序，那咱们就看接下来的这个例子，比较简单，但是一目了然：

public class ActivityLife extends Activity {

static String TAG = "ActivityLife";

@Override
public void onCreate(Bundle savedInstanceState) {

super.onCreate(savedInstanceState);
Log.d(TAG, "onCreate");
setContentView(R.layout.activity_demo);
}

@Override
public void onContentChanged() {
super.onContentChanged();
Log.d(TAG, "onContentChanged");

}

public void onStart() {
super.onStart();
Log.d(TAG, "onStart");
}

public void onRestart() {
 super.onRestart();
Log.d(TAG, "onRestart");
}

public void onPostCreate(Bundle savedInstanceState) {
super.onPostCreate(savedInstanceState);
Log.d(TAG, "onPostCreate");
}

@Override
public void onResume() {
super.onResume();
Log.d(TAG, "onResume");
}

public void onPostResume() {
super.onPostResume();
Log.d(TAG, "onPostResume");
}

public void onPause() {
super.onPause();
Log.d(TAG, "onPause");
}

public void onStop() {
super.onStop();
Log.d(TAG, "onStop");
}

public void onDestroy() {
super.onDestroy();
Log.d(TAG, "onDestroy");
}

public void onConfigurationChanged(Configuration newConfig) {
super.onConfigurationChanged(newConfig);
Log.d(TAG, "onConfigurationChanged");
}

public void onSaveInstanceState(Bundle outState) {
super.onSaveInstanceState(outState);
Log.d(TAG, "onSaveInstanceState");
}

public void onRestoreInstanceState(Bundle outState) {
super.onRestoreInstanceState(outState);
Log.d(TAG, "onRestoreInstanceState");
}
}

程序运行结果出来了，不知道大家心里是否也有一个结果，生命周期的方法执行顺序是这样的：
onCreate –> onContentChanged –> onStart –> onPostCreate –> onResume –> onPostResume –> onPause –> onStop –> onDestroy

onContentChanged
onContentChanged()是Activity中的一个回调方法 当Activity的布局改动时，即setContentView()或者addContentView()方法执行完毕时就会调用该方法， 例如，Activity中各种View的findViewById()方法都可以放到该方法中。

onPostCreate、onPostResume
onPostCreate方法是指onCreate方法彻底执行完毕的回调，onPostResume类似，这两个方法官方说法是一般不会重写，现在知道的做法也就只有在使用ActionBarDrawerToggle的使用在onPostCreate需要在屏幕旋转时候等同步下状态，Google官方提供的一些实例就是如下做法：
@Override
protected void onPostCreate(Bundle savedInstanceState) {
super.onPostCreate(savedInstanceState);

// Sync the toggle state after onRestoreInstanceState has occurred.
mDrawerToggle.syncState();
}

onPause、 onStop
这里顺便再提一下onPause、 onStop的区别， onPause是在整个窗口被半遮盖或者半透明的时候会执行，个人感觉在onPause中进行数据的保存时比较合适的；而onStop则是在整个窗口被完全遮盖才会触发， 触发onStop的方法之前必定会触发onPause方法。

onCreate、 onStart
onCreate方法会在第一次创建的时候执行，紧接着便会执行onStart方法，之后页面被完全遮挡会执行onStop方法，再返回的时候一般便会执行onRestart –> onStart方法， 但是如果如果这时候App内存不够需要更多的内存的时候，App便会杀死该进程，结束掉该Activity，所以这时候再返回的时候便会重新执行onCreate –> onStart –> onResume方法。

onSaveInstanceState
onSaveInstanceState字面理解就是保存实例的状态，当某个activity变得“容易”被系统销毁时，该activity的onSaveInstanceState就会被执行，除非该activity是被用户主动销毁的，例如当用户按BACK键的时候。

注意上面的双引号，何为“容易”？言下之意就是该activity还没有被销毁，而仅仅是一种可能性。这种可能性有这么几种情况：

1、当用户按下HOME键时
这是显而易见的，系统不知道你按下HOME后要运行多少其他的程序，自然也不知道activityA是否会被销毁，故系统会调用onSaveInstanceState，让用户有机会保存某些非永久性的数据。以下几种情况的分析都遵循该原则

2、长按HOME键，选择运行其他的程序时。

3、按下电源按键（关闭屏幕显示）时。

4、从activity A中启动一个新的activity时。

5、屏幕方向切换时，例如从竖屏切换到横屏时。
在屏幕切换之前，系统会销毁activity A，在屏幕切换之后系统又会自动地创建activity A，所以onSaveInstanceState一定会被执行
总而言之，onSaveInstanceState的调用遵循一个重要原则，即当系统“未经你许可”时销毁了你的activity，则onSaveInstanceState会被系统调用，这是系统的责任，因为它必须要提供一个机会让你保存你的数据（当然你不保存那就随便你了）。

onRestoreInstanceState
onSaveInstanceState字面理解就是恢复实例的状态, 需要注意的是，onSaveInstanceState方法和onRestoreInstanceState方法“不一定”是成对的被调用的，onRestoreInstanceState被调用的前提是，activity A“确实”被系统销毁了，而如果仅仅是停留在有这种可能性的情况下，则该方法不会被调用，例如，当正在显示activityA的时候，用户按下HOME键回到主界面，然后用户紧接着又返回到activityA，这种情况下activityA一般不会因为内存的原因被系统销毁，故activity A的onRestoreInstanceState方法不会被执行。

不过大多数情况下也是很少使用onRestoreInstanceState方法的，经常我们还是在onCreate方法里直接恢复状态的，onCreate方法里本身会有一个Bundle参数的，很多时候我们是这样使用的。（onCreate在onStart之前调用，而onRestoreInstanceState是在onStart之后调用）
