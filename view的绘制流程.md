ttView的绘制流程主要包括measure，layout，draw三大流程，measure用来确定view的测量宽/高，layout用来确定view的最终宽/高和四个顶点的位置，而draw则将View绘制到屏幕上

------

### Measure

1. 如果只是一个原始的View，那么通过meaure方法就完成了其测量过程，如果是一个ViewGroup，除了完成自己的测量过程外，还会遍历去调用所有子View的measure方法，各个子元素再去递归调用这个流程

2. view的measure过程由measure方法来完成，measure方法是一个final方法，这意味着子类不能重写此方法，在View的measure方法中回去调用view的onMeasure方法

   <div align=center><img src="https://img-blog.csdnimg.cn/202002250919472.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BhcmFkZTAzOTM=,size_16,color_FFFFFF,t_70"/></div>

3. onMeasure方法如下(直接继承原始View)

   ```java
   /**
    * <p>
    * 测量视图及其内容以确定测量的宽度和测量的高度,这个方法由measure方法调用，并且应该被子类重写，以
    * 准确和有效的测量其内容
    * Measure the view and its content to determine the measured width and the
    * measured height. This method is invoked by {@link #measure(int, int)} and
    * should be overridden by subclasses to provide accurate and efficient
    * measurement of their contents.
    * </p>
    *
    * <p>
    * 当子类重写了此方法，则必须调用setMeasuredDimension方法来存储view的测量宽/高，如果不这样做将会触发由
    *measure方法抛出的异常throw new IllegalStateException("View with id " + getId() + ": "
                           + getClass().getName() + "#onMeasure() did not set the"
                           + " measured dimension by calling"
                           + " setMeasuredDimension()");
    * <strong>CONTRACT:</strong> When overriding this method, you
    * <em>must</em> call {@link #setMeasuredDimension(int, int)} to store the
    * measured width and height of this view. Failure to do so will trigger an
    * <code>IllegalStateException</code>, thrown by
    * {@link #measure(int, int)}. Calling the superclass'
    * {@link #onMeasure(int, int)} is a valid use.
    * </p>
    *
    * <p>
    * The base class implementation of measure defaults to the background size,
    * unless a larger size is allowed by the MeasureSpec. Subclasses should
    * override {@link #onMeasure(int, int)} to provide better measurements of
    * their content.
    * </p>
    *
    * <p>
    * If this method is overridden, it is the subclass's responsibility to make
    * sure the measured height and width are at least the view's minimum height
    * and width ({@link #getSuggestedMinimumHeight()} and
    * {@link #getSuggestedMinimumWidth()}).
    * </p>
    *
    * @param widthMeasureSpec horizontal space requirements as imposed by the parent.
    *                         The requirements are encoded with
    *                         {@link android.view.View.MeasureSpec}.
    * @param heightMeasureSpec vertical space requirements as imposed by the parent.
    *                         The requirements are encoded with
    *                         {@link android.view.View.MeasureSpec}.
    *
    * @see #getMeasuredWidth()
    * @see #getMeasuredHeight()
    * @see #setMeasuredDimension(int, int)
    * @see #getSuggestedMinimumHeight()
    * @see #getSuggestedMinimumWidth() 取决于背景尺寸和minWidth的二者中的较大值minWidth默认为0
    * @see android.view.View.MeasureSpec#getMode(int)
    * @see android.view.View.MeasureSpec#getSize(int)
    */
   protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
       setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(), widthMeasureSpec),
               getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec));
   }
   ```

   onMeasure方法中必须调用`setMeasuredDimension`方法，否则将抛出异常，在这里我们只需关注`getDefaultSize`方法

   ```java
    /**
     * 一个用来返回默认大小的实用方法，如果MeasureSpec没有提供任何限制则用系统提供的尺寸(背景尺寸或者minWidth
     * 的尺寸取二者最大);如果MeasureSpec允许，尺寸将会变大
     * Utility to return a default size. Uses the supplied size if the
     * MeasureSpec imposed no constraints. Will get larger if allowed
     * by the MeasureSpec.
     *
     * @param size Default size for this view
     * @param measureSpec Constraints imposed by the parent
     * @return The size this view should be.
     */
    public static int getDefaultSize(int size, int measureSpec) {
        int result = size;
        int specMode = MeasureSpec.getMode(measureSpec);
        int specSize = MeasureSpec.getSize(measureSpec);
        switch (specMode) {
        case MeasureSpec.UNSPECIFIED:
            result = size;
            break;
        case MeasureSpec.AT_MOST:
        case MeasureSpec.EXACTLY:
            result = specSize;
            break;
        }
        return result;
   ```

   可以看出getDefaultSize的逻辑很简单，我们只需要看`AT_MOST`和`EXACTLY`这两种情况，返回的大小就是MeasureSpec中的specSize，也就是View测量后的大小，直接继承view的自定义控件需要重写onMeasure方法并设置WRAP_CONTENT时自身的大小，否则在布局中使用WRAP_CONTENT就相当于MATCH_PARENT

