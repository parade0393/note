文章大部分内容引自Android开发艺术探索

#### MeasureSpec

1. 系统内部是通过MeasureSpec来进行view的测量，但是正常情况下我们无法使用它，但是我们可以使用LayoutParams，在view测量的时候，系统会将LayoutParams在父容器的约束下转换成对应的MeasureSpec,然后再根据这个MeasureSpec确定view测量后的宽高。(也就是说子元素的MeasureSpec是又父容器传递的，是有自身的LayoutParams有父容器的MeasureSpec共同决定的)

2. 对于顶级View（DecorView）其MeasureSpec由自身的LayoutParams和窗口的尺寸共同决定；但是对于普通View(我们布局中的view)来说，其measure过程由viewGroup传递而来(`measureChildWithMargins()`)，继承viewGroup的控件(比如LinearLayout)会在其`onMeasure()`方法中调用`measureChildWithMargins()`，此方法既考虑了padding也考虑了margin，`measureChild`是只考虑了padding，没有考虑margin

   ```java
   /**
    * sdk版本29
    * Ask one of the children of this view to measure itself, taking into
    * account both the MeasureSpec requirements for this view and its padding
    * and margins. The child must have MarginLayoutParams The heavy lifting is
    * done in getChildMeasureSpec. 大部分工作在getChildMeasureSpec中完成
    *
    * @param child The child to measure
    * @param parentWidthMeasureSpec The width requirements for this view
    * @param widthUsed Extra space that has been used up by the parent
    *        horizontally (possibly by other children of the parent)
    * @param parentHeightMeasureSpec The height requirements for this view
    * @param heightUsed Extra space that has been used up by the parent
    *        vertically (possibly by other children of the parent)
    */
   protected void measureChildWithMargins(View child,
           int parentWidthMeasureSpec, int widthUsed,
           int parentHeightMeasureSpec, int heightUsed) {
       final MarginLayoutParams lp = (MarginLayoutParams) child.getLayoutParams();
       final int childWidthMeasureSpec = getChildMeasureSpec(parentWidthMeasureSpec,
               mPaddingLeft + mPaddingRight + lp.leftMargin + lp.rightMargin
                       + widthUsed, lp.width);
       final int childHeightMeasureSpec = getChildMeasureSpec(parentHeightMeasureSpec,
               mPaddingTop + mPaddingBottom + lp.topMargin + lp.bottomMargin
                       + heightUsed, lp.height);
       child.measure(childWidthMeasureSpec, childHeightMeasureSpec);
   }
   ```

   可以看出上述方法会对子元素进行measure，在调用子元素的measure之前会先通过`getChildMeasureSpec`方法来得到子元素的MeasureSpec。**从代码上验证又可以得出子元素的MeasureSpec的创建与父容器的MeasureSpec和子元素本身的LayoutParams有关，此外还和view的padding和margin有关**。

   ```java
   /**
    * 测量孩子最重要的部分，计算出子元素的MeasureSpec并传递给它，此方法表明了一个子view的宽或高的合适的尺寸
    * Does the hard part of measureChildren: figuring out the MeasureSpec to
    * pass to a particular child. This method figures out the right MeasureSpec
    * for one dimension (height or width) of one child view.
    *
    * 目标是从父元素自身的MeasureSpec和子元素的LayoutParams来得出最合适的结果(子元素的MeasureSpec)
    * The goal is to combine information from our MeasureSpec with the
    * LayoutParams of the child to get the best possible results. For example,
    * if the this view knows its size (because its MeasureSpec has a mode of
    * EXACTLY), and the child has indicated in its LayoutParams that it wants
    * to be the same size as the parent, the parent should ask the child to
    * layout given an exact size.
    *
    * @param spec The requirements for this view
    * @param padding The padding of this view for the current dimension and
    *        margins, if applicable
    * @param childDimension How big the child wants to be in the current
    *        dimension
    * @return a MeasureSpec integer for the child
    */
   public static int getChildMeasureSpec(int spec, int padding, int childDimension) {
       int specMode = MeasureSpec.getMode(spec);
       int specSize = MeasureSpec.getSize(spec);
       int size = Math.max(0, specSize - padding);//父元素中剩余的尺寸
       int resultSize = 0;
       int resultMode = 0;
       switch (specMode) {
       // Parent has imposed an exact size on us，父元素是MATCH_PARENT或者确定的尺寸
       case MeasureSpec.EXACTLY:
           if (childDimension >= 0) {
               resultSize = childDimension;
               resultMode = MeasureSpec.EXACTLY;
           } else if (childDimension == LayoutParams.MATCH_PARENT) {//MATCH_PARENT-1
               // Child wants to be our size. So be it.
               resultSize = size;//充满父布局
               resultMode = MeasureSpec.EXACTLY;
           } else if (childDimension == LayoutParams.WRAP_CONTENT) {//WRAP_CONTENT-2
               // Child wants to determine its own size. It can't be
               // bigger than us.  子元素是WRAP_CONTENT是，子元素的尺寸就是父元素剩余的所有尺寸，也就是说会  			充满父布局，这就是为什么我们自定义view时需要处理WRAP_CONTENT来达到自适应尺寸
               resultSize = size;
               resultMode = MeasureSpec.AT_MOST;
           }
           break;
       // Parent has imposed a maximum size on us，父元素是WRAP_CONTENT
       case MeasureSpec.AT_MOST:
           if (childDimension >= 0) {
               // Child wants a specific size... so be it
               resultSize = childDimension;
               resultMode = MeasureSpec.EXACTLY;
           } else if (childDimension == LayoutParams.MATCH_PARENT) {
               // Child wants to be our size, but our size is not fixed.
               // Constrain child to not be bigger than us.
               resultSize = size;//同上
               resultMode = MeasureSpec.AT_MOST;
           } else if (childDimension == LayoutParams.WRAP_CONTENT) {
               // Child wants to determine its own size. It can't be
               // bigger than us.
               resultSize = size;//同上
               resultMode = MeasureSpec.AT_MOST;
           }
           break;
       // Parent asked to see how big we want to be，对尺寸没有任何限制，这种情况一般用于系统内部，表示一种	   测量的状态
       case MeasureSpec.UNSPECIFIED:
           if (childDimension >= 0) {
               // Child wants a specific size... let him have it
               resultSize = childDimension;
               resultMode = MeasureSpec.EXACTLY;
           } else if (childDimension == LayoutParams.MATCH_PARENT) {
               // Child wants to be our size... find out how big it should
               // be
               resultSize = View.sUseZeroUnspecifiedMeasureSpec ? 0 : size;
               resultMode = MeasureSpec.UNSPECIFIED;
           } else if (childDimension == LayoutParams.WRAP_CONTENT) {
               // Child wants to determine its own size.... find out how
               // big it should be
               resultSize = View.sUseZeroUnspecifiedMeasureSpec ? 0 : size;
               resultMode = MeasureSpec.UNSPECIFIED;
           }
           break;
       }
       //noinspection ResourceType
       return MeasureSpec.makeMeasureSpec(resultSize, resultMode);
   }
   ```

   

