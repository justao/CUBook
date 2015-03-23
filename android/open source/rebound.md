#Rebound
android 弹性动画库  
>翻译自[facebook官方文档](http://facebook.github.io/rebound/)

##简介
* Rebound 是一个Java库，用于对弹簧动力学进行建模。反弹回弹模型可用于创建看起来自然的动画，通过将真实世界的物理规律引入到您的应用程序。  
* Rebound不是一个通用的物理库;然而，弹簧动力学可用于驱动各种动画。Rebound的简洁使得它易于集成，并作为构建模块用于创建更复杂的组件，如可切换页面，切换开关和滚动条。  
* Rebound使用与origami相同的弹簧常数,因此很容易将origami交互原型直接转换，用于你的Android应用程序中。

##用法
这是一个使用弹簧模型驱动的视图缩放动画的简单的例子。了解更多内容，请参考[API指南](http://facebook.github.io/rebound/javadocs/)。
```
// Create a system to run the physics loop for a set of springs.
SpringSystem springSystem = SpringSystem.create();
// Add a spring to the system.
Spring spring = springSystem.createSpring();
// Add a listener to observe the motion of the spring.
spring.addListener(new SimpleSpringListener() {
  @Override
  public void onSpringUpdate(Spring spring) {
    // You can observe the updates in the spring
    // state by asking its current value in onSpringUpdate.
    float value = (float) spring.getCurrentValue();
    float scale = 1f - (value * 0.5f);
    myView.setScaleX(scale);
    myView.setScaleY(scale);
  }
});
// Set the spring in motion; moving from 0 to 1
spring.setEndValue(1);
```