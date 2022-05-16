# Android端开发基础知识

### 1 目录架构

如下图所示，Android Mode下，项目目录主要分为以下3层：manifests、java、res与Gradle。

![image-20220516091743213](..\交接\图片\image-20220516091743213.png)

#### 1.1 manifests

该目录下主要存放AndroidMainfest.xml，该文件可以简单理解为项目整体配置文件。

例如下图部分，用于配置权限。

![image-20220516093105065](..\交接\图片\image-20220516093105065.png)

例如下图部分，用于添加APP信息，例如名字、标签等。

![image-20220516093224527](..\交接\图片\image-20220516093224527.png)

例如下图部分，目的是将我们写好的activity添加到项目中（必须添加）。

其中通过intent-filter可以指定第一个Activity（也就是app打开以后跳出来的第一个界面）

![image-20220516093424538](..\交接\图片\image-20220516093424538.png)

### 1.2 java

这个目录主要存放我们的activity文件，当然也可以存放其他的class（如下图的MyUtils）

![image-20220516093658693](..\交接\图片\image-20220516093658693.png)

### 1.3 res

- color：存放颜色信息，当我们指定某个组件（例如button）颜色时，可以使用
- drawable：存放图片
- layout：存放布局
- mipmap：存放图片
- values：可存放一些项目全局变量

![image-20220516093802186](..\交接\图片\image-20220516093802186.png)

#### 1.4 Gradle

该目录下最常使用的是：build.gradle（Module app）。

如下图所示，通过添加依赖，我们可以引入第三方功能。

例如为了实现图片上传功能（一般不存数据库，而是阿里云OSS），我们需要添加**com.aliyun.dpa:oss-android-sdk:2.9.5**依赖。

例如为了实现PDF生成功能，我们需要添加**libs/itextpdf-5.5.5.jar**依赖。（注意该jar包已经在项目中）

![image-20220516094142709](..\交接\图片\image-20220516094142709.png)

***

### 2 布局

#### 2.1 常用组件

- LinearLayout：线性布局
- Button：按钮
- TextView：文本框
- EditView：编辑框（例如可以让用户手动输入用户名密码）

- ImageView：图片
- ScrollView：正常情况下我们的界面是无法滑动的，通过这个组件可以实现滑动。

- com.kongzue.tabbar.TabBarView：本项目中采用的一种第三方tabbar，也就是屏幕下侧的导航切换界面
- com.allen.library.CommonTextView：本项目采用的一种第三方文本框

#### 2.2 组件属性

- android:id：定义组件的id，这样我们可以在acvivity中找到它。注意：a.xml和b.xml都设计了一个button，它们的取名可以相同。
- android:layout_width：宽度
- android:layout_height：高度
- android:text：文本内容

- android:background：背景

当然还有其他很多属性，通过英文命名大家可以确认。

注意：我们并不只能在xml中定义组件属性，我们也可以在activity中定义。

***

#### 3 Activity

接下来用MainActivity讲解：

```java
// 该activity所在包
package com.example.a47490.suyuan;

// 可以理解为c语言中的include
import android.content.DialogInterface;
import android.content.Intent;
import android.content.SharedPreferences;
import android.os.Bundle;
import android.support.v7.app.AlertDialog;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;


// 一个Activity本质上也是一个类。这里类的名字MainActivity必须与文件名一致
// 继承AppCompatActivity 
public class MainActivity extends AppCompatActivity {
    // 定义一个按钮
    private Button customer = null;
    // 重写AppCompatActivity的OnCreate方法 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // 指定布局。这里指定了main.xml布局
        setContentView(R.layout.main);
        // 绑定按钮
        customer = (Button)findViewById(R.id.customer);
        // 监听按钮
        customer.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                // do something。eg：界面跳转
            }
        });
    }
}
```

***

### 4 安装APP

通常我们可以在Android Studio安装虚拟设备，实现APP测试。

又或者将手机连到电脑上，并开启开发者模式（以及USB调试等功能）。此时点击下图的三角按钮，会出现我们的设备（这里手机未连接电脑，未出现），选择设备，点击OK，即可。

![image-20220516100505899](..\交接\图片\image-20220516100505899.png)