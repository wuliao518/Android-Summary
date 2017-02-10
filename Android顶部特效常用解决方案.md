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
<<<<<<< HEAD

=======
![蝉游记效果演示](./res/ezgif.com-video-to-gif.gif)
>>>>>>> remotes/origin/master

```xml

```

![siyitu](http://upload-images.jianshu.io/upload_images/163855-7c1918caa208982c.png)












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
下面举蝉游记的一个例子，刚做Android没多久时遇到的，通过在ListView套一个自定义FrameLayout，然后自己处理手势和事件分发，越写越麻烦，后来通过反编译知道了蝉游记的做法，印象深刻。
![蝉游记效果演示](./res/ezgif.com-video-to-gif.gif)

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/content"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">

    <ListView
        android:id="@+id/article_listview"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:cacheColorHint="@android:color/transparent"
        android:divider="@null"
        android:dividerHeight="0.0dip"
        android:fadingEdge="none"
        android:fastScrollEnabled="false"
        android:footerDividersEnabled="false"
        android:headerDividersEnabled="false"
        android:listSelector="@android:color/transparent"
        android:scrollbarStyle="outsideOverlay"
        android:smoothScrollbar="true" />

    <FrameLayout
        android:id="@+id/article_header"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content">
        <ImageView
            android:layout_width="fill_parent"
            android:layout_height="240.0dip"
            android:src="@mipmap/test"
            android:scaleType="centerCrop" />
    </FrameLayout>
</FrameLayout>
```

```java
View headerView = getLayoutInflater().inflate(R.layout.list_header, null);
//这里为了简单直接设置ListView的一个Header（里面是一个简单布局，只是为了占位置）高度与ListView上面的布局一致
mListView.addHeaderView(headerView);
tempView = findViewById(R.id.article_header);
mListView.setOnScrollListener(new AbsListView.OnScrollListener() {
    @Override
    public void onScrollStateChanged(AbsListView absListView, int i) {

    }

    @Override
    public void onScroll(AbsListView absListView, int i, int i1, int i2) {
        int y = getScrollY();
        //这里只是为了提供思路，做了最简单的实现
        tempView.setTranslationY(-y);
    }
});
public int getScrollY() {
        View c = mListView.getChildAt(0);
        if (c == null) {
            return 0;
        }
        int firstVisiblePosition = mListView.getFirstVisiblePosition();
        int top = c.getTop();
        return -top + firstVisiblePosition * c.getHeight();
    }

```
+ 复杂模式
以上所举的例子都相对简单，还存在很多相对复杂的，下图所示就是一种，当ViewPager内有ListView的时候，它的滚动会响应TabLayout和HeaderView的滚动，洪荒时代的做法是在最外层放置一个自定义View，然后自己控制事件和手势的分发，不仅写起来很吗发，而且通用性能差，写得想让大喊MMP，这里就不讲解了，因为接下来就将迎来救世主。
![示意图](http://upload-images.jianshu.io/upload_images/163855-7c1918caa208982c.png)






