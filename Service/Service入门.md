# Service是什么
安卓的四大组件之一。实际效果和Activity差不多。在实际的使用中来看，Service如同是一个没有界面的Activity。


# 什么时候使用Service
需要做和Activity差不多的事，但是又没必要占用一个界面时就可以使用Service。例如在后台和服务器通信。


# 初学者关于Service的常见认知错误
1. 有初学者误以为Service是一个线程，实际上Service不是一个线程，它工作在主线程中。  
2. 和上面类似，有的初学者误以为Service是和进程分开的，实际上，一般情况下Service是作为启动它的进程的一部分。


# 启动Service的两种方式
> 		context.startService(Intent intent)

> 调用服务者与服务没有关联，即使调用者不存在了，服务依然运行。  

<br>
> 		context.bindService(Intent intent)

> 调用服务者与服务绑定，调用者退出，服务也退出。


# Service的生命周期
相比Activity简单得多。   
![](https://github.com/Sino-Snack/Android/blob/master/Service/images/server%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)  
第一次启动Service之后，依次调用onCreate()、onStart()，之后运行。
当Service停止运行时，将会调用onDestroy()。

**注意：**  
1. 如果Service没有Destroy，无论启动多少次都只有第一次才会执行onCreate()。除了第一次以外的都只会执行onStart()。  
2. 在API5之后 onStart()就被废弃了，我们可以用onStartCommand()代替。


**代码示例**  
这是一个在service中播放音乐的例子。

**MyService.java**  
```java
package zhp.app.servicetest;

import android.app.Service;
import android.content.Intent;
import android.media.MediaPlayer;
import android.os.Bundle;
import android.os.IBinder;

/**
 * 一个播放音乐的Service
 *	@since 2015/3/16
 */
public class MyService extends Service {
	protected static final String MSG = "message";
	protected static final String CMD_PLAY = "play";
	protected static final String CMD_PAUSE = "pause";

	private MediaPlayer mediaPlayer;

	// ==============================
	// 继承或重写的方法
	// ==============================
	@Override
	public IBinder onBind(Intent arg0) {
		return null;
	}

	@Override
	public void onCreate() {
		// 在创建Service的时候初始化MediaPlayer
		initMediaPlayer();
		super.onCreate();
	}

	@Override
	public void onDestroy() {
		// 在服务结束的时候停止播放音乐
		musicStop();
		super.onDestroy();
	}

	@Override
	public int onStartCommand(Intent intent, int flags, int startId) {
		// 接收数据指令，并按指令执行
		if (intent != null) {
			Bundle bundle = intent.getExtras();
			if (bundle != null) {
				String command = bundle.getString(MSG);
				switch (command) {
				case CMD_PLAY:
					musicPlay();
					break;

				case CMD_PAUSE:
					musicPause();
					break;
				}
			}
		}
		return super.onStartCommand(intent, flags, startId);
	}

	// ==============================
	// 方法
	// ==============================
	/**
	 * 初始化MediaPlayer
	 */
	private void initMediaPlayer() {
		if (mediaPlayer == null) {
			mediaPlayer = MediaPlayer.create(getApplicationContext(), R.raw.bgm);
		}
		mediaPlayer.seekTo(0);
	}

	/**
	 * 音乐播放
	 */
	public void musicPlay() {
		if (mediaPlayer == null) {
			initMediaPlayer();
		}
		mediaPlayer.start();
	}

	/**
	 * 音乐暂停
	 */
	public void musicPause() {
		if (mediaPlayer != null) {
			mediaPlayer.pause();
		}
	}

	/**
	 * 音乐结束
	 */
	public void musicStop() {
		if (mediaPlayer != null) {
			mediaPlayer.stop();
			mediaPlayer.release();
			mediaPlayer = null;
		}

	}
}
```

**MainActivity.java**
```java
package zhp.app.servicetest;

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;

/**
 *	@since 2015/3/16
 */
public class MainActivity extends Activity implements OnClickListener {

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
	}

	@Override
	public void onClick(View v) {
		// 和Activity的启动一样
		Intent intent = new Intent(this, MyService.class);
		Bundle bundle = new Bundle();
		switch (v.getId()) {
		case R.id.main_play:
			bundle.putString(MyService.MSG, MyService.CMD_PLAY);
			break;
		case R.id.main_pause:
			bundle.putString(MyService.MSG, MyService.CMD_PAUSE);
			break;
		case R.id.main_stop:
			stopService(intent);
			return;
		}
		// 在这里传送数据，Service在onStartCommand()中接收数据
		intent.putExtras(bundle);

		// Activity是startActivity()， 类似的，Service是startService()
		// 也可以改为Service的另外一种启动方式： bindService()
		startService(intent);
	}
}
```

**Service和Activity都需要在manifest中注册：**  
```xml
<application  
    android:allowBackup="true"  
    android:icon="@drawable/ic_launcher"  
    android:label="@string/app_name"  
    android:theme="@style/AppTheme" >  
      
    <activity  
        android:name=".MainActivity"  
        android:label="@string/app_name" >  
        <intent-filter>  
            <action android:name="android.intent.action.MAIN" />  
            <category android:name="android.intent.category.LAUNCHER" />  
        </intent-filter>  
    </activity>  
      
 <service    
     android:name=".MyService" >  
 </service>    
      
</application>  
```
