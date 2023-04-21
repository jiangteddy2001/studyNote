

# Android

快捷键：alt+enter / alt+ctl+f



## 1、开发环境

### 1.1 准备开发环境

1. 安装android studio
2. 安装 sdk（当前使用最新版33）
3. 创建一个新项目

![image-20230222134724409](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221347550.png)

![image-20230222134955371](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221349434.png)

启动成功后

![image-20230222140415324](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221404375.png)

### 1.2 创建模拟器

![image-20230222144251354](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221442395.png)

进入DeviceManager-->点击create device

![image-20230222144354453](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221443526.png)

选择Android11 的操作系统

![image-20230222144849486](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221448527.png)

点击download

![image-20230222144820386](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221448435.png)

![image-20230222145106028](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221451074.png)

点击 finish

![image-20230222145133936](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221451989.png)

![image-20230222145245668](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221452723.png)

启动完成

![image-20230222145403818](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221454932.png)

注意项目的视图

![image-20230222145617484](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221456521.png)

### 1.3 观察App日志

在代码里输出日志

```
package com.example.myapplication;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.util.Log;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Log.d("ning","onCreate");
    }
}
```

查看日志

![image-20230222163456647](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221635767.png)

Android系统为开发者提供了良好的日志工具android.util.Log，常用的方法有如下5个，将log的输出等级也依次指定了5个级别：

 （1）Log.v：这里的v代表Verbose啰嗦的意思，对应的log等级为VERVOSE。采用该等级的log，任何消息都会输出。

  （2）Log.d：这里的d代表Debug调试的意思，对应的log等级为DEBUG。采用该等级的log，除了VERBOSE级别的log外，剩余的4个等级的log都会被输出。

  （3）Log.i：这里的i代表information，为一般提示性的消息，对应的log等级为INFO。采用该等级的log，不会输出VERBOSE和DEBUG信息，只会输出剩余3个等级的信息。

  （4）Log.w：w代表warning警告信息，一般用于系统提示开发者需要优化android代码等场景，对应的等级为WARN。该级别log，只会输出WARN和ERROR的信息。

  （5）Log.e：e代表error错误信息，一般用于输出异常和报错信息。该级别的log，只会输出该级别信息。一般Android系统在输出crassh等致命信息的时候，都会采用该级别的log。

### 1.4 连接蓝叠模拟器

修改模拟器配置

![image-20230323101905031](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231019075.png)

进入目录

![image-20230323101837736](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231018791.png)

```
cd E:\AndroidSDK\platform-tools
```

- 输入如下命令后回车

  > adb connect localhost:5555

```
adb connect localhost:5555
```

![image-20230323101824601](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231018736.png)

## 2、App开发基础

### 2.1 目录结构

App工程分为两个层次，第一个层次是项目，依次选择菜单File→New→New Project即可创建新项目。

另一个层次是模块，模块依附于项目，每个项目至少有一个模块，也能拥有多个模块，依次选择菜单 File→New→New Module即可在当前项目创建新模块。

![image-20230222172651805](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221726846.png)

从图中看到，该项目下面有两个分类：一个是app（代表app模块）；另一个是Gradle Scripts。其中，app下面又有 3 个子目录，其功能说明如下：

（ 1 ）manifests子目录，下面只有一个XML文件，即AndroidManifest.xml，它是App的运行配置文件。

（ 2 ）java子目录，下面有 3 个com.example.myapp包，其中第一个包存放当前模块的Java源代码，后面两个包存放测试用的Java代码。

（ 3 ）res子目录，存放当前模块的资源文件。res下面又有 4 个子目录：

drawable目录存放图形描述文件与图片文件。

layout目录存放App页面的布局文件。

mipmap目录存放App的启动图标。

values目录存放一些常量定义文件，例如字符串常量strings.xml、像素常量dimens.xml、颜色常量colors.xml、样式风格定义styles.xml等

Gradle Scripts下面主要是工程的编译配置文件，主要有：

（ 1 ）build.gradle，该文件分为项目级与模块级两种，用于描述App工程的编译规则。

（ 2 ）proguard-rules.pro，该文件用于描述Java代码的混淆规则。

（ 3 ）gradle.properties，该文件用于配置编译工程的命令行参数，一般无须改动。

（ 4 ）settings.gradle，该文件配置了需要编译哪些模块。初始内容为include ‘:app’，表示只编译app模块。

（ 5 ）local.properties，项目的本地配置文件，它在工程编译时自动生成，用于描述开发者电脑的环境配置，包括SDK的本地路径、NDK的本地路径等。

### 2.2 配置文件build.gradle

新创建的App项目默认有两个build.gradle，一个是Project项目级别的build.gradle；另一个是Module模块级别的build.gradle。

项目级别的build.gradle指定了当前项目的总体编译规则，打开该文件在buildscript下面找到repositories和dependencies两个节点.

其中repositories节点用于设置Android Studio插件的网络仓库地址，而dependencies节点用于设置gradle插件的版本号。

```groovy
buildscript {
   repositories {
       // 以下四行添加阿里云的仓库地址，方便国内开发者下载相关插件
       maven { url 'https://maven.aliyun.com/repository/jcenter' }
       maven { url 'https://maven.aliyun.com/repository/google'}
       maven { url 'https://maven.aliyun.com/repository/gradle-plugin'}
       maven { url 'https://maven.aliyun.com/repository/public'}
       google()
       jcenter()
 }
   dependencies {
       // 配置gradle插件版本，下面的版本号就是Android Studio的版本号
       classpath 'com.android.tools.build:gradle:4.1.0'
 } 
}

```

模块级别的build.gradle对应于具体模块，每个模块都有自己的build.gradle，它指定了当前模块的详细编译规则。下面给chapter02模块的build.gradle补充文字注释，方便读者更好地理解每个参数的用途。

```java
android {
   // 指定编译用的SDK版本号。比如30表示使用Android 11.0编译 
   compileSdkVersion 30
   // 指定编译工具的版本号。这里的头两位数字必须与compileSdkVersion保持一致，具体的版本号可 
在sdk安装目录的“sdk\build-tools”下找到
   buildToolsVersion "30.0.3"
   defaultConfig {
       // 指定该模块的应用编号，也就是App的包名
       applicationId "com.example.chapter02"
       // 指定App适合运行的最小SDK版本号。比如19表示至少要在Android 4.4上运行
       minSdkVersion 19
       // 指定目标设备的SDK版本号。表示App最希望在哪个版本的Android上运行
       targetSdkVersion 30
       // 指定App的应用版本号
       versionCode 1
       // 指定App的应用版本名称
       versionName "1.0"
       testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
 }
   buildTypes {
       release {
           minifyEnabled false
           proguardFiles getDefaultProguardFile('proguard-android- 
optimize.txt'), 'proguard-rules.pro'
    } 
 } 
}
// 指定App编译的依赖信息 
dependencies {
   // 指定引用jar包的路径
   implementation fileTree(dir: 'libs', include: ['*.jar'])
   // 指定编译Android的高版本支持库。如AppCompatActivity必须指定编译appcompat库 
   //appcompat库各版本见
https://mvnrepository.com/artifact/androidx.appcompat/appcompat 
   implementation 'androidx.appcompat:appcompat:1.2.0'
   // 指定单元测试编译用的junit版本号
   testImplementation 'junit:junit:4.13'
   androidTestImplementation 'androidx.test.ext:junit:1.1.2'
   androidTestImplementation 'androidx.test.espresso:espresso-core:3.3.0' 
}

```

Gradle工具的版本配置在gradle\wrapper\gradle-wrapper.properties，也可以依次选择菜单File→Project Structure→Project，在弹出的设置页面中修改Gradle Version。注意每个版本的Android Studio都有对应的Gradle版本，只有二者的版本正确对应，App工程才能成功编译。比如Android Studio 4.1对应的Gradle版本为6.5

更多的版本对应关系见https://developer.android.google.cn/studio/releases/gradle-plugin#updating-plugin。


### 2.3 配置文件AndroidManifest.xml

AndroidManifest.xml指定了App的运行配置信息，它是一个XML描述文件，初始内容如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
   package="com.example.chapter02">
   <application
       android:allowBackup="true"
       android:icon="@mipmap/ic_launcher"
       android:label="@string/app_name"
       android:roundIcon="@mipmap/ic_launcher_round"
       android:supportsRtl="true"
       android:theme="@style/AppTheme">
       <activity android:name=".Main2Activity"></activity>
       <!-- activity节点指定了该App拥有的活动页面信息，其中拥有android.intent.action.MAIN的activity说明它是入口页面 -->
       <activity android:name=".MainActivity"> 
           <intent-filter>
               <action android:name="android.intent.action.MAIN" />
               <category android:name="android.intent.category.LAUNCHER" /> 
           </intent-filter>
       </activity> 
   </application>
</manifest>

```

可见AndroidManifest.xml的根节点为manifest，它的package属性指定了该App的包名。manifest下面有个application节点，它的各属性说明如下：

- android:allowBackup，是否允许应用备份。允许用户备份系统应用和第三方应用的apk安装包和应用数据，以便在刷机或者数据丢失后恢复应用，用户即可通过adb backup和adb restore来进行对应用数据的备份和恢复。为true表示允许，为false则表示不允许。

- android:icon，指定App在手机屏幕上显示的图标。

- android:label，指定App在手机屏幕上显示的名称。

- android:roundIcon，指定App的圆角图标。

- android:supportsRtl，是否支持阿拉伯语／波斯语这种从右往左的文字排列顺序。为true表示支
  持，为false则表示不支持。

- android:theme，指定App的显示风格。
  

注意到application下面还有个activity节点，它是活动页面的注册声明，只有在AndroidManifest.xml中正确配置了activity节点，才能在运行时访问对应的活动页面。初始配置的MainActivity正是App的默认主页，之所以说该页面是App主页，是因为它的activity节点内部还配置了以下的过滤信息：

```xml
<intent-filter>
   <action android:name="android.intent.action.MAIN" />
   <category android:name="android.intent.category.LAUNCHER" /> 
</intent-filter>
```

其中action节点设置的android.intent.action.MAIN表示该页面是App的入口页面，启动App时会最先打开该页面。而category节点设置的android.intent.category.LAUNCHER决定了是否在手机屏幕上显示App图标，如果同时有两个activity节点内部都设置了android.intent.category.LAUNCHER，那么桌面就会显示两个App图标


### 2.4 设计规范

![image-20230319155203549](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303191552770.png)

#### 2.4.1 界面设计与代码逻辑

将界面设计从Java代码剥离出来，通过单独的XML文件定义界面布局，就像网站使用HTML文件定义网页那样。直观地看，把界面设计与代码逻辑分开，不仅参考了网站的Web前后端分离，还有下列几点好处：

（ 1 ）使用XML文件描述App界面，可以很方便地在Android Studio上预览界面效果。

（ 2 ）一个界面布局可以被多处代码复用，反过来，一段Java代码也可能适配多个界面布局。

总的来说，界面设计与代码逻辑分离的好处多多，后续的例程都由XML布局与Java代码两部分组成。


#### 2.4.2 利用XML标记描绘应用界面

打开activity_main.xml

![image-20230319155632600](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303191556686.png)

修改代码

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center">
    <!-- 这是个线性布局， match_parent意思是与上级视图保持一致-->
        <TextView
            android:id="@+id/tv_hello"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hello World!" />
</LinearLayout>
```

![image-20230319160723273](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303191607333.png)

修改MainActivity类

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        TextView tv=findViewById(R.id.tv_hello);
        tv.setText("你好，亲爱的工程师");
        Log.d("ning","onCreate");
    }
}
```



#### 2.4.3 Activity创建与跳转

步骤：

1、在layout目录下创建xml文件

修改strings.xml

```
<resources>
    <string name="app_name">My Application</string>
    <string name="text2">Acivity Main2</string>
</resources>
```

新增xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:text="@string/text2"
        />

</LinearLayout>
```

![image-20230323103013635](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231030694.png)



2、创建与XML文件对应的Java代码

![image-20230323113818290](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231138333.png)

```

```

3、在AndroidManifest.xml注册页面配置

![image-20230323113428850](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231134886.png)

4 修改XML

添加一个按钮控件

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center">
    <!-- 这是个线性布局， match_parent意思是与上级视图保持一致-->
        <TextView
            android:id="@+id/tv_hello"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hello World!" />
       <Button
           android:id="@+id/button"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="跳转"/>
</LinearLayout>
```

![image-20230323121956682](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231219734.png)

5、修改MainActivity

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        TextView tv=findViewById(R.id.tv_hello);
        tv.setText("你好，亲爱的工程师");

        Button button=findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent=new Intent();
                intent.setClass(MainActivity.this,MainActivity2.class);
                startActivity( intent );
            }
        });

        Log.d("ning","onCreate");
    }
}
```

#### 2.4.4 创建Activity另一个方法

![image-20230323122211078](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231222158.png)

![image-20230323122233081](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231222140.png)

自动生成XML并完成注册

![image-20230323122334872](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231223935.png)

## 3、简单控件

### 3.1 文本显示

