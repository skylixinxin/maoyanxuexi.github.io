---
layout: post  
title:  Android Wear入门
categories: [blog ]  
    
---
# Android Wear新手入门

****Android Wear的Notification设置以及数据接收同步****


## 准备工作

### Android Wear与模拟器的连接

#### 1.首先是SDK的升级

    请确定你的Android SDK为23.0以上的版本，Extras下的Android Support Library为最新版本，一般的API下载x86的SystemImage就可以。    
    
#### 2.Android Wear模拟器的连接

    更新了SDK之后就可以创建并打开Android Wear的模拟器了，然后把手机通过USB连接到开发机器上，并且保证手机上的Google Play一直处于登陆状态，且已经安装了最新版的Android Wear应用。然后打开命令行终端，进入到Android SDK的platform－tools目录下，执行adb －d forward tcp:5601 tcp:5601，然后点击手机上Android Wear右上角设置旁边的Icon，选择连接模拟器，这样就可以将手机和Android Wear模拟器连接起来了。
![Android_Wear.jpg](http://i2.buimg.com/4851/36ad2bf85ce1a5b9.png)

## Notification的创建

    想要实现Android Wear可穿戴设备上的通知，其实根本就不需要进行什么特殊的设置（这个记得以前看的Google的官方文档是需要进行设置的，但是实践中，国内的通知栏不需要任何设置就可以出现在Android Wear的可穿戴设备上）。
    
### Notification设置Intent激活Action的三种方式

    1.通过setContentIntent（）设置的PendingItent激活Action

   ![Android_Wear.jpg](http://i2.buimg.com/4851/e63de96db8fbdbc3.png)

    2.通过传递一个 PendingIntent 给 addAction()) 来添加其它action。
    
   ![Android_wear.jpg](http://i2.buimg.com/4851/e63de96db8fbdbc3.png)
   
    3.使用 WearableExtender.addAction()
    
   ![Android_Wear.jpg](http://i2.buimg.com/4851/5c7a56c29183238c.png)
   
   **注意：** 第3种方式是可穿戴式独有的Actions，一旦我们通过这种方式添加了action，可穿戴式设备便不会显示任何其他通过NotificationCompat.Builder.addAction()) 添加的action。这是因为，只有通过 WearableExtender.addAction()) 添加的action才能只在可穿戴设备上显示且不在手持式设备上显示。
   
### BigTextStyle

    我们可以通过setLargeIcon（）为Notification添加一个大图标，但是这些图标在可穿戴设备上会显示成大的背景图片，通过setStyle()来进行BigTextStyle或者InboxStyle的实例设置。
    
   ![Android_Wear.jpg](http://i2.buimg.com/4851/40df423098f324aa.png)
   
   ![Android_Wear.jpg](http://hukai.me/android-training-course-in-chinese/wearables/notifications/06_images.png)
   
### Notification的可穿戴特性

    我们可以使用 NotificationCompat.WearableExtender 来添加一些可穿戴式的特性设置，比如制定额外的内容页，或者让用户通过语音输入一些文字等。
    
   ![Android_Wear.jpg](http://i2.buimg.com/4851/ded293210457ec95.png)
   
### Notification添加Page以及卡片从的设置

    通过addPage())方法将这些页面应用到主 Notification 中。
    
   ![Android_Wear.jpg](http://i2.buimg.com/4851/ded293210457ec95.png)
   
    可穿戴设备可以将所有的Notification都集中起来，放成一叠。这叠Notification以一张卡片的形式显示出来，用户可以将它展开，分别看到每个Notification的详细内容。只要通过新方法setGroup())能够实现该功能。
   ![Android_Wear.jpg](http://i4.buimg.com/4851/a4f401838035b4dc.png)
   
    稍后，当开发者创建另一个Notification的时候，指定同样的group key。当在调用notify())的时候，这个Notification就会出现在之前那个Notification的同一个stack中，而非新建一张卡片。在默认的情况下，Notification的排列顺序由开发者添加的先后顺序决定，最近的Notification会被放置在最顶端。你可以通过setSortKey())来修改Notification的排顺序。
    
## Android Wear自定义UI

    当我们创建一个Wearable应用的时候，可以通过给build.gradle文件添加下面的依赖声明把库文件添加到项目
    
   ![Android_Wear.jpg](http://i1.buimg.com/4851/7c78c3a17f3a12b6.png)
   
    可穿戴设备存在方形和圆形两种不同的屏幕，这时候我们有两种不同的解决方式。
    1.为方形和圆形屏幕指定不同的Layouts
    包含在Wearable UI库中的WatchViewStub类允许我们为方形和圆形屏幕指定不同的layout。这个类会在运行时检查屏幕形状并inflate相应的layout。
    
   ![Android_Wear.jpg](http://i4.buimg.com/4851/043643268d55cd30.png)
   
    然后为方形和圆形屏幕创建不同的layout文件，在这个例子中，我们需要创建res/layout/rect_activity_wear.xml和res/layout/round_activity_wear.xml两个文件。像创建手持应用的layouts一样定义这些layouts，但需要考虑可穿戴设备的限制。系统会在运行时根据屏幕形状来inflate适合的layout。
    方形或圆形屏幕定义的layouts在WatchViewStub检测到屏幕形状之前不会被inflate，所以你的app不能立即取得它们的view。为了取得这些view，需要在我们的activity中设置一个listener，当屏幕适配的layout被inflate时会通知这个listener：
    
![Android_Wear.jpg](http://i4.buimg.com/4851/80c69ba2eaae0b74.png)

    2.使用感知形状的Layout
    包含在Wearable UI库中的BoxInsetLayout类继承自 FrameLayout，该类允许我们定义一个同时适配方形和圆形屏幕的layout。BoxInsetLayout自动将它的子view放置在圆形屏幕的区域。为了显示在这个区域内，子view需要用下面这些值指定 layout_box属性：一个top、bottom、left和right的组合。比如，"left|top"将子view的左和上边缘定位在figure 2的灰色区域里面。all将所有子view的内容定位在figure 2的灰色区域里面。在方形屏幕上，窗口间隔为0，layout_box属性会被忽略。
    
   ![Android_Wear](http://hukai.me/android-training-course-in-chinese/wearables/ui/02_uilib.png)
   
### List

    Wearable UI库包含了WearableListView类，该类是对可穿戴设备进行优化的List实现。下面的layout使用BoxInsetLayout添加了一个List view到activity中，所以这个List可以正确地显示在圆形和方形两种设备上：
    
![Android_Wear](http://i4.buimg.com/4851/f2ca3258c9a40a92.png)

    每个List选项都由一个图标和一个描述组成。WearableListItemLayout继承LinearLayout以合并两元素到每个List选项。这个layout也实现了 WearableListView.OnCenterProximityListener接口里的方法，以实现在用户在List中滚动时，因WearableListView的事件而改变选项图标颜色和渐隐文字,然后我们在创建一个继承自WearableListView.Adapter的Adapter对WearableListView进行填充。
   
   ![Android_Wear](http://hukai.me/android-training-course-in-chinese/wearables/ui/06_uilib.png)
   
### 2D Picker

    Android Wear中的2D Picker模式允许用户像换页一样从一组选项中导航和选择。
    实现这个模式，我们需要添加一个GridViewPager元素到activity的layout中，然后实现一个继承FragmentGridPagerAdapter类的adapter以提供一组页面。
    
   ![Android_Wear.jpg](http://i2.buimg.com/4851/62b317fff97057a6.png)
   
    Page Adapter提供一组页面以填充GridViewPager部件。要实现这个adapter，需要继承Wearable UI库中的FragmentGridPageAdapter类。picker调用getFragment和getBackground来取得内容以显示到grid的每个位置中，adapter是实现细节取决于我们指定的某组页面。由adapter提供的每个页面是Fragement类型，但是不是所有行都需要有同样数量的页面。设置好了之后将该Adapter设置给GridViewPager就好。

   ![Android_Wear.jpg](http://hukai.me/android-training-course-in-chinese/wearables/ui/07_uilib.png)