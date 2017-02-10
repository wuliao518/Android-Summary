Android顶部特效这里指当页面滚动时，页面顶部的UI变化，下面本文探讨这种效果的通用解决方案   效果如下图所示。

![滚动效果](http://upload-images.jianshu.io/upload_images/186157-3ebabd7564a4d264?imageMogr2/auto-orient/strip)

##洪荒时时代（Android L控件之前的解决方案）
+ ScroollView
当整个页面的根布局是ScrollView的时候，最关键的是获取ScrollView的滚动监听，根据滚动的距离做出效果，setOnScrollChangeListener方法23之后才提供，更好的选择是使用NestedScrollView代替ScrollView
以下是ScrollView的解决方案
```java
public class MyScrollView extends ScrollView {
    public MyScrollView(Context context) {
        super(context);
    }

    public MyScrollView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public MyScrollView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    protected void onScrollChanged(int l, int t, int oldl, int oldt) {
        super.onScrollChanged(l, t, oldl, oldt);
        if (mListener != null) {
            mListener.doSomething(t);
        }
    }

    private OnScrollListener mListener;

    public void setOnScrollListener(OnScrollListener listener) {
        mListener = listener;
    }

    interface OnScrollListener {
        void doSomething(int t);
    }
}
```
+ ListView
ListView通过添加Header的方式，然后监听滚动，也可以做到类似ScrollView的效果
Tip:ScrollView内部不能嵌套ListView，致使情况稍微复杂一点（ScrollView内嵌套ListView的方案不推荐使用，会破坏ListView的混存机制，不如改为LinearLayout）。
![蝉游记效果演示](./ezgif.com-video-to-gif.gif)

```xml

```

![示意图](http://upload-images.jianshu.io/upload_images/163855-7c1918caa208982c.png)