本节介绍了如何在文本视图[TextView](https://so.csdn.net/so/search?q=TextView&spm=1001.2101.3001.7020)上显示规定的文本，包括：怎样在XML文件和Java代码中设置文本内容，尺寸的大小有哪些单位、又该怎样设置文本的大小，颜色的色值是如何表达的、又该怎样设置文本的颜色。

新建一个模块

![image-20230323170601546](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231706677.png)

#### 3.1.1 设置文本内容

设置文本内容的两种方式：

1、在XML文件中通过属性android:text设置文本

```
<TextView
android:id="@+id/tv_hello"
android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:text="你好，世界" />
```

2、在Java代码中调用文本视图对象的setText方法设置文本

```
// 获取名为tv_hello的文本视图
TextView tv_hello = findViewById(R.id.tv_hello);
tv_hello.setText("你好，世界");  // 设置tv_hello的文字内容
```

![image-20230323171305535](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231713608.png)

把鼠标移到“你好，世界”上方时，Android Studio会弹出如图所示的提示框。

![image-20230323172049318](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231720363.png)

意思说这几个字是硬编码的字符串，建议使用来自@string的资源。原来Android Studio不推荐在XML布局文件里直接写字符串，因为可能有好几个页面都显示“你好，世界”，若想把这句话换成“你吃饭了吗？”，就得一个一个XML文件改过去，无疑费时费力。故而Android Studio推荐把字符串放到专门的地方管理，这个名为@string的地方位于res/values目录下的strings.xml

strings.xml定义了一个名为“app_name”的字符串常量，其值为“chapter03”。那么在此添加新的字符串定义，字符串名为“hello”，字符串值为“你好，世界”，添加之后的strings.xml内容如下所示：

```
<resources>
   <string name="app_name">chapter03</string> 
   <string name="hello">你好，世界</string> 
</resources>

```

![image-20230323172319395](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231723479.png)

添加完新的字符串定义，回到XML布局文件，将android:text属性值改为“@string/字符串名”这般，也就是“@string/hello”，修改之后的TextView标签示例如下：

```
<TextView
       android:id="@+id/tv_hello"
       android:layout_width="wrap_content" 
       android:layout_height="wrap_content" 
       android:text="@string/hello" />

```

把鼠标移到“你好，世界”上方，此时Android Studio不再弹出任何提示了。

![image-20230323172437083](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231724122.png)

若要在Java代码中引用字符串资源，则调用setText方法时填写形如“R.string.字符串名”的参数，就本例而言填入“R.string.hello”，修改之后的Java代码示例如下

```
public class TextViewActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_text_view);
        TextView tv_hello = findViewById(R.id.tv_hello);
        tv_hello.setText(R.string.hello);  // 设置tv_hello的文字内容
    }
}
```

![image-20230323172621089](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231726133.png)

至此不管XML文件还是Java代码都从strings.xml引用字符串资源，以后想把“你好，世界”改为其他文字的话，只需改动strings.xml一个地方即可。

#### 3.1.2 设置文本大小

TextView允许设置文本内容，也允许设置文本大小，在Java代码中调用setTextSize方法，即可指定文本大小

```
tv_hello.setTextSize(30);
```

![image-20230323174759818](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231747873.png)

在XML文件中则通过属性android:textSize指定文本大小，可是如果给TextView标签添加“android:textSize=“30””，数字马上变成红色，鼠标移过去还会提示错误“Cannot resolve symbol ‘30’”，意思是无法解析“30”这个符号。原来文本大小存在不同的字号单位，XML文件要求在字号数字后面写明单位类型，常见的字号单位主要有px、dp、sp 3种，分别介绍如下。

1 ．px

px是手机屏幕的最小显示单位，它与设备的显示屏有关。一般来说，同样尺寸的屏幕（比如 6 英寸手机），如果看起来越清晰，则表示像素密度越高，以px计量的分辨率也越大。

2 ．dp

dp有时也写作dip，指的是与设备无关的显示单位，它只与屏幕的尺寸有关。一般来说，同样尺寸的屏幕以dp计量的分辨率是相同的，比如同样是 6 英寸手机，无论它由哪个厂家生产，其分辨率换算成dp单位都是一个大小。

3 ．sp

sp的原理跟dp差不多，但它专门用来设置字体大小。手机在系统设置里可以调整字体的大小（小、标准、大、超大）。设置普通字体时，同数值dp和sp的文字看起来一样大；如果设置为大字体，用dp设置的文字没有变化sp设置的文字就变大了。字体大小采用不同单位的话，显示的文字大小各不相同。例如，30px、30dp、30sp这 3 个字号，在不同手机上的显示大小有所差异。有的手机像素密度较低，一个dp相当于两个px，此时30px等同于15dp；有的手机像素密度较高，一个dp相当于 3 个px，此时30px等同于10dp。假设某个App的内部文本使用字 号30px，则该App安装到前一部手机的字体大小为15dp，安装到后一部手机的字体大小为10dp，显然 后一部手机显示的文本会更小。

既然XML文件要求android:textSize必须指定字号单位，为什么Java代码调用setTextSize只填数字不填单位呢？其实查看SDK源码，找到setTextSize方法的实现代码如下所示：

```
public void setTextSize(float size) {
   setTextSize(TypedValue.COMPLEX_UNIT_SP, size); 
}
```

原来纯数字的setTextSize方法，内部默认字号单位为sp（COMPLEX_UNIT_SP），这也从侧面印证了之前的说法：sp才是Android推荐的字号单位。

综上：
dp的UI效果只在相同尺寸的屏幕上相同，如果屏幕尺寸差异过大，则需要重做dp适配 。这也是平板需要单独做适配的原因，可见 dp不是比例 。

#### 3.1.2 设置文本的颜色

在Java代码中调用setTextColor方法即可设置文本颜色，具体在Color类中定义了 12 种颜色

![image-20230323194108208](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303231941363.png)

```
     // 从布局文件中获取名为tv_code_system的文本视图
        TextView tv_code_system = findViewById(R.id.tv_code_system);
       // 将tv_code_system的文字颜色设置系统自带的绿色
        tv_code_system.setTextColor(Color.GREEN);
```

可是XML文件无法引用Color类的颜色常量，为此Android制定了一套规范的编码标准，将色值交由透明度alpha和RGB三原色（红色red、绿色green、蓝色blue）联合定义。该标准又有八位十六进制数与六位十六进制数两种表达方式，例如八位编码FFEEDDCC中，FF表示透明度，EE表示红色的浓度，DD表示绿色的浓度，CC表示蓝色的浓度。透明度为FF表示完全不透明，为 00 表示完全透明。RGB三色的数值越大，表示颜色越浓，也就越暗；数值越小，表示颜色越淡，也就越亮。RGB亮到极致就是白色，暗到极致就是黑色。

```java
public class TextColorActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_text_color);
        // 从布局文件中获取名为tv_code_system的文本视图
        TextView tv_code_system = findViewById(R.id.tv_code_system);
       // 将tv_code_system的文字颜色设置系统自带的绿色
        tv_code_system.setTextColor(Color.GREEN);
        // 从布局文件中获取名为tv_code_eight的文本视图
        TextView tv_code_eight = findViewById(R.id.tv_code_eight);
        // 将tv_code_eight的文字颜色设置为不透明的绿色，即正常的绿色
        tv_code_eight.setTextColor(0xff00ff00);
        // 从布局文件中获取名为tv_code_six的文本视图
        TextView tv_code_six = findViewById(R.id.tv_code_six);
        // 将tv_code_six的文字颜色设置为透明的绿色，透明就是看不到
        tv_code_six.setTextColor(0x00ff00);

    }
}
```

也可以用XML来设置颜色

![image-20230323203326353](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303232033440.png)



### 3.2 视图基础

#### 3.2.1 设置视图的宽高

手机屏幕是块长方形区域，较短的那条边叫作宽，较长的那条边叫作高。

App控件通常也是长方形状，控件宽度通过属性android:layout_width表达，控件高度通过属性android:layout_height表达.

宽高的取值有三种

1、match_parent：表示与上级视图保持一致。

2、wrap_content：表示与内容自适应。

3、以dp为单位的具体尺寸。

创建一个ViewBorderActivity

![image-20230324152431647](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303241524765.png)

![image-20230324152446425](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303241524495.png)

工具类代码

```java
// 根据手机的分辨率从 dp 的单位 转成为 px(像素)
public static int dip2px(Context context, float dpValue) { 
   // 获取当前手机的像素密度（1个dp对应几个px）
   float scale = context.getResources().getDisplayMetrics().density; 
   return (int) (dpValue * scale + 0.5f); // 四舍五入取整
}
```

![image-20230324152538169](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303241525228.png)

有了上面定义的公共方法dip2px，就能将某个dp数值转换成px数值

```java
// 获取名为tv_code的文本视图
TextView tv_code = findViewById(R.id.tv_code); 
// 获取tv_code的布局参数（含宽度和高度）
ViewGroup.LayoutParams params = tv_code.getLayoutParams(); 
// 修改布局参数中的宽度数值，注意默认px单位，需要把dp数值转成px数值 
params.width = Utils.dip2px(this, 300);
tv_code.setLayoutParams(params);  // 设置tv_code的布局参数

```

具体代码：

```
public class ViewBorderActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_view_border);
        // 获取名为tv_code的文本视图
        TextView tv_code = findViewById(R.id.tv_code);
        // 获取tv_code的布局参数（含宽度和高度）
        ViewGroup.LayoutParams params = tv_code.getLayoutParams();
        // 修改布局参数中的宽度数值，注意默认px单位，需要把dp数值转成px数值
        params.width = Utils.dip2px(this, 300);
        tv_code.setLayoutParams(params);  // 设置tv_code的布局参数

    }
}
```

XML

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="5dp"
        android:background="#00ffff"
        android:text="视图宽度采用wrap_content定义"
        android:textColor="#000000"
        android:textSize="17sp" />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="5dp"
        android:background="#00ffff"
        android:text="视图宽度采用match_parent定义"
        android:textColor="#000000"
        android:textSize="17sp" />
    <TextView
        android:layout_width="300dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="5dp"
        android:background="#00ffff"
        android:text="视图宽度采用固定大小"
        android:textColor="#000000"
        android:textSize="17sp" />
    <TextView
        android:id="@+id/tv_code"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="5dp"
        android:background="#00ffff"
        android:text="通过代码指定视图宽度"
        android:textColor="#000000"
        android:textSize="17sp" />

</LinearLayout>
```

打开演示界面如图所示，依据背景色判断文本视图的边界，可见wrap_content方式刚好包住了文本内容，match_parent方式扩展到了与屏幕等宽，而300dp的宽度介于前两者之间。

![image-20230324152731124](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303241527170.png)

#### 3.2.2 设置视图的间距

设置间距有2个方式

外间距：android:layout_margin一次性设置四周的间距。包括android:layout_margin、android:layout_marginLeft、android:layout_marginRight、android:layout_marginTop、android:layout_marginBottom

内间距：采用padding属性。padding也提供了paddingTop、paddingBottom、paddingLeft、paddingRight四个方向的距离属性

```
<!-- 最外层的布局背景为蓝色 -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="300dp"
    android:background="#00aaff"
    android:orientation="vertical">
    <!-- 中间层的布局背景为黄色 -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_margin="20dp"
        android:background="#ffff99"
        android:padding="60dp">
        <!-- 最内层的视图背景为红色 -->
        <View
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#ff0000" />

    </LinearLayout>

</LinearLayout>
```

![image-20230324161137317](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303241611369.png)

**layout_margin指的是当前图层与外部图层的距离**
**而padding指的是当前图层与内部图层的距离。**

#### 3.2.3 视图的对齐方式

有2种设置方式：

1、采用layout_gravity属性，指定当前视图相对上级视图的哪个方向对齐

2、采用gravity属性，指定了下级视图相对于当前视图的对齐方式



**layout_gravity和gravity取值包括**：top、bottom、left、right 还可以用竖线拼接 ，比如"left|top" 表示朝左上角对其

```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- 最外层的布局背景为橙色，它的下级视图在水平方向排列 -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="300dp"
    android:background="#ffff99"
    android:padding="5dp">
    <!-- 第一个子布局背景为红色，它在上级视图中朝下对齐，它的下级视图则靠左对齐 -->
    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="200dp"
        android:layout_weight="1"
        android:layout_gravity="bottom"
        android:gravity="left"
        android:background="#ff0000"
        android:layout_margin="10dp"
        android:padding="10dp">
        <!-- 内部视图的宽度和高度都是100dp，且背景色为青色 -->
        <View
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:background="#00ffff" />
    </LinearLayout>
    <!-- 第二个子布局背景为红色，它在上级视图中朝上对齐，它的下级视图则靠右对齐 -->
    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="200dp"
        android:layout_weight="1"
        android:layout_gravity="top"
        android:gravity="right"
        android:background="#ff0000"
        android:layout_margin="10dp"
        android:padding="10dp">
        <!-- 内部视图的宽度和高度都是100dp，且背景色为青色 -->
        <View
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:background="#00ffff" />
    </LinearLayout>
</LinearLayout>

```

![image-20230325104149373](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303251041497.png)



### 3.3 常用布局

#### 3.3.1 线性布局LinearLayout

LinearLayout通过属性android:orientation区分两种方向：

- 水平方向，属性值为horizontal

- 垂直方向，属性值为vertical

- 如果LinearLayout标签不指定具体方向，则系统默认该布局为水平方向排列

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="横排第一个"
            android:textSize="17dp"
            android:textColor="#000000"/>

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="横排第2个"
            android:textSize="17dp"
            android:textColor="#000000"/>
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="竖排第一个"
            android:textSize="17dp"
            android:textColor="#000000"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="竖排第2个"
            android:textSize="17dp"
            android:textColor="#000000"/>

    </LinearLayout>

</LinearLayout>
```

![image-20230325214434407](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303252144521.png)

默认除了方向之外，线性布局还有一个权重概念layout_weight

所谓权重，指的是线性布局的下级视图各自拥有多大比例的宽高。

```
layout_width=0 ,layout_weight表示水平方向的宽度比例

layout_height=0 ,layout_weight表示垂直方向的高度比例
```

举例

```xml
   <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <TextView
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="横排第一个"
            android:textSize="17dp"
            android:textColor="#000000"/>

        <TextView
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="横排第2个"
            android:textSize="17dp"
            android:textColor="#000000"/>
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="0dp"
            android:layout_weight="1"
            android:text="竖排第一个"
            android:textSize="17dp"
            android:textColor="#000000"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="0dp"
            android:layout_weight="1"
            android:text="竖排第2个"
            android:textSize="17dp"
            android:textColor="#000000"/>

    </LinearLayout>
```

![image-20230325215720699](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303252157760.png)

![image-20230325215825970](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303252158022.png)

#### 3.3.2 相对布局RelativeLayout

相对布局的下级视图位置则由其他视图决定。参照物分为2种

- 与该视图自身平级的视图；

- 该视图的上级视图（也就是它归属的RelativeLayout）

如果不设定下级视图的参照物，那么下级视图默认显示在RelativeLayout内部的左上角。



a）第一类:属性值为true或false
　　android:layout_centerHrizontal 水平居中
　　android:layout_centerVertical 垂直居中
　　android:layout_centerInparent 相对于父元素完全居中
　　android:layout_alignParentBottom 贴紧父元素的下边缘
　　android:layout_alignParentLeft 贴紧父元素的左边缘
　　android:layout_alignParentRight 贴紧父元素的右边缘
　　android:layout_alignParentTop 贴紧父元素的上边缘

b）第二类：属性值必须为id的引用名“@id/id-name”
　　android:layout_below 在某元素的下方
　　android:layout_above 在某元素的的上方
　　android:layout_toLeftOf 在某元素的左边
　　android:layout_toRightOf 在某元素的右边
　　android:layout_alignTop 本元素的上边缘和某元素的的上边缘对齐
　　android:layout_alignLeft 本元素的左边缘和某元素的的左边缘对齐
　　android:layout_alignBottom 本元素的下边缘和某元素的的下边缘对齐
　　android:layout_alignRight 本元素的右边缘和某元素的的右边缘对齐

c）第三类：属性值为具体的像素值，如30dip，40px
　　android:layout_marginBottom 离某元素底边缘的距离
　　android:layout_marginLeft 离某元素左边缘的距离
　　android:layout_marginRight 离某元素右边缘的距离
　　android:layout_marginTop 离某元素上边缘的距离

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="150dp">

    <TextView
        android:id="@+id/tv_center"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="#ffffff"
        android:layout_centerInParent="true"
        android:text="我在中间"
        android:textSize="11sp"
        android:textColor="#000000" />
    <TextView
        android:id="@+id/tv_center_horizontal"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="#ffffff"
        android:layout_centerHorizontal="true"
        android:text="我在中间"
        android:textSize="11sp"
        android:textColor="#000000" />


</RelativeLayout>
```

![image-20230325224529714](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303252245764.png)

#### 3.3.3 网格布局GridLayout

网格布局GridLayout支持多行多列

网格布局默认从左往右、从上到下排列，新增2个属性

- columnCount指定了网格的列数，即每行能放多少个视图

- rowCount指定了网格的行数，即每列能放多少个视图。

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:columnCount="2"
    android:rowCount="2">

    <TextView
        android:layout_width="180dp"
        android:layout_height="60dp"
        android:text="浅红色"
        android:background="#ffcccc"
        android:textSize="17sp"
        android:textColor="#000000"/>
    <TextView
        android:layout_width="180dp"
        android:layout_height="60dp"
        android:text="橙色"
        android:background="#ffaa00"
        android:textSize="17sp"
        android:textColor="#000000"/>
    <TextView
        android:layout_width="180dp"
        android:layout_height="60dp"
        android:text="绿色"
        android:background="#00ff00"
        android:textSize="17sp"
        android:textColor="#000000"/>
    <TextView
        android:layout_width="180dp"
        android:layout_height="60dp"
        android:text="深紫色"
        android:background="#660066"
        android:textSize="17sp"
        android:textColor="#000000"/>

</GridLayout>
```



![image-20230325225513002](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303252255057.png)

  android:gravity="center"属性让文字水平居中

```
    <TextView
        android:layout_width="180dp"
        android:layout_height="60dp"
        android:text="绿色"
        android:gravity="center"
        android:background="#00ff00"
        android:textSize="17sp"
        android:textColor="#000000"/>
```

![image-20230325225707014](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303252257062.png)

让单元格填充满，使用属性

```
android:layout_width="0dp"
android:layout_columnWeight="1"
```

```
<TextView
        android:layout_width="0dp"
        android:layout_columnWeight="1"
        android:layout_height="60dp"
        android:text="浅红色"
        android:gravity="center"
        android:background="#ffcccc"
        android:textSize="17sp"
        android:textColor="#000000"/>
```

![image-20230325225927779](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303252259831.png)

#### 3.3.4 滚动视图ScrollView

滚动视图也分为垂直方向和水平方向两类

- ScrollView（垂直滚动视图名为），layout_width属性值设置为match_parent，layout_height属性值设置为wrap_content。

- HorizontalScrollView（水平滚动视图），layout_width属性值设置为wrap_content，layout_height属性值设置为match_parent。

- 滚动视图节点下面必须且只能挂着一个子布局节点，否则会在运行时报错Caused by：java.lang.IllegalStateException：ScrollView can host only one direct child。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
   <!--  水平滚动-->
   <HorizontalScrollView
       android:layout_width="wrap_content"
       android:layout_height="200dp">
        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:orientation="horizontal">
           
           <View
               android:layout_width="400dp"
               android:layout_height="match_parent"
              android:background="#aaffff"/>
           <View
               android:layout_width="400dp"
               android:layout_height="match_parent"
               android:background="#ffff00"/>
        </LinearLayout>
   </HorizontalScrollView>

    <!-- 垂直滚动-->
    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:orientation="vertical">

            <View
                android:layout_width="match_parent"
                android:layout_height="600dp"
                android:background="#00ff00"/>
            <View
                android:layout_width="match_parent"
                android:layout_height="600dp"
                android:background="#ffffaa"/>
        </LinearLayout>


    </ScrollView>

</LinearLayout>
```



![image-20230325232440065](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303252324123.png)

### 3.4按钮触控

本节介绍了按钮控件的常见用法，包括：如何设置大小写属性与点击属性，如何响应按钮的点击事件和长按事件，如何禁用按钮又该如何启用按钮，等等。

#### 3.4.1 按钮控件Button

Button是由TextView派生而来，所以文本视图拥有的属性和方法

- Button拥有默认的按钮背景，而TextView默认无背景；

- Button的内部文本默认居中对齐，而TextView的内部文本默认靠左对齐

- Button按钮控件英文都默认转成大写字母显示

Button的2个属性：

textAllCaps属性：指定是否把英文字母转为大写。true表示自动转为大写，false表示不转换

onClick属性：用户的点击按钮事件，该属性的值是个方法名



快捷键：ctl+alt+f

![image-20230326104638662](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303261046785.png)

```
public class DateUtil{

    public static String getNowTime(){
        SimpleDateFormat sdf=new SimpleDateFormat("HH:mm:ss");
        return sdf.format(new Date());
    }
}
```



```java
public class ButtonStyleActivity extends AppCompatActivity {

    private TextView tv_result;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_button_style);
        //控件的初始化，一般放在onCreate
        tv_result= findViewById(R.id.tv_result);
    }

    public void doClick(View view){
       String desc =String.format("%s 您点击了按钮:  %s", DateUtil.getNowTime(),((Button)view).getText());
       tv_result.setText(desc);

    }
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="17sp"
        android:gravity="center"
        android:text="下面的按钮英文默认大写"
        android:textColor="@color/black"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Hello World"
        android:textSize="17sp"
        android:textColor="@color/black"/>

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="17sp"
        android:gravity="center"
        android:text="下面的按钮英文保持原状"
        android:textColor="@color/black"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Hello World"
        android:textSize="17sp"
        android:textAllCaps="false"
        android:textColor="@color/black"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="直接点击方法"
        android:textSize="17sp"
        android:textAllCaps="false"
        android:textColor="@color/black"
        android:onClick="doClick"/>

    <TextView
        android:id="@+id/tv_result"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="17sp"
        android:text="这里查看按钮点击效果"
        android:textColor="@color/black"/>
</LinearLayout>
```

![image-20230326105344381](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303261053432.png)



#### 3.4.2 点击事件和长按事件

监听器，意思是专门监听控件的动作行为，它平时无所事事，只有控件发生了指定的动作，监听器才会触发开关去执行对应的代码逻辑。

按钮控件有2种常用的监听器：

点击监听器，通过setOnClickListener设置，按钮被按住少于500毫秒，会触发此事件

长按监听器，通过setOnLongClickListener设置。按钮被按住超过500毫秒，会触发长按事件



1 点击事件：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <Button
        android:id="@+id/btn_click_single"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="指定单独的点击监听器"
        android:textColor="#000000"
        android:textSize="15sp"/>

    <TextView
        android:id="@+id/tv_result"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:gravity="center"
        android:textColor="#000000"
        android:textSize="15sp"
        android:text="这里查看按钮点击的结果" />
</LinearLayout>
```

 定义一个点击监听器，它实现了接口View.OnClickListener

```java
public class ButtonClickActivity extends AppCompatActivity {

    private TextView tv_result;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_button_click);
        tv_result= findViewById(R.id.tv_result);
        // 从布局文件中获取名为btn_click_single的按钮控件
        Button btn_click_single = findViewById(R.id.btn_click_single);
        btn_click_single.setOnClickListener(new MyOnClickListener(tv_result));

    }

    static class MyOnClickListener implements View.OnClickListener{
        private final TextView tv_result;

        public MyOnClickListener(TextView tv_result){
              this.tv_result=tv_result;
        }

        @Override
        public void onClick(View view) {
            String desc = String.format("%s 您点击了按钮：%s",
                    DateUtil.getNowTime(), ((Button)view).getText());
            tv_result.setText(desc); // 设置文本视图的文本内容

        }
    }
}
```

然后还有一种写法,implements  View.OnClickListener

注册统一的监听器，也就是让当前页面实现接口View.OnClickListener

```

   <Button
        android:id="@+id/btn_click_public"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="指定公共的点击监听器"
        android:textColor="#000000"
        android:textSize="15sp"/>
```

```java
    @Override
    public void onClick(View view) {
        if(view.getId() == R.id.btn_click_public){
            String desc = String.format("%s 您点击了按钮：%s",DateUtil.getNowTime(), ((Button)view).getText());
            tv_result.setText(desc); // 设置文本视图的文本内容
        }
    }
```

![image-20230328152714417](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303281527478.png)

![image-20230328152625371](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303281526462.png)

2 长按事件

每当控件被按住超过 500 毫秒之后，就会触发该控件的长按事件。若要捕捉按钮的长按事件，可调用按钮对象的setOnLongClickListener方法设置长按监听器

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <Button
        android:id="@+id/btn_long_click"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="指定单独的长按点击监听器"
        android:textColor="#000000"
        android:textSize="15sp"/>

    <TextView
        android:id="@+id/tv_result"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:gravity="center"
        android:textColor="#000000"
        android:textSize="15sp"
        android:text="这里查看按钮长按点击的结果" />

</LinearLayout>
```

```java
public class ButtonLongActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_button_long);
        TextView tv_result= findViewById(R.id.tv_result);

        Button btn_long_click = findViewById(R.id.btn_long_click);
        btn_long_click.setOnLongClickListener(view -> {
            String desc = String.format("%s 您点击了按钮：%s", DateUtil.getNowTime(), ((Button)view).getText());
            tv_result.setText(desc); // 设置文本视图的文本内容
            return true;
        });
    }
}
```

![image-20230328154124213](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303281541257.png)



#### 3.4.3 禁用和恢复按钮

在实际的业务场景中，按钮先后拥有两种状态，即不可用状态与可用状态

（ 1 ）不可用按钮：按钮不允许点击，即使点击也没反应，同时按钮文字为灰色。

（ 2 ）可用按钮：按钮允许点击，点击按钮会触发点击事件，同时按钮文字为正常的黑色。

android:enabled，该属性值为true时表示启用按钮，即允许点击按钮；该属性值为false时表示禁用按钮

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btn_enable"
            android:layout_width="0dp"
            android:layout_weight="1"
            android:layout_height="wrap_content"
            android:text="启用测试按钮"
            android:textColor="#000000"
            android:textSize="17sp"/>
        <Button
            android:id="@+id/btn_disable"
            android:layout_width="0dp"
            android:layout_weight="1"
            android:layout_height="wrap_content"
            android:text="禁用测试按钮"
            android:textColor="#000000"
            android:textSize="17sp"/>
    </LinearLayout>
    <Button
        android:id="@+id/btn_test"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:enabled="false"
        android:text="测试按钮"
        android:textColor="#888888"
        android:textSize="17sp"/>
    <TextView
        android:id="@+id/tv_result"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:gravity="center"
        android:textColor="#000000"
        android:textSize="17sp"
        android:text="这里查看按钮点击的结果" />

</LinearLayout>
```

