web性能优化：
ANR要求：Activity对事件响应不超过5秒，BroadcastReceiver中执行时间不超过10秒。

# 电量优化
## 监控电量与充电
### 判断当前充电状态
BatteryManager会广播一个带有电池与充电详情的Sticky Intent。
```
IntentFilter ifilter = new IntentFilter(Intent.ACTION_BATTERY_CHANGED);
Intent batteryStatus = context.registerReceiver(null, ifilter);
int status = batteryStatus.getIntExtra(BatteryManager.EXTRA_STATUS, -1);
if(status == BatteryManager.BATTERY_STATUS_CHARGING || status == BatteryManager.BATTERY_STATUS_FULL){
    int chargePlug = battery.getIntExtra(BatteryManager.EXTRA_PLUGGED, -1);
    if(chargePlug == BATTERY_PLUGGED_USB){//降低更新操作
    }
    else if(chargePlug == BATTERY_PLUGGED_AC){//最大化更新操作
    }
}
else{//最小化化更新操作
}
```
### 监测充电状态改变
```
<receiver android:name=".PowerConnectionReceiver">
  <intent-filter>
    <action android:name="android.intent.action.ACTION_POWER_CONNECTED"/>
    <action android:name="android.intent.action.ACTION_POWER_DISCONNECTED"/>
  </intent-filter>
</receiver>
```

```
public class PowerConnectionReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        int status = intent.getIntExtra(BatteryManager.EXTRA_STATUS, -1);
        boolean isCharging = status == BatteryManager.BATTERY_STATUS_CHARGING ||
                            status == BatteryManager.BATTERY_STATUS_FULL;
        int chargePlug = intent.getIntExtra(BatteryManager.EXTRA_PLUGGED, -1);
        boolean usbCharge = chargePlug == BATTERY_PLUGGED_USB;
        boolean acCharge = chargePlug == BATTERY_PLUGGED_AC;
    }
}
```
### 判断当前电量
```
int level = battery.getIntExtra(BatteryManager.EXTRA_LEVEL, -1);
int scale = battery.getIntExtra(BatteryManager.EXTRA_SCALE, -1);
float batteryPct = level / (float)scale;
```
### 监测低电量
```
<receiver android:name=".BatteryLevelReceiver">
<intent-filter>
  <action android:name="android.intent.action.ACTION_BATTERY_LOW"/>
  <action android:name="android.intent.action.ACTION_BATTERY_OKAY"/>
  </intent-filter>
</receiver>
```

## 判断监测网络状态
### 判断当前网络状态
```
ConnectivityManager cm =   (ConnectivityManager)context.getSystemService(Context.CONNECTIVITY_SERVICE);
NetworkInfo activeNetwork = cm.getActiveNetworkInfo();
boolean isConnected = activeNetwork.isConnectedOrConnecting();
```
### 监测网络切换
```
<action android:name="android.net.conn.CONNECTIVITY_CHANGE"/>
```
### 切换Receiver开启状态
监测到网络断开，只保留ConnectionReceiver,关闭其他所有Receiver
```
ComponentName receiver = new ComponentName(context, myReceiver.class);
PackageManager pm = context.getPackageManager();
pm.setComponentEnabledSetting(receiver,
        PackageManager.COMPONENT_ENABLED_STATE_ENABLED,
        PackageManager.DONT_KILL_APP)
```