4. ViewGoup的measure过程

   对于ViewGroup来说，除了完成自己的measure过程外，还会遍历去调用所有子元素的measure方法，各个子元素再去递归执行这个过程，ViewGroup是一个抽象类，没有重写onMeasure方法，但是提供了`measureChildren`，`measureChild`，`measureChildWithMargins`方法

   ```java
   /**
    * Ask all of the children of this view to measure themselves, taking into
    * account both the MeasureSpec requirements for this view and its padding.
    * We skip children that are in the GONE state The heavy lifting is done in
    * getChildMeasureSpec.
    *
    * @param widthMeasureSpec The width requirements for this view
    * @param heightMeasureSpec The height requirements for this view
    */
   protected void measureChildren(int widthMeasureSpec, int heightMeasureSpec) {
       final int size = mChildrenCount;
       final View[] children = mChildren;
       for (int i = 0; i < size; ++i) {
           final View child = children[i];
           if ((child.mViewFlags & VISIBILITY_MASK) != GONE) {
               measureChild(child, widthMeasureSpec, heightMeasureSpec);
           }
       }
   }
   
   /**
    * Ask one of the children of this view to measure itself, taking into
    * account both the MeasureSpec requirements for this view and its padding.
    * The heavy lifting is done in getChildMeasureSpec.
    *
    * @param child The child to measure
    * @param parentWidthMeasureSpec The width requirements for this view
    * @param parentHeightMeasureSpec The height requirements for this view
    */
   protected void measureChild(View child, int parentWidthMeasureSpec,
           int parentHeightMeasureSpec) {
       final LayoutParams lp = child.getLayoutParams();
       final int childWidthMeasureSpec = getChildMeasureSpec(parentWidthMeasureSpec,
               mPaddingLeft + mPaddingRight, lp.width);
       final int childHeightMeasureSpec = getChildMeasureSpec(parentHeightMeasureSpec,
               mPaddingTop + mPaddingBottom, lp.height);
       child.measure(childWidthMeasureSpec, childHeightMeasureSpec);
   }
   ```

   measureChild的思想就是就是取出子元素的LayoutParams，然后再通过`getChildMeasureSpec`来创建子元素的MeasureSpec从而传递给子元素的measure方法。ViewGroup并没有onMeasure方法，这是因为ViewGroup是一个抽象类，其测量过程需要各个子类去具体实现，比如LinearLayout、RelativeLayout都有不同的布局特征，它们在onMeasure中都会调用`measureChildWithMargins`或者`measureChild`去传递测量过程，最终传递到单一View的onMeasure，**最终父元素会先测量子元素，测量完成子元素后根据子元素的测量情况来测量自己的大小**

   ------
   
   #### 获取View的宽高的一种方式

​	手动调用View的measure方法`view.measure(int widthMeasureSpec, int heightMeasureSpec)`，这种方法比较复杂，这里要分情况处理，根据View的LayoutParams区分：

1. match_parent

   直接放弃，根据`getChildMeasureSpec`方法得知，构造此种MeasureSpec需要知道父元素剩余可用空间，而这个我们是无从得知的，所以理论上是不可能测量出View的大小

2. 具体的竖直(dp/px)

   比如宽高都是100px，如下measure

   ```java
   int widthMeasureSpec = View.MeasureSpec.makeMeasureSpec(100, View.MeasureSpec.EXACTLY);
   int heightMeasureSpec = View.MeasureSpec.makeMeasureSpec(100, View.MeasureSpec.EXACTLY);
   view.measure(widthMeasureSpec,heightMeasureSpec);
   ```

3. wrap_content 如下measure

   ```java
   int widthMeasureSpec = View.MeasureSpec.makeMeasureSpec((1 << 30) -1, View.MeasureSpec.AT_MOST);
   int heightMeasureSpec = View.MeasureSpec.makeMeasureSpec((1 << 30) -1, View.MeasureSpec.AT_MOST);
   view.measure(widthMeasureSpec,heightMeasureSpec);
   ```

   注意到(1 << 30) -1，因为View的尺寸用30位二进制表示，也就是最大是30个1（即2^30-1），也就是(1 << 30) -1，在最大化模式下，我们用View理论上能支持的最大值去构造MeasureSpec是合理的