通过setEnabled方法设置按钮的可用状态（true表示启用，false表示禁用）。

```java
public class ButtonEnableActivity extends AppCompatActivity implements View.OnClickListener {

    private Button btn_test;
    private TextView tv_result;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_button_enable);
        tv_result = findViewById(R.id.tv_result);

        Button btn_enable = findViewById(R.id.btn_enable);
        Button btn_disable = findViewById(R.id.btn_disable);
        btn_test = findViewById(R.id.btn_test);

        btn_enable.setOnClickListener(this);
        btn_disable.setOnClickListener(this);
        btn_test.setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()){
            case R.id.btn_enable:
                btn_test.setEnabled(true);
                btn_test.setTextColor(Color.BLACK);
                break;
            case R.id.btn_disable:
                btn_test.setEnabled(false);
                btn_test.setTextColor(Color.GRAY);
                break;
            case R.id.btn_test:
                String desc = String.format("%s 您点击了按钮：%s", DateUtil.getNowTime(), ((Button)view).getText());
                tv_result.setText(desc); // 设置文本视图的文本内容
                break;
        }

    }
}
```

![image-20230328172621930](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303281726974.png)



### 3.5 图像显示

#### 3.5.1 图像视图ImageView

图片一般放到res/drawable目录，设置显示图片有2种方式

在XML文件，通过属性android:src设置图片资源。如“@drawable/不含扩展名的图片名称”

```
<ImageView
        android:id="@+id/iv_scale"
        android:layout_width="match_parent"
        android:layout_height="220dp"
        android:src="@drawable/camera" />
```

在Java代码中设置，调用ImageView控件的setImageResource方法，方法参数格式形如“R.drawable.不含扩展名的图片名称”。

```java
  ImageView iv_scale = findViewById(R.id.iv_scale);
  iv_scale.setImageResource(R.drawable.camera);
```

Java代码中可调用setScaleType方法设置图像视图的缩放类型

![image-20230328173847557](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303281738653.png)

```
// 将缩放类型设置为“保持宽高比例，缩放图片使其位于视图中间”
iv_scale.setScaleType(ImageView.ScaleType.FIT_CENTER);
```

用XML设置

```
 <ImageView
        android:id="@+id/iv_scale"
        android:layout_width="match_parent"
        android:layout_height="220dp"
        android:layout_marginTop="5dp"
        android:src="@drawable/camera"
        android:scaleType="center"/>
```

- fitCenter既允许缩小图片、也允许放大图片。
- centerInside只允许缩小图片、不允许放大图标。
- 而center自始至终保持原始尺寸（既不允许缩小图片、也不允许放大图片）。



#### 3.5.2 图像按钮ImageButton

ImageButton才是显示图片的图像按钮

与Button区别

- Button既可显示文本也可显示图片（通过setBackgroundResource方法设置背景图片），而
- ImageButton只能显示图片不能显示文本。
- ImageButton上的图像可按比例缩放，而Button通过背景设置的图像会拉伸变形，因为背景图采取fitXY方式，无法按比例缩放。
- Button只能靠背景显示一张图片，而ImageButton可分别在前景和背景显示图片，从而实现两张图片叠加的效果。
  

```
<ImageButton
       android:layout_width="match_parent" 
       android:layout_height="80dp"
       android:src="@drawable/sqrt" 
       android:scaleType="fitCenter" />
```

图像按钮默认的缩放类型为center（保持原始尺寸不缩放图片），而非图像视图默认的fitCenter，倘若图片尺寸较大，那么图像按钮将无法显示整个图片。为避免显示不完整的情况，XML文件中的ImageButton标签必须指定fitCenter的缩放类型

#### 3.5.3 同时展示文本与图像

方法有2种：

用LinearLayout对ImageView和TextView组合布局

通过Button的drawable属性设置文本周围的图标

- drawableTop：指定文字上方的图片。
- drawableBottom：指定文字下方的图片。
- drawableLeft：指定文字左边的图片。
- drawableRight：指定文字右边的图片。
- drawablePadding：指定图片与文字的间距。

```
 <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="图标在左边"
        android:drawableLeft="@drawable/camera"/>
```

![image-20230328175308432](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303281753473.png)



## 4、活动Activity

### 4.1 启停活动页面

#### 4.1.1 Activity的启动和结束

从当前页面跳转到新页面，代码：

```
startActivity(new Intent(源页面.this, 目标页面.class));”
```

从当前页面回到上一个页面，相当于关闭当前页面，返回代码如下：

```
调用finish方法
```

创建2个页面

![image-20230330171212947](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303301712005.png)

![image-20230330171228252](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303301712334.png)

```
public class ActStartActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_act_start);
        findViewById(R.id.btn_act_next).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        if (view.getId() == R.id.btn_act_next) {
            startActivity(new Intent(this,ActFinishActivity.class));
        }
    }
}
```

```
public class ActFinishActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_act_finish);
        findViewById(R.id.btn_finish).setOnClickListener(this);
        findViewById(R.id.iv_back).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.iv_back || v.getId() == R.id.btn_finish) {
            finish(); // 结束当前的活动页面
        }
    }
}
```



#### 4.1.2  Activity的生命周期

活动还有其他几种生命周期行为，它们对应的方法说明如下：

- onCreate：创建活动。此时会把页面布局加载进内存，进入了初始状态。
- onStart：开启活动。此时会把活动页面显示在屏幕上，进入了就绪状态。
- onResume：恢复活动。此时活动页面进入活跃状态，能够与用户正常交互，例如允许响应用户的点击动作、允许用户输入文字等。
- onPause：暂停活动。此时活动页面进入暂停状态（也就是退回就绪状态），无法与用户正常交互。
- onStop：停止活动。此时活动页面将不在屏幕上显示。
- onDestroy：销毁活动。此时回收活动占用的系统资源，把页面从内存中清除掉。
- onRestart：重启活动。处于停止状态的活动，若想重新开启的话，无须经历onCreate的重复创建过程，而是走onRestart的重启过程。
- onNewIntent：重用已有的活动实例。

上述的生命周期方法，涉及复杂的App运行状态，更直观的活动状态切换过程如图所示。

![image-20230330162727703](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303301627816.png)

过滤Log的方式也发生了变化，如果只想看APP某个级别的Log，可以在过滤框输入“package:mine level:info”，如果还想过滤TAG，可以再添加个 “tag:xxx”（不同的过滤条件用空格分隔）

测试生命周期

![image-20230330230716117](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303302307169.png)

注意如果logcat不显示

![image-20230330230636315](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303302306434.png)

![image-20230330230650757](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303302306829.png)

#### 4.1.3 Activity的启动模式

某APP先后打开两个活动，此时活动栈的变动情况如图所示：

![image-20230331153937327](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303311539418.png)

依次结束已打开的两个活动，此时活动栈的变动情况：

![image-20230331154007950](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303311540998.png)

Android允许在创建活动时指定该活动的启动模式，通过启动模式控制活动的出入栈行为。

有2种方式来设置（配置文件和代码配置）

修改AndroidManifest.xml

打开AndroidManifest.xml，给activity节点添加属性android:launchMode，属性值填入standard表示采取标准模式，当然不添加属性的话默认就是标准模式。具体的activity节点配置内容示例如下：

```
<activity android:name=".JumpFirstActivity" android:launchMode="standard" />
```

- 默认启动模式 standard
  该模式可以被设定，不在 manifest 设定时候，Activity 的默认模式就是 standard。在该模式下，启动的 Activity 会依照启动顺序被依次压入 Task 栈中：

![image-20230331154452202](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303311544283.png)

- 栈顶复用模式 singleTop
- 在该模式下，如果栈顶 Activity 为我们要新建的 Activity（目标Activity），那么就不会重复创建新的Activity。
  应用场景：
  适合开启渠道多、多应用开启调用的 Activity，通过这种设置可以避免已经创建过的 Activity 被重复创建，多数通过动态设置使用。例如微信支付宝支付界面。

![image-20230331154657488](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303311546571.png)

- 栈内复用模式 singleTask
  与 singleTop 模式相似，只不过 singleTop 模式是只是针对栈顶的元素，而 singleTask 模式下，如果task 栈内存在目标 Activity 实例，则将 task 内的对应 Activity 实例之上的所有 Activity 弹出栈，并将对应 Activity 置于栈顶，获得焦点。

![image-20230331154903803](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303311549889.png)

应用场景：

1 程序主界面 ：我们肯定不希望主界面被创建多次，而且在主界面退出的时候退出整个 App 是最好的效果。

2 耗费系统资源的Activity ：对于那些及其耗费系统资源的 Activity，我们可以考虑将其设为 singleTask模式，减少资源耗费。



- 全局唯一模式 singleInstance

在该模式下，我们会为目标 Activity 创建一个新的 Task 栈，将目标 Activity 放入新的 Task，并让目标Activity获得焦点。新的 Task 有且只有这一个 Activity 实例。 如果已经创建过目标 Activity 实例，则不会创建新的 Task，而是将以前创建过的 Activity 唤醒。
![image-20230331155145010](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303311551090.png)



- 动态设置启动模式

在上述所有情况，都是我们在Manifest中通过 launchMode 属性设置的，这个被称为静态设置，动态设置是通过 Java 代码设置的。通过 Intent 动态设置 Activity启动模式intent.setFlags();如果同时有动态和静态设置，那么动态的优先级更高。接下来我们来看一下如何动态的设置 Activity 启动模式。

1 对于不允许重复返回的情况，可以设置启动标志FLAG_ACTIVITY_CLEAR_TOP

![image-20230331162427366](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303311624423.png)

```java
public class JumpFirstActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_jump_first);
        findViewById(R.id.btn_jump_second).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        //创建一个意图对象
        Intent intent = new Intent(this, JumpSecondActivity.class);
       // 当栈中存在待跳转的活动实例时，则重新创建该活动的实例，并清除原实例上方的所有实例
        intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);  // 设置启动标志
        startActivity(intent);  // 跳转到意图对象指定的活动页面

    }
}
```

```java
public class JumpSecondActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_jump_second);
        findViewById(R.id.btn_jump_first).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        //创建一个意图对象
        Intent intent = new Intent(this, JumpFirstActivity.class);
        // 当栈中存在待跳转的活动实例时，则重新创建该活动的实例，并清除原实例上方的所有实例
        intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);  // 设置启动标志
        startActivity(intent);  // 跳转到意图对象指定的活动页面
    }
}
```

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <Button
        android:id="@+id/btn_jump_second"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="跳到第二个页面" />


</LinearLayout>
```

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <Button
        android:id="@+id/btn_jump_first"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="跳到第一个页面" />
</LinearLayout>
```

![image-20230331162803950](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303311628992.png)

两个活动的跳转代码都设置了FLAG_ACTIVITY_CLEAR_TOP，运行测试App发现多次跳转之后，每个活动仅会返回一次而已。



2 登录成功后不再返回登录页面

对于回不去的登录页面情况，可以设置启动标志FLAG_ACTIVITY_CLEAR_TASK，该标志会清空当前活动栈里的所有实例。

```java
// 创建一个意图对象，准备跳到指定的活动页面
Intent intent = new Intent(this, LoginSuccessActivity.class);
// 设置启动标志：跳转到新页面时，栈中的原有实例都被清空，同时开辟新任务的活动栈 
intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK |
               Intent.FLAG_ACTIVITY_NEW_TASK); 
startActivity(intent);  // 跳转到意图指定的活动页面

```

![image-20230402112111803](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304021121930.png)

演示：第一步点击登录按钮

![image-20230402112151083](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304021121153.png)

跳转到登录成功页面，此时再次点击返回按钮。

![image-20230402112234117](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304021122166.png)

直接跳到桌面

![image-20230402112303415](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304021123469.png)

没有设置，则将栈内的对应 Activity 销毁重新创建。按位或运算符
运算规则：0|0=0 0|1=1 1|0=1 1|1=1
总结：参加运算的两个对象只要有一个为 1 ，其值为 1 。
例如：3|5即 0000 0011| 0000 0101 = 0000 0111，因此，3|5的值得 7



### 4.2 在活动之间传递消息

#### 4.2.1 显式Intent和隐式Intent

Intent是各个组件之间信息沟通的桥梁，它主要完成下列 3 部分工作：

（ 1 ）标明本次通信请求从哪里来、到哪里去、要怎么走。

（ 2 ）发起方携带本次通信需要的数据内容，接收方从收到的意图中解析数据。

（ 3 ）发起方若想判断接收方的处理结果，意图就要负责让接收方传回应答的数据内容。

![image-20230402113837538](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304021138593.png)

指定意图对象的目标有两种表达方式，一种是显式Intent，另一种是隐式Intent。

1 . 显式Intent，直接指定来源活动与目标活动：

（ 1 ）在Intent的构造函数中指定，示例代码如下：

```
Intent intent = new Intent(this, ActNextActivity.class);  // 创建一个目标确定的意图
```


（ 2 ）调用意图对象的setClass方法指定，示例代码如下：

```
Intent intent = new Intent();  // 创建一个新意图
intent.setClass(this, ActNextActivity.class); // 设置意图要跳转的目标活动
```

（ 3 ）调用意图对象的setComponent方法指定，示例代码如下：

```
Intent intent = new Intent();  // 创建一个新意图 
// 创建包含目标活动在内的组件名称对象
ComponentName component = new ComponentName(this, ActNextActivity.class); 
intent.setComponent(component);  // 设置意图携带的组件信息
```

例子

```java
        //第一种显示intent
        //Intent intent=new Intent(this,ActFinishActivity.class);

        //第二种方式
        Intent intent=new Intent();
        //intent.setClass(this,ActFinishActivity.class);

        //第三种
        ComponentName component=new ComponentName(this,ActFinishActivity.class);
        intent.setComponent(component);
        startActivity(intent);
```



2 ．隐式Intent，没有明确指定要跳转的目标活动，只给出一个动作字符串让系统自动匹配

属于模糊匹配通常App不希望向外部暴露活动名称，只给出一个事先定义好的标记串

![image-20230402115821814](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304021158890.png)

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:text=" 点击一下按钮将向号码12345发起请求" />
    <Button
        android:id="@+id/btn_dial"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="跳到拨号页面" />
    <Button
        android:id="@+id/btn_sms"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="跳到短信页面" />
    <Button
        android:id="@+id/btn_my"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="跳到我的页面" />


</LinearLayout>
```

```
public class ActionUriActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_action_uri);
        findViewById(R.id.btn_dial).setOnClickListener(this);
        findViewById(R.id.btn_sms).setOnClickListener(this);
        findViewById(R.id.btn_my).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        String phoneNumber= "12345";
        switch (view.getId()){
            case R.id.btn_dial:
                Intent intent=new Intent();
                intent.setAction(Intent.ACTION_DIAL);
                Uri uri =Uri.parse("tel:"+phoneNumber);
                intent.setData(uri);
                startActivity(intent);
                break;
        }

    }
}
```

点击第一个按钮，跳转到拨打电话

![image-20230402211342095](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022113260.png)

![image-20230402211354809](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022113859.png)

修改chapter03

![image-20230402211756896](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022117965.png)

```
   case R.id.btn_my:
                intent.setAction("android.intent.action.NING");
                intent.addCategory(Intent.CATEGORY_DEFAULT);
                startActivity(intent);
                break;
```

![image-20230402212206465](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022122543.png)

启动2个应用，测试

点击chatper04,跳转到 chapter03

![image-20230402212403436](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022124479.png)



![image-20230402212349548](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022123593.png)



#### 4.2.2 向下一个Activity发送数据

Intent使用Bundle对象来存放待传递的数据信息

Bundle对象操作各类型数据的读写方法说明见表 

![image-20230402212616305](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022126361.png)

创建2个类ActSendActivity、ActReceiveActivity

首先在上一个活动使用包裹封装好数据，把包裹塞给意图对象，再调用startActivity方法跳到意图指定的目标活动。

ActSendActivity代码

```java
public class ActSendActivity extends AppCompatActivity implements View.OnClickListener {

    private TextView tv_send;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_act_send);
        tv_send = findViewById(R.id.tv_send);
        findViewById(R.id.btn_send).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        Intent intent = new Intent(this,ActReceiveActivity.class);
        Bundle bundle=new Bundle();
        bundle.putString("request_time", DateUtil.getNowTime());
        bundle.putString("request_content",tv_send.getText().toString());
        intent.putExtras(bundle);
        startActivity(intent);
    }
}
```

ActReceiveActivity

```java
public class ActReceiveActivity extends AppCompatActivity {

    private TextView tv_receive;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_act_receive);
        tv_receive = findViewById(R.id.tv_receive);

        Bundle bundle =getIntent().getExtras();
        String requestTime =bundle.getString("request_time");
        String requestContent =bundle.getString("request_content");
        String desc = String.format("收到请求消息：\n请求时间为%s\n请求内容为%s",
                requestTime, requestContent);
        tv_receive.setText(desc);  // 把请求消息的详情显示在文本视图上
    }
}
```

运行测试App，打开上一个页面如图所示。单击页面上的发送按钮跳到下一个页面如图所示，根据展示文本可知正确获得了传来的数据。

![image-20230402222300660](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022223706.png)

![image-20230402222311884](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022223932.png)

#### 4.2.3 向上一个Activity返回数据

处理下一个页面的应答数据，详细步骤如下：

1 上一个页面打包好请求数据，调用startActivityForResult方法执行跳转动作

```
 registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), new ActivityResultCallback<ActivityResult>() {
            @Override
            public void onActivityResult(ActivityResult result) {

            }
        }).var;（注意这里要输入.var，然后回车）
```

![image-20230402225732938](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022257995.png)

选择下面的register

![image-20230402225952700](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022259762.png)

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tv_request"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
    <Button
        android:id="@+id/btn_request"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="传送请求数据" />
    <TextView
        android:id="@+id/tv_response"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</LinearLayout>
```



```java
public class ActRequestActivity extends AppCompatActivity implements View.OnClickListener {

    private String mRequest = "你吃饭了吗？来我家吃吧";
    private ActivityResultLauncher<Intent> register;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_act_request);
        TextView tv_request = findViewById(R.id.tv_request);
        tv_request.setText(mRequest);

        findViewById(R.id.btn_request).setOnClickListener(this);
        register = registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), new ActivityResultCallback<ActivityResult>() {
            @Override
            public void onActivityResult(ActivityResult result) {

            }
        });
    }

    @Override
    public void onClick(View view) {
        // 创建一个意图对象，准备跳到指定的活动页面
        Intent intent = new Intent(this, ActResponseActivity.class);
        Bundle bundle = new Bundle();  // 创建一个新包裹
         // 往包裹存入名为request_time的字符串
        bundle.putString("request_time", DateUtil.getNowTime());
        // 往包裹存入名为request_content的字符串
        bundle.putString("request_content", mRequest);
        intent.putExtras(bundle);  // 把快递包裹塞给意图
        register.launch(intent);

    }
}
```

2 下一个页面接收并解析请求数据，进行相应处理。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tv_request"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
    <Button
        android:id="@+id/btn_response"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="返回应答数据" />
    <TextView
        android:id="@+id/tv_response"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</LinearLayout>
```

```
public class ActResponseActivity extends AppCompatActivity implements View.OnClickListener {

