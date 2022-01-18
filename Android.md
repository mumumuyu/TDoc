# Android

### 控件

#### textview

走马灯~

```xml
<LinearLayout
        android:layout_height="match_parent"
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:orientation="vertical">
    <com.lgd.MyTextView
            android:layout_width="match_parent"
            android:layout_height="200dp"
            android:id="@+id/tv_one"
            android:textColor="@color/black"
            android:gravity="center_vertical"
            android:textStyle="italic"
            android:textSize="20sp"
            android:text="@string/tv_one"

            android:shadowColor="@color/red"
            android:shadowRadius="3.0"
            android:shadowDx="10.0"
            android:shadowDy="10.0"

            android:singleLine="true"
            android:ellipsize="marquee"
            android:focusableInTouchMode="true"
            android:marqueeRepeatLimit="marquee_forever"
            android:focusable="true">
        <requestFocus/>
    </com.lgd.MyTextView>
<!--            android:background="@color/red"-->
<!--    android:clickable="true"-->

</LinearLayout>
```

```java
@SuppressLint("AppCompatCustomView")
public class MyTextView extends TextView {
    public MyTextView(Context context) {
        super(context);
    }

    public MyTextView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public MyTextView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    public boolean isFocused() {
        return true;
    }
}
```

#### Button