### layout过程

Layout的作用是ViewGroup用来确定子元素的位置，当ViewGroup的位置被确定后，它会在onLayout中会遍历所有子元素并调用其layout方法，在layout方法中，onLayout又会被调用。**layout方法确定view本身的位置，而onLayout方法确定所有子元素的位置(当然单一View会做一下其它事情参考TextView)**。先看View的layout方法如下

```java
/**
 * 为view及其子view分匹配大小和位置
 * Assign a size and position to a view and all of its
 * descendants
 *
 * 这是布局机制的第二阶段，第一阶段是测量(measure),在这一阶段,每个父级视图都会调用子元素的layout方法以确定子元素
 * 的位置
 * <p>This is the second phase of the layout mechanism.
 * (The first is measuring). In this phase, each parent calls
 * layout on all of its children to position them.
 * This is typically done using the child measurements
 * that were stored in the measure pass().</p>
 *
 * 派生类不应该重写此方法,派生类如果有子元素，那应该重写onLayout方法，在onLayout方法中去调用每个子元素
 * 的layout方法
 * <p>Derived classes should not override this method.
 * Derived classes with children should override
 * onLayout. In that method, they should
 * call layout on each of their children.</p>
 *
 * @param l Left position, relative to parent //相对父布局
 * @param t Top position, relative to parent
 * @param r Right position, relative to parent
 * @param b Bottom position, relative to parent
 */
@SuppressWarnings({"unchecked"})
public void layout(int l, int t, int r, int b) {
    if ((mPrivateFlags3 & PFLAG3_MEASURE_NEEDED_BEFORE_LAYOUT) != 0) {
        onMeasure(mOldWidthMeasureSpec, mOldHeightMeasureSpec);
        mPrivateFlags3 &= ~PFLAG3_MEASURE_NEEDED_BEFORE_LAYOUT;
    }
    int oldL = mLeft;
    int oldT = mTop;
    int oldB = mBottom;
    int oldR = mRight;//四个顶点的位置
    boolean changed = isLayoutModeOptical(mParent) ?
            setOpticalFrame(l, t, r, b) : setFrame(l, t, r, b);
    if (changed || (mPrivateFlags & PFLAG_LAYOUT_REQUIRED) == PFLAG_LAYOUT_REQUIRED) {
        onLayout(changed, l, t, r, b);//调用onLayout方法
        if (shouldDrawRoundScrollbar()) {
            if(mRoundScrollbarRenderer == null) {
                mRoundScrollbarRenderer = new RoundScrollbarRenderer(this);
            }
        } else {
            mRoundScrollbarRenderer = null;
        }
        mPrivateFlags &= ~PFLAG_LAYOUT_REQUIRED;
        ListenerInfo li = mListenerInfo;
        if (li != null && li.mOnLayoutChangeListeners != null) {
            ArrayList<OnLayoutChangeListener> listenersCopy =
                    (ArrayList<OnLayoutChangeListener>)li.mOnLayoutChangeListeners.clone();
            int numListeners = listenersCopy.size();
            for (int i = 0; i < numListeners; ++i) {
                listenersCopy.get(i).onLayoutChange(this, l, t, r, b, oldL, oldT, oldR, oldB);
            }
        }
    }
    //........后面省略
```

从代码注释也可以看出，我们自定义View只需重写onLayout方法，在onLayout方法中去调用子元素(如果有)的layout方法；layout的大致流程如下：首先会通过`setOpticalFrame`或者`setFrame`来设置View的四个顶点的位置，View的四个顶点一旦确定，那么View在父容器中的位置也就确定了，接着会调用onLayout方法，这个方法用来确定每个子元素的位置，和onMeasure类似，onLayout的具体实现同样和具体的布局有关，所以View和ViewGroup均没有真正实现onLayout方法

### draw过程

draw的过程比较简单但是最能玩出花样的，它的作用是将View绘制到屏幕上，View的绘制过程一般遵循以下几步

1. Draw the background，绘制背景
2. If necessary, save the canvas' layers to prepare for fading,如果有需要保存画布的图层
3.  Draw view's content，绘制自己的内容(onDraw)
4. Draw children 绘制children(diapatchDraw)
5. If necessary, draw the fading edges and restore layers
6. Draw decorations (scrollbars for instance)绘制装饰