    private static final String mResponse = "我吃过了，还是你来我家吃";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_act_response);
        TextView tv_request = findViewById(R.id.tv_request);
        Bundle bundle = getIntent().getExtras();
        // 从包裹中取出名为request_time的字符串
        String request_time = bundle.getString("request_time");
        // 从包裹中取出名为request_content的字符串
        String request_content = bundle.getString("request_content");
        String desc = String.format("收到请求消息：\n请求时间为%s\n请求内容为%s",  request_time, request_content);
        tv_request.setText(desc);  // 把请求消息的详情显示在文本视图上

        findViewById(R.id.btn_response).setOnClickListener(this);

        TextView tv_response = findViewById(R.id.tv_response);
        tv_response.setText("待返回的消息："+mResponse);

    }

    @Override
    public void onClick(View view) {
        Intent intent = new Intent();  // 创建一个新意图
        Bundle bundle = new Bundle();  // 创建一个新包裹
        // 往包裹存入名为response_time的字符串
        bundle.putString("response_time", DateUtil.getNowTime());
        // 往包裹存入名为response_content的字符串
        bundle.putString("response_content", mResponse);
        intent.putExtras(bundle);  // 把快递包裹塞给意图
        // 携带意图返回上一个页面。RESULT_OK表示处理成功
        setResult(Activity.RESULT_OK, intent);
        finish();  // 结束当前的活动页面
    }

}
```



3 下一个页面在返回上一个页面时，打包应答数据并调用setResult方法返回数据包裹。

```
        Intent intent = new Intent();  // 创建一个新意图
        Bundle bundle = new Bundle();  // 创建一个新包裹
        // 往包裹存入名为response_time的字符串
        bundle.putString("response_time", DateUtil.getNowTime());
        // 往包裹存入名为response_content的字符串
        bundle.putString("response_content", mResponse);
        intent.putExtras(bundle);  // 把快递包裹塞给意图
        // 携带意图返回上一个页面。RESULT_OK表示处理成功
        setResult(Activity.RESULT_OK, intent);
        finish();  // 结束当前的活动页面
```

4 上一个页面重写方法onActivityResult，解析获得下一个页面的返回数据

```
 register = registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), new ActivityResultCallback<ActivityResult>() {
            @Override
            public void onActivityResult(ActivityResult result) {
                 if(result != null){
                     Intent intent=result.getData();
                     if (intent!=null  && result.getResultCode()== Activity.RESULT_OK){
                         Bundle bundle = intent.getExtras(); // 从返回的意图中获取快递包裹
                         // 从包裹中取出名叫response_time的字符串
                         String response_time = bundle.getString("response_time");
                         // 从包裹中取出名叫response_content的字符串
                         String response_content = bundle.getString("response_content");
                         String desc = String.format("收到返回消息：\n应答时间为：%s\n应答内容为：%s",
                                 response_time, response_content);
                         tv_response.setText(desc); // 把返回消息的详情显示在文本视图上

                     }
                 }
            }
        });
```

![image-20230402231613676](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022316760.png)

**注意：在androidx.appcompat:appcompat:1.3.0中startActivityForResult被标记过时的方法，官方建议使用registerForActivityResult**

![image-20230402231715997](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022317047.png)

![image-20230402231732268](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022317319.png)

![image-20230402231748432](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304022317476.png)

### 4.3 为活动补充附加信息

#### 4.3.1 利用资源文件配置字符串

在string.xml增加文字

```
<string name="weather_str">晴天</string>
```

代码设置

```java
public class ReadStringActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_read_string);
        TextView tv_resource = findViewById(R.id.tv_resource);
        String value = getString(R.string.weather_str);
        tv_resource.setText(value);
    }
}
```

![image-20230403131114602](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031311753.png)



上面的getString方法来自于Context类，由于页面所在的活动类AppCompatActivity追根溯源来自Context这个抽象类，因此凡是活动页面代码都能直接调用getString方法。然后在onCreate方法中调用showStringResource方法



#### 4.3.2 利用元数据传递配置信息

有时候为安全起见，某个参数要给某个活动专用，并不希望其他活动也能获取该参数，此时就不方便到处使用getString了（用途：配置第三方SDK的TOKEN）

Activity提供了元数据（Metadata）的概念，元数据是一种描述其他数据的数据，它相当于描述固定活动的参数信息。

打开AndroidManifest.xml，在测试活动的activity节点内部添加meta-data标签，通过属性name指定元数据的名称，通过属性value指定元数据的值。

```
<meta-data android:name="weather" android:value="@string/weather_str" />
```

![image-20230403131753124](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031317206.png)

配置好了activity节点的meta-data标签，再回到Java代码获取元数据信息，获取步骤分为下列 3 步：

- 调用getPackageManager方法获得当前应用的包管理器。

- 调用包管理器的getActivityInfo方法获得当前活动的信息对象。

- 活动信息对象的metaData是Bundle包裹类型，调用包裹对象的getString即可获得指定名称的参数值。

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tv_meta"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```

```java
public class MetaDataActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_meta_data);
        TextView tv_meta=findViewById(R.id.tv_meta);

        PackageManager pm = getPackageManager(); // 获取应用包管理器
        try {
            ActivityInfo act = pm.getActivityInfo(getComponentName(), PackageManager.GET_META_DATA);
            Bundle bundle = act.metaData; // 获取活动附加的元数据信息
            String value = bundle.getString("weather"); // 从包裹中取出名叫weather的字符串
            tv_meta.setText("来自元数据信息：今天的天气是"+value); // 在文本视图上显示文字
        } catch (PackageManager.NameNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

测试结果

![image-20230403132954431](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031329476.png)



#### 4.3.3 给应用页面注册快捷方式

元数据不单单能传递简单的字符串参数，还能传送更复杂的资源数据

譬如在手机桌面上长按支付宝图标，会弹出如图所示的快捷菜单。

![image-20230403133212907](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031332954.png)

先创建一个xml的文件夹

![image-20230403133434421](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031334483.png)

![image-20230403133452597](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031334642.png)

创建一个xml

![image-20230403133547166](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031335237.png)

![image-20230403133623654](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031336708.png)

```
<string name="first_short">first</string> 
<string name="first_long">启停活动</string> 
<string name="second_short">second</string> 
<string name="second_long">来回跳转</string> 
<string name="third_short">third</string> 
<string name="third_long">登录返回</string>
```

![image-20230403135235224](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031352274.png)

增加一个

![image-20230403140352629](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031403678.png)

修改主页面

```
<activity
            android:name=".ActStartActivity"
            android:exported="true" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
            <meta-data android:name="android.app.shortcuts" android:resource="@xml/shortcuts" />
        </activity>
```

这个XML文件用来保存 3 组菜单项的快捷方式定义

```xml
<?xml version="1.0" encoding="utf-8"?>
<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">
    <shortcut
        android:shortcutId="first"
        android:enabled="true"
        android:icon="@mipmap/ic_launcher"
        android:shortcutShortLabel="@string/first_short"
        android:shortcutLongLabel="@string/first_long">
        <!-- targetClass指定了点击该项菜单后要打开哪个活动页面 -->
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="com.jiang.chapter04"
            android:targetClass="com.jiang.chapter04.ActStartActivity" />
        <categories android:name="android.shortcut.conversation"/>
    </shortcut>
    <shortcut
        android:shortcutId="second"
        android:enabled="true"
        android:icon="@mipmap/ic_launcher"
        android:shortcutShortLabel="@string/second_short"
        android:shortcutLongLabel="@string/second_long">
        <!-- targetClass指定了点击该项菜单后要打开哪个活动页面 -->
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="com.jiang.chapter04"
            android:targetClass="com.jiang.chapter04.JumpFirstActivity" />
        <categories android:name="android.shortcut.conversation"/>
    </shortcut>
    <shortcut
        android:shortcutId="third"
        android:enabled="true"
        android:icon="@mipmap/ic_launcher"
        android:shortcutShortLabel="@string/third_short"
        android:shortcutLongLabel="@string/third_long">
        <!-- targetClass指定了点击该项菜单后要打开哪个活动页面 -->
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="com.jiang.chapter04"
            android:targetClass="com.jiang.chapter04.LogininputActivity" />
        <categories android:name="android.shortcut.conversation"/>
    </shortcut>
</shortcuts>
```



由上述的XML例子中看到，每个shortcut节点都代表了一个菜单项，该节点的各属性说明如下：

- shortcutId：快捷方式的编号。

- enabled：是否启用快捷方式。true表示启用，false表示禁用。

- icon：快捷菜单左侧的图标。

- shortcutShortLabel：快捷菜单的短标签。

- shortcutLongLabel：快捷菜单的长标签。优先展示长标签的文本，长标签放不下时才展示短标签的文本。

测试结果：

![image-20230403150726843](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031507908.png)

## 5、中级控件

### 5.1 图形定制

#### 5.1.1 图形Drawable

表达了各种各样的图片，还包括色块、画板、背景等

![image-20230403150715630](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304031507692.png)

包含图片在内的图形文件放在res目录的各个drawable目录下，其中drawable目录一般保存描述性的XML文件

各视图的background属性、ImageView和ImageButton的src属性、TextView和Button四个方向的drawable系列属性

- drawable目录一般保存描述性的XML文件，而图片文件一般放在具体分辨率的drawable目录下。例如：
- drawable-ldpi里面存放低分辨率的图片（如240×320），现在基本没有这样的智能手机了。
- drawable-mdpi里面存放中等分辨率的图片（如320×480），这样的智能手机已经很少了。
- drawable-hdpi里面存放高分辨率的图片（如480×800），一般对应 4 英寸～4.5英寸的手机（但不绝对，同尺寸的手机有可能分辨率不同，手机分辨率就高不就低，因为分辨率低了屏幕会有模糊的感觉）。
- drawable-xhdpi里面存放加高分辨率的图片（如720×1280），一般对应 5 英寸～5.5英寸的手机。
- drawable-xxhdpi里面存放超高分辨率的图片（如1080×1920），一般对应 6 英寸～6.5英寸的手机。
- drawable-xxxhdpi里面存放超超高分辨率的图片（如1440×2560），一般对应 7 英寸以上的平板电脑。
  

基本上，分辨率每加大一级，宽度和高度就要增加二分之一或三分之一像素。如果各目录存在同名图片，Android就会根据手机的分辨率分别适配对应文件夹里的图片。在开发App时，为了兼容不同的手机屏幕，在各目录存放不同分辨率的图片，才能达到最合适的显示效果

例如，在drawable-hdpi放了一张背景图片bg.png（分辨率为480×800），其他目录没放，使用分辨率为480×800的手机查看该App界面没有问题，但是使用分辨率为720×1280的手机查看该App会发现背景图片有点模糊，原因是Android为了让bg.png适配高分辨率的屏幕，强行把bg.png拉伸到了720×1280，拉伸的后果是图片变模糊了。


#### 5.1.2 形状图形

Shape图形又称形状图形，它用来描述常见的几何形状，包括矩形、圆角矩形、圆形、椭圆等。

首先右击drawable目录，并依次选择右键菜单的New→Drawable resource file，在弹窗中输入文件名称再单击OK按钮，即可自动生成一个XML描述文件

![image-20230404115430194](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041154322.png)

![image-20230404115737533](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041157577.png)

```
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- 指定了形状内部的填充颜色 -->
    <solid android:color="#ffdd66" />
    <!-- 指定了形状轮廓的粗细与颜色 -->
    <stroke
        android:width="1dp"
        android:color="#aaaaaa" />
    <!-- 指定了形状四个圆角的半径 -->
    <corners android:radius="10dp" />
</shape>
```

再新建一个椭圆的XML描述文件

![image-20230404120140001](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041201055.png)

```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">
    <!-- 指定了形状内部的填充颜色 -->
    <solid android:color="#ff66aa" />
    <!-- 指定了形状轮廓的粗细与颜色 -->
    <stroke
        android:width="1dp"
        android:color="#aaaaaa" />
</shape>
```

```java
public class DrawableShapeActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_drawable_shape);
        // 从布局文件中获取名为v_content的视图
        View v_content = findViewById(R.id.v_content);
        // v_content的背景设置为圆角矩形
        v_content.setBackgroundResource(R.drawable.shape_rect_gold);

    }
}
```

![image-20230404130811766](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041308818.png)

前述的视图对象v_content背景改为R.drawable.shape_oval_rose

![image-20230404131441348](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041314397.png)

![image-20230404131404830](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041314882.png)

2 ．size（尺寸）

size是shape的下级节点，它描述了形状图形的宽高尺寸。若无size节点，则表示宽高与宿主视图一样大小。下面是size节点的常用属性说明。

- height：像素类型，图形高度。
- width：像素类型，图形宽度。

3 ．stroke（描边）

- stroke是shape的下级节点，它描述了形状图形的描边规格。若无stroke节点，则表示不存在描边。下面是stroke节点的常用属性说明。
- color：颜色类型，描边的颜色。
- dashGap：像素类型，每段虚线之间的间隔。
- dashWidth：像素类型，每段虚线的宽度。若dashGap和dashWidth有一个值为 0 ，则描边为实线。
- width：像素类型，描边的厚度。

4 ．corners（圆角）

- corners是shape的下级节点，它描述了形状图形的圆角大小。若无corners节点，则表示没有圆角。下面是corners节点的常用属性说明。
- bottomLeftRadius：像素类型，左下圆角的半径。
- bottomRightRadius：像素类型，右下圆角的半径。
- topLeftRadius：像素类型，左上圆角的半径。
- topRightRadius：像素类型，右上圆角的半径。
- radius：像素类型， 4 个圆角的半径（若有上面 4 个圆角半径的定义，则不需要radius定义）。

5 ．solid（填充）

- solid是shape的下级节点，它描述了形状图形的填充色彩。若无solid节点，则表示无填充颜色。下面是solid节点的常用属性说明。
- color：颜色类型，内部填充的颜色。

6 ．padding（间隔）

- padding是shape的下级节点，它描述了形状图形与周围边界的间隔。若无padding节点，则表示四周不设间隔。下面是padding节点的常用属性说明。
- top：像素类型，与上方的间隔。
- bottom：像素类型，与下方的间隔。
- left：像素类型，与左边的间隔。
- right：像素类型，与右边的间隔。
  

7 ．gradient（渐变）

- gradient是shape的下级节点，它描述了形状图形的颜色渐变。若无gradient节点，则表示没有渐变效果。下面是gradient节点的常用属性说明。

- angle：整型，渐变的起始角度。为 0 时表示时钟的 9 点位置，值增大表示往逆时针方向旋转。例如，值为 90 表示 6 点位置，值为 180 表示 3 点位置，值为 270 表示 0 点/12点位置。

渐变类型

- linear 线性渐变，默认值

- radial 放射渐变，起始颜色就是圆心颜色
- sweep 滚动渐变，即一个线段以某个端点为圆心做 360 度旋转
- type：字符串类型，渐变类型。
- centerX：浮点型，圆心的X坐标。当android:type="linear"时不可用。
- centerY：浮点型，圆心的Y坐标。当android:type="linear"时不可用。
- gradientRadius：整型，渐变的半径。当android:type="radial"时需要设置该属性。
- centerColor：颜色类型，渐变的中间颜色。
- startColor：颜色类型，渐变的起始颜色。
- endColor：颜色类型，渐变的终止颜色。
- useLevel：布尔类型，设置为true为无渐变色、false为有渐变色。

#### 5.1.3 九宫格图片

将某张图片设置成视图背景时，如果图片尺寸太小，则系统会自动拉伸图片使之填满背景

可是一旦图片拉得过大，其画面容易变得模糊

![image-20230404131636131](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041316185.png)

为了演示九宫格图片的展示效果，要利用Android Studio制作一张点九图片。首先在drawable目录下找到待加工的原始图片，右击它弹出右键菜单如图所示。

![image-20230404132129281](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041321337.png)

图片加工区域，只有九宫格中间的一格会被拉伸。

![image-20230404132408132](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041324186.png)

效果对比

![image-20230404132426051](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041324098.png)



#### 5.1.4 状态列表图形

比如按钮控件的背景在正常情况下是凸起的，在按下时是凹陷的

从按下到弹起的过程，用户便晓得点击了该按钮

创建一个btn_nine_selector.xml

![image-20230404132808513](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041328571.png)

```xml
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/button_pressed" android:state_pressed="true" />
    <item android:drawable="@drawable/button_normal" />
</selector>
```

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp"
    android:gravity="center">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="默认样式按钮"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="5dp"
        android:background="@drawable/btn_nine_selector"
        android:padding="5dp"
        android:text="定制样式按钮"/>
</LinearLayout>
```

![image-20230404135005058](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041350108.png)

上述XML文件的关键点是state_pressed属性，该属性表示按下状态，值为true表示按下时显示button_pressed图像，其余情况显示button_normal图像。



### 5.2 选择按钮

#### 5.2.1 复选框CheckBox

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp">

    <CheckBox
        android:id="@+id/ck_system"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:text="这是系统的CheckBox" />

    <CheckBox
        android:id="@+id/ck_custom"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:button="@drawable/checkbox_selector"
        android:checked="true"
        android:padding="5dp"
        android:text="这个CheckBox换了图标" />
</LinearLayout>

```

主要是如何处理勾选监听器

```
// 该页面实现了接口OnCheckedChangeListener，意味着要重写勾选监听器的onCheckedChanged方法 
public class CheckBoxActivity extends AppCompatActivity
       implements CompoundButton.OnCheckedChangeListener {
   @Override
   protected void onCreate(Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       setContentView(R.layout.activity_check_box);
       // 从布局文件中获取名叫ck_system的复选框
       CheckBox ck_system = findViewById(R.id.ck_system);
       // 从布局文件中获取名叫ck_custom的复选框
       CheckBox ck_custom = findViewById(R.id.ck_custom);
       // 给ck_system设置勾选监听器，一旦用户点击复选框，就触发监听器的onCheckedChanged方 
法
       ck_system.setOnCheckedChangeListener(this);
       // 给ck_custom设置勾选监听器，一旦用户点击复选框，就触发监听器的onCheckedChanged方 
法
       ck_custom.setOnCheckedChangeListener(this); 
 }
   @Override
   public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) { 
       String desc = String.format("您%s了这个CheckBox", isChecked ? "勾选" : "取 
消勾选");
       buttonView.setText(desc); 
 }
}

```

#### 5.2.2 开关按钮Switch

Switch是开关按钮，它像一个高级版本的CheckBox，在选中与取消选中时可展现的界面元素比复选框丰富。Switch控件新添加的XML属性说明如下：

- textOn：设置右侧开启时的文本。
- textOff：设置左侧关闭时的文本。
- track：设置开关轨道的背景。
- thumb：设置开关标识的图标。

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">?

    <TextView
        android:layout_width="0dp"
        android:layout_weight="1"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:layout_gravity="start"
        android:text="Switch开关:"/>

        <Switch
            android:id="@+id/sw_status"
            android:layout_width="80dp"
            android:layout_height="30dp"
            android:padding="5dp"
            android:layout_gravity="end"/>
    </LinearLayout>
    <TextView
        android:id="@+id/tv_result"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:gravity="start"/>

</LinearLayout>
```

代码

```java
public class SwitchDefaultActivity extends AppCompatActivity implements CompoundButton.OnCheckedChangeListener {
    private TextView textView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_switch_default);
        Switch sw_status=findViewById(R.id.sw_status);
        textView = findViewById(R.id.tv_result);
        sw_status.setOnCheckedChangeListener(this);
    }

    @Override
    public void onCheckedChanged(CompoundButton compoundButton, boolean isChecked) {
       String desc = String.format("Switch按钮的状态是%s",isChecked?"开":"关");
        textView.setText(desc);
    }
}
```



#### 5.2.3 单选按钮RadioButton

单选按钮，指的是在一组按钮中选择其中一项，并且不能多选.

这要求有个容器确定这组按钮的范围，这个容器便是单选组RadioGroup

单选组实质上是个布局，同一组RadioButton都要放在同一个RadioGroup节点下

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="请选择您的性别"
        android:textColor="@color/black"
        android:textSize="17sp" />
    <RadioGroup
        android:id="@+id/rg_sex"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal" >
        <RadioButton
            android:id="@+id/rb_male"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:checked="false"
            android:text="男"
            android:textColor="@color/black"
            android:textSize="17sp" />
        <RadioButton
            android:id="@+id/rb_female"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:checked="false"
            android:text="女"
            android:textColor="@color/black"
            android:textSize="17sp" />
    </RadioGroup>
    <TextView
        android:id="@+id/tv_sex"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textColor="@color/black"
        android:textSize="17sp" />
