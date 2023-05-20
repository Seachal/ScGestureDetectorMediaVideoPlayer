
# 前言

- [手势识别](https://cloud.tencent.com/product/gr?from=20065&from_column=20065)在Android开发的应用非常常见
- Android手势识别类：GestureDetector类的使用。（含实例讲解）

---


# 目录
![](https://cdn.nlark.com/yuque/0/2023/png/317107/1684145416702-2d62a827-8b31-4f1b-95e2-b0b795ba0e34.png#averageHue=%23eeeeee&clientId=uc7891883-5e93-4&from=paste&id=u2c0e555e&originHeight=642&originWidth=928&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=uf935acad-e24d-44cb-a2f1-637c01cc105&title=)

---


# 简介
![](https://cdn.nlark.com/yuque/0/2023/png/317107/1684145416520-54fe847c-e9f9-48c4-b46d-bc7812382fb8.png#averageHue=%23f4f4f4&clientId=uc7891883-5e93-4&from=paste&id=ua50bbc3c&originHeight=736&originWidth=1200&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=u108d8226-f346-4b8b-945c-ff722b1a14a&title=)
下面，我将结合实例，详细介绍GestureDetector的使用接口 & 使用类。

---


# 接口1：OnGestureListener
### 1. 作用
检测用户在屏幕的以下操作：按下瞬间、按压、长按、轻击、快速滑屏、拖动
### 2. 使用步骤

```javascript
// 步骤1：创建手势检测器实例 & 传入OnGestureListener接口（需要复写对应方法）
// 构造函数有3个，常用的是第二个
// 1. GestureDetector gestureDetector=new GestureDetector(GestureDetector.OnGestureListener listener);
// 2. GestureDetector gestureDetector=new GestureDetector(Context context,GestureDetector.OnGestureListener listener);
// 3. GestureDetector gestureDetector=new GestureDetector(Context context,GestureDetector.SimpleOnGestureListener listener);
    GestureDetector mGestureDetector = new GestureDetector(this, new GestureDetector.OnGestureListener() {

        // 1. 用户轻触触摸屏
        public boolean onDown(MotionEvent e) {
            Log.i("MyGesture", "onDown");
            return false;
        }

        // 2. 用户轻触触摸屏，尚未松开或拖动
        // 与onDown()的区别：无松开 / 拖动
        // 即：当用户点击的时，onDown（）就会执行，在按下的瞬间没有松开 / 拖动时onShowPress就会执行
        public void onShowPress(MotionEvent e) {
            Log.i("MyGesture", "onShowPress");
        }

        // 3. 用户长按触摸屏
        public void onLongPress(MotionEvent e) {
            Log.i("MyGesture", "onLongPress");
        }

        // 4. 用户轻击屏幕后抬起
        public boolean onSingleTapUp(MotionEvent e) {
            Log.i("MyGesture", "onSingleTapUp");
            return true;
        }

        // 5. 用户按下触摸屏 & 拖动
        public boolean onScroll(MotionEvent e1, MotionEvent e2,
                                float distanceX, float distanceY) {
            Log.i("MyGesture", "onScroll:");
            return true;
        }

        // 6. 用户按下触摸屏、快速移动后松开
        // 参数：
        // e1：第1个ACTION_DOWN MotionEvent
        // e2：最后一个ACTION_MOVE MotionEvent
        // velocityX：X轴上的移动速度，像素/秒
        // velocityY：Y轴上的移动速度，像素/秒
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
                               float velocityY) {
            Log.i("MyGesture", "onFling");
            return true;
        }

    });

// 步骤2-1：让某个View检测手势 - 重写View的onTouch函数，将View的触屏事件交给GestureDetector处理，从而对用户手势作出响应
    View.setOnTouchListener(new View.OnTouchListener() {
        @Override
        public boolean onTouch(View v, MotionEvent event) {
            mGestureDetector.onTouchEvent(event);
            return true; // 注：返回true才能完整接收触摸事件
        }
    });

// 步骤2-2：让某个Activity检测手势：重写Activity的dispatchTouchEvent函数，将触屏事件交给GestureDetector处理，从而对用户手势作出响应
  @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        mGestureDetector.onTouchEvent(ev); // 让GestureDetector响应触碰事件
        super.dispatchTouchEvent(ev); // 让Activity响应触碰事件
        return false;
    }
```
复制
### 3. 实例说明
现在对一个TextView进行手势检测 _activity_main.xml_

```javascript
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    >

    <TextView
        android:id="@+id/textView"
        android:layout_width="match_parent"
        android:layout_height="600dp"
        android:text="carson_ho Test"
        android:textAppearance="?android:attr/textAppearanceLarge" />

</LinearLayout>
```
复制
_MainActivity.java_

```javascript
public class MainActivity extends AppCompatActivity {

    TextView mTextView;
    GestureDetector mGestureDetector;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 步骤1：创建手势检测器实例 & 传入OnGestureListener接口（需要复写对应方法）
        mGestureDetector = new GestureDetector(this, new GestureDetector.OnGestureListener() {

            // 1. 用户轻触触摸屏
            public boolean onDown(MotionEvent e) {
                Log.i("MyGesture", "onDown");
                return false;
            }

            // 2. 用户轻触触摸屏，尚未松开或拖动
            // 与onDown()的区别：无松开 / 拖动
            // 即：当用户点击的时，onDown（）就会执行，在按下的瞬间没有松开 / 拖动时onShowPress就会执行
            public void onShowPress(MotionEvent e) {
                Log.i("MyGesture", "onShowPress");
            }

            // 3. 用户长按触摸屏
            public void onLongPress(MotionEvent e) {
                Log.i("MyGesture", "onLongPress");
            }

            // 4. 用户轻击屏幕后抬起
            public boolean onSingleTapUp(MotionEvent e) {
                Log.i("MyGesture", "onSingleTapUp");
                return true;
            }

            // 5. 用户按下触摸屏 & 拖动
            public boolean onScroll(MotionEvent e1, MotionEvent e2,
                                    float distanceX, float distanceY) {
                Log.i("MyGesture", "onScroll:");
                return true;
            }

            // 6. 用户按下触摸屏、快速移动后松开
            // 参数：
            // e1：第1个ACTION_DOWN MotionEvent
            // e2：最后一个ACTION_MOVE MotionEvent
            // velocityX：X轴上的移动速度，像素/秒
            // velocityY：Y轴上的移动速度，像素/秒
            public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
                                   float velocityY) {
                Log.i("MyGesture", "onFling");
                return true;
            }

        });

        // 步骤2：让TextView检测手势：重写View的onTouch函数，将触屏事件交给GestureDetector处理，从而对用户手势作出响应
        mTextView = (TextView) findViewById(R.id.textView);
        mTextView.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                mGestureDetector.onTouchEvent(event);
                return false;
            }
        });
    }
}
```
复制
### 4. 示意图
我在屏幕作出一系列手势进行测试
![](https://cdn.nlark.com/yuque/0/2023/gif/317107/1684145416631-fa146897-b664-4641-82aa-8c41b36a5e07.gif#averageHue=%238f8f8d&clientId=uc7891883-5e93-4&from=paste&id=uaa3fe6b1&originHeight=544&originWidth=608&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=u872d96ff-7bb7-40b0-a47b-c3311ac4e44&title=)

---


# 接口2：OnDoubleTapListener
### 1. 作用
检测用户单击、双击屏幕
### 2. 使用步骤

```javascript
// 步骤1：创建手势检测器实例
    // 注：使用OnDoubleTapListener接口时，需要使用GestureDetector，而GestureDetector的创建则必须传入OnGestureListener接口
    // 所以在使用OnDoubleTapListener接口时，也必须实现OnGestureListener接口
    // 构造函数有3个，常用的是第二个
    // 1. GestureDetector gestureDetector=new GestureDetector(GestureDetector.OnGestureListener listener);
    // 2. GestureDetector gestureDetector=new GestureDetector(Context context,GestureDetector.OnGestureListener listener);
    // 3. GestureDetector gestureDetector=new GestureDetector(Context context,GestureDetector.SimpleOnGestureListener listener);
        GestureDetector mGestureDetector = new GestureDetector(this, new GestureDetector.OnGestureListener() {

            // 1. 用户轻触触摸屏
            public boolean onDown(MotionEvent e) {
                Log.i("MyGesture", "onDown");
                return false;
            }

            // 2. 用户轻触触摸屏，尚未松开或拖动
            // 与onDown()的区别：无松开 / 拖动
            // 即：当用户点击的时，onDown（）就会执行，在按下的瞬间没有松开 / 拖动时onShowPress就会执行
            public void onShowPress(MotionEvent e) {
                Log.i("MyGesture", "onShowPress");
            }

            // 3. 用户长按触摸屏
            public void onLongPress(MotionEvent e) {
                Log.i("MyGesture", "onLongPress");
            }

            // 4. 用户轻击屏幕后抬起
            public boolean onSingleTapUp(MotionEvent e) {
                Log.i("MyGesture", "onSingleTapUp");
                return true;
            }

            // 5. 用户按下触摸屏 & 拖动
            public boolean onScroll(MotionEvent e1, MotionEvent e2,
                                    float distanceX, float distanceY) {
                Log.i("MyGesture", "onScroll:");
                return true;
            }

            // 6. 用户按下触摸屏、快速移动后松开
            // 参数：
            // e1：第1个ACTION_DOWN MotionEvent
            // e2：最后一个ACTION_MOVE MotionEvent
            // velocityX：X轴上的移动速度，像素/秒
            // velocityY：Y轴上的移动速度，像素/秒
            public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
                                   float velocityY) {
                Log.i("MyGesture", "onFling");
                return true;
            }

        });

// 步骤2：创建 & 设置OnDoubleTapListener接口实现类
    mGestureDetector.setOnDoubleTapListener(new GestureDetector.OnDoubleTapListener() {

        // 1. 单击事件
        // 关于OnDoubleTapListener.onSingleTapConfirmed（）和 OnGestureListener.onSingleTapUp()的区别
        // onSingleTapConfirmed：再次点击（即双击），则不会执行
        // onSingleTapUp：手抬起就会执行
        public boolean onSingleTapConfirmed(MotionEvent e) {
            Log.i("MyGesture", "onSingleTapConfirmed");
            return false;
        }

        // 2. 双击事件
        public boolean onDoubleTap(MotionEvent e) {
            Log.i("MyGesture", "onDoubleTap");
            return false;
        }
        // 3. 双击间隔中发生的动作
        // 指触发onDoubleTap后，在双击之间发生的其它动作，包含down、up和move事件；
        public boolean onDoubleTapEvent(MotionEvent e) {
            Log.i("MyGesture", "onDoubleTapEvent");
            return false;
        }
    });

// 步骤3-1：让某个View检测手势 - 重写View的onTouch函数，将View的触屏事件交给GestureDetector处理，从而对用户手势作出响应
    View.setOnTouchListener(new View.OnTouchListener() {
        @Override
        public boolean onTouch(View v, MotionEvent event) {
            mGestureDetector.onTouchEvent(event);
            return true; // 注：返回true才能完整接收触摸事件
        }
    });

// 步骤3-2：让某个Activity检测手势：重写Activity的dispatchTouchEvent函数，将触屏事件交给GestureDetector处理，从而对用户手势作出响应
    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        mGestureDetector.onTouchEvent(ev); // 让GestureDetector响应触碰事件
        super.dispatchTouchEvent(ev); // 让Activity响应触碰事件
        return false;
    }
```
复制
### 3. 实例说明
现在对一个TextView进行手势检测 _activity_main.xml_

```javascript
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    >

    <TextView
        android:id="@+id/textView"
        android:layout_width="match_parent"
        android:layout_height="600dp"
        android:text="carson_ho Test"
        android:textAppearance="?android:attr/textAppearanceLarge" />

</LinearLayout>
```
复制
_MainActivity.java_

```javascript
public class MainActivity extends AppCompatActivity {

    TextView mTextView;
    GestureDetector mGestureDetector;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 步骤1：创建手势检测器实例 & 传入OnGestureListener接口（需要复写对应方法）
        mGestureDetector = new GestureDetector(this, new GestureDetector.OnGestureListener() {

            // 1. 用户轻触触摸屏
            public boolean onDown(MotionEvent e) {
                Log.i("MyGesture1", "onDown");
                return false;
            }

            // 2. 用户轻触触摸屏，尚未松开或拖动
            // 与onDown()的区别：无松开 / 拖动
            // 即：当用户点击的时，onDown（）就会执行，在按下的瞬间没有松开 / 拖动时onShowPress就会执行
            public void onShowPress(MotionEvent e) {
                Log.i("MyGesture", "onShowPress");
            }

            // 3. 用户长按触摸屏
            public void onLongPress(MotionEvent e) {
                Log.i("MyGesture", "onLongPress");
            }

            // 4. 用户轻击屏幕后抬起
            public boolean onSingleTapUp(MotionEvent e) {
                Log.i("MyGesture", "onSingleTapUp");
                return true;
            }

            // 5. 用户按下触摸屏 & 拖动
            public boolean onScroll(MotionEvent e1, MotionEvent e2,
                                    float distanceX, float distanceY) {
                Log.i("MyGesture", "onScroll:");
                return true;
            }

            // 6. 用户按下触摸屏、快速移动后松开
            // 参数：
            // e1：第1个ACTION_DOWN MotionEvent
            // e2：最后一个ACTION_MOVE MotionEvent
            // velocityX：X轴上的移动速度，像素/秒
            // velocityY：Y轴上的移动速度，像素/秒
            public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
                                   float velocityY) {
                Log.i("MyGesture", "onFling");
                return true;
            }

        });

        // 步骤2：创建 & 设置OnDoubleTapListener接口实现类
        mGestureDetector.setOnDoubleTapListener(new GestureDetector.OnDoubleTapListener() {

            // 1. 单击事件
            // 关于OnDoubleTapListener.onSingleTapConfirmed（）和 OnGestureListener.onSingleTapUp()的区别
            // onSingleTapConfirmed：再次点击（即双击），则不会执行
            // onSingleTapUp：手抬起就会执行
            public boolean onSingleTapConfirmed(MotionEvent e) {
                Log.i("MyGesture", "onSingleTapConfirmed");
                return false;
            }

            // 2. 双击事件
            public boolean onDoubleTap(MotionEvent e) {
                Log.i("MyGesture", "onDoubleTap");
                return false;
            }
            // 3. 双击间隔中发生的动作
            // 指触发onDoubleTap后，在双击之间发生的其它动作，包含down、up和move事件；
            public boolean onDoubleTapEvent(MotionEvent e) {
                Log.i("MyGesture", "onDoubleTapEvent");
                return false;
            }
        });

        // 步骤3：重写View的onTouch函数，将触屏事件交给GestureDetector处理，从而对用户手势作出响应
        mTextView = (TextView) findViewById(R.id.textView);

        mTextView.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                mGestureDetector.onTouchEvent(event);
                return true;
            }
        });
    }
}
```
复制
### 4. 测试效果
日志效果如下 
![](https://cdn.nlark.com/yuque/0/2023/png/317107/1684145417113-42afdb9a-184f-4e5c-a0ef-75a50b8d805c.png#averageHue=%236a6a66&clientId=uc7891883-5e93-4&from=paste&id=ua76b54ac&originHeight=185&originWidth=888&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=ueaca18bf-24e1-41b7-935a-4b5fa333f3d&title=)

---


# 使用类：SimpleOnGestureListener
# 1. 作用
集成了两个接口的手势检测功能
# 2. 与上述两个接口的区别

- OnGestureListener和OnDoubleTapListener接口里的函数都是强制必须重写的
- 而SimpleOnGestureListener类的函数则可根据需要选择性复写，因为SimpleOnGestureListener类本身已经实现了这两个接口的所有函数，只是里面全是空的而已
# 3. 使用步骤

```javascript
// 步骤1：创建手势检测器实例
    // 构造函数有3个，此处用的是第三个
    // 1. GestureDetector gestureDetector=new GestureDetector(GestureDetector.OnGestureListener listener);
    // 2. GestureDetector gestureDetector=new GestureDetector(Context context,GestureDetector.OnGestureListener listener);
    // 3. GestureDetector gestureDetector=new GestureDetector(Context context,GestureDetector.SimpleOnGestureListener listener);
    GestureDetector mGestureDetector = new GestureDetector(this, new GestureDetector.SimpleOnGestureListener() {

        // OnGestureListener接口的函数
        // 1. 用户轻触触摸屏
        public boolean onDown(MotionEvent e) {
            Log.i("MyGesture1", "onDown");
            return false;
        }

        // 2. 用户轻触触摸屏，尚未松开或拖动
        // 与onDown()的区别：无松开 / 拖动
        // 即：当用户点击的时，onDown（）就会执行，在按下的瞬间没有松开 / 拖动时onShowPress就会执行
        public void onShowPress(MotionEvent e) {
            Log.i("MyGesture", "onShowPress");
        }

        // 3. 用户长按触摸屏
        public void onLongPress(MotionEvent e) {
            Log.i("MyGesture", "onLongPress");
        }

        // 4. 用户轻击屏幕后抬起
        public boolean onSingleTapUp(MotionEvent e) {
            Log.i("MyGesture", "onSingleTapUp");
            return true;
        }

        // 5. 用户按下触摸屏 & 拖动
        public boolean onScroll(MotionEvent e1, MotionEvent e2,
                                float distanceX, float distanceY) {
            Log.i("MyGesture", "onScroll:");
            return true;
        }

        // 6. 用户按下触摸屏、快速移动后松开
        // 参数：
        // e1：第1个ACTION_DOWN MotionEvent
        // e2：最后一个ACTION_MOVE MotionEvent
        // velocityX：X轴上的移动速度，像素/秒
        // velocityY：Y轴上的移动速度，像素/秒
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
                               float velocityY) {
            Log.i("MyGesture", "onFling");
            return true;
        }

        // OnDoubleTapListener的函数
        // 1. 单击事件
        // 关于OnDoubleTapListener.onSingleTapConfirmed（）和 OnGestureListener.onSingleTapUp()的区别
        // onSingleTapConfirmed：再次点击（即双击），则不会执行
        // onSingleTapUp：手抬起就会执行
        public boolean onSingleTapConfirmed(MotionEvent e) {
            Log.i("MyGesture", "onSingleTapConfirmed");
            return false;
        }

        // 2. 双击事件
        public boolean onDoubleTap(MotionEvent e) {
            Log.i("MyGesture", "onDoubleTap");
            return false;
        }
        // 3. 双击间隔中发生的动作
        // 指触发onDoubleTap后，在双击之间发生的其它动作，包含down、up和move事件；
        public boolean onDoubleTapEvent(MotionEvent e) {
            Log.i("MyGesture", "onDoubleTapEvent");
            return false;
        }
    });

// 步骤2-1：让某个View检测手势 - 重写View的onTouch函数，将View的触屏事件交给GestureDetector处理，从而对用户手势作出响应
    View.setOnTouchListener(new View.OnTouchListener() {
        @Override
        public boolean onTouch(View v, MotionEvent event) {
            mGestureDetector.onTouchEvent(event);
            return true; // 注：返回true才能完整接收触摸事件
        }
    });

// 步骤2-2：让某个Activity检测手势：重写Activity的dispatchTouchEvent函数，将触屏事件交给GestureDetector处理，从而对用户手势作出响应
    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        mGestureDetector.onTouchEvent(ev); // 让GestureDetector响应触碰事件
        super.dispatchTouchEvent(ev); // 让Activity响应触碰事件
        return false;
    }
```
复制
### 4. 实例说明
现在对一个TextView进行手势检测 _activity_main.xml_

```javascript
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    >

    <TextView
        android:id="@+id/textView"
        android:layout_width="match_parent"
        android:layout_height="600dp"
        android:text="carson_ho Test"
        android:textAppearance="?android:attr/textAppearanceLarge" />

</LinearLayout>
```
复制
_MainActivity.java_

```javascript
public class MainActivity extends AppCompatActivity {

    TextView mTextView;
    GestureDetector mGestureDetector;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 步骤1：创建手势检测器实例 & 传入OnGestureListener接口（需要复写对应方法）
        mGestureDetector = new GestureDetector(this, new GestureDetector.SimpleOnGestureListener() {

            // OnGestureListener接口的函数
            // 1. 用户轻触触摸屏
            public boolean onDown(MotionEvent e) {
                Log.i("MyGesture1", "onDown");
                return false;
            }

            // 2. 用户轻触触摸屏，尚未松开或拖动
            // 与onDown()的区别：无松开 / 拖动
            // 即：当用户点击的时，onDown（）就会执行，在按下的瞬间没有松开 / 拖动时onShowPress就会执行
            public void onShowPress(MotionEvent e) {
                Log.i("MyGesture", "onShowPress");
            }

            // 3. 用户长按触摸屏
            public void onLongPress(MotionEvent e) {
                Log.i("MyGesture", "onLongPress");
            }

            // 4. 用户轻击屏幕后抬起
            public boolean onSingleTapUp(MotionEvent e) {
                Log.i("MyGesture", "onSingleTapUp");
                return true;
            }

            // 5. 用户按下触摸屏 & 拖动
            public boolean onScroll(MotionEvent e1, MotionEvent e2,
                                    float distanceX, float distanceY) {
                Log.i("MyGesture", "onScroll:");
                return true;
            }

            // 6. 用户按下触摸屏、快速移动后松开
            // 参数：
            // e1：第1个ACTION_DOWN MotionEvent
            // e2：最后一个ACTION_MOVE MotionEvent
            // velocityX：X轴上的移动速度，像素/秒
            // velocityY：Y轴上的移动速度，像素/秒
            public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
                                   float velocityY) {
                Log.i("MyGesture", "onFling");
                return true;
            }

            // OnDoubleTapListener的函数
            // 1. 单击事件
            // 关于OnDoubleTapListener.onSingleTapConfirmed（）和 OnGestureListener.onSingleTapUp()的区别
            // onSingleTapConfirmed：再次点击（即双击），则不会执行
            // onSingleTapUp：手抬起就会执行
            public boolean onSingleTapConfirmed(MotionEvent e) {
                Log.i("MyGesture", "onSingleTapConfirmed");
                return false;
            }

            // 2. 双击事件
            public boolean onDoubleTap(MotionEvent e) {
                Log.i("MyGesture", "onDoubleTap");
                return false;
            }
            // 3. 双击间隔中发生的动作
            // 指触发onDoubleTap后，在双击之间发生的其它动作，包含down、up和move事件；
            public boolean onDoubleTapEvent(MotionEvent e) {
                Log.i("MyGesture", "onDoubleTapEvent");
                return false;
            }
        });

        // 步骤2：重写View的onTouch函数，将触屏事件交给GestureDetector处理，从而对用户手势作出响应
        mTextView = (TextView) findViewById(R.id.textView);
        mTextView.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                mGestureDetector.onTouchEvent(event);
                return true;
            }
        });
    }
}
```
复制
### 5. 测试效果
日志效果如下 
![](https://cdn.nlark.com/yuque/0/2023/png/317107/1684145416633-b31eb7ea-0b05-41e9-b764-2f71c671cc36.png#averageHue=%23716d67&clientId=uc7891883-5e93-4&from=paste&id=u742f01d8&originHeight=236&originWidth=888&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=u0bd0daa9-7e5e-4d9a-8673-4a7433837d1&title=)
至此，关于Android手势识别类GestureDetector类使用讲解完毕。

---


# 总结

- 本文主要对Android手势识别类：GestureDetector类的使用进行全面讲解
- 接下来我将继续介绍 Android开发中的相关知识，感兴趣的同学可以继续关注本人博客[Carson_Ho的开发笔记](https://blog.csdn.net/carson_ho)

---



# 手势操作和 view  activity 的关系
将触屏事件交给GestureDetector处理后，  问题他的touch 事件还有吗？

当触摸    activity区域时（下图红色区域）
因为事件并没有交给  gestureDetector 处理。 从日志可以看到是没有手势事件的回调的。 
![image.png](https://cdn.nlark.com/yuque/0/2023/png/317107/1684145225316-06f3cac9-65d4-4589-b4be-8ab2d5c9f26c.png#averageHue=%231e6c6d&clientId=uc7891883-5e93-4&from=paste&height=761&id=u0a335c01&originHeight=1522&originWidth=724&originalType=binary&ratio=2&rotation=0&showTitle=false&size=231778&status=done&style=none&taskId=ua851dc04-8cdd-4407-8546-fd88c73a0ed&title=&width=362)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/317107/1684145146238-cf538edf-57aa-4e65-8cbd-efaf83d82654.png#averageHue=%23d7d7d7&clientId=uc7891883-5e93-4&from=paste&height=629&id=zcfKY&originHeight=1258&originWidth=2268&originalType=binary&ratio=2&rotation=0&showTitle=false&size=278804&status=done&style=none&taskId=ud8c23bb6-ed1b-4079-8dfb-039b294ee71&title=&width=1134)


---



### GestureDetectorOnGestureListener1Activity   onTouch   return  true 
```
   mTextView.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                mGestureDetector.onTouchEvent(event);
//                return true;表示该事件已经被消费，不会再向下传递，
//                return false;表示该事件没有被消费，会再向下传递，这里返回false，会再向下传递给Activity的onTouchEvent()方法
                return false;
            }
        });
```




#### true
用户轻触 后抬起
```

     I      onDown
     I  onSingleTapUp
```
#### 
用户长按未松开
```
  I  onDown
  I  onShowPress
  I  onLongPress
```
#### 
用户轻触后快速滑动后松开。 
```
 com.seachal.seachaltest              I  onDown
 com.seachal.seachaltest              I  onShowPress
 com.seachal.seachaltest              I  onScroll:
 com.seachal.seachaltest              I  onScroll:
 com.seachal.seachaltest              I  onScroll:
 com.seachal.seachaltest              I  onScroll:
 com.seachal.seachaltest              I  onScroll:
 com.seachal.seachaltest              I  onScroll:
 com.seachal.seachaltest              I  onScroll:
 com.seachal.seachaltest              I  onFling
```


只有快速滑动才会触发 fling，慢速滑动不会触发onFling。
```
  com.seachal.seachaltest              I  onDown
  com.seachal.seachaltest              I  onShowPress
  com.seachal.seachaltest              I  onScroll:
  com.seachal.seachaltest              I  onScroll:
  com.seachal.seachaltest              I  onScroll:
  com.seachal.seachaltest              I  onScroll:
  com.seachal.seachaltest              I  onScroll:
```

#### fasle    不论怎样操作都是打印这三个内容
```
               com.seachal.seachaltest              I  onDown
               com.seachal.seachaltest              I  onShowPress
               com.seachal.seachaltest              I  onLongPress
```

---



 
## OnGestureListener3Activity 
```
       mTextView = (TextView) findViewById(R.id.textView);
        mTextView.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                mGestureDetector.onTouchEvent(event);
//                return true;表示该事件已经被消费，不会再向下传递，
//                return false;表示该事件没有被消费，会再向下传递，这里返回false，会再向下传递给Activity的onTouchEvent()方法
                return true;
            }
        });
    }

    // 步骤2-2：让某个Activity检测手势：重写Activity的dispatchTouchEvent函数，将触屏事件交给GestureDetector处理，从而对用户手势作出响应
    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        mGestureDetector.onTouchEvent(ev); // 让GestureDetector响应触碰事件
        super.dispatchTouchEvent(ev); // 让Activity响应触碰事件
        return true;
    }
```
```
2023-05-17 14:39:14.634 20436-20436 ScGesture               com.seachal.seachaltest              I  onDown
2023-05-17 14:39:14.634 20436-20436 ScGesture               com.seachal.seachaltest              I  onDown
2023-05-17 14:39:14.699 20436-20436 ScGesture               com.seachal.seachaltest              I  onScroll:
2023-05-17 14:39:14.699 20436-20436 ScGesture               com.seachal.seachaltest              I  onScroll:
2023-05-17 14:39:14.977 20436-20436 ScGesture               com.seachal.seachaltest              I  onScroll:
2023-05-17 14:39:14.977 20436-20436 ScGesture               com.seachal.seachaltest              I  onScroll:
2023-05-17 14:39:14.978 20436-20436 ScGesture               com.seachal.seachaltest              I  onFling
```
```
2023-05-17 14:40:22.708 20436-20436 ScGesture               com.seachal.seachaltest              I  onDown
2023-05-17 14:40:22.801 20436-20436 ScGesture               com.seachal.seachaltest              I  onSingleTapUp
2023-05-17 14:40:22.801 20436-20436 ScGesture               com.seachal.seachaltest              I  onSingleTapUp
```


## OnGestureListener4Activity 

```
        mTextView = (TextView) findViewById(R.id.textView);
        mTextView.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                mGestureDetector.onTouchEvent(event);
//                return true;表示该事件已经被消费，不会再向下传递，
//                return false;表示该事件没有被消费，会再向下传递，这里返回false，会再向下传递给Activity的onTouchEvent()方法
                return true;
            }
        });
    }

    // 步骤2-2：让某个Activity检测手势：重写Activity的dispatchTouchEvent函数，将触屏事件交给GestureDetector处理，从而对用户手势作出响应
    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        mGestureDetector.onTouchEvent(ev); // 让GestureDetector响应触碰事件
        super.dispatchTouchEvent(ev); // 让Activity响应触碰事件
        return false;
    }
```
```
2023-05-17 14:39:14.634 20436-20436 ScGesture               com.seachal.seachaltest              I  onDown
2023-05-17 14:39:14.634 20436-20436 ScGesture               com.seachal.seachaltest              I  onDown
2023-05-17 14:39:14.699 20436-20436 ScGesture               com.seachal.seachaltest              I  onScroll:
2023-05-17 14:39:14.699 20436-20436 ScGesture               com.seachal.seachaltest              I  onScroll:
2023-05-17 14:39:14.977 20436-20436 ScGesture               com.seachal.seachaltest              I  onScroll:
2023-05-17 14:39:14.977 20436-20436 ScGesture               com.seachal.seachaltest              I  onScroll:
2023-05-17 14:39:14.978 20436-20436 ScGesture               com.seachal.seachaltest              I  onFling
```
```
2023-05-17 14:41:13.273 20436-20436 ScGesture               com.seachal.seachaltest              I  onDown
2023-05-17 14:41:13.337 20436-20436 ScGesture               com.seachal.seachaltest              I  onSingleTapUp
2023-05-17 14:41:13.337 20436-20436 ScGesture               com.seachal.seachaltest              I  onSingleTapUp
```



## SimpleOnGestureListener2Activity

单击日志
```
2023-05-17 14:51:56.467 22675-22675 MyGesture1              com.seachal.seachaltest              I  onDown
2023-05-17 14:51:56.533 22675-22675 ScGesture               com.seachal.seachaltest              I  onSingleTapUp
2023-05-17 14:51:56.769 22675-22675 ScGesture               com.seachal.seachaltest              I  onSingleTapConfirmed
```

双击日志
```
2023-05-17 14:51:14.475 22675-22675 MyGesture1              com.seachal.seachaltest              I  onDown
2023-05-17 14:51:14.570 22675-22675 ScGesture               com.seachal.seachaltest              I  onSingleTapUp
2023-05-17 14:51:14.687 22675-22675 ScGesture               com.seachal.seachaltest              I  onDoubleTap
2023-05-17 14:51:14.687 22675-22675 ScGesture               com.seachal.seachaltest              I  onDoubleTapEvent
2023-05-17 14:51:14.687 22675-22675 MyGesture1              com.seachal.seachaltest              I  onDown
2023-05-17 14:51:14.765 22675-22675 ScGesture               com.seachal.seachaltest              I  onDoubleTapEvent

```
