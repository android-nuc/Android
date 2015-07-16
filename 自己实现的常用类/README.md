# 自己实现的常用类

## 提示
这个类中包含的都是源码，可以直接使用。部分类和其他类具有耦合性，需要一起使用。好友一些类需要主题和API的支持。

## 类的介绍
### Screen.java
这是一个关于屏幕相关信息的类，可以通过这个类方便地获得屏幕的高度、宽度、百分比宽度、百分比高度等等信息。  


### SlidingFinishActionBarActivity.java
从左往右滑动结束本Activity。需要使用透明的主题。里面用到了Screen类。

## CircleImageView
圆形的ImageView。需要用到：  
```xml
<declare-styleable name="CircleImageView">  
    <attr name="border_width" format="dimension" />  
    <attr name="border_color" format="color" />  
</declare-styleable>  
```

## FloatingActionButton.java
MaterialDesign中的FAB。