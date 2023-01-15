```java
public boolean dispatchTouchEvent(MotionEvent ev) {
    if (mInputEventConsistencyVerifier != null) {
        mInputEventConsistencyVerifier.onTouchEvent(ev, 1);
    }

    // If the event targets the accessibility focused view and this is it, start
    // normal event dispatch. Maybe a descendant is what will handle the click.
    if (ev.isTargetAccessibilityFocus() && isAccessibilityFocusedViewOrHost()) {
        ev.setTargetAccessibilityFocus(false);
    }

    boolean handled = false;
    if (onFilterTouchEventForSecurity(ev)) {
        final int action = ev.getAction();
        final int actionMasked = action & MotionEvent.ACTION_MASK;
		//第一步：处理初始的down事件
        // Handle an initial down.
        if (actionMasked == MotionEvent.ACTION_DOWN) {
            //当开始一个新的触摸手势时，重置之前所有的状态
            // Throw away all previous state when starting a new touch gesture.
            //由于切换应用，应用ANR或者其他一些状态的改变，框架可能会放弃上一个手势的up和cancel事件
            // The framework may have dropped the up or cancel event for the previous gesture
            // due to an app switch, ANR, or some other state change.
            cancelAndClearTouchTargets(ev);//Cancels and clears all touch targets.取消并清楚所有捕获到的事件目标,mFirstTouchTarget会被置空
            resetTouchState();//Resets all touch state in preparation for a new cycle.
            //重置所有触摸状态为新的事件序列做准备
        }

        //第二部：2.1检测ViewGroup是否拦截事件
        // Check for interception.
        final boolean intercepted;
        //是down事件或者存在消耗down事件的目标控件会进入代码块
        if (actionMasked == MotionEvent.ACTION_DOWN
                || mFirstTouchTarget != null) {
            //见补充说明：1
            final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;
            if (!disallowIntercept) {
                intercepted = onInterceptTouchEvent(ev);
                ev.setAction(action); // restore action in case it was changed
            } else {
                intercepted = false;
            }
        } else {
            //不存在消耗down事件的目标控件并且不是初始的down事件时，ViewGroup继续拦截事件
            // There are no touch targets and this action is not an initial down
            // so this view group continues to intercept touches.
            intercepted = true;
        }

        // If intercepted, start normal event dispatch. Also if there is already
        // a view that is handling the gesture, do normal event dispatch.
        if (intercepted || mFirstTouchTarget != null) {
            ev.setTargetAccessibilityFocus(false);
        }
		//第二部：2.2检测是否取消
        // Check for cancelation.
        final boolean canceled = resetCancelNextUpFlag(this)
                || actionMasked == MotionEvent.ACTION_CANCEL;

        // Update list of touch targets for pointer down, if needed.
        //是否支持多点触控，一般为true
        final boolean split = (mGroupFlags & FLAG_SPLIT_MOTION_EVENTS) != 0;
        TouchTarget newTouchTarget = null;//当事件已经做出分发时，记录分发到的控件
        boolean alreadyDispatchedToNewTouchTarget = false;//是否已经分发到了目标
        //第三步：事件未被取消和拦截时，进入核心逻辑，该逻辑会查找消耗事件的newTouchTarget
        if (!canceled && !intercepted) {
            // If the event is targeting accessibility focus we give it to the
            // view that has accessibility focus and if it does not handle it
            // we clear the flag and dispatch the event to all children as usual.
            // We are looking up the accessibility focused host to avoid keeping
            // state since these events are very rare.
            View childWithAccessibilityFocus = ev.isTargetAccessibilityFocus()
                    ? findChildWithAccessibilityFocus() : null;
			//会对ACTION_DOWN和ACTION_POINTER_DOWN(多点触控，有2个及以上手指按下)的情况进行处理
            if (actionMasked == MotionEvent.ACTION_DOWN
                    || (split && actionMasked == MotionEvent.ACTION_POINTER_DOWN)
                    || actionMasked == MotionEvent.ACTION_HOVER_MOVE) {
                //触控点下表，表示这是第几个触控点
                final int actionIndex = ev.getActionIndex(); // always 0 for down
                final int idBitsToAssign = split ? 1 << ev.getPointerId(actionIndex)
                        : TouchTarget.ALL_POINTER_IDS;

                // Clean up earlier touch targets for this pointer id in case they
                // have become out of sync.
                removePointersFromTouchTargets(idBitsToAssign);

                final int childrenCount = mChildrenCount;
                if (newTouchTarget == null && childrenCount != 0) {
                    final float x = ev.getX(actionIndex);
                    final float y = ev.getY(actionIndex);
                    // Find a child that can receive the event.
                    // Scan children from front to back.//对子控件排序(根据z值倒序)
                    final ArrayList<View> preorderedList = buildTouchDispatchChildList();
                    final boolean customOrder = preorderedList == null
                            && isChildrenDrawingOrderEnabled();
                    final View[] children = mChildren;
                    for (int i = childrenCount - 1; i >= 0; i--) {
                        final int childIndex = getAndVerifyPreorderedIndex(
                                childrenCount, i, customOrder);
                        final View child = getAndVerifyPreorderedView(
                                preorderedList, children, childIndex);

                        // If there is a view that has accessibility focus we want it
                        // to get the event first and if not handled we will perform a
                        // normal dispatch. We may do a double iteration but this is
                        // safer given the timeframe.
                        if (childWithAccessibilityFocus != null) {
                            if (childWithAccessibilityFocus != child) {
                                continue;
                            }
                            childWithAccessibilityFocus = null;
                            i = childrenCount - 1;
                        }
						//判断控件是否可以接收事件；判断view是否包含事件的坐标
                        if (!child.canReceivePointerEvents()
                                || !isTransformedTouchPointInView(x, y, child, null)) {
                            ev.setTargetAccessibilityFocus(false);
                            continue;
                        }
						//当view可以接收事件且点击坐标在view空间内
                        newTouchTarget = getTouchTarget(child);
                        if (newTouchTarget != null) {//down事件newTouchTarget必定为null
                            // Child is already receiving touch within its bounds.
                            // Give it the new pointer in addition to the ones it is handling.
                            newTouchTarget.pointerIdBits |= idBitsToAssign;
                            break;
                        }

                        resetCancelNextUpFlag(child);
                        //dispatchTransformedTouchEvent主要作用于调用子view的dispatchTouchEvent分发事件，当传入参数child为空时，ViewGroup自己处理事件
                        if (dispatchTransformedTouchEvent(ev, false, child, idBitsToAssign)) {
                            // Child wants to receive touch within its bounds.
                            mLastTouchDownTime = ev.getDownTime();
                            if (preorderedList != null) {
                                // childIndex points into presorted list, find original index
                                for (int j = 0; j < childrenCount; j++) {
                                    if (children[childIndex] == mChildren[j]) {
                                        mLastTouchDownIndex = j;
                                        break;
                                    }
                                }
                            } else {
                                mLastTouchDownIndex = childIndex;
                            }
                            mLastTouchDownX = ev.getX();
                            mLastTouchDownY = ev.getY();
                            //产生一个新的newTouchTarget
                            newTouchTarget = addTouchTarget(child, idBitsToAssign);
                            //alreadyDispatchedToNewTouchTarget置位true
                            alreadyDispatchedToNewTouchTarget = true;
                            break;
                        }

                        // The accessibility focus didn't handle the event, so clear
                        // the flag and do a normal dispatch to all children.
                        ev.setTargetAccessibilityFocus(false);
                    }
                    if (preorderedList != null) preorderedList.clear();
                }
				//ACTION_DOWN无法找到目标时会导致后续所有的派分都直接传到ViewGroup本身
                if (newTouchTarget == null && mFirstTouchTarget != null) {
                    // Did not find a child to receive the event.
                    // Assign the pointer to the least recently added target.
                    newTouchTarget = mFirstTouchTarget;
                    while (newTouchTarget.next != null) {
                        newTouchTarget = newTouchTarget.next;
                    }
                    newTouchTarget.pointerIdBits |= idBitsToAssign;
                }
            }
        }
		//ACTION_DOWN无法找到目标
        // Dispatch to touch targets.
        if (mFirstTouchTarget == null) {
            // No touch targets so treat this as an ordinary view.
            handled = dispatchTransformedTouchEvent(ev, canceled, null,
                    TouchTarget.ALL_POINTER_IDS);
        } else {
            // Dispatch to touch targets, excluding the new touch target if we already
            // dispatched to it.  Cancel touch targets if necessary.
            TouchTarget predecessor = null;
            TouchTarget target = mFirstTouchTarget;
            while (target != null) {
                final TouchTarget next = target.next;
                if (alreadyDispatchedToNewTouchTarget && target == newTouchTarget) {
                    handled = true;
                } else {
                    final boolean cancelChild = resetCancelNextUpFlag(target.child)
                            || intercepted;
                    if (dispatchTransformedTouchEvent(ev, cancelChild,
                            target.child, target.pointerIdBits)) {
                        handled = true;
                    }
                    if (cancelChild) {
                        if (predecessor == null) {
                            mFirstTouchTarget = next;
                        } else {
                            predecessor.next = next;
                        }
                        target.recycle();
                        target = next;
                        continue;
                    }
                }
                predecessor = target;
                target = next;
            }
        }

        // Update list of touch targets for pointer up or cancel, if needed.
        if (canceled
                || actionMasked == MotionEvent.ACTION_UP
                || actionMasked == MotionEvent.ACTION_HOVER_MOVE) {
            resetTouchState();
        } else if (split && actionMasked == MotionEvent.ACTION_POINTER_UP) {
            final int actionIndex = ev.getActionIndex();
            final int idBitsToRemove = 1 << ev.getPointerId(actionIndex);
            removePointersFromTouchTargets(idBitsToRemove);
        }
    }

    if (!handled && mInputEventConsistencyVerifier != null) {
        mInputEventConsistencyVerifier.onUnhandledEvent(ev, 1);
    }
    return handled;
}
```