</LinearLayout>
```

```java
public class RadioHorizontalActivity extends AppCompatActivity implements RadioGroup.OnCheckedChangeListener {
    private TextView tv_sex; // 声明一个文本视图对象

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_radio_horizontal);
        tv_sex = findViewById(R.id.tv_sex);
        // 从布局文件中获取名叫rg_sex的单选组
        RadioGroup rg_sex = findViewById(R.id.rg_sex);
        // 设置单选监听器，一旦点击组内的单选按钮，就触发监听器的onCheckedChanged方法
        rg_sex.setOnCheckedChangeListener(this);
    }

    @Override
    public void onCheckedChanged(RadioGroup radioGroup, int checkedId) {
        if (checkedId == R.id.rb_male) {
            tv_sex.setText("哇哦，你是个帅气的男孩");
        } else if (checkedId == R.id.rb_female) {
            tv_sex.setText("哇哦，你是个漂亮的女孩");
        }
    }
}
```



### 5.3 文本输入

#### 5.3.1 编辑框EditText

编辑框EditText用于接收软键盘输入的文字，例如用户名、密码、评价内容等，它由文本视图派生而来

![image-20230404160957203](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041610369.png)

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp">
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="下面是登录信息" />

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="请输入用户名"
        android:inputType="text" />

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="请输入密码"
        android:inputType="textPassword" />

</LinearLayout>
```

如何设置圆角边框

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp">

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="这是默认边框"
        android:inputType="text" />

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="5dp"
        android:background="@null"
        android:hint="我的边框不见了"
        android:inputType="text" />

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="5dp"
        android:background="@drawable/editext_selector"
        android:hint="我的边框是圆角"
        android:inputType="text" />
</LinearLayout>

```

#### 5.3.2 焦点变更监听器

焦点变更监听器来自于接口View.OnFocusChangeListener，若想注册该监听器，就要调用编辑框对象的setOnFocusChangeListener方法

![image-20230404162552089](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041625147.png)

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp">

    <EditText
        android:id="@+id/et_phone"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="请输入11位手机号"
        android:inputType="number"
        android:maxLength="11"/>
    <EditText
        android:id="@+id/et_password"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="请输入密码"
        android:inputType="numberPassword"
        android:maxLength="11"/>
    <Button
        android:id="@+id/btn_login"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="登录" />
</LinearLayout>
```

```
public class EditFocusActivity extends AppCompatActivity implements View.OnFocusChangeListener {

    private EditText et_phone;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_edit_focus);
        et_phone = findViewById(R.id.et_phone);
        // 从布局文件中获取名为et_password的编辑框
        EditText et_password = findViewById(R.id.et_password);
        // 给编辑框注册一个焦点变化监听器，一旦焦点发生变化，就触发监听器的onFocusChange方法
        et_password.setOnFocusChangeListener(this);
    }

    @Override
    public void onFocusChange(View v, boolean hasFocus) {
         // 判断密码编辑框是否获得焦点。hasFocus为true表示获得焦点，为false表示失去焦点
        if (v.getId()==R.id.et_password && hasFocus) {
            String phone = et_phone.getText().toString();
            if (TextUtils.isEmpty(phone) || phone.length() < 11) { // 手机号码不足11位
                // 手机号码编辑框请求焦点，也就是把光标移回手机号码编辑框
                et_phone.requestFocus();
                Toast.makeText(this, "请输入11位手机号码", Toast.LENGTH_SHORT).show();
            }
        }
    }
}
```



#### 5.3.3 文本变化监听器

调用编辑框对象的addTextChangedListener方法注册文本监听器

文本变化监听器的接口TextWatcher，该接口提供了 3 个监控方法，具体说明如下：

- beforeTextChanged：在文本改变之前触发。
- onTextChanged：在文本改变过程中触发。
- afterTextChanged：在文本改变之后触发。

监听操作建议在afterTextChanged方法中完成，如果同时监听 11 位的手机号码和 6 位的密码，一旦输入文字达到指定长度就关闭键盘

使用系统服务关闭软键盘的代码:

```
public class ViewUtil {

    public static void hideOneInputMethod(Activity act, View v) {
        // 从系统服务中获取输入法管理器
        InputMethodManager imm = (InputMethodManager) act.getSystemService(Context.INPUT_METHOD_SERVICE);
        // 关闭屏幕上的输入法软键盘
        imm.hideSoftInputFromWindow(v.getWindowToken(), 0);
    }
}
```

监听操作建议在afterTextChanged方法中完成，如果同时监听 11 位的手机号码和 6 位的密码，一旦输入文字达到指定长度就关闭键盘

```java
public class EditHideActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_edit_hide);
        EditText et_phone = findViewById(R.id.et_phone);
        EditText et_password = findViewById(R.id.et_password);

        et_phone.addTextChangedListener(new HideTextWatcher(et_phone,11));
        // 给密码编辑框添加文本变化监听器
        et_password.addTextChangedListener(new HideTextWatcher(et_password, 6));
    }

    // 定义一个编辑框监听器，在输入文本达到指定长度时自动隐藏输入法
    private class HideTextWatcher implements TextWatcher {
        private EditText mView; // 声明一个编辑框对象
        private int mMaxLength; // 声明一个最大长度变量
        public HideTextWatcher(EditText v, int maxLength) {
            super();
            mView = v;
            mMaxLength = maxLength;
        }
        // 在编辑框的输入文本变化前触发
        public void beforeTextChanged(CharSequence s, int start, int count, int
                after) {}
        // 在编辑框的输入文本变化时触发
        public void onTextChanged(CharSequence s, int start, int before, int count)
        {}
        // 在编辑框的输入文本变化后触发
        public void afterTextChanged(Editable s) {
            String str = s.toString(); // 获得已输入的文本字符串
            // 输入文本达到11位（如手机号码），或者达到6位（如登录密码）时关闭输入法
            if ((str.length() == 11 && mMaxLength == 11)
                    || (str.length() == 6 && mMaxLength == 6)) {
                ViewUtil.hideOneInputMethod(EditHideActivity.this, mView); // 隐藏输入法软键盘
            }
        }
    }
```

然后运行测试App，先输入手机号码的前 10 位，因为还没达到 11 位，所以软键盘依然展示，如图所示。接着输入最后一位手机号，总长度达到 11 位，于是软键盘自动关闭。

### 5.4 对话框

#### 5.4.1 提醒对话框AlertDialog

![image-20230404171320031](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304041713098.png)

AlertDialog是Android中最常用的对话框，可以完成常见的交互操作，例如提示、确认、选择等功能。

调用建造器的create方法才能生成对话框实例。最后调用对话框实例的show方法:

```
public class AlertDialogActivity extends AppCompatActivity implements View.OnClickListener {

    private TextView tv_alert;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_alert_dialog);

        Button btn_alert = findViewById(R.id.btn_alert);
        btn_alert.setOnClickListener(this);
        tv_alert = findViewById(R.id.tv_alert);
    }

    @Override
    public void onClick(View view) {
        AlertDialog.Builder builder =new AlertDialog.Builder(this);
        // 设置对话框的标题文本
        builder.setTitle("尊敬的用户");
        // 设置对话框的内容文本
        builder.setMessage("你真的要卸载我吗？");
        // 设置对话框的肯定按钮文本及其点击监听器
        builder.setPositiveButton("残忍卸载", (dialog, which) -> {
            tv_alert.setText("虽然依依不舍，但是只能离开了");
        });
        // 设置对话框的否定按钮文本及其点击监听器
        builder.setNegativeButton("我再想想", (dialog, which) -> {
            tv_alert.setText("让我再陪你三百六十五个日夜");
        });

        // 根据建造器构建提醒对话框对象
        AlertDialog dialog = builder.create();
        // 显示提醒对话框
        dialog.show();
    }
}
```



#### 5.4.2 日期对话框

专门的日期选择器DatePicker，供用户选择具体的年月日

特别注意onDateSet的月份参数，它的起始值不是 1 而是 0 。也就是说，一月份对应的参数值为 0 ，十二月份对应的参数值为 11

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <Button
        android:id="@+id/btn_date"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="请选择日期" />
    <DatePicker
        android:id="@+id/dp_date"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:datePickerMode="spinner"
        android:calendarViewShown="false"/>
    <Button
        android:id="@+id/btn_ok"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="确 定" />

    <TextView
        android:id="@+id/tv_date"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</LinearLayout>
```

```
public class DatePickerActivity extends AppCompatActivity implements View.OnClickListener, DatePickerDialog.OnDateSetListener {
    private TextView tv_date; // 声明一个文本视图对象
    private DatePicker dp_date; // 声明一个日期选择器对象

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_date_picker);
        tv_date = findViewById(R.id.tv_date);
        // 从布局文件中获取名叫dp_date的日期选择器
        dp_date = findViewById(R.id.dp_date);
        findViewById(R.id.btn_ok).setOnClickListener(this);
        findViewById(R.id.btn_date).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.btn_ok:
                String desc = String.format("您选择的日期是%d年%d月%d日",dp_date.getYear(),dp_date.getMonth()+1,dp_date.getDayOfMonth());
                tv_date.setText(desc);
                break;
            case R.id.dp_date:
                // 获取日历的一个实例，里面包含了当前的年月日
                Calendar calendar = Calendar.getInstance();
                DatePickerDialog dialog = new DatePickerDialog(this, this,
                        calendar.get(Calendar.YEAR), // 年份
                        calendar.get(Calendar.MONTH), // 月份
                        calendar.get(Calendar.DAY_OF_MONTH)); // 日子
                dialog.show(); // 显示日期对话框
                break;
        }

    }

    @Override
    public void onDateSet(DatePicker datePicker,  int year, int monthOfYear, int
            dayOfMonth) {
        // 获取日期对话框设定的年月份
        String desc = String.format("您选择的日期是%d年%d月%d日",
                year, monthOfYear + 1, dayOfMonth);
        tv_date.setText(desc);
    }
}
```



#### 5.4.3 时间对话框TimePickerDialog

特色：

（ 1 ）构造方法传的是当前的小时与分钟，最后一个参数表示是否采取 24 小时制，一般为true表示小时的数值范围为 0 ～ 23 ；若为false则表示采取 12 小时制。

（ 2 ）时间选择监听器为OnTimeSetListener，对应需要实现onTimeSet方法，在该方法中可获得用户选择的小时和分钟。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <Button
        android:id="@+id/btn_time"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="请选择时间" />
    <TimePicker
        android:id="@+id/tp_time"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:timePickerMode="spinner"/>
    <Button
        android:id="@+id/btn_ok"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="确 定" />

    <TextView
        android:id="@+id/tv_time"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```

```
public class TimePickerActivity extends AppCompatActivity implements View.OnClickListener , TimePickerDialog.OnTimeSetListener{
    private TextView tv_time; // 声明一个文本视图对象
    private TimePicker tp_time; // 声明一个时间选择器对象

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_time_picker);
        tv_time = findViewById(R.id.tv_time);
        // 从布局文件中获取名叫tp_time的时间选择器
        tp_time = findViewById(R.id.tp_time);
        findViewById(R.id.btn_time).setOnClickListener(this);
        findViewById(R.id.btn_ok).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.btn_ok:
                String desc = String.format("您选择的日期是%d时%d分",tp_time.getHour(),tp_time.getMinute());
                tv_time.setText(desc);
                break;
            case R.id.btn_time:
                // 获取日历的一个实例，里面包含了当前的年月日
                Calendar calendar = Calendar.getInstance();
                TimePickerDialog dialog = new TimePickerDialog(this, this,
                        calendar.get(Calendar.HOUR_OF_DAY), // 小时
                        calendar.get(Calendar.MINUTE), // 分钟
                        true); // true表示24小时制，false表示12小时制
                dialog.show(); // 显示时间对话框
                break;
        }
    }

    // 一旦点击时间对话框上的确定按钮，就会触发监听器的onTimeSet方法
    @Override
    public void onTimeSet(TimePicker view, int hourOfDay, int minute) {
        // 获取时间对话框设定的小时和分钟
        String desc = String.format("您选择的时间是%d时%d分", hourOfDay, minute);
        tv_time.setText(desc);
    }
}
```



## 6、数据存储

### 6.1 共享参数SharedPreferences

#### 6.1.1 共享参数的用法

SharedPreferences是Android的一个轻量级存储工具，它采用的存储结构是Key-Value的键值对方式（类似于Java的Properties）

保存共享参数键值对信息的文件路径为：/data/data/应用包名/shared_prefs/文件名.xml。

路径如下：

![image-20230406115827455](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304061158533.png)

例子

```xml
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
<string name="name">Mr Jiang</string> 
<int nane="age" value="40"/>
<boolean name="married" value="true" /> 
<float name="weight" value="100.0"/>
</map>

```

使用场景：

（ 1 ）简单且孤立的数据。若是复杂且相互关联的数据，则要保存于关系数据库。

（ 2 ）文本形式的数据。若是二进制数据，则要保存至文件。

（ 3 ）需要持久化存储的数据。App退出后再次启动时，之前保存的数据仍然有效。

实际开发中，共享参数经常存储的数据包括：App的个性化配置信息、用户使用App的行为信息、临时需要保存的片段信息等。

案例：

![image-20230406120320218](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304061203270.png)

调用getSharedPreferences方法可以获得共享参数实例，获取代码示例如下：

```
SharedPreferences preferences = getSharedPreferences("config", Context.MODE_PRIVATE);
```

```
public class SharedWriteActivity extends AppCompatActivity implements View.OnClickListener {
    private EditText et_name;
    private EditText et_age;
    private EditText et_height;
    private EditText et_weight;
    private CheckBox ck_married;
    private SharedPreferences preferences;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_shared_write);
        et_name = findViewById(R.id.et_name);
        et_age = findViewById(R.id.et_age);
        et_height = findViewById(R.id.et_height);
        et_weight = findViewById(R.id.et_weight);
        ck_married = findViewById(R.id.ck_married);

        findViewById(R.id.btn_save).setOnClickListener(this);
        preferences = getSharedPreferences("config", Context.MODE_PRIVATE);
    }

    @Override
    public void onClick(View v) {
        String name = et_name.getText().toString();
        String age = et_age.getText().toString();
        String height = et_height.getText().toString();
        String weight = et_weight.getText().toString();

        SharedPreferences.Editor editor = preferences.edit();  // 获得编辑器的对象
        editor.putString("name", name);  // 添加一个名为name的字符串参数
        editor.putInt("age", Integer.parseInt(age));  // 添加一个名为age的整型参数
        editor.putFloat("height", Float.parseFloat(height));  // 添加一个名为height的浮点数参数
        editor.putFloat("weight", Float.parseFloat(weight));  // 添加一个名为weight的浮点数参数
        editor.putBoolean("married", ck_married.isChecked());  // 添加一个名为married的布尔型参数
        editor.commit();  // 交编辑器中的修改
    }
}
```

测试结果

![image-20230406203418731](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304062034787.png)

从共享参数读取数据相对简单，直接调用共享参数实例的get 方法即可读取键值，注意 get方法的第二个参数表示默认值，读取数据的代码示例如下：

```java
  private void reload(){
        String name = preferences.getString ( "name","");//从共享参数获取名为name的字符串
        if(name!=null){
            et_name.setText(name);
        }
        int age = preferences.getInt ("age",0);// 从共享参数获取名为age 的整型数
        if(age!=0){
            et_age.setText(String.valueOf(age));
        }
        float height = preferences.getFloat ( "height",0f);//从共享参数获取名为weight的浮点数
        if(height!=0f){
            et_height.setText(String.valueOf(height));
        }
        float weight = preferences.getFloat ( "weight",0f);//从共享参数获取名为weight的浮点数
        if(weight!=0f){
            et_weight.setText(String.valueOf(weight));
        }
        boolean married = preferences.getBoolean ( "married", false);//从共享参数获取名为married布尔数
        ck_married.setChecked(married);

    }
```

直接运行，如果有数据会直接显示：

![image-20230406210548529](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304062105584.png)

#### 6.1.2 利用设备浏览器寻找共享参数文件

进到/data/data/com.example.chapter06/shared_prefs目录，在该目录下看到了参数文件

右键选择导出

![image-20230406203649664](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304062036723.png)

![image-20230406203736641](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304062037688.png)

### 6.2 SQLite

SQLite是一种小巧的嵌入式数据库，使用方便、开发简单。如同MySQL、Oracle那样，SQLite也采用SQL语句管理数据，由于它属于轻型数据库，不涉及复杂的数据控制操作，

#### 6.2.1 数据库管理器SQLiteDatabase

SQLiteDatabase便是Android提供的SQLite数据库管理器

数据库管理器SQLiteDatabase提供了若干操作数据表的API，常用的方法有 3 类，列举如下：

1 管理类：用于数据库层面操作

- openDatabase：打开指定路径的数据库。
-  isOpen：判断数据库是否已打开。 
- close：关闭数据库。
-  getVersion：获取数据库的版本号。 
- setVersion：设置数据库的版本号。

2 事务类，用于事务层面的操作

```
beginTransaction：开始事务。

setTransactionSuccessful：设置事务的成功标志。

endTransaction：结束事务。执行本方法时，系统会判断之前是否调用了

setTransactionSuccessful方法，如果之前已调用该方法就提交事务，如果没有调用该方法就回滚事务。
```

3 数据处理类，用于数据表层面的操作

```
execSQL：执行拼接好的SQL控制语句。一般用于建表、删表、变更表结构。

delete：删除符合条件的记录。

update：更新符合条件的记录信息。

insert：插入一条记录。

query：执行查询操作，并返回结果集的游标。

rawQuery：执行拼接好的SQL查询语句，并返回结果集的游标。
```

![image-20230406213622730](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304062136787.png)

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">
        <Button
            android:id="@+id/btn_database_create"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="创建数据库"
            android:textColor="@color/black"
            android:textSize="17sp"/>
        <Button
            android:id="@+id/btn_database_delete"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="删除数据库"
            android:textColor="@color/black"
            android:textSize="17sp"/>
    </LinearLayout>
    <TextView
        android:id="@+id/tv_database"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingLeft="5dp"
        android:textColor="@color/black"
        android:textSize="17sp" />
</LinearLayout>
```

```java
public class DatabaseActivity extends AppCompatActivity implements View.OnClickListener {

    private TextView tv_database;
    private String mDatabaseName;
    private String desc;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_database);
        tv_database = findViewById(R.id.tv_database);
        findViewById(R.id.btn_database_create).setOnClickListener(this);
        findViewById(R.id.btn_database_delete).setOnClickListener(this);
        mDatabaseName = getFilesDir()+"/test.db";
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()){
            case R.id.btn_database_create:
                SQLiteDatabase db = openOrCreateDatabase(mDatabaseName, Context.MODE_PRIVATE, null);
                desc = String.format("数据库%s创建%s",db.getPath(),(db!=null)?"成功":"失败");
                tv_database.setText(desc);
                break;
            case R.id.btn_database_delete:
                boolean result =  deleteDatabase(mDatabaseName);
                desc = String.format("数据库%s删除%s",mDatabaseName,result?"成功":"失败");
                tv_database.setText(desc);
                break;
        }
    }
}
```

![image-20230406223730614](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304062237664.png)

数据库创建成功

![image-20230406224052245](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304062240306.png)

#### 6.2.2 数据库帮助器SQLiteOpenHelper

SQLiteOpenHelper是数据库辅助工具，用于指导开发者进行sqllite的合理使用

SQLiteOpenHelper的具体使用步骤如下：

- 新建一个继承自SQLiteOpenHelper的数据库操作类，按提示重写onCreate和onUpgrade两个方法。
- 封装保证数据库安全的必要方法
- 提供对表记录增加、删除、修改、查询的操作方法

创建实体类

```java
public class User {

    public int id;
    public String name;
    public int age;
    public long height;
    public float weight;
    public boolean married;

    public User(){

    }

    public User(String name, int age, long height, float weight, boolean married) {
        this.name = name;
        this.age = age;
        this.height = height;
        this.weight = weight;
        this.married = married;
    }
}
```

创建SQLiteOpenHelper

```java
public class UserDBHelper extends SQLiteOpenHelper {

    private  static final String DB_NAME = "user.db" ;
    private static final String TABLE_NAME= "user_info";
    private static final int DB_VERSION= 1;
    private static  UserDBHelper mHelper = null ;

    private SQLiteDatabase mRDB = null;
    private SQLiteDatabase mWDB = null;

