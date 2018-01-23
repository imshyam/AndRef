# AndRef
Android Reference Read

Intent
---
Android application components can connect to other Android applications. This connection is based on a task description represented by an Intent object.

Intents are asynchronous messages which allow application components to request functionality from other Android components. Intents allow you to interact with components from the same applications as well as with components contributed by other applications. For example, an activity can start an external activity for taking a picture.

An intent can contain data via a Bundle. This data can be used by the receiving component.

```java
Intent i = new Intent(this, ActivityTwo.class);
i.putExtra("KEY_1", "This value one for ActivityTwo ");
i.putExtra("KEY_2", "This value two ActivityTwo");
startActivity(i);
```

Get data in other activity :
```java
Bundle extras = getIntent().getExtras();
// extras != null
String value1 = extras.getString("KEY_1");
```
#### Explicit vs Implicit Intent
Implicit Intents do not directly specify the Android components which should be called , it only specifies action to be performed.An Uri can be used with the implicit intent to specify data type.

Explicit intents are used in the application itself wherein one activity can switch to other activity.

#### Determine valid intent receivers
Sometimes you want to determine if a component has registered for an intent. For example, you want to check if a certain intent receiver is available and in case a component is available, you enable a functionality in your application.
```java
public static boolean isIntentAvailable(Context ctx, Intent intent) {
    final PackageManager mgr = ctx.getPackageManager();
    List<ResolveInfo> list =
        mgr.queryIntentActivities(intent,
            PackageManager.MATCH_DEFAULT_ONLY);
    return list.size() > 0;
}
```
> Intents can be used to send broadcast messages into the Android system. A broadcast receiver can register to an event and is notified if such an event is sent.
> Your application can register to system events, e.g., a new email has arrived, system boot is complete or a phone call is received and react accordingly.
#### Start Activity for Result
For this start activity as:
```java
startActivityForResult(i, REQUEST_CODE);
```

If the sub-activity is finished, it can send data back to its caller via an Intent. This is done in the finish() method.

```java
@Override
public void finish() {
    // Prepare data intent
    Intent data = new Intent();
    data.putExtra("returnKey1", "Swinging on a star. ");
    data.putExtra("returnKey2", "You could be better then you are. ");
    // Activity finished ok, return the data
    setResult(RESULT_OK, data);
    super.finish();
}
```

Once the sub-activity finishes, the onActivityResult() method in the calling activity is called.
```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (resultCode == RESULT_OK && requestCode == REQUEST_CODE) {
        if (data.hasExtra("returnKey1")) {
            String value1 = data.getExtras().getString("returnKey1");
        }
    }
}
```

> If a component does not define an intent filter, it can only be called by explicit intents.
#### Registering for intents via intent filters : Register an Activity as Browser

```xml 
<activity android:name=".BrowserActivitiy"
          android:label="@string/app_name">
  <intent-filter>
     <action android:name="android.intent.action.VIEW" />
     <category android:name="android.intent.category.DEFAULT" />
     <data android:scheme="http"/>
  </intent-filter>
</activity>
```

#### Registering for intents via intent filters : Register an Activity for the share intent
```xml 
<activity
    android:name=".ActivityTest"
    android:label="@string/app_name" >
    <intent-filter>
      <action android:name="android.intent.action.SEND" />
      <category android:name="android.intent.category.DEFAULT" />
      <data android:mimeType="text/plain" />
    </intent-filter>

</activity>
```

Some Actions and How to achieve that: 

 Action | How to 
 ------ | ------ 
 Open Browser | new Intent(Intent.ACTION_VIEW, Uri.parse("http://www.vogella.com"))
 Dial | new Intent(Intent.ACTION_DIAL, Uri.parse("tel:(+49)12345789"))
 Show Map | new Intent(Intent.ACTION_VIEW, Uri.parse("geo:50.123,7.1434?z=19"))
 Search on Map | new Intent(Intent.ACTION_VIEW, Uri.parse("geo:0,0?q=query"))
 Take Picture | new Intent("android.media.action.IMAGE_CAPTURE")
 Show Contacts | new Intent(Intent.ACTION_VIEW, Uri.parse("content://contacts/people/"))
 Edit First Contact | new Intent(Intent.ACTION_EDIT, Uri.parse("content://contacts/people/1"))