------

```java
@Override
public void requestDisallowInterceptTouchEvent(boolean disallowIntercept) {

    if (disallowIntercept == ((mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0)) {
        // We're already in this state, assume our ancestors are too
        return;
    }

    if (disallowIntercept) {
        mGroupFlags |= FLAG_DISALLOW_INTERCEPT;
    } else {
        mGroupFlags &= ~FLAG_DISALLOW_INTERCEPT;
    }

    // Pass it up to our parent
    if (mParent != null) {
        mParent.requestDisallowInterceptTouchEvent(disallowIntercept);
    }
}
```

------

```java
public boolean onInterceptTouchEvent(MotionEvent ev) {
    //这里一般情况下都进不去if代码快，所以返回false
    if (ev.isFromSource(InputDevice.SOURCE_MOUSE)
            && ev.getAction() == MotionEvent.ACTION_DOWN
            && ev.isButtonPressed(MotionEvent.BUTTON_PRIMARY)
            && isOnScrollbarThumb(ev.getX(), ev.getY())) {
        return true;
    }
    return false;
}
```

------

```java
private static final class TouchTarget {
        // The touched child view.被点击的子控件，即消耗事件的目标控件
        public View child;
	
    	//目标控件捕获的所有触控点的触控点ID的组合位掩码
        // The combined bit mask of pointer ids for all pointers captured by the target.
        public int pointerIdBits;
    //为了区分多点触控时不同的触控点，每一个触控点都会携带一个pointerId。而pointerIdBits即是所有被目标控件消耗的触控点的pointerId的组合。即pointerIdBits包含一个或以上的pointerId数据

        // The next target in the target list.
        public TouchTarget next;//记录下一个TouchTarget对象，由此组成链表
}
```

