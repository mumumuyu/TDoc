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

```xml
<Button android:layout_width="200dp"
        android:id="@+id/btn"
        android:layout_height="100dp"
        android:text="我是lgd的按钮"
/>
```



```java
Button btn = findViewById(R.id.btn);

//点击事件
btn.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Log.e(TAG, "onClick: OnClick");
    }
});
//长按事件
btn.setOnLongClickListener(new View.OnLongClickListener() {
    @Override
    public boolean onLongClick(View v) {
        Log.e(TAG, "OnLongClick: OnLongClick");
        return false;
    }
});
//触摸事件
btn.setOnTouchListener(new View.OnTouchListener() {
    @Override
    public boolean onTouch(View v, MotionEvent event) {
        Log.e(TAG, "OnTouch: OnTouch");
        return false;
    }
});
```

#### TextView

```xml
<EditText android:layout_width="200dp" android:layout_height="100dp"
                  android:id="@+id/et"
                  android:inputType="phone"
                  android:textColorHint="#95a1aa"
                  android:hint="请输入用户名"/>

        <EditText android:layout_width="200dp" android:layout_height="100dp"
                  android:inputType="numberPassword"
                  android:textColorHint="#95a1aa"
                  android:drawableLeft="@drawable/ic_person"
                  android:drawablePadding="20dp"
                  android:paddingLeft="20dp"
                  android:hint="请输入用户名"/>

        <Button android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:text="获取用户名"
                android:id="@+id/gain_btn"/>
```

button获取edittext

```java
Button gainBtn = findViewById(R.id.gain_btn);
et = findViewById(R.id.et);

gainBtn.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        String text = et.getText().toString();
        Log.e("lgd","输入的内容为："+text);
    }
});
```

### Sqlite

SQLite是一个进程内的库，实现了自给自足的、无服务器的、零配置的、事务性的 SQL 数据库引擎。它是一个零配置的数据库，这意味着与其他数据库不一样，您不需要在系统中配置。

就像其他数据库，SQLite 引擎不是一个独立的进程，可以按应用程序需求进行静态或动态连接。SQLite 直接访问其存储文件。

android的sdk自带sqlite3、

首先

##### 建表

```java
private static final String DB_NAME = "mySQLite.db";
    private static final String TABLE_NAME_STUDENT = "student";

    private static final String CREATE_TABLE_SQL = "create table " + TABLE_NAME_STUDENT + " (id integer primary key autoincrement, name text, number text, gender text, score text)";//建表

    public MySQLiteOpenHelper(Context context) {
        super(context, DB_NAME, null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(CREATE_TABLE_SQL);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
```

##### 增

```java
public long insertData(Student student) {

        SQLiteDatabase db = getWritableDatabase();

        ContentValues values = new ContentValues();
        values.put("name", student.getName());
        values.put("number", student.getNumber());
        values.put("gender", student.getGender());
        values.put("score", student.getScore());

        return db.insert(TABLE_NAME_STUDENT, null, values);
    }
```

```java
private EditText etName,etNumber,etScore;
    private RadioButton rbMan,rbWoman;


    private MySQLiteOpenHelper mMySQLiteOpenHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_insert);

        initView();
        mMySQLiteOpenHelper = new MySQLiteOpenHelper(this);

    }
	//获取输入信息
    private void initView() {
        etName = findViewById(R.id.et_name);
        etNumber = findViewById(R.id.et_number);
        etScore = findViewById(R.id.et_score);
        rbMan = findViewById(R.id.rb_man);
        rbWoman = findViewById(R.id.rb_woman);

    }

    public void insert(View view) {
        String name = etName.getText().toString().trim();
        String number = etNumber.getText().toString().trim();
        String score = etScore.getText().toString().trim();
        String gender = "";

        if (rbMan.isChecked()) {
            gender = "男";
        }

        if (rbWoman.isChecked()) {
            gender = "女";
        }

        Student student = new Student();
        student.setName(name);
        student.setNumber(number);
        student.setGender(gender);
        student.setScore(score);

        // 插入数据库中
        long rowId = mMySQLiteOpenHelper.insertData(student);
        if (rowId != -1) {
            ToastUtil.toastShort(this, "添加成功！");
        } else {
            ToastUtil.toastShort(this, "添加失败！");
        }

    }
```



##### 删

```java
public int deleteFromDbByName(String name) {
    SQLiteDatabase db = getWritableDatabase();
    return db.delete(TABLE_NAME_STUDENT, "name like ?", new String[]{name});
}
```

##### 查

```java
public List<Student> queryFromDbByName(String name) {

        SQLiteDatabase db = getWritableDatabase();
        List<Student> studentList = new ArrayList<>();


        Cursor cursor = db.query(TABLE_NAME_STUDENT, null, "name like ?", new String[]{name}, null, null, null);

        if (cursor != null) {

            while (cursor.moveToNext()) {
                String name1 = cursor.getString(cursor.getColumnIndex("name"));
                String number = cursor.getString(cursor.getColumnIndex("number"));
                String gender = cursor.getString(cursor.getColumnIndex("gender"));
                String score = cursor.getString(cursor.getColumnIndex("score"));

                Student student = new Student();
                student.setName(name1);
                student.setNumber(number);
                student.setGender(gender);
                student.setScore(score);

                studentList.add(student);

            }

            cursor.close();

        }

        return studentList;

    }
```



##### 改

```java
    public int updateData(Student student) {

        SQLiteDatabase db = getWritableDatabase();

        ContentValues values = new ContentValues();
        values.put("name", student.getName());
        values.put("number", student.getNumber());
        values.put("gender", student.getGender());
        values.put("score", student.getScore());

        return db.update(TABLE_NAME_STUDENT, values, "name like ?", new String[]{student.getName()});
    }
```

##### AndroidManifest.xml

```xml
<application
            android:allowBackup="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name"
            android:roundIcon="@mipmap/ic_launcher_round"
            android:supportsRtl="true"
            android:theme="@style/Theme.MyApplicationBase">
        <activity android:name=".QueryActivity"
                  android:label="查询数据"
                  android:parentActivityName=".MainActivity"
        ></activity>
<!--        <activity-->
<!--                android:name=".UpdateActivity"-->
<!--                android:label="更新数据"-->
<!--                android:parentActivityName=".MainActivity" />-->
<!--        <activity-->
<!--                android:name=".DeleteActivity"-->
<!--                android:label="删除数据"-->
<!--                android:parentActivityName=".MainActivity" />-->
        <activity
                android:name=".InsertActivity"
                android:label="添加数据"
                android:parentActivityName=".MainActivity" />
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
```

##### Toast提示用法

```java
public static void toastShort(Context context, String msg) {
    Toast.makeText(context, msg, Toast.LENGTH_SHORT).show();
}

public static void toastLong(Context context, String msg) {
    Toast.makeText(context, msg, Toast.LENGTH_LONG).show();
}
```