------

总结：前面已经提到，对于普通View，其MeasureSpec由父容器的MeasureSpec和自身的LayoutParams共同决定。那么针对不同的父容器和View本身不同的LayoutParams，View就可以有多种MeasureSpec。

The last：当View采用固定宽高时，不管父容器的MeasureSpec是什么，View的MeasureSpec都是精确模式(`EXACTLY`)并且其大小遵循LayoutParams的大小；当View的宽/高是`MATCH_PARENT`时，如果父容器是精确模式`EXACTLY`，那么View也是精确模式并且其大小是父容器的剩余空间，如果父容器是最大模式`AT_MOST`，那么View也是最大模式并且大小不会超过父容器的剩余空间；当View的宽/高是`WRAP_CONTENT`，不管父容器的模式是精准模式还是最大模式，View的模式总是最大模式`AT_MOST`并且其大小不能超过父容器的剩余空间---------------------------------------------------------------------------------------------------------------------精确模式`EXACTLY`，父容器已经检测出View的精确大小，这个时候View的最终大小就是SpecSize(`int specSize = MeasureSpec.getSize(spec);`)所指定的值，它对应LayoutParams中的MATCH_PARENT和具体数值这两种模式；最大模式`AT_MOST`，父容器指定了一个可用大小及SpecSize，View的大小不能大于这个值，**具体是什么要看不同View的具体实现**，它对应LayoutParams中的WRAP_CONTENT