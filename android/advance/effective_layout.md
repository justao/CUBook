#Effective Layout
##优化Layout层级
Hierarchy Viewer :在程序运行时分析 Layout  
Lint:优化代码

* compound drawable->包含 ImageView 和 TextView 的 LinearLayout
* merge->FrameLayout根节点(没有使用padding 或者背景)
* 子节点->只有一个子节点（非ScrollView、根节点，没有背景）的节点
* 去掉->没有子节点或者背景的 Layout 
* RelativeLayout 或 GridLayout->嵌套层数太深的Layout

##<include/>重用Layout
使用 <include> 标签，可以在 Layout 中添加可重用的组件。也可以覆写被include元素的根元素的所有 Layout 参数（任何 android:layout_* 属性）.要在 <include> 中覆写某些属性，你必须先覆写 android:layout_height 和 android:layout_width。
使用<merge/> 标签在嵌套 Layout 时取消了 UI 层级中冗余的 ViewGroup 。

## 按需载入视图
```
<ViewStub
    android:id="@+id/stub_import"
    android:inflatedId="@+id/panel_import"
    android:layout="@layout/progress_overlay"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom" />
```
>ViewStub目前不支持使用 <merge/> 标签的 Layout

```
((ViewStub) findViewById(R.id.stub_import)).setVisibility(View.VISIBLE);
// or
View importPanel = ((ViewStub) findViewById(R.id.stub_import)).inflate();
```
一旦 ViewStub 可见或是被 inflate 了，ViewStub 元素就不存在了。取而代之的是被 inflate 的 Layout，其 id 是 ViewStub 上的 android:inflatedId 属性。（ViewStub 的 android:id 属性仅在 ViewStub 可见以前可用）

##优化Listview滑动性能
一个 ViewHolder 对象存储了他的标签下的每个视图。这样你不用频繁查找这个元素。