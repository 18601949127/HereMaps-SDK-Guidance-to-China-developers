
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221153348342.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)

@[TOC](目录)

## 前言

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221153232320.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)

说起导航地图，无论是在手机移动端还是在车机端，国内的车主肯定使用得最多的无疑是高德和百度，高德一直深耕定位和导航功能，百度则在O2O领域布局已久，在百度地图里面可以看到等多的周边商家服务。在车载前装导航领域，国内汽车制造商似乎并不准备将这块蛋糕拱手交给像高德、百度、谷歌这样的第三方服务商，毕竟地图作为基础能力，在智能驾驶、自动驾驶和移动互联网服务等方面都有着极为重要的作用，汽车制造商更希望拥有自主地图服务。这也是为什么宝马、奥迪和戴姆勒组成的财团，会斥巨资收购海外地图数据公司Here的原因。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022115331063.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)


  HERE Technologies原本是诺基亚旗下的海外地图数据供应商，被宝马奥迪和戴姆勒联合收购以后也在试图进入中国这个全球最大的汽车消费国，包括和四图维新合作完善中国的地图数据和动态信息，提升在自动驾驶领域的研发进度，近两年也越来越开放，去年开始和高德合作帮助后者完善海外的地图数据。
  
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221153321928.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)

  我在之前的文章 [仿滴滴出行打车APP界面 增加RFID认证、海外版、司机证件号码识别功能](https://blog.csdn.net/weixin_37734988/article/details/92796055)提到了国内手机在谷歌地图被阉割的情况下，拿到国外如何使用的问题。很多国外开发的应用落地中国也需要招聘人员负责地图和定位模块的本地化工作。这里就介绍一下 Here地图在Android应用上的集成，我前东家的德国开发同事对Here地图的实用性评价还是非常高的。

这里先看一下整体的效果：


![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221230549723.gif#pic_center)

下面这个是加上卫星地图一起显示的效果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221230633620.gif#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221230651827.gif#pic_center)

卫星地图模式很像Google Earth.

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221231115985.jpeg#pic_center)


下面进入主题，先介绍一下 Here地图的SDK：
## 1.  Here地图Android SDK几个版本对比

Here地图服务对开发者开放下下面几种不同的版本：
 
1. Lite Edition: 这个是精简版SDS，主要是给低ROM和比较差的渲染能力的手机型号使用，考虑到当前手机更新换代的频率，非常不建议开发者使用Lite版本。
2. Explore Edition: 这个版本非常适合非付费开发者体验Here服务，它可以提供多个MapView 实例，具备3D镜头，也允许开发者设置不同Map层级的绘制顺序，或者为MapView定制不同地图组件。我们下面的实例就使用的 Explore Edition。
3. Navigate Edition: 相比上面介绍的Explore Edition，这个版本增加了 turn-by-turn navigation功能，也就是针对每一个路口，都会提供专门的转向提示，更适合真实驾驶环境中使用。
4. Premium Edition： 这个是功能最丰富的版本，增加了Here地图非常骄傲的离线导航和离线路径规划功能，也提供了专门针对卡车，私家车和行人步行的导航模块，也有高速费用计算，限速，**坡度**和**弯道曲率**这些更深维度的数据。

国内的开发者也可以去我的GitHub上去下载 Explore版本的Android SDK：[SDK下载地址](https://github.com/18601949127)


## 2. 申请Here地图服务的 Credentials key

1. 就像国内的高德地图和百度地图SDK一样，选定了Here地图服务的SDK版本，要想使用这个选定的SDK版本，还需要申请ACCESS KEY ID 和 ACESS KEY SECRET。

```java
ACESS KEY ID ：是开发者在Here账号对应的唯一ID号码
ACESS KEY SECRET：有点像高德地图和百度地图的 Service Key，需要注意的是，对于Lite 和 Explore 版本，Here 服务的KEY SECRET 可以用在多个应用，
```


[申请地址](https://developer.here.com/projects)

![Here账号的Credentials申请](https://img-blog.csdnimg.cn/20210221181659870.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)


2. 之后点击你新建的 Project，Here会引导你下载一个包含Key的文档到你的电脑里。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221182503270.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)

3. 下载完的文档是这样的，里面可以看到 Key ID 和 SECRET

![Credentials](https://img-blog.csdnimg.cn/20210221183006644.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)
***这里为了方便国内的开发者试用，大家可以直接用我已经申请好的：*** 


```kotlin
        <meta-data android:name="com.here.sdk.access_key_id" android:value="3wjgOaM6HWUI6V_dnWi_Yg" />
        <meta-data android:name="com.here.sdk.access_key_secret" android:value="dVOGgmxCAOdvZpH6EmQ3O8cKVqCAWvrRunoLUngaqU5HJg1cBHPtHdWoDXDRGFHffLs3HvDjwXlbH9ML3g1KdQ" />
```

## 2. 开始集成第一个基础地图
有了Here 地图服务的SDK和 Credentials, 就可以开始集成第一个基础地图了

1. 首先把Here的.aar文件放置到app/libs 文件夹下：
  
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210305153659794.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)


 2. module 的Build.gradle 下记得把 fileTree里面的 .aar文件路径加上，之后记得 Sync一下 Project。 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221184853805.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)

3. 在 Manifest.xml 文件里面添加Here服务的Credentials： 

	![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022118545379.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)
上面也提到了，为了方便国内的开发者试用，大家可以直接用我已经申请好的的Credentials。

```kotlin
        <meta-data android:name="com.here.sdk.access_key_id" android:value="3wjgOaM6HWUI6V_dnWi_Yg" />
        <meta-data android:name="com.here.sdk.access_key_secret" android:value="dVOGgmxCAOdvZpH6EmQ3O8cKVqCAWvrRunoLUngaqU5HJg1cBHPtHdWoDXDRGFHffLs3HvDjwXlbH9ML3g1KdQ" />

```
记得申请权限： 
 

```kotlin
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

4. Here 地图的MapView
  和高德地图和百度地图一样， Here地图服务也使用MapView 来展示地图界面。
  

```kotlin
com.here.sdk.mapview.MapView
```

```kotlin
<com.here.sdk.mapview.MapView
   android:id="@+id/map_view"
   android:layout_width="match_parent"
   android:layout_height="match_parent">
</com.here.sdk.mapview.MapView>

```
然后直接在Activity 里面调用这个mapView的 onCreate() 方法，这个方法会执行MapView的初始化渲染工作。（Here地图的渲染引擎是在当前Activity 或者 Fragment 的 onResume() 方法中加载 ，在同一个layout 文件里的 MapView实例都会被一起渲染）

```kotlin
    mapView = findViewById(R.id.map_view);
    mapView.onCreate(savedInstanceState);
```

Here地图的 loadMapScene() 方法允许开发者对 MapView界面进行客制化的渲染,比如下面的图，大家可以看到MapScheme类提供了一共7中不同的视觉效果。

1. NORMAL_DAY 
2. GREY_NIGHT
3. GREY_DAY
4. HYBRID_DAY
5. HYBRID_NIGHT
6. NORMAL_NIGHT 
7. SATELLITE 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221204829869.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)

这里分别看一下效果：

1.夜空灰色的效果

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221220615905.gif#pic_center)

下面是截图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210305153103520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)




2. 日间灰的效果
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221221742196.gif#pic_center)

日间灰的效果图： 

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030515332668.png#pic_center)


3. 第四个按键显示卫星地图的效果

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221222236684.gif#pic_center)


相应的，对mapVIew 实例设置`setLayerState(MapScene.Layers.TRAFFIC_FLOW, MapScene.LayerState.VISIBLE);` 可以像高德和百度那样添加实时交通状况图层

```kotlin
    private void enableTrafficVisualization() {
        mapView.getMapScene().setLayerState(MapScene.Layers.TRAFFIC_FLOW, MapScene.LayerState.VISIBLE);
        mapView.getMapScene().setLayerState(MapScene.Layers.TRAFFIC_INCIDENTS, MapScene.LayerState.VISIBLE);
    }

    private void disableTrafficVisualization() {
        mapView.getMapScene().setLayerState(MapScene.Layers.TRAFFIC_FLOW, MapScene.LayerState.HIDDEN);
        mapView.getMapScene().setLayerState(MapScene.Layers.TRAFFIC_INCIDENTS, MapScene.LayerState.HIDDEN);
    }
```

## 结语

好了，以上就是Here地图的集成方式。

国内的开发者还要注意Here默认使用 WGS坐标系，需要转换才能适配国内的GCJ坐标系。


*我的GitHub地址：https://github.com/18601949127
CSDN博客：https://blog.csdn.net/weixin_37734988*



![在这里插入图片描述](https://img-blog.csdnimg.cn/20210131223121254.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzczNDk4OA==,size_16,color_FFFFFF,t_70#pic_center)