    private UserDBHelper(Context context){
        super(context,DB_NAME,null,DB_VERSION);
    }

    public static UserDBHelper getInstance(Context context){
       if(mHelper == null){
           mHelper = new UserDBHelper(context);
       }
       return mHelper;
    }

    //打开数据库的读连接
    public SQLiteDatabase openReadLink(){
        if( mRDB ==null || !mRDB.isOpen()){
            mRDB = mHelper.getReadableDatabase();
        }
        return  mRDB;
    }

    //打开数据库的写连接
    public SQLiteDatabase openWriteLink(){
        if( mWDB ==null ||!mWDB.isOpen()){
            mWDB = mHelper.getWritableDatabase();
        }
        return  mWDB;
    }

    //关闭连接
    public void closeLink(){
        if(mRDB !=null || mRDB.isOpen()){
            mRDB.close();
            mRDB = null;
        }

        if(mWDB !=null || mWDB.isOpen()){
            mWDB.close();
            mWDB = null;
        }
    }


    //创建数据库，执行建表语句
    @Override
    public void onCreate(SQLiteDatabase db) {
        String sql = "CREATE TABLE IF NOT EXISTS "+TABLE_NAME+" (" +
                "_id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL," +
                " name VARCHAR NOT NULL," +
                " age INTEGER NOT NULL," +
                " height LONG NOT NULL," +
                " weight FLOAT NOT NULL," +
                " married INTEGER NOT NULL);" ;
        db.execSQL(sql);
    }

    @Override
    public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {

    }

    public long insert(User user){
        ContentValues values = new ContentValues();
        values.put("name",user.getName());
        values.put("age",user.getAge());
        values.put("height",user.getHeight());
        values.put("weight",user.getWeight());
        values.put("married",user.married);
        return  mWDB.insert(TABLE_NAME,null,values);
    }
}
```

代码

```java
public class SQLiteHelperActivity extends AppCompatActivity implements View.OnClickListener {
    private EditText et_name;
    private android.widget.EditText et_age;
    private EditText et_height;
    private EditText et_weight;
    private CheckBox ck_married;
    private UserDBHelper mHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_sqlite_helper);
        et_name = findViewById(R.id.et_name);
        et_age = findViewById(R.id.et_age);
        et_height = findViewById(R.id.et_height);
        et_weight = findViewById(R.id.et_weight);
        ck_married = findViewById(R.id.ck_married);

        findViewById(R.id.btn_save).setOnClickListener(this);
        findViewById(R.id.btn_delete).setOnClickListener(this);
        findViewById(R.id.btn_update).setOnClickListener(this);
        findViewById(R.id.btn_query).setOnClickListener(this);
    }

    @Override
    protected void onStart() {
        super.onStart();
       mHelper = UserDBHelper.getInstance(this);
       mHelper.openReadLink();
       mHelper.openWriteLink();
    }

    @Override
    protected void onStop() {
        super.onStop();
         mHelper.closeLink();
    }

    @Override
    public void onClick(View view) {
        String name = et_name.getText().toString();
        String age = et_age.getText().toString();
        String height = et_height.getText().toString();
        String weight = et_weight.getText().toString();
        User user = null;

        switch (view.getId()){
            case R.id.btn_save:
                     // 以下声明一个用户信息对象，并填写它的各字段值
                    user = new User(name,
                    Integer.parseInt(age),
                    Long.parseLong(height),
                    Float.parseFloat(weight),
                    ck_married.isChecked());
                if (mHelper.insert(user) > 0) {
                    ToastUtil.show(this, "添加成功");
                }
                break;
        }

    }
}
```

继续完成其他操作的代码

```
 public long deleteByName(String name) {
        //删除所有
        //mWDB.delete(TABLE_NAME, "1=1", null);
        return mWDB.delete(TABLE_NAME, "name=?", new String[]{name});
    }
```

能被SQLite直接使用的数据结构是ContentValues类，它类似于映射Map，也提供了put和get方法存取键值对。区别之处在于：ContentValues的键只能是字符串，不能是其他类型。ContentValues主要用于增加记录和更新记录，对应数据库的insert和update方法。

```
 public List<User> queryAll() {
        List<User> list = new ArrayList<>();
        // 执行记录查询动作，该语句返回结果集的游标
        Cursor cursor = mRDB.query(TABLE_NAME, null, null, null, null, null, null);
        // 循环取出游标指向的每条记录
        while (cursor.moveToNext()) {
            User user = new User();
            user.id = cursor.getInt(0);
            user.name = cursor.getString(1);
            user.age = cursor.getInt(2);
            user.height = cursor.getLong(3);
            user.weight = cursor.getFloat(4);
            //SQLite没有布尔型，用0表示false，用1表示true
            user.married = (cursor.getInt(5) == 0) ? false : true;
            list.add(user);
        }
        return list;
    }
```

![image-20230408112054278](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304081120341.png)

记录的查询操作用到了游标类Cursor，调用query和rawQuery方法返回的都是Cursor对象，若要获取全部的查询结果，则需根据游标的指示一条一条遍历结果集合。Cursor的常用方法可分为 3 类，说明如下：

1 游标控制类方法，用于指定游标的状态

```
close：关闭游标。

isClosed：判断游标是否关闭。

isFirst：判断游标是否在开头。

isLast：判断游标是否在末尾。
```

2 游标移动类方法，把游标移动到指定位置

```
moveToFirst：移动游标到开头。

moveToLast：移动游标到末尾。

moveToNext：移动游标到下一条记录。

moveToPrevious：移动游标到上一条记录。

move：往后移动游标若干条记录。

moveToPosition：移动游标到指定位置的记录。
```

3 获取记录类方法，可获取记录的数量、类型以及取值

```
getCount：获取结果记录的数量。

getInt：获取指定字段的整型值。

getLong：获取指定字段的长整型值。

getFloat：获取指定字段的浮点数值。

getString：获取指定字段的字符串值。

getType：获取指定字段的字段类型。
```



#### 6.2.3 事务管理

事务类，用于事务层面的操作

```
beginTransaction：开始事务。

setTransactionSuccessful：设置事务的成功标志。

endTransaction：结束事务。执行本方法时，系统会判断之前是否调用了

setTransactionSuccessful方法，如果之前已调用该方法就提交事务，如果没有调用该方法就回滚事务。
```

代码

```
     try {
            mWDB.beginTransaction();
            mWDB.insert(TABLE_NAME, null, values);
            //int i = 10 / 0;
            mWDB.insert(TABLE_NAME, null, values);
            mWDB.setTransactionSuccessful();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            mWDB.endTransaction();
        }
```



#### 6.2.4 数据库版本升级

onUpgrade方法在数据库版本升高时执行，在此可以根据新旧版本号变更表结构。

```

    //升级数据库会执行
    @Override
    public void onUpgrade(SQLiteDatabase db, int i, int i1) {
        String sql = "ALTER TABLE " + TABLE_NAME + " ADD COLUMN phone VARCHAR;";
        db.execSQL(sql);
        sql = "ALTER TABLE " + TABLE_NAME + " ADD COLUMN password VARCHAR;";
        db.execSQL(sql);
    }
```



### 6.3 存储卡的文件操作

#### 6.3.1 私有存储空间与公共存储空间

Android从7.0开始将存储卡划分为私有存储和公共存储两大部分，也就是分区存储方式

![image-20230408112226746](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304081122810.png)

导出到外部存储的私有空间（卸载应用后，私有空间不存在）

代码如下：

```
public class FileWriteActivity extends AppCompatActivity implements View.OnClickListener {
    private EditText et_name;
    private EditText et_age;
    private EditText et_height;
    private EditText et_weight;
    private CheckBox ck_married;
    private String path;//文件保存路径
    private TextView tv_txt;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_file_write);
        et_name = findViewById(R.id.et_name);
        et_age = findViewById(R.id.et_age);
        et_height = findViewById(R.id.et_height);
        et_weight = findViewById(R.id.et_weight);
        ck_married = findViewById(R.id.ck_married);

        tv_txt = findViewById(R.id.tv_txt);

        findViewById(R.id.btn_save).setOnClickListener(this);
        findViewById(R.id.btn_read).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()){
            case R.id.btn_save:
                String name = et_name.getText().toString();
                String age = et_age.getText().toString();
                String height = et_height.getText().toString();
                String weight = et_weight.getText().toString();

                StringBuilder sb=new StringBuilder();
                sb.append("姓名：").append(name);
                sb.append("\n年龄：").append(age);
                sb.append("\n身高：").append(height);
                sb.append("\n体重：").append(weight);
                sb.append("\n婚否：").append(ck_married.isChecked()?"是":"否");

                String fileName= System.currentTimeMillis()+ ".txt";
                String directory = null;
                //外部存储的私有空间
                directory = getExternalFilesDir(Environment.DIRECTORY_DOWNLOADS).toString();
                path = directory + File.separatorChar +fileName;
                FileUtil.saveText(path,sb.toString());
                ToastUtil.show(this,"保存成功");
                break;
            case R.id.btn_read:
                tv_txt.setText(FileUtil.openText(path));
                break;
        }

    }
}
```

工具类

```
public class FileUtil {

