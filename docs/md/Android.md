# Android

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



## 2、工程项目结构

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