#### 补充说明：

1. requestDisallowInterceptTouchEvent
   1. 当ViewGroup拿到down事件时，会重置mGroupFlags的值，通过`resetTouchState`方法--`mGroupFlags &= ~FLAG_DISALLOW_INTERCEPT`，这里再判断`mGroupFlags & FLAG_DISALLOW_INTERCEPT`肯定为0，所以会执行`onInterceptTouchEvent`方法
   2. ViewGroup的`onInterceptTouchEvent`默认是返回false的,所以down事件就会传递到子view
   3. 当调用父viewGroup的`requestDisallowInterceptTouchEvent`的时候，当参数为true的时候，会执行`mGroupFlags |= FLAG_DISALLOW_INTERCEPT;`，接下来在move事件的时候`final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0`计算变成`mGroupFlags | FLAG_DISALLOW_INTERCEPT & FLAG_DISALLOW_INTERCEPT`,而这个计算值肯定为FLAG_DISALLOW_INTERCEPT`的值(0x8000)，所以disallowIntercept为true，所以直接接入else方法intercepted =false，接下来的move，up事件都能传递到子view
   4. 我们一个手势的操作，会经历down，move，up等事件，子view调用`requestDisallowInterceptTouchEvent(true)`的前提是子view能拿到事件，比如我们在down事件的时候调用了次方法，在接下来的move，up事件都会传递到子view上，如果在子view的move方法调用，那么要确认父view在move的过程中，能将事件传递给子view
      1. move事件唯一和父类有关系的就是父类的onTerceptTouchEvent方法
   
2. 事件处理的本质：
   1. 一个事件必须要先经过传递流程才会经历处理流程，在这个过程中我们可以修改返回值来控制流程
      1. 对于事件的传递：返回true，表示拦截不再**往下**传递，返回false不拦截继续往下传递，主要针对`onInterceptTouchEvent`方法
      2. 对于事件的处理，返回true，表示拦截，不再**往上**传递，返回false则继续向上传递，针对的就是`onTouchEvent`方法

3. TouchTarget
   1. mFirstTouchTarget是viewGroup的全局变量(First touch target in the linked list of touch targets.)
   2. newTouchTarget是dispatchTouchEvent方法的局部变量
   3. ACTION_DOWN--这个很好理解，当屏幕检测到有手指按下之后就触发到这个事件
   4. ACTION_POINTER_DOWN：这个是实现多点的关键，当屏幕检测到有多个手指同时按下之后，就触发了这个事件。

   ------

   #### 源码精简说明：ViewGroup-dispatchTouchEvent

   源码整体上可分为四部：

   - 如果是down事件，则重置状态,这一步之前有一个重要局部变量`boolean handled = false`

     ```java
     boolean handled = false;
     ...
     // Handle an initial down.
     if (actionMasked == MotionEvent.ACTION_DOWN) {
         // Throw away all previous state when starting a new touch gesture.
         // The framework may have dropped the up or cancel event for the previous gesture
         // due to an app switch, ANR, or some other state change.
         cancelAndClearTouchTargets(ev);
         resetTouchState();
     }
     ```

   - 检测ViewGroup是否拦截和取消事件,在这一步，有两个重要的局部变量intercepted和canceled

     ```java
     // Check for interception.
     final boolean intercepted;
     // Check for cancelation.
     final boolean canceled = resetCancelNextUpFlag(this) || actionMasked == MotionEvent.ACTION_CANCEL;
     ```

   - 核心逻辑->查找消耗事件的newTouchTarget，这一步之前有两个重要的局部变量`TouchTarget newTouchTarget = null;`和`boolean alreadyDispatchedToNewTouchTarget = false;`

     ```java
     if (!canceled && !intercepted) {//判断1
     	//第一，对down事件做判断,不是down事件就直接跳出整个的核心逻辑
     	if (actionMasked == MotionEvent.ACTION_DOWN
             || (split && actionMasked == MotionEvent.ACTION_POINTER_DOWN)
             || actionMasked == MotionEvent.ACTION_HOVER_MOVE) {//判断2
             
             //第二，循环遍历子View，查找newTouchTarget
             final int childrenCount = mChildrenCount;
             if (newTouchTarget == null && childrenCount != 0) {//判断3
                 for (int i = childrenCount - 1; i >= 0; i--) {//循环1
                     ...
                      //第2.1这里如果TouchTarget链表的头部是child，则直接跳出循环遍历
                     newTouchTarget = getTouchTarget(child);
                     if (newTouchTarget != null) {
                         newTouchTarget.pointerIdBits |= idBitsToAssign;
                         break;
                     }
                     if (dispatchTransformedTouchEvent(ev, false, child, idBitsToAssign)) {//判断4
                         ....
                          //第2.2添加之前判断newTouchTarget头部，添加之后判断为null，更新指针
                         newTouchTarget = addTouchTarget(child, idBitsToAssign);
                         alreadyDispatchedToNewTouchTarget = true;
                         break;//传入的child消耗了事件就会跳出循环遍历
                     }//判断4
                     //第2.3newTouchTarget为null,则更新newTouchTarget为链表的尾部
                     if (newTouchTarget == null && mFirstTouchTarget != null) {//判断5
                         // Did not find a child to receive the event.
                          // Assign the pointer to the least recently added target.
                         newTouchTarget = mFirstTouchTarget;
                          while (newTouchTarget.next != null) {
                               newTouchTarget = newTouchTarget.next;
                          }
                     }//判断5
                     
                 }//循环1
                 
             }//判断3
             
          }//判断2
      }//判断1
     ```

     说明：

     1. newTouchTarget永远指向链表的尾部(链表只有一个元素时，尾部为null，newTouchTarget就是链表的头部)
     2. 点击事件是在ACTION_UP事件产生的

   - 非ACTION_DOWN类型的事件的处理

     ```java
      if (mFirstTouchTarget == null) {
         // No touch targets so treat this as an ordinary view.
          //第三个参数为null，则ViewGroup本身处理
         handled = dispatchTransformedTouchEvent(ev, canceled, null, TouchTarget.ALL_POINTER_IDS);
     }else{
          //子view处理
      }
     ```

     