    //把字符串保存到指定的文件路径
    public static void saveText(String path,String txt) {
        BufferedWriter os = null;
        try {
            os = new BufferedWriter(new FileWriter(path));
            os.write(txt);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (os != null) {
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    // 从指定路径的文本文件中读取内容字符串
    public static String openText(String path) {
        BufferedReader is = null;
        StringBuilder sb = new StringBuilder();
        try {
            is = new BufferedReader(new FileReader(path));
            String line = null;
            while ((line = is.readLine()) != null) {
                sb.append(line);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (is != null) {
                try {
                    is.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        return sb.toString();
    }
    
}
```



![image-20230410151257545](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101513665.png)

存储路径

![image-20230410155017041](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101550102.png)

导出这个文件看

![image-20230410155151349](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101551408.png)

![image-20230410155225624](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101552673.png)

外部存储空间，则要在AndroidManifest.xml里面添加下述的权限配置。

```
<!-- 存储卡读写 -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/> 
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAG" />
```

代码：

```
//外部存储的公共空间
directory = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS).toString();
```

![image-20230410155837703](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101558759.png)

存储路径：

![image-20230410160010325](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101600384.png)

内部存储的私有空间(卸掉应用后，也不存在了)

```
 //内部存储的私有空间
 directory = getFilesDir().toString();
```

存储路径

data/data/项目/files/

![image-20230410160527890](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101605951.png)



#### 6.3.2 在存储卡上读写文本文件

文本文件的读写借助于文件IO流FileOutputStream和FileInputStream。其中，FileOutputStream用于写文件，FileInputStream用于读文件

```
// 把字符串保存到指定路径的文本文件
public static void saveText(String path, String txt) { 
   // 根据指定的文件路径构建文件输出流对象
   try (FileOutputStream fos = new FileOutputStream(path)) { 
       fos.write(txt.getBytes()); // 把字符串写入文件输出流
  } catch (Exception e) { 
       e.printStackTrace(); 
 }
}
// 从指定路径的文本文件中读取内容字符串
public static String openText(String path) {
   String readStr = "";
   // 根据指定的文件路径构建文件输入流对象
   try (FileInputStream fis = new FileInputStream(path)) {
       byte[] b = new byte[fis.available()];
       fis.read(b); // 从文件输入流读取字节数组
       readStr = new String(b); // 把字节数组转换为字符串
  } catch (Exception e) {
       e.printStackTrace();
 }
   return readStr; // 返回文本文件中的文本字符串 
}

```



#### 6.3.3 在存储卡上读写图片文件

图片文件保存的是图像数据，需要专门的位图工具Bitmap处理。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <Button
        android:id="@+id/btn_save"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="保存"/>

    <Button
        android:id="@+id/btn_read"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="读取"/>

    <ImageView
        android:id="@+id/iv_content"
        android:layout_width="match_parent"
        android:layout_height="400dp"
        android:scaleType="fitCenter"/>

</LinearLayout>
```

工具类

```
    //打开图片
    public static void saveImage(String path, Bitmap bitmap) {
        FileOutputStream fos = null;
        try {
            fos = new FileOutputStream(path);
            // 把位图数据压缩到文件输出流中
            bitmap.compress(Bitmap.CompressFormat.JPEG, 100, fos);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (fos != null) {
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

读取图片

```java
    // 从指定路径的图片文件中读取位图数据
    public static Bitmap openImage(String path) {
        Bitmap bitmap = null;
        FileInputStream fis = null;
        try {
            fis = new FileInputStream(path);
            bitmap = BitmapFactory.decodeStream(fis);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (fis != null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        return bitmap;
    }
```

图像视图ImageView提供了下列方法显示各种来源的图片：

```
setImageResource：设置图像视图的图片资源，该方法的入参为资源图片的编号，形如“R.drawable.去掉扩展名的图片名称”。

setImageBitmap：设置图像视图的位图对象，该方法的入参为Bitmap类型。

setImageURI：设置图像视图的路径对象，该方法的入参为Uri类型。字符串格式的文件路径可通过代码“Uri.parse(file_path)”转换成路径对象。
```



```java
public class ImageWriteActivity extends AppCompatActivity implements View.OnClickListener {

    private ImageView iv_content;
    private String path;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_image_write);
        iv_content = findViewById(R.id.iv_content);

        findViewById(R.id.btn_save).setOnClickListener(this);
        findViewById(R.id.btn_read).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.btn_save:
                // 获取当前App的私有下载目录
                String fileName= System.currentTimeMillis()+ ".png";
                path = getExternalFilesDir(Environment.DIRECTORY_DOWNLOADS).toString() + File.separatorChar+fileName;
                Bitmap b1 = BitmapFactory.decodeResource(getResources(), R.drawable.camera);
                FileUtil.saveImage(path, b1); // 把位图对象保存为图片文件
               // tv_path.setText("图片文件的保存路径为：\n" + file_path);
                ToastUtil.show(this,"保存成功");
                break;
            case R.id.btn_read:
              //  Bitmap b2 = FileUtil.openImage(path);
              //  iv_content.setImageBitmap(b2);

                //第二种办法
                Bitmap b2 = BitmapFactory.decodeFile(path);
                iv_content.setImageBitmap(b2);

                //第三种
                //iv_content.setImageURI(Uri.parse(path));
                break;
        }
    }
}
```

![image-20230410170759569](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101708683.png)

测试结果：

![image-20230410171057951](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101710008.png)



### 6.4 应用组件Application

#### 6.4.1 Application的生命周期

**Application是Android的一大组件**，在App运行过程中有且仅有一个Application对象贯穿应用的整个生命周期。

自定义一个MyApplication

```
public class MyApplication extends Application {
   @Override
   public void onCreate() {
       super.onCreate();
       Log.d(TAG, "onCreate");
 }
   @Override
   public void onTerminate() {
       super.onTerminate();
       Log.d(TAG, "onTerminate");
 } 
}

```

打开AndroidManifest.xml，给application节点加上name属性

```
<application
       android:name=".MyApplication" 
       android:icon="@mipmap/ic_launcher" 
       android:label="@string/app_name" 
       android:theme="@style/AppTheme">

```

![image-20230410172331232](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101723305.png)

```
onCreate：在App启动时调用。

onTerminate：在App终止时调用（按字面意思）。

onConfigurationChanged：在配置改变时调用，例如从竖屏变为横屏。
```

#### 6.4.2 Application操作全局变量

Application的生命周期覆盖了App运行的全过程。不像短暂的Activity生命周期，一旦退出该页面，Activity实例就被销毁。因此，利用Application的全生命特性，能够在Application实例中保存全局变量。

![image-20230410172725392](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101727457.png)

创建全局变量

```
 // 声明一个公共的信息映射对象，可当作全局变量使用
    public HashMap<String, String> infoMap = new HashMap<String, String>();
```

再创建一个AppWriteActivity

```java
public class AppWriteActivity extends AppCompatActivity implements View.OnClickListener {
    private EditText et_name;
    private EditText et_age;
    private EditText et_height;
    private EditText et_weight;
    private CheckBox ck_married;
    private MyApplication app;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_app_write);

        et_name = findViewById(R.id.et_name);
        et_age = findViewById(R.id.et_age);
        et_height = findViewById(R.id.et_height);
        et_weight = findViewById(R.id.et_weight);
        ck_married = findViewById(R.id.ck_married);

        findViewById(R.id.btn_save).setOnClickListener(this);
        app = MyApplication.getInstance();
        reload();
    }

    private void reload() {
        String name = app.infoMap.get("name");
        if (name == null) {
            return;
        }
        String age = app.infoMap.get("age");
        String height = app.infoMap.get("height");
        String weight = app.infoMap.get("weight");
        String married = app.infoMap.get("married");
        et_name.setText(name);
        et_age.setText(age);
        et_height.setText(height);
        et_weight.setText(weight);
        if ("是".equals(married)) {
            ck_married.setChecked(true);
        } else {
            ck_married.setChecked(false);
        }
    }

    @Override
    public void onClick(View view) {
        String name = et_name.getText().toString();
        String age = et_age.getText().toString();
        String height = et_height.getText().toString();
        String weight = et_weight.getText().toString();

        app.infoMap.put("name", name);
        app.infoMap.put("age", age);
        app.infoMap.put("height", height);
        app.infoMap.put("weight", weight);
        app.infoMap.put("married", ck_married.isChecked() ? "是" : "否");

    }
}
```

适合在Application中保存的全局变量主要有下面 3 类数据：

（ 1 ）会频繁读取的信息，例如用户名、手机号码等。(不推荐把太多数据放在全局变量中，更不要用静态变量去存储）

（ 2 ）不方便由意图传递的数据，例如位图对象、非字符串类型的集合对象等。

（ 3 ）容易因频繁分配内存而导致内存泄漏的对象，例如Handler处理器实例等



### 6.5 JetPack Room

Room是数据库处理框架

#### 6.5.1 导入Room库

修改模块的build.gradle文件，往dependencies节点添加下面两行配置

```
implementation 'androidx.room:room-runtime:2.2.5' 
annotationProcessor 'androidx.room:room-compiler:2.2.5'
```

![image-20230410181628664](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101816763.png)

![image-20230410181657402](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304101816470.png)

#### 6.5.2 实体类和DAO

实体类

```java
@Entity
public class BookInfo {

    @PrimaryKey(autoGenerate = true)
    private int id;

    private String name; // 书籍名称
    private String author; // 作者
    private String press; // 出版社
    private double price; // 价格

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public String getPress() {
        return press;
    }

    public void setPress(String press) {
        this.press = press;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }

    @Override
    public String toString() {
        return "BookInfo{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", author='" + author + '\'' +
                ", press='" + press + '\'' +
                ", price=" + price +
                '}';
    }
}
```

dao

```java
@Dao
public interface  BookDao {

    @Insert
    void insert(BookInfo... book);

    @Delete
    void delete(BookInfo... book);

    // 删除所有书籍信息
    @Query("DELETE FROM BookInfo")
    void deleteAll();

    @Update
    int update(BookInfo... book);

    // 加载所有书籍信息
    @Query("SELECT * FROM BookInfo")
    List<BookInfo> queryAll();

    // 根据名字加载书籍
    @Query("SELECT * FROM BookInfo WHERE name = :name ORDER BY id DESC limit 1")
    BookInfo queryByName(String name);

}
```

#### 6.5.3 指定数据库及位置

下面是数据库类BookDatabase的定义代码例子

```java
//entities表示该数据库有哪些表，version表示数据库的版本号
//exportSchema表示是否导出数据库信息的json串，建议设为false，若设为true还需指定json文件的保存路径
@Database(entities = {BookInfo.class}, version = 1, exportSchema = true)
public abstract class BookDatabase extends RoomDatabase {
    // 获取该数据库中某张表的持久化对象
    public abstract BookDao bookDao();

}
```

然后在build.gradle指定目录

```
javaCompileOptions {
            annotationProcessorOptions {
                arguments = ["room.schemaLocation": "$projectDir/schemas".toString()]
            }
        }
```

![image-20230411131516283](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304111315439.png)

#### 6.5.4 声明全局唯一实例

在自定义的Application类中声明图书数据库的唯一实例

```java
public class MyApplication extends Application {

    // 声明一个公共的信息映射对象，可当作全局变量使用
    public HashMap<String, String> infoMap = new HashMap<String, String>();
    private static MyApplication mApp; // 声明一个当前应用的静态实例

    private BookDatabase bookDatabase; // 声明一个书籍数据库对象

    // 利用单例模式获取当前应用的唯一实例
    public static MyApplication getInstance() {
        return mApp;
    }

    @Override
    public void onCreate() {
        super.onCreate();
        Log.d("ning", "onCreate");
        mApp = this; // 在打开应用时对静态的应用实例赋值
        // 构建书籍数据库的实例
        bookDatabase = Room.databaseBuilder(mApp, BookDatabase.class,"book")
                .addMigrations() // 允许迁移数据库（发生数据库变更时，Room默认删除原数据库再创建新数据库。如此一来原来的记录会丢失，故而要改为迁移方式以便保存原有记录）
                .allowMainThreadQueries() // 允许在主线程中操作数据库（Room默认不能在主线程中操作数据库）
                .build();
    }

    // 获取书籍数据库的实例
    public BookDatabase getBookDB(){
        return bookDatabase;
    }

    //App终止时调用
    @Override
    public void onTerminate() {
        super.onTerminate();
        Log.d("ning", "onTerminate");
    }

    // 在配置改变时调用，例如横屏变竖屏
    @Override
    public void onConfigurationChanged(@NonNull Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
    }
}
```

![image-20230411151315760](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304111513868.png)

![image-20230411151436993](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304111514066.png)

#### 6.5.5 增删改查代码

在操作图书信息表的地方获取数据表的持久化对象持久化对象的获取

```
// 从App实例中获取唯一的图书持久化对象
BookDao bookDao = MyApplication.getInstance().getBookDB().bookDao();
```

![image-20230411152613706](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304111526785.png)

其他代码

```java
    switch (view.getId()){
            case R.id.btn_save:
                BookInfo b1 = new BookInfo();
                b1.setName(name);
                b1.setAuthor(author);
                b1.setPress(press);
                b1.setPrice(Double.parseDouble(price));
                bookDao.insert(b1);

                break;
            case R.id.btn_delete:
                BookInfo b2 = new BookInfo();
                b2.setId(1);
                bookDao.delete(b2);
                break;
            case R.id.btn_update:
                BookInfo b3 = new BookInfo();
                BookInfo b4 = bookDao.queryByName(name);
                b3.setId(b4.getId());
                b3.setName(name);
                b3.setAuthor(author);
                b3.setPress(press);
                b3.setPrice(Double.parseDouble(price));
                bookDao.update(b3);
                break;
            case R.id.btn_query:
                List<BookInfo> bookInfoList = bookDao.queryAll();
                break;
        }
```



## 7、高级控件





## 8、内容提供器

内容提供器(Content Provider)主要用于在不同的应用程序之间实现数据共享的功能

![image-20230411172456070](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304111725233.png)

客户端APP将用户的输入内容，通过Content Provider跨进程通信传输给服务端App

![image-20230411172914656](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304111729745.png)

### 8.1 Server端代码编写

创建一个Content Provider

![image-20230412175624267](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304121756441.png)

![image-20230412175718684](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304121757757.png)

把刚才xxx改成完整类名

![image-20230412180639543](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304121806602.png)





### 8.2 Client端代码

利用ContentProvider只实现服务端App的数据封装，如果客户端App想访问对方的内部数据，就要通过内容解析器ContentResolver访问。

![image-20230413143721765](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304131437890.png)

```

```

出于安全考虑，Android11需要事先声明需要访问的其他应用

在AndroidManifest.xml中添加如下：

```
   <queries>
        <!--服务端应用包名 -->
        <package android:name="com.jiang.chapter07_server.provider"/>

        <!--或者直接指定authorities-->
        <!-- <provider android:authorities="com.jiang.chapter07_server.provider.UserInfoProvider"/>   -->
    </queries>
```

测试，先发布server,再发布client

![image-20230414104814110](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304141048176.png)

插入数据成功，读取成功

![image-20230414112443291](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304141124352.png)

注意出现异常

```
java.lang.IllegalArgumentException: Unknown URL content://
```

Android高版本收紧了权限以防止程序随便访问其他程序的文件。解决办法：

```
<uses-permission android:name="DatabaseProvider._READ_PERMISSION" />
<uses-permission android:name="DatabaseProvider._WRITE_PERMISSION" />
```

![image-20230414112522939](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304141125005.png)

删除操作，UserInfoProvider

```
 private static final UriMatcher URI_MATCHER = new UriMatcher(UriMatcher.NO_MATCH);

    private static final int USERS = 1;
    private static final int USER = 2;

    static {
        URI_MATCHER.addURI(UserInfoContent.AUTHORITIES,"/user",USERS);
        URI_MATCHER.addURI(UserInfoContent.AUTHORITIES,"/user/#",USER);
    }
    
    
        @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        int count = 0;
        switch (URI_MATCHER.match(uri)) {
            //这种是uri不带参数："content://com.jiang.chapter07_server.UserInfoProvider/user"
            //删除多行
            case USERS:
                 // 获取SQLite数据库的写连接
                SQLiteDatabase db1 = userDBHelper.getWritableDatabase();
                // 执行SQLite的删除操作，并返回删除记录的数目
                count = db1.delete(UserDBHelper.TABLE_NAME, selection, selectionArgs);
                db1.close();
                break;
            //这种是uri带参数："content://com.jiang.chapter07_server.UserInfoProvider/user/2"
            //删除单行
            case USER:
                String id = uri.getLastPathSegment();
                SQLiteDatabase db2 = userDBHelper.getWritableDatabase();
                count = db2.delete(UserDBHelper.TABLE_NAME, "_id = ?", new String[]{id});
                db2.close();
                break;
        }
        return count;
    }
```

```
  case R.id.btn_delete:
                // content://com.jiang.chapter07_server.provider.UserInfoProvider/user/2
                Uri uri = ContentUris.withAppendedId(UserInfoContent.CONTENT_URI, 2);
                int count = getContentResolver().delete(uri, null, null);
                if(count >0 ){
                    ToastUtil.show(this,"删除成功");
                }

                break;
```

测试删除，现在有2条数据

点击删除按钮

![](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304141228568.png)

也可以根据条件删除

```
int count = getContentResolver().delete(UserInfoContent.CONTENT_URI,"name = ?",new String[]{"Jiang"});
```



### 8.3 使用内容组件获取通讯信息

![image-20230414142940669](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304141429949.png)

#### 8.3.1 动态申请权限

动态申请权限有三个步骤：

1 检查App是否开启了指定权限：

​    调用ContextCompat的checkSelfPermission方法

2 请求系统弹窗，以便用户选择是否开启权限：

​    调用ActivityCompat的requestPermissions方法，即可命令系统自动弹出权限申请窗口。

3 判断用户的权限选择结果，是开启还是拒绝：

​    重写活动页面的权限请求回调方法onRequestPermissionsResult，在该方法内部处理用户的权限选择结果




动态申请权限有两种方式：饿汉式 和 懒汉式。

饿汉式

工具类

```
public class PermissionUtil {

    //检查权限，返回true表示完全启用权限，返回false则表示为完全启用所有权限
    public static boolean checkPermission(Activity activity, String[] permissions, int requestCode){
        //Android6.0之后采取动态权限管理
        if(Build.VERSION.SDK_INT > Build.VERSION_CODES.M){
            int check = PackageManager.PERMISSION_GRANTED;  // 0

            for (String permission : permissions) {
                check = ContextCompat.checkSelfPermission(activity, permission);
                if(check != PackageManager.PERMISSION_GRANTED){
                    break;
                }
            }

            //如果未开启该权限，则请求系统弹窗，好让用户选择是否开启权限
            if(check != PackageManager.PERMISSION_GRANTED){
                //请求权限
                ActivityCompat.requestPermissions(activity, permissions, requestCode);
                return false;
            }
              return true;
        }
        return  false;
    }

    //检查权限数组，返回true表示都已经授权
    public static boolean checkGrant(int[] grantResults) {
        if(grantResults != null){
            for (int grant : grantResults) {
                if(grant != PackageManager.PERMISSION_GRANTED){
                    return false;
                }
            }
            return true;
        }

        return false;
    }

}
```

```
public class PermissionLazyActivity extends AppCompatActivity implements View.OnClickListener {

    //通讯录的读写权限
    private static final String[] PERMISSION_CONTACT = {
            Manifest.permission.READ_CONTACTS,
            Manifest.permission.WRITE_CONTACTS
    };

    //短信的读写权限
    private static final String[] PERMISSION_SMS = {
            Manifest.permission.SEND_SMS,
            Manifest.permission.RECEIVE_SMS
    };

    private static final int REQUEST_CODE_CONTACTS = 1;
    private static final int REQUEST_CODE_SMS = 2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_permission_lazy);

        findViewById(R.id.btn_contact).setOnClickListener(this);
        findViewById(R.id.btn_sms).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.btn_contact:
                PermissionUtil.checkPermission(PermissionLazyActivity.this, PERMISSION_CONTACT, REQUEST_CODE_CONTACTS);
                break;
            case R.id.btn_sms:
                PermissionUtil.checkPermission(PermissionLazyActivity.this, PERMISSION_SMS, REQUEST_CODE_SMS);
                break;
        }

    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        switch (requestCode){
            case REQUEST_CODE_CONTACTS:
                if(PermissionUtil.checkGrant(grantResults)){
                    Log.d("ning", "通讯录获取成功");
                }else{
                    Log.d("ning", "通讯录获取失败");
                    //跳转到设置界面
                    jumpToSettings();
                }
                break;
            case REQUEST_CODE_SMS:
                if(PermissionUtil.checkGrant(grantResults)){
                    Log.d("ning", "短信权限获取成功");
                }else{
                    Log.d("ning", "短信权限获取失败");
                    //跳转到设置界面
                    jumpToSettings();
                }
                break;
        }
    }

    //跳转到设置界面
    private void jumpToSettings(){
        Intent intent = new Intent();
        intent.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
        intent.setData(Uri.fromParts("package", getPackageName(), null));
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(intent);
    }
}public class PermissionLazyActivity extends AppCompatActivity implements View.OnClickListener {

    //通讯录的读写权限
    private static final String[] PERMISSION_CONTACT = {
            Manifest.permission.READ_CONTACTS,
            Manifest.permission.WRITE_CONTACTS
    };

    //短信的读写权限
    private static final String[] PERMISSION_SMS = {
            Manifest.permission.SEND_SMS,
            Manifest.permission.RECEIVE_SMS
    };

    private static final int REQUEST_CODE_CONTACTS = 1;
    private static final int REQUEST_CODE_SMS = 2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_permission_lazy);

        findViewById(R.id.btn_contact).setOnClickListener(this);
        findViewById(R.id.btn_sms).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.btn_contact:
                PermissionUtil.checkPermission(PermissionLazyActivity.this, PERMISSION_CONTACT, REQUEST_CODE_CONTACTS);
                break;
            case R.id.btn_sms:
                PermissionUtil.checkPermission(PermissionLazyActivity.this, PERMISSION_SMS, REQUEST_CODE_SMS);
                break;
        }

    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        switch (requestCode){
            case REQUEST_CODE_CONTACTS:
                if(PermissionUtil.checkGrant(grantResults)){
                    Log.d("ning", "通讯录获取成功");
                }else{
                    Log.d("ning", "通讯录获取失败");
                    //跳转到设置界面
                    jumpToSettings();
                }
                break;
            case REQUEST_CODE_SMS:
                if(PermissionUtil.checkGrant(grantResults)){
                    Log.d("ning", "短信权限获取成功");
                }else{
                    Log.d("ning", "短信权限获取失败");
                    //跳转到设置界面
                    jumpToSettings();
                }
                break;
        }
    }

    //跳转到设置界面
    private void jumpToSettings(){
        Intent intent = new Intent();
        intent.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
        intent.setData(Uri.fromParts("package", getPackageName(), null));
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(intent);
    }
}
```

另外还需要在AndroidManifest.xml中配置：（在低版本中只需要配置这些信息即可，高版本就需要上面的动态申请权限）

```xml
<!--    开启通讯录权限-->
    <uses-permission android:name="android.permission.READ_CONTACTS"/>
    <uses-permission android:name="android.permission.WRITE_CONTACTS"/>

<!--    开启短信收发权限-->
    <uses-permission android:name="android.permission.SEND_SMS"/>
    <uses-permission android:name="android.permission.RECEIVE_SMS"/>

```

![image-20230414152823569](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304141528641.png)

效果：

![image-20230414153505208](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304141535271.png)

![image-20230414153525351](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304141535411.png)

懒汉式：在页面打开之后就一次性需要用户获取所有权限。

```java
public class PermissionHungryActivity extends AppCompatActivity implements View.OnClickListener {

    //所需全部读写权限
    private static final String[] PERMISSIONS = {
            Manifest.permission.READ_CONTACTS,
            Manifest.permission.WRITE_CONTACTS,
            Manifest.permission.SEND_SMS,
            Manifest.permission.RECEIVE_SMS
    };

    private static final int REQUEST_CODE_ALL = 1;
    private static final int REQUEST_CODE_CONTACTS = 2;
    private static final int REQUEST_CODE_SMS = 3;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_permission_hungry);
        //检查是否拥有所有所需权限
        PermissionUtil.checkPermission(this, PERMISSIONS, REQUEST_CODE_ALL);

        findViewById(R.id.btn_contact).setOnClickListener(this);
        findViewById(R.id.btn_sms).setOnClickListener(this);
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        switch (requestCode){
            case REQUEST_CODE_ALL:
                if(PermissionUtil.checkGrant(grantResults)){
                    Log.d("ning", "所有权限获取成功");
                }else{
                    //部分权限获取失败
                    for (int i = 0; i < grantResults.length; i++) {
                        if(grantResults[i] != PackageManager.PERMISSION_GRANTED){
                            //判断是什么权限获取失败
                            switch (permissions[i]){
                                case Manifest.permission.WRITE_CONTACTS:
                                case Manifest.permission.READ_CONTACTS:
                                    Log.d("ning", "通讯录获取失败");
                                    jumpToSettings();
                                    break;
                                case Manifest.permission.SEND_SMS:
                                case Manifest.permission.RECEIVE_SMS:
                                    Log.d("ning", "短信权限获取失败");
                                    jumpToSettings();
                                    break;
                            }
                        }
                    }
                }
                break;
        }
    }

    //跳转到设置界面
    private void jumpToSettings(){
        Intent intent = new Intent();
        intent.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
        intent.setData(Uri.fromParts("package", getPackageName(), null));
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(intent);
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.btn_contact:
                PermissionUtil.checkPermission(PermissionHungryActivity.this, new String[]{PERMISSIONS[0],PERMISSIONS[1]}, REQUEST_CODE_CONTACTS);
                break;
            case R.id.btn_sms:
                PermissionUtil.checkPermission(PermissionHungryActivity.this, new String[]{PERMISSIONS[2],PERMISSIONS[3]}, REQUEST_CODE_SMS);
                break;
        }
    }
}
```





#### 8.3.2 使用ContentResolver读写联系人

手机中通讯录的主要表结构有：

`raw_contacts`表：

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304181216318.png)

data表：记录了用户的通讯录所有数据，包括手机号，显示名称等，但是里面的mimetype_id表示不同的数据类型，这与表mimetypes表中的id相对应，raw_contact_id 与上面的 raw_contacts表中的 id 相对应。

![image-20230122114040243](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304181217145.png)

mimetypes表：

![image-20230122114056962](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304181217551.png)

插入步骤如下：

- 首先往raw_contacts表中插入一条数据得到id
- 接着由于一个联系人有姓名，电话号码，邮箱，因此需要分三次插入data表中，将raw_contact_id和上面得到的id进行关联

##### 1 单个插入联系人

界面

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:gravity="center"
            android:text="联系人姓名："
            android:textSize="17sp"
            android:textColor="@color/black"/>

        <EditText
            android:id="@+id/et_contact_name"
            android:layout_width="0dp"
            android:layout_weight="1"
            android:layout_height="match_parent"
            android:background="@drawable/edit_background"
            android:layout_marginStart="10dp"
            android:text="Jiang"
            android:hint="请输入用户名"
            android:inputType="text" />
    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:gravity="center"
            android:text="联系人号码："
            android:textSize="17sp"
            android:textColor="@color/black"/>

        <EditText
            android:id="@+id/et_contact_phone"
            android:layout_width="0dp"
            android:layout_weight="1"
            android:layout_height="match_parent"
            android:background="@drawable/edit_background"
            android:layout_marginStart="10dp"
            android:text="18137066576"
            android:hint="请输入号码"
            android:inputType="number" />
    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:text="联系人邮箱："
            android:gravity="center"
            android:textSize="17sp"
            android:textColor="@color/black"/>

        <EditText
            android:id="@+id/et_contact_email"
            android:layout_width="0dp"
            android:layout_weight="1"
            android:layout_height="match_parent"
            android:background="@drawable/edit_background"
            android:layout_marginStart="10dp"
            android:text="156772@qq.com"
            android:hint="请输入邮箱"
            android:inputType="number" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <Button
            android:id="@+id/btn_add_contact"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="添加联系人"/>
        <Button
            android:id="@+id/btn_read_contact"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="查询联系人"/>

    </LinearLayout>

</LinearLayout>
```

代码

实体类

```
public class Contact {
    public String name;
    public String phone;
    public String email;

    @Override
    public String toString() {
        return "Contact{" +
                "name='" + name + '\'' +
                ", phone='" + phone + '\'' +
                ", email='" + email + '\'' +
                '}';
    }
}
```

```
public class ContactAddActivity extends AppCompatActivity implements View.OnClickListener {
    private EditText et_contact_name;
    private EditText et_contact_phone;
    private EditText et_contact_email;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_contact_add);
        et_contact_name = findViewById(R.id.et_contact_name);
        et_contact_phone = findViewById(R.id.et_contact_phone);
        et_contact_email = findViewById(R.id.et_contact_email);
        findViewById(R.id.btn_add_contact).setOnClickListener(this);
        findViewById(R.id.btn_read_contact).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()){
            case R.id.btn_add_contact:
                // 创建一个联系人对象
                Contact contact = new Contact();
                contact.name = et_contact_name.getText().toString().trim();
                contact.phone = et_contact_phone.getText().toString().trim();
                contact.email = et_contact_email.getText().toString().trim();
                // 方式一，使用ContentResolver多次写入，每次一个字段
                addContact(getContentResolver(),contact);
                break;
            case R.id.btn_read_contact:

                break;
        }
        
    }

    //往通讯录添加一个联系人信息（姓名，号码，邮箱）
    private void addContact(ContentResolver contentResolver, Contact contact) {
        ContentValues values = new ContentValues();
        //插入记录得到id
        Uri uri = contentResolver.insert(ContactsContract.RawContacts.CONTENT_URI, values);
        long rawContentId = ContentUris.parseId(uri);

        //插入名字
        ContentValues name = new ContentValues();
        //关联上面得到的联系人id
        name.put(ContactsContract.Contacts.Data.RAW_CONTACT_ID, rawContentId);
        //关联联系人姓名的类型
        name.put(ContactsContract.Contacts.Data.MIMETYPE, ContactsContract.CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE);
        //关联联系人姓名
        name.put(ContactsContract.Data.DATA2, contact.name);
        contentResolver.insert(ContactsContract.Data.CONTENT_URI, name);

        //插入电话号码
        ContentValues phone = new ContentValues();
        //关联上面得到的联系人id
        phone.put(ContactsContract.Contacts.Data.RAW_CONTACT_ID, rawContentId);
        //关联联系人电话号码的类型
        phone.put(ContactsContract.Contacts.Data.MIMETYPE, ContactsContract.CommonDataKinds.Phone.CONTENT_ITEM_TYPE);
        //关联联系人电话号码
        phone.put(ContactsContract.Data.DATA1, contact.phone);
        //指定该号码是家庭号码还是工作号码 (家庭)
        phone.put(ContactsContract.Data.DATA2, ContactsContract.CommonDataKinds.Phone.TYPE_MOBILE);
        contentResolver.insert(ContactsContract.Data.CONTENT_URI, phone);

        //插入邮箱
        ContentValues email = new ContentValues();
        //关联上面得到的联系人id
        email.put(ContactsContract.Contacts.Data.RAW_CONTACT_ID, rawContentId);
        //关联联系人邮箱的类型
        email.put(ContactsContract.Contacts.Data.MIMETYPE, ContactsContract.CommonDataKinds.Email.CONTENT_ITEM_TYPE);
        //关联联系人邮箱
        email.put(ContactsContract.Data.DATA1, contact.email);
        //指定该号码是家庭邮箱还是工作邮箱
        email.put(ContactsContract.Data.DATA2, ContactsContract.CommonDataKinds.Phone.TYPE_WORK);
        contentResolver.insert(ContactsContract.Data.CONTENT_URI, email);
    }
}
```

