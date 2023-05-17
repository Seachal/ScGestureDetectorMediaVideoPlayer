# GestureDetector

android开发之 GestureDetector 手势识别（调节音量、亮度、快进和后退）


![](http://seachal-blog-picture-host.oss-cn-beijing.aliyuncs.com/20150704181852957.gif)

写UI布局：

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true"
    android:orientation="vertical" >

    <include
        android:id="@+id/root_layout"
        android:layout_width="match_parent"
        android:layout_height="200dp"
        layout="@layout/video_layout" />

</LinearLayout>
```

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="match_parent" >

    <com.seachal.gesturedetectordemo.views.MyVideoView
            android:id="@+id/tv_pro_play"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />

    <LinearLayout
            android:id="@+id/ll_player_controller"
            android:layout_width="match_parent"
            android:layout_height="50dp"
            android:layout_alignParentBottom="true"
            android:layout_alignParentLeft="true"
            android:background="#c0000000"
            android:gravity="center"
            android:orientation="horizontal"
            android:visibility="gone">

        <ImageView
                android:id="@+id/iv_play_pause"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:padding="15dp"
                android:src="@drawable/btn_pause" />

        <TextView
                android:id="@+id/tv_playing_time"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginLeft="15dp"
                android:text
="00:00"
                android:textColor="@android:color/darker_gray"
                android:textSize="15sp" />

        <SeekBar
                android:id="@+id/sb_video_progress"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_marginLeft="5dp"
                android:layout_weight="2" />

        <TextView
                android:id="@+id/tv_total_time"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginLeft="5dp"
                android:text="00:00"
                android:textColor="@android:color/darker_gray"
                android:textSize="15sp" />

        <ImageView
                android:id="@+id/iv_full_screen"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginLeft="15dp"
                android:padding="15dp"
                android:src="@drawable/btn_full_screen" />
    </LinearLayout>

    <RelativeLayout
            android:id="@+id/gesture_volume_layout"
            android:layout_width="120dip"
            android:layout_height="100dip"
            android:layout_centerInParent="true"
            android:background="@drawable/souhu_player_gesture_bg"
            android:gravity="center"
            android:visibility="gone" >

        <ImageView
                android:id="@+id/gesture_iv_player_volume"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_centerHorizontal="true"
                android:src="@drawable/souhu_player_volume" />

        <TextView
                android:id="@+id/geture_tv_volume_percentage"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_below="@id/gesture_iv_player_volume"
                android:layout_centerHorizontal="true"
                android:gravity="right"
                android:text="80%"
                android:textColor="#ffececec" />
    </RelativeLayout>

    <RelativeLayout
            android:id="@+id/gesture_bright_layout"
            android:layout_width="120dip"
            android:layout_height="100dip"
            android:layout_centerInParent="true"
            android:background="@drawable/souhu_player_gesture_bg"
            android:gravity="center"
            android:visibility="gone" >

        <ImageView
                android:id="@+id/gesture_iv_player_bright"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_centerHorizontal="true"
                android:src="@drawable/souhu_player_bright" />

        <TextView
                android:id="@+id/geture_tv_bright_percentage"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_below="@id/gesture_iv_player_bright"
                android:layout_centerHorizontal="true"
                android:gravity="right"
                android:text="80%"
                android:textColor="#ffececec" />
    </RelativeLayout>

    <RelativeLayout
            android:id="@+id/gesture_progress_layout"
            android:layout_width="120dip"
            android:layout_height="100dip"
            android:layout_centerInParent="true"
            android:background="@drawable/souhu_player_gesture_bg"
            android:gravity="center"
            android:visibility="gone">

        <ImageView
                android:id="@+id/gesture_iv_progress"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_centerHorizontal="true"
                android:src="@drawable/souhu_player_backward" />

        <TextView
                android:id="@+id/geture_tv_progress_time"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_below="@id/gesture_iv_progress"
                android:layout_centerHorizontal="true"
                android:gravity="right"
                android:text="00:35/24:89"
                android:textColor="#ffececec" />
    </RelativeLayout>

</RelativeLayout>
```

以上将UI布局给大伙粘贴上来了，大家按照各自的需求改吧改吧…即可

那么，就下来我将首先给大家分析分析如何通过[手势识别](https://so.csdn.net/so/search?q=%E6%89%8B%E5%8A%BF%E8%AF%86%E5%88%AB\&spm=1001.2101.3001.7020)来调节音量、亮度、快键和后退需求！

一提到手势识别，大伙第一反应绝对是Google提供给我们的GestureDetector类，没错今天我们就使用使用这个类来给大家完成以上的需求！

第一：将主逻辑代码类继承FragmentActivity类并实现OnGestureListener监听和OnTouchListener监听，同时在onCreate方法中创建GestureDetector对象，这时需要传递两个对象，这也是为啥我要继承和实现了！

```
GestureDetector gestureDetector = new GestureDetector(this, this);
1
```

第二：获取UI布局中定义的控件




```
// 视频播放控件
tv_pro_play = (MyVideoView) findViewById(R.id.tv_pro_play);

/*
iv_full_screen = (ImageView) findViewById(R.id.iv_full_screen);
iv_play_pause = (ImageView) findViewById(R.id.iv_play_pause);

ll_player_controller = (LinearLayout) findViewById(R.id.ll_player_controller);

iv_play_pause = (ImageView) findViewById(R.id.iv_play_pause);
sb_video_progress = (SeekBar) findViewById(R.id.sb_video_progress);
iv_full_screen = (ImageView) findViewById(R.id.iv_full_screen);
tv_playing_time = (TextView) findViewById(R.id.tv_playing_time);
tv_total_time = (TextView) findViewById(R.id.tv_total_time);*/

// ****************音量/进度/亮度*********************

root_layout = (RelativeLayout) findViewById(R.id.root_layout);
gesture_volume_layout = (RelativeLayout) findViewById(R.id.gesture_volume_layout);
gesture_bright_layout = (RelativeLayout) findViewById(R.id.gesture_bright_layout);
gesture_progress_layout = (RelativeLayout) findViewById(R.id.gesture_progress_layout);
geture_tv_progress_time = (TextView) findViewById(R.id.geture_tv_progress_time);
geture_tv_volume_percentage = (TextView) findViewById(R.id.geture_tv_volume_percentage);
geture_tv_bright_percentage = (TextView) findViewById(R.id.geture_tv_bright_percentage);
gesture_iv_progress = (ImageView) findViewById(R.id.gesture_iv_progress);
gesture_iv_player_volume = (ImageView) findViewById(R.id.gesture_iv_player_volume);
gesture_iv_player_bright = (ImageView) findViewById(R.id.gesture_iv_player_bright);
gestureDetector = new GestureDetector(this, this); //需要实现OnGestureListener监听
root_layout.setLongClickable(true);
gestureDetector.setIsLongpressEnabled(true);
root_layout.setOnTouchListener(this);//需要实现OnTouchListener监听
audiomanager = (AudioManager) getSystemService(Context.AUDIO_SERVICE);
maxVolume = audiomanager.getStreamMaxVolume(AudioManager.STREAM_MUSIC); // 获取系统最大音量
currentVolume = audiomanager.getStreamVolume(AudioManager.STREAM_MUSIC); // 获取当前值
```

第三：获取视频播放窗口的尺寸，判断触发的视频播放窗口位置来识别不同的操作和需求：

```
/** 获取视频播放窗口的尺寸 */
ViewTreeObserver viewObserver = root_layout.getViewTreeObserver();
viewObserver.addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
    @Override
    public void onGlobalLayout() {
        root_layout.getViewTreeObserver().removeGlobalOnLayoutListener(this);
        playerWidth = root_layout.getWidth();
        playerHeight = root_layout.getHeight();
    }
});
```

第四：当你实现OnGestureListener监听和OnTouchListener监听时是需要您实现其方法的，分别是：

```
当你实现了OnTouchListener监听需要覆写其方法：
@Override
public boolean onTouch(View v, MotionEvent event) {
    return false;
}

当你实现了OnGestureListener监听需要覆写一下方法：
// // 1. 用户轻触触摸屏，由1个MotionEvent ACTION_DOWN触发  
@Override
public boolean onDown(MotionEvent e) {
    return false; 
}

// 2.用户按下触摸屏，并拖动，由1个MotionEvent ACTION_DOWN, 多个ACTION_MOVE触发
@Override
public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
    return false;
}

// 3.用户（轻触触摸屏后）松开，由1个MotionEvent ACTION_UP触发
@Override
public boolean onSingleTapUp(MotionEvent e) {
    return false;
}

// 4.用户按下触摸屏、快速移动后松开，由1个MotionEvent ACTION_DOWN, 多个ACTION_MOVE, 1个ACTION_UP触发
  
   // 参数：
   // e1：第1个ACTION_DOWN MotionEvent
   // e2：最后一个ACTION_MOVE MotionEvent
   // velocityX：X轴上的移动速度，像素/秒
   // velocityY：Y轴上的移动速度，像素/秒
@Override
public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
    return false;
}

// 5.用户长按触摸屏，由多个MotionEvent ACTION_DOWN触发
@Override
public void onLongPress(MotionEvent e) {}

// 6.用户轻触触摸屏，尚未松开或拖动，由1个MotionEvent ACTION_DOWN触发, 注意和onDown()的区别，强调的是没有松开或者拖动的状态
  // 与onDown()的区别：无松开 / 拖动
 // 即：当用户点击的时，onDown（）就会执行，在按下的瞬间没有松开 / 拖动时onShowPress就会执行     
@Override
public void onShowPress(MotionEvent e) {}
```

我们知道OnTouchListener只能监听到三种触摸事件，即按下，移动，松开，如果想要监听到双击、滑动、长按等复杂的手势操作，这个时候就必须得用到OnGestureListener了。

因此在onTouch()方法中，我们要进行如下的处理：

```
@Override
public boolean onTouch(View v, MotionEvent event) {
    // 手势里除了singleTapUp，没有其他检测up的方法
    if (event.getAction() == MotionEvent.ACTION_UP) {
        GESTURE_FLAG = 0;// 手指离开屏幕后，重置调节音量或进度的标志
        gesture_volume_layout.setVisibility(View.GONE);
        gesture_bright_layout.setVisibility(View.GONE);
        gesture_progress_layout.setVisibility(View.GONE);
    }
    return gestureDetector.onTouchEvent(event);//如果想要监听到双击、滑动、长按等复杂的手势操作，这个时候就必须得用到OnGestureListener了
}
```

同时点击屏幕时触发的onDown()方法中：

```
@Override
public boolean onDown(MotionEvent e) {
    firstScroll = true;// 设定是触摸屏幕后第一次scroll的标志
    return false;
}
```

通过在onTouch()方法中调用gestureDetector.onTouchEvent(event)方法时，它会去调用onScroll()方法，这样在该方法中通过手势识别来完成调节音量、亮度、快键和后退操作：

```
@Override
public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
    float mOldX = e1.getX(), mOldY = e1.getY();
    int y = (int) e2.getRawY();
    if (firstScroll) {// 以触摸屏幕后第一次滑动为标准，避免在屏幕上操作切换混乱
        // 横向的距离变化大则调整进度，纵向的变化大则调整音量
        if (Math.abs(distanceX) >= Math.abs(distanceY)) {
            gesture_progress_layout.setVisibility(View.VISIBLE);
            gesture_volume_layout.setVisibility(View.GONE);
            gesture_bright_layout.setVisibility(View.GONE);
            GESTURE_FLAG = GESTURE_MODIFY_PROGRESS;
        } else {
            if (mOldX > playerWidth * 3.0 / 5) {// 音量 右侧上下滑动调音量
                gesture_volume_layout.setVisibility(View.VISIBLE);
                gesture_bright_layout.setVisibility(View.GONE);
                gesture_progress_layout.setVisibility(View.GONE);
                GESTURE_FLAG = GESTURE_MODIFY_VOLUME;
            } else if (mOldX < playerWidth * 2.0 / 5) {// 亮度  左侧上下滑动调亮度
                gesture_bright_layout.setVisibility(View.VISIBLE);
                gesture_volume_layout.setVisibility(View.GONE);
                gesture_progress_layout.setVisibility(View.GONE);
                GESTURE_FLAG = GESTURE_MODIFY_BRIGHT;
            }
        }
    }

    // 如果每次触摸屏幕后第一次scroll是调节进度，那之后的scroll事件都处理进度，直到离开屏幕执行下一次操作
    if (GESTURE_FLAG == GESTURE_MODIFY_PROGRESS) {
        // distanceX=lastScrollPositionX-currentScrollPositionX，因此为正时是快进
        if (Math.abs(distanceX) > Math.abs(distanceY)) {// 横向移动大于纵向移动
            if (distanceX >= DensityUtil.dip2px(this, STEP_PROGRESS)) {// 快退，用步长控制改变速度，可微调
                gesture_iv_progress.setImageResource(R.drawable.souhu_player_backward);
                if (playingTime > 3) {// 避免为负
                    playingTime -= 3;// scroll方法执行一次快退3秒
                } else {
                    playingTime = 0;
                }
            } else if (distanceX <= -DensityUtil.dip2px(this, STEP_PROGRESS)) {// 快进
                gesture_iv_progress.setImageResource(R.drawable.souhu_player_forward);
                if (playingTime < videoTotalTime - 16) {// 避免超过总时长
                    playingTime += 3;// scroll执行一次快进3秒
                } else {
                    playingTime = videoTotalTime - 10;
                }
            }
            if (playingTime < 0) {
                playingTime = 0;
            }
            tv_pro_play.seekTo(playingTime);
            geture_tv_progress_time.setText(DateTools.getTimeStr(playingTime) + "/" + DateTools.getTimeStr(videoTotalTime));
        }
    }

    // 如果每次触摸屏幕后第一次scroll是调节音量，那之后的scroll事件都处理音量调节，直到离开屏幕执行下一次操作
    else if (GESTURE_FLAG == GESTURE_MODIFY_VOLUME) {
        currentVolume = audiomanager.getStreamVolume(AudioManager.STREAM_MUSIC); // 获取当前值
        if (Math.abs(distanceY) > Math.abs(distanceX)) {// 纵向移动大于横向移动
            if (distanceY >= DensityUtil.dip2px(this, STEP_VOLUME)) {// 音量调大,注意横屏时的坐标体系,尽管左上角是原点，但横向向上滑动时distanceY为正
                if (currentVolume < maxVolume) {// 为避免调节过快，distanceY应大于一个设定值
                    currentVolume++;
                }
                gesture_iv_player_volume.setImageResource(R.drawable.souhu_player_volume);
            } else if (distanceY <= -DensityUtil.dip2px(this, STEP_VOLUME)) {// 音量调小
                if (currentVolume > 0) {
                    currentVolume--;
                    if (currentVolume == 0) {// 静音，设定静音独有的图片
                        gesture_iv_player_volume.setImageResource(R.drawable.souhu_player_silence);
                    }
                }
            }
            int percentage = (currentVolume * 100) / maxVolume;
            geture_tv_volume_percentage.setText(percentage + "%");
            audiomanager.setStreamVolume(AudioManager.STREAM_MUSIC,currentVolume, 0);
        }
    }

    // 如果每次触摸屏幕后第一次scroll是调节亮度，那之后的scroll事件都处理亮度调节，直到离开屏幕执行下一次操作
    else if (GESTURE_FLAG == GESTURE_MODIFY_BRIGHT) {
        gesture_iv_player_bright.setImageResource(R.drawable.souhu_player_bright);
        if (mBrightness < 0) {
            mBrightness = getWindow().getAttributes().screenBrightness;
            if (mBrightness <= 0.00f)
                mBrightness = 0.50f;
            if (mBrightness < 0.01f)
                mBrightness = 0.01f;
        }
        WindowManager.LayoutParams lpa = getWindow().getAttributes();
        lpa.screenBrightness = mBrightness + (mOldY - y) / playerHeight;
        if (lpa.screenBrightness > 1.0f)
            lpa.screenBrightness = 1.0f;
        else if (lpa.screenBrightness < 0.01f)
            lpa.screenBrightness = 0.01f;
        getWindow().setAttributes(lpa);
        geture_tv_bright_percentage.setText((int) (lpa.screenBrightness * 100) + "%");
    }

    firstScroll = false;// 第一次scroll执行完成，修改标志
    return false;
}
```
