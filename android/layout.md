#常用的布局
##RelativeLayout
　　相对布局可以理解为某一个元素为参照物来定位的布局方式。由于其比较灵活，能比较好的适应不同分辨率的界面，且相比其他方式能减少布局层级以提高性能，因此推荐使用。在该布局中的组件，主要用以下三类属性来布局：  
###布尔型属性值，元素在整个布局中的位置
  * android:layout_centerHrizontal  水平居中
  * android:layout_centerVertical   垂直居中 
  * android:layout_centerInparent    相对于父元素完全居中 
  * android:layout_alignParentBottom 贴紧父元素的下边缘 
  * android:layout_alignParentLeft   贴紧父元素的左边缘 
  * android:layout_alignParentRight  贴紧父元素的右边缘 
  * android:layout_alignParentTop    贴紧父元素的上边缘 
  * android:layout_alignWithParentIfMissing  如果对应的兄弟元素找不到的话就以父元素做参照物  
  
###id型属性值,用已经布局好的元素（“@id/view_id”）进行定位
  * android:layout_below      在某元素的下方 
  * android:layout_above      在某元素的的上方 
  * android:layout_toLeftOf   在某元素的左边 
  * android:layout_toRightOf  在某元素的右边 

  * android:layout_alignTop   本元素的上边缘和某元素的的上边缘对齐 
  * android:layout_alignLeft  本元素的左边缘和某元素的的左边缘对齐 
  * android:layout_alignBottom 本元素的下边缘和某元素的的下边缘对齐 
  * android:layout_alignRight  本元素的右边缘和某元素的的右边缘对齐
  
###像素值（dp）型属性值,设置元素与其他元素的在四个方向上的边距，这一类值任何元素在任何情况下都是可用的
  * android:layout_marginBottom  离某元素底边缘的距离 
  * android:layout_marginLeft    离某元素左边缘的距离 
  * android:layout_marginRight   离某元素右边缘的距离 
  * android:layout_marginTop     离某元素上边缘的距离
  
##LinearLayout 
　　线性布局，根据其本身制定的方向（android:orientation），将元素从上往下（"horizontal" ）或者从左到右（"vertical"）依次罗列在屏幕上。
　　对于布局中的元素，可以通过设置android:layout_weight属性值指定元素在布局中所占的比例，如水平布局中的宽度或垂直布局中的高度，不过要注意将所对应的属性值（android:width / android:height）设置为0dp.

##FrameLayout

  这个布局可以看成是墙脚堆东西，有一个四方的矩形的左上角墙脚，我们放了第一个东西，要再放一个，那就在放在原来放的位置的上面，这样依次的放，会盖住原来的东西。这个布局比较简单，也只能放一点比较简单的东西。  
##AbsoluteLayout
　　绝对布局犹如div指定了absolute属性，用X,Y坐标来指定元素的位置android:layout_x="20px" android:layout_y="12px" 这种布局方式也比较简单，但是在垂直随便切换时，往往会出问题，而且多个元素的时候，计算比较麻烦。
##TableLayout
　　表格布局类似Html里面的Table。每一个TableLayout里面有表格行TableRow，TableRow里面可以具体定义每一个元素，设定他的对齐方式android:gravity="" 。