测试结果，添加成功

![image-20230418140755308](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304181407381.png)

##### 2 批量添加联系人

增加批量方法

```
 addFullContacts(getContentResolver(), contact);
```

代码如下

```
private void addFullContacts(ContentResolver contentResolver, Contact contact) {
        //创建一个插入联系人主记录的内容操作器
        ContentProviderOperation op_main = ContentProviderOperation
                .newInsert(ContactsContract.RawContacts.CONTENT_URI)
                //没有实际意义，不加这个会报错（不加这个导致没有创建ContentValue，导致报错）
                .withValue(ContactsContract.RawContacts.ACCOUNT_NAME, null)
                .build();
         //创建一个插入联系人姓名记录的内容操作器
        ContentProviderOperation op_name = ContentProviderOperation.newInsert(ContactsContract.Data.CONTENT_URI)
                //将第0个操作的id，即raw_contacts中的id作为data表中的raw_contact_id
                .withValueBackReference(ContactsContract.Contacts.Data.RAW_CONTACT_ID, 0)
                .withValue(ContactsContract.Contacts.Data.MIMETYPE, ContactsContract.CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE)
                .withValue(ContactsContract.Data.DATA2, contact.name)
                .build();
      //创建一个插入联系人电话号码记录的内容操作器
        ContentProviderOperation op_phone = ContentProviderOperation.newInsert(ContactsContract.Data.CONTENT_URI)
                //将第0个操作的id，即raw_contacts中的id作为data表中的raw_contact_id
                .withValueBackReference(ContactsContract.Contacts.Data.RAW_CONTACT_ID, 0)
                .withValue(ContactsContract.Contacts.Data.MIMETYPE, ContactsContract.CommonDataKinds.Phone.CONTENT_ITEM_TYPE)
                .withValue(ContactsContract.Data.DATA1, contact.phone)
                .withValue(ContactsContract.Data.DATA2, ContactsContract.CommonDataKinds.Phone.TYPE_MOBILE)
                .build();

        //创建一个插入联系人邮箱记录的内容操作器
        ContentProviderOperation op_email = ContentProviderOperation.newInsert(ContactsContract.Data.CONTENT_URI)
                //将第0个操作的id，即raw_contacts中的id作为data表中的raw_contact_id
                .withValueBackReference(ContactsContract.Contacts.Data.RAW_CONTACT_ID, 0)
                .withValue(ContactsContract.Contacts.Data.MIMETYPE, ContactsContract.CommonDataKinds.Email.CONTENT_ITEM_TYPE)
                .withValue(ContactsContract.Data.DATA1, contact.email)
                .withValue(ContactsContract.Data.DATA2, ContactsContract.CommonDataKinds.Phone.TYPE_WORK)
                .build();

        //全部放在集合中一次性提交
        ArrayList<ContentProviderOperation> operations = new ArrayList<>();
        operations.add(op_main);
        operations.add(op_name);
        operations.add(op_phone);
        operations.add(op_email);

        try {
            //批量提交四个操作
            contentResolver.applyBatch(ContactsContract.AUTHORITY, operations);
        } catch (OperationApplicationException e) {
            e.printStackTrace();
        } catch (RemoteException e) {
            e.printStackTrace();
        }
        
    }
```

测试结果

![](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304181815007.png)



##### 3 查询联系人

```
 private void readPhoneContacts(ContentResolver contentResolver) {
        //先查询raw_contacts表，再根据raw_contacts_id表 查询data表
        Cursor cursor = contentResolver.query(ContactsContract.RawContacts.CONTENT_URI, new String[]{ContactsContract.RawContacts._ID}, null, null, null);
        while(cursor.moveToNext()){
            int rawContactId = cursor.getInt(0);
            Uri uri = Uri.parse("content://com.android.contacts/contacts/" + rawContactId + "/data");
            Cursor dataCursor = contentResolver.query(uri, new String[]{ContactsContract.Contacts.Data.MIMETYPE, ContactsContract.Contacts.Data.DATA1, ContactsContract.Contacts.Data.DATA2}, null, null, null);
            Contact contact = new Contact();
            while (dataCursor.moveToNext()) {
                @SuppressLint("Range") String data1 = dataCursor.getString(dataCursor.getColumnIndex(ContactsContract.Contacts.Data.DATA1));
                @SuppressLint("Range") String mimeType = dataCursor.getString(dataCursor.getColumnIndex(ContactsContract.Contacts.Data.MIMETYPE));
                switch (mimeType) {
                    //是姓名
                    case ContactsContract.CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE:
                        contact.name = data1;
                        break;

                    //邮箱
                    case ContactsContract.CommonDataKinds.Email.CONTENT_ITEM_TYPE:
                        contact.email = data1;
                        break;

                    //手机
                    case ContactsContract.CommonDataKinds.Phone.CONTENT_ITEM_TYPE:
                        contact.phone = data1;
                        break;
                }
            }

            dataCursor.close();

            // RawContacts 表中出现的 _id，不一定在 Data 表中都会有对应记录
            if (contact.name != null) {
                Log.d("ning", contact.toString());
            }
        }
        cursor.close();
    }
```

![image-20230418222453977](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304182225087.png)



#### 8.3.3 使用ContentObserver监听短信

ContentResolver（内容观察器）获取数据采用的是主动查询方式，有查询就有数据，没查询就没数据。ContentResolver能够实时获取新增的数据，最常见的业务场景是短信验证码。

为了替用户省事，App通常会监控手机刚收到的短信验证码，并自动填写验证码输入框。这时就用到了内容观察器ContentObserver，从而执行开发者预先定义的代码。

![image-20230414153142996](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304141531064.png)

我们要利用ContentResolver来监听短信，事先给目标内容注册一个观察器，目标内容的数据一旦发生变化，就马上触发观察器的监听事件。

注意，需要配置短信的权限

![image-20230418224626545](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304182246623.png)

```
    <!-- 开启短信收发权限 -->
    <uses-permission android:name="android.permission.SEND_SMS" />
    <uses-permission android:name="android.permission.RECEIVE_SMS" />
```

代码

```
public class MonitorSmsActivity extends AppCompatActivity {
    private SmsGetObserver mObserver;
    final private int REQUEST_CODE_ASK_PERMISSIONS = 123;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_monitor_sms);

        // 给指定Uri注册内容观察器，一旦发生数据变化，就触发观察器的onChange方法
        Uri uri = Uri.parse("content://sms");
        // notifyForDescendents：
        // false ：表示精确匹配，即只匹配该Uri，true ：表示可以同时匹配其派生的Uri
        mObserver = new SmsGetObserver(this);

        getContentResolver().registerContentObserver(uri, true, mObserver);


    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        //取消注册
        getContentResolver().unregisterContentObserver(mObserver);
    }

    private static class SmsGetObserver extends ContentObserver {

        private final Context mContext;

        public SmsGetObserver(Context context) {
            super(new Handler(Looper.getMainLooper()));
            this.mContext = context;
        }

        @SuppressLint("Range")
        @Override
        public void onChange(boolean selfChange, @Nullable Uri uri) {
            super.onChange(selfChange,uri);
            // onChange会多次调用，收到一条短信会调用两次onChange
            // mUri===content://sms/raw/20
            // mUri===content://sms/inbox/20
            // 安卓7.0以上系统，点击标记为已读，也会调用一次
            // mUri===content://sms
            // 收到一条短信都是uri后面都会有确定的一个数字，对应数据库的_id，比如上面的20
            if (uri == null) {
                return;
            }
            if (uri.toString().contains("content://sms/raw") ||
                    uri.toString().equals("content://sms")) {
                return;
            }

            // 通过内容解析器获取符合条件的结果集游标
            Cursor cursor = mContext.getContentResolver().query(uri, new String[]{"address", "body", "date"}, null, null, "date DESC");
            if (cursor.moveToNext()) {
                // 短信的发送号码
               String sender = cursor.getString(cursor.getColumnIndex("address"));
                // 短信内容
                String content = cursor.getString(cursor.getColumnIndex("body"));
                Log.d("ning", String.format("sender:%s,content:%s", sender, content));
            }
            cursor.close();
        }
    }
}
```

测试

![image-20230418225638402](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304182256498.png)

切换到phone

![image-20230418225705182](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304182257250.png)

点击发送短信

![image-20230418225808867](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304182258940.png)

![image-20230418225827140](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304182258201.png)





## 9、网络通信









## 10  服务

可在后台执行长时间运行操作而不提供界面的应用组件。如下载文件、播放音乐

Service有两种方式：

- startService ：简单地启动，之后不能进行交互
- bindService：启动并绑定Service之后，可以进行交互









## 11、广播

广播组件 Broadcast 是Android 四大组件之一。

广播有以下特点：

- 活动只能一对一通信；而广播可以一对多，一人发送广播，多人接收处理。
- 对于发送方来说，广播不需要考虑接收方有没有在工作，接收方在工作就接收广播，不在工作就丢弃广播。
- 对于接收方来说，因为可能会收到各式各样的广播，所以接收方要自行过滤符合条件的广播，之后再解包处理
  

与广播有关的方法主要有以下3个。

- sendBroadcast：发送广播。
- registerReceiver：注册广播的接收器，可在onStart或onResume方法中注册接收器。
- unregisterReceiver：注销广播的接收器，可在onStop或onPause方法中注销接收器。



广播的作用：强制下线



### 11.1 收发标准广播

XML

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp">

    <Button
        android:id="@+id/btn_send_standard"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="发送标准广播"
        android:textColor="@color/black"
        android:textSize="17sp" />

</LinearLayout>
```

接收器

```
public class StandardReceiver extends BroadcastReceiver {

    public static final String STANDARD_ACTION = "com.jiang.broadcaststudy.standard";

    // 一旦接收到标准广播，马上触发接收器的onReceive方法
    @Override
    public void onReceive(Context context, Intent intent) {
        if(intent != null && intent.getAction().equals(STANDARD_ACTION)){
            Log.d("ning", "收到一个标准广播");
        }
    }
}
```

代码

```
public class BroadcastStandardActivity extends AppCompatActivity implements View.OnClickListener {
    private StandardReceiver standardReceiver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        Log.d("ning", "启动了");
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_broadcast_standard);
        findViewById(R.id.btn_send_standard).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        Log.d("ning", "发送一个标准广播");
        Intent intent = new Intent("com.jiang.broadcaststudy.standard");
        sendBroadcast(intent);
    }

    @Override
    protected void onStart() {
        super.onStart();
        standardReceiver = new StandardReceiver();
        // 创建一个意图过滤器，只处理STANDARD_ACTION的广播
        IntentFilter filter = new IntentFilter(StandardReceiver.STANDARD_ACTION);
        // 注册接收器，注册之后才能正常接收广播
        registerReceiver(standardReceiver, filter);
    }

    @Override
    protected void onStop() {
        super.onStop();
        // 注销接收器，注销之后就不再接收广播
        unregisterReceiver(standardReceiver);
    }
}
```

测试

![image-20230421202125087](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304212021204.png)

### 11.2 有序广播

广播没指定唯一的接收者，因此可能存在多个接收器

这些接收器默认是都能够接受到指定广播并且是之间的顺序按照注册的先后顺序，也可以通过指定优先级来指定顺序。

![image-20230421202354766](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304212023837.png)

xml

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp">

    <Button
        android:id="@+id/btn_send_order"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="发送标准广播"
        android:textColor="@color/black"
        android:textSize="17sp" />

</LinearLayout>

```

接收器2个

```
public class OrderAReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if(intent != null && intent.getAction().equals(BroadOrderActivity.ORDER_ACTION)){
            Log.d("ning", "接收器A收到一个标准广播");
        }
    }
}

public class OrderBReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if(intent != null && intent.getAction().equals(BroadOrderActivity.ORDER_ACTION)){
            Log.d("ning", "接收器B收到一个标准广播");
        }
    }
}
```

```
public class BroadOrderActivity extends AppCompatActivity implements View.OnClickListener {

    public static final String ORDER_ACTION = "com.example.broadcaststudy.order";
    private OrderAReceiver orderAReceiver;
    private OrderBReceiver orderBReceiver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_broad_order);
        findViewById(R.id.btn_send_order).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        // 创建一个指定动作的意图
        Intent intent = new Intent(ORDER_ACTION);
        // 发送有序广播
        sendOrderedBroadcast(intent, null);
    }

    @Override
    protected void onStart() {
        super.onStart();
        // 多个接收器处理有序广播的顺序规则为：
        // 1、优先级越大的接收器，越早收到有序广播；
        // 2、优先级相同的时候，越早注册的接收器越早收到有序广播
        orderAReceiver = new OrderAReceiver();
        IntentFilter filterA = new IntentFilter(ORDER_ACTION);
        filterA.setPriority(8);
        registerReceiver(orderAReceiver, filterA);

        orderBReceiver = new OrderBReceiver();
        IntentFilter filterB = new IntentFilter(ORDER_ACTION);
        filterB.setPriority(10);
        registerReceiver(orderBReceiver, filterB);
    }

    @Override
    protected void onStop() {
        super.onStop();
        unregisterReceiver(orderAReceiver);
        unregisterReceiver(orderBReceiver);
    }
}
```

测试结果：

![image-20230421203848191](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304212038257.png)



### 11.3 静态广播

广播也可以通过静态代码的方式来进行注册。广播接收器也能在AndroidManifest.xml注册，并且注册时候的节点名为receiver，一旦接收器在AndroidManifest.xml注册，就无须在代码中注册了。

创建接收者

![image-20230421205520835](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304212055938.png)

增加权限

```
<uses-permission android:name="android.permission.VIBRATE"></uses-permission>
```

接收器

```
public class ShockReceiver extends BroadcastReceiver {
    public static final String SHOCK_ACTION = "com.jiang.shock";

    @Override
    public void onReceive(Context context, Intent intent) {
        if(intent != null && intent.getAction().equals(SHOCK_ACTION)){
            Log.d("ning", "收到一个震动");
            Vibrator vb = (Vibrator)context.getSystemService(Context.VIBRATOR_SERVICE);
            vb.vibrate(500);
        }
    }
}
```

xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp">

    <Button
        android:id="@+id/btn_send_shock"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="发送震动广播"
        android:textColor="@color/black"
        android:textSize="17sp" />

</LinearLayout>
```

代码

```
public class BroadStaticActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_broad_static);
        findViewById(R.id.btn_send_shock).setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        //为了让应用继续接收静态广播，需要给静态注册广播指定包名
        String fullName = "com.example.chapter09.receiver.ShockReceiver";
        Intent intent = new Intent(ShockReceiver.SHOCK_ACTION);
        ComponentName componentName = new ComponentName(this,fullName);
        intent.setComponent(componentName);
        sendBroadcast(intent);
    }
}
```



### 11.4 监听系统广播

除了应用自身的广播，系统也会发出各式各样的广播，通过监听这些系统广播，App能够得知周围环境发生了什么变化，从而按照最新环境调整运行逻辑。

#### 11.4.1 监听分钟广播

步骤一，定义一个分钟广播的接收器，并重写接收器的onReceive方法，补充收到广播之后的处理逻辑。

步骤二，重写活动页面的onStart方法，添加广播接收器的注册代码，注意要让接收器过滤分钟到达广播Intent.ACTION_TIME_TICK。

步骤三，重写活动页面的onStop方法，添加广播接收器的注销代码。

```
public class TimeReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent !=null ){
            Log.d("ning","收到一个分钟达到广播");
        }
    }
}
```

```
public class SystemMinuteActivity extends AppCompatActivity {
    private TimeReceiver timeReceiver;// 声明一个分钟广播的接收器实例
    private String desc = "开始侦听分钟广播，请稍等。注意要保持屏幕亮着，才能正常收到广播";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_system_minute);
    }

    @Override
    protected void onStart() {
        super.onStart();
        timeReceiver = new TimeReceiver(); // 创建一个分钟变更的广播接收器
        // 创建一个意图过滤器，只处理系统分钟变化的广播
        IntentFilter filter = new IntentFilter(Intent.ACTION_TIME_TICK);
        registerReceiver(timeReceiver, filter); // 注册接收器，注册之后才能正常接收广播
    }

    @Override
    protected void onStop() {
        super.onStop();
        unregisterReceiver(timeReceiver); // 注销接收器，注销之后就不再接收广播
    }
}
```

测试结果

![image-20230421221935377](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304212219442.png)

#### 11.4.2  监听网络变更广播

![image-20230421223238650](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304212232728.png)

```
public class NetworkReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent != null) {
            NetworkInfo networkInfo = intent.getParcelableExtra("networkInfo");
            String networkClass =
                    NetworkUtil.getNetworkClass(networkInfo.getSubtype());
            String desc = String.format("收到一个网络变更广播，网络大类为%s，" +
                            "网络小类为%s，网络制式为%s，网络状态为%s",
                    networkInfo.getTypeName(),
                    networkInfo.getSubtypeName(), networkClass,
                    networkInfo.getState().toString());
            Log.d("ning",desc);

        }
    }
}
```

```
public class NetworkUtil {
    public static String getNetworkClass(int subType) {
        switch (subType) {
            case TelephonyManager.NETWORK_TYPE_GPRS:
            case TelephonyManager.NETWORK_TYPE_CDMA:
            case TelephonyManager.NETWORK_TYPE_EDGE:
            case TelephonyManager.NETWORK_TYPE_1xRTT:
            case TelephonyManager.NETWORK_TYPE_IDEN:
              return  "2G";
            case TelephonyManager.NETWORK_TYPE_EVDO_A:
            case TelephonyManager.NETWORK_TYPE_UMTS:
            case TelephonyManager.NETWORK_TYPE_EVDO_0:
            case TelephonyManager.NETWORK_TYPE_HSDPA:
            case TelephonyManager.NETWORK_TYPE_HSUPA:
            case TelephonyManager.NETWORK_TYPE_HSPA:
            case TelephonyManager.NETWORK_TYPE_EVDO_B:
            case TelephonyManager.NETWORK_TYPE_EHRPD:
            case TelephonyManager.NETWORK_TYPE_HSPAP:
                return  "3G";
            case TelephonyManager.NETWORK_TYPE_LTE:
                return  "4G";
            case TelephonyManager.NETWORK_TYPE_NR:
                return  "5G";
            default:
                return  "未知";
        }
    }
```

```
public class SystemNetworkActivity extends AppCompatActivity {

    private NetworkReceiver networkReceiver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_system_network);
    }

    @Override
    protected void onStart() {
        super.onStart();
        // 创建一个网络变更的广播接收器
        networkReceiver = new NetworkReceiver();
        // 创建一个意图过滤器，只处理网络状态变化的广播
        IntentFilter filter = new
                IntentFilter("android.net.conn.CONNECTIVITY_CHANGE");
        registerReceiver(networkReceiver, filter); // 注册接收器，注册之后才能正常接收广播
    }

    @Override
    protected void onStop() {
        super.onStop();
        unregisterReceiver(networkReceiver); // 注销接收器，注销之后就不再接收广播
    }
}
```

测试

![image-20230421225503050](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202304212255118.png)







## 12、定位服务



