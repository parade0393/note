​	setContentView()只是new DecorView()然后把我们的布局添加进去，并没有测量，所以再onCreate里为0

绘制->` wm.addView(decor, l);`->`WindowManagerImpl.addView()`
-> `root.setView(view, wparams, panelParentView);`->`requestLayout()`->`scheduleTraversals(),mTraversalRunnable`->`doTraversal()`->`performTraversals()`

```java
private void performTraversals() {
 	// cache mView since it is used so much below...
	final View host = mView;//DecorView----1946
	//->mView.measure(childWidthMeasureSpec, childHeightMeasureSpec);->onMeasure()->计算	 	MeasureSpec
 	performMeasure(childWidthMeasureSpec, childHeightMeasureSpec);//---2541
 }
```
 

