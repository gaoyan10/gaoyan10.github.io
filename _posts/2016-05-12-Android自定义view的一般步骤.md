---
layout: post
title: android自定义attr，obtainStyledAttributes函数的说明
categories: [移动开发]
tags: [Android, attr]
---

### 自定义View的一般步骤

#### 在attrs文件里定义<declare-styleable>，为自定义View添加属性

在

```
<declare-styleable name="MyCustomView">
        <attr name="customColor" format="color" />
        <attr name="customString" format="string"/>
        <attr name="customLength" format="dimension"/>
</declare-styleable>

```

编译后R文件会生成相关属性

```
 public static final int[] MyCustomView = {
     0x7f0100a7, 0x7f0100a8, 0x7f0100a9
 };
 public static final int MyCustomView_customColor = 0;
 public static final int MyCustomView_customLength = 2;
 public static final int MyCustomView_customString = 1;

```

如果attr属性不是声明在declare-styleable里，会生成类似的R文件，但没有MyCustomView数组。


#### 在layout_xml里声明属性的值，同理可以在style里设置属性，也可以在Activity的Theme里指定

一. 在layout里可以声明如下

```
 <com.yan.mycustomview.MyCustomView
        android:layout_width="300dp"
        android:layout_height="300dp"
        android:background="#ccc"
        android:paddingBottom="40dp"
        android:paddingLeft="20dp"
        app:customColor="@color/colorPrimaryDark"
        app:customLength="100dp"
        app:customString="Hello Custom View"
        android:layout_gravity="center"
        />

```

二. style里加入以下声明

```

<\style name="CustomViewStyle">
     <item name="customColor">#ff0000</item>
     <item name="customLength">10dp</item>
     <item name="customString">I from Style</item>
</style>

```

然后在layout xml里引用这个 style，但是layout里的attr优先级更高，会覆盖style里的属性。

三. 把属性写到Theme里

首先去attrs.xml里声明一个attr，作为Theme里的引用，

```
//这个属性是为了在Theme中增加可以配置的style.
<attr name="CustomViewStyle" format="reference"/>

```

修改style文件如下

```

    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
        <item name="CustomViewStyle">@style/CustomViewStyle</item>
    </style>

    <style name="CustomViewStyle">
        <item name="customColor">#ff0000</item>
        <item name="customLength">10dp</item>
        <item name="customString">I from Style</item>
    </style>

```

修改CustomView的构造函数，表示

```
 public MyCustomView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init(attrs, R.attr.CustomViewStyle); //这一行传入自定义StyleAttr作为defStyleAttr
    }

    public MyCustomView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init(attrs, defStyleAttr);
    }
    private void init(AttributeSet attrs, int defStyle) {
        TypedArray array = getContext().obtainStyledAttributes(attrs, R.styleable.MyCustomView, defStyle, 0);
        customColor = array.getColor(R.styleable.MyCustomView_customColor, 0x00ff00);
        customLength = array.getDimension(R.styleable.MyCustomView_customLength, 1);
        customString = array.getString(R.styleable.MyCustomView_customString);
        array.recycle();
        textPaint = new TextPaint();
        textLenth = textPaint.measureText(customString);
        textPaint.setColor(customColor);
        textPaint.setTextSize(customLength);
    }

```

这种的优先级更低，layout xml直接引用的style会覆盖相同的属性。

另外可以在AppTheme直接定义那些属性，不过这种方式优先级更低。

```
<\style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
        <item name="CustomViewStyle">@style/CustomViewStyle</item>
        <item name="customColor">#00ff00</item> //这里优先级最低
    </style>

```

#### 在自定义View的构造函数里获取属性值

在上面的代码init中就是获取属性值的过程。

#### 在onDraw等方法里使用这些属性值

这个自定义组件比较简单，只是使用了属性里的颜色值，字体大小和要显示的内容。

```

array.recycle();
textPaint = new TextPaint();
textLenth = textPaint.measureText(customString);
textPaint.setColor(customColor);
textPaint.setTextSize(customLength); 
}

	 @Override
    public void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawText(customString, getLeft(), getTop(), textPaint);
    }

```

### View类的四个构造方法

#### View(Context context)

Simple constructor to use when creating a view from code。只有在代码里显示的使用new来创建一个View对象时，才调用这个构造方法。

#### View(Context context, AttributeSet attrs)

Constructor that is called when inflating a view from XML.

使用LayoutInflater来创建一个View时会通过反射来调用这个方法，所以自定义View时要保证重写这个构造方法，否则无法获取Layout里配置的属性。其中**attrs的数据包括layoutxml定义的，也包括style="@style/xx"里定义的**

#### View(Context context, AttributeSet attrs, int defStyleAttr)

Perform inflation from XML and apply a class-specific base style from a theme attribute.

这个方法不会被自动调用，只有通过其他构造方法来显式调用。defStyleAttr就是上文在attr里定义在Theme里使用的CustomViewStyle。

#### View(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes)

Perform inflation from XML and apply a class-specific base style from a theme attribute or style resource.

这个方法在SDK版本高于21时才出现。

参考资料

[http://developer.android.com/intl/zh-cn/training/custom-views/create-view.html](http://developer.android.com/intl/zh-cn/training/custom-views/create-view.html)

[http://www.jianshu.com/p/61b79e7f88fc](http://www.jianshu.com/p/61b79e7f88fc)

[http://www.cnblogs.com/angeldevil/p/3479431.html](http://www.cnblogs.com/angeldevil/p/3479431.html)