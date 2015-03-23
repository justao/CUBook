#Butter Knife
将字段和方法绑定到Android视图，使用注释处理为你生成样板代码。  
##简介
* 在字段上使用@InjectView，杜绝findViewById调用。
* 使用@InjectViews将多个视图分组。一次actions、setters、properties操作同时作用在所有视图上。  
* 通过用@OnClick等注解方法消除匿名内部类监听器。

```
class ExampleActivity extends Activity {
  @InjectView(R.id.user) EditText username;
  @InjectView(R.id.pass) EditText password;

  @OnClick(R.id.submit) void submit() {
    // TODO call server...
  }

  @Override public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.simple_activity);
    ButterKnife.inject(this);
    // TODO Use fields...
  }
}
```
用@InjectView和视图ID注释字段，以便ButterKnife找到并自动转换为布局中相应的视图。
抛掉速度较慢的反射，代码将被自动生成以便于视图查找。调用inject用于代理这些生成的代码，你可以看到并进行调试。
上面的例子生成的代码大致相当于如下所示的代码：

```
public void inject(ExampleActivity activity) {
  activity.username = (android.widget.EditText) activity.findViewById(2130968578);
  activity.password = (android.widget.EditText) activity.findViewById(2130968579);
}
```
##非Activity注入
您也可以通过提供根视图，对任意对象进行注解。

```
public class FancyFragment extends Fragment {
  @InjectView(R.id.button1) Button button1;
  @InjectView(R.id.button2) Button button2;

  @Override public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    View view = inflater.inflate(R.layout.fancy_fragment, container, false);
    ButterKnife.inject(this, view);
    // TODO Use "injected" views...
    return view;
  }
}
```

另一个用途是简化列表适配器里的ViewHolder模式。

```
public class MyAdapter extends BaseAdapter {
  @Override public View getView(int position, View view, ViewGroup parent) {
    ViewHolder holder;
    if (view != null) {
      holder = (ViewHolder) view.getTag();
    } else {
      view = inflater.inflate(R.layout.whatever, parent, false);
      holder = new ViewHolder(view);
      view.setTag(holder);
    }

    holder.name.setText("John Doe");
    // etc...

    return view;
  }

  static class ViewHolder {
    @InjectView(R.id.title) TextView name;
    @InjectView(R.id.job_title) TextView jobTitle;

    public ViewHolder(View view) {
      ButterKnife.inject(this, view);
    }
  }
}
```

ButterKnife.inject调用可用在任何你想调用findViewById的地方。

其他提供的注入API：

* 注解任意使用activity作为根视图的对象。如果您使用类似MVC的模式，你可以利用activity，调用ButterKnife.inject(this, activity)注解控制器。
* 注入一个视图的孩子调用ButterKnife.inject（this）到字段上。如果你在布局中使用<merge>标签，解析的自定义视图的构造函数后可以立即调用这个语句。另外，从XML解析的自定义视图类型可以在onFinishInflate（）回调中使用它。

##视图列表
你可以将几个视图组织成一个列表或数组

```
@InjectViews({ R.id.first_name, R.id.middle_name, R.id.last_name })
List<EditText> nameViews;
```
apply方法可以让你在一个性对列表中的所有视图进行操作。

```
ButterKnife.apply(nameViews, DISABLE);
ButterKnife.apply(nameViews, ENABLED, false);
```

Action和Setter接口允许指定简单的行为。

```
static final Action<View> DISABLE = new Action<>() {
  @Override public void apply(View view, int index) {
    view.setEnabled(false);
  }
}
static final Setter<View, Boolean> ENABLED = new Setter<>() {
  @Override public void set(View view, Boolean value, int index) {
    view.setEnabled(value);
  }
}
```

android属性可以和apply方法一起使用
```
ButterKnife.apply(nameViews, View.ALPHA, 0);
```
LISTENER注入
监听器也可以自动在方法上进行配置
```
@OnClick(R.id.submit)
public void submit(View view) {
  // TODO submit data to server...
}
```
listener方法的所有参数都是可选的
```
@OnClick(R.id.submit)
public void submit() {
  // TODO submit data to server...
}
```
定义一个明确的类型它将自动转换
```
@OnClick(R.id.submit)
public void sayHi(Button button) {
  button.setText("Hello!");
}
```
在一个绑定里制定几个ID，用于通用的事件处理。
```
@OnClick({ R.id.door1, R.id.door2, R.id.door3 })
public void pickDoor(DoorView door) {
  if (door.hasPrizeBehind()) {
    Toast.makeText(this, "You win!", LENGTH_SHORT).show();
  } else {
    Toast.makeText(this, "Try again", LENGTH_SHORT).show();
  }
}
```
自定义视图可以不指定ID绑定到他们自己的监听器。
```
public class FancyButton extends Button {
  @OnClick
  public void onClick() {
    // TODO do something!
  }
}
```

##重置注入
Fragments有与Activities不同的视图生命周期，当在一个Fragment的onCreateView方法里注入时，在onDestroyView方法里面将视图置为null.Butter Knife有个reset方法可以自动做这件事情。
```
public class FancyFragment extends Fragment {
  @InjectView(R.id.button1) Button button1;
  @InjectView(R.id.button2) Button button2;

  @Override public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    View view = inflater.inflate(R.layout.fancy_fragment, container, false);
    ButterKnife.inject(this, view);
    // TODO Use "injected" views...
    return view;
  }

  @Override public void onDestroyView() {
    super.onDestroyView();
    ButterKnife.reset(this);
  }
}
```

##可选注入
默认上，@InjectView和listener注入都是必须的。如果目标视图无法找到，将抛出一个异常。为了避免这个行为并构建一个可选的注入，给字段或方法添加@Optional标记.  
```
@Optional @InjectView(R.id.might_not_be_there) TextView mightNotBeThere;

@Optional @OnClick(R.id.maybe_missing) void onMaybeMissingClicked() {
  // TODO ...
}
```

##多方法监听器
方法的注解，其相应的监听器具有多个回调可以用于绑定到其中的任何一个。每个注解具有其缺省绑定的一个回调。使用回调参数指定备用回调。
```
@OnItemSelected(R.id.list_view)
void onItemSelected(int position) {
  // TODO ...
}

@OnItemSelected(value = R.id.maybe_missing, callback = NOTHING_SELECTED)
void onNothingSelected() {
  // TODO ...
}
```

##其他
另外，findById方法可以简化代码，如果仍然需要在View, Activity, 或者 Dialog里查找视图。它使用泛型推断返回类型，并自动进行转换。
```
View view = LayoutInflater.from(context).inflate(R.layout.thing, null);
TextView firstName = ButterKnife.findById(view, R.id.first_name);
TextView lastName = ButterKnife.findById(view, R.id.last_name);
ImageView photo = ButterKnife.findById(view, R.id.photo);
```
为ButterKnife.findById添加静态引入，然后享受更多的乐趣。