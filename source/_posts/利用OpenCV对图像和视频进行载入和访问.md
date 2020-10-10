---
title: 利用OpenCV对图像和视频进行载入和访问
abbrlink: 3ad132bb
date: 2020-02-28 12:12:26
categories: 
     - [学习笔记, C++]
     - [学习笔记, OpenCV]
tags: OpenCV, 图像处理
keywords: OpenCV, 图像处理
description:
---

很久都没更新博客了（跪）....**前期因为疫情和考研压力，所以就一直~~咕咕咕~~**

在家上网课，学习OpenCV，做了一点笔记吧。  
抽空写博客，仍然更新很慢（~~大声哭泣~~）

<!-- more -->

## 运行平台

+ OpenCV 4.2
+ VS 2019

## 图像在内存中的存储和操作

### 从图像数据性质推理图像在内存中的存储方式：

>图像数据是多通道：1/3/4通道；  
>像素数据类型：1位，uchar，short，int，float，double等；

### OpenCV中的C++ Mat类：

```c++
class CV_EXPORTS Mat
{
public:
    // …大量方法…
    ...
 
    /*! includes several bit-fields:
         - the magic signature
         - continuity flag
         - depth
         - number of channels
     */
    int flags;

    //! the array dimensionality, >= 2
    int dims;

    //! the number of rows and columns or (-1, -1) when the array has more than 2 dimensions
    int rows, cols;

    //! pointer to the data
    uchar* data;

    // other members
    ...
};
```
>图像在内存中的基本要素：

①列数cols = 宽度，行数rows = 高度，通道数（彩色空间类型），位深度（数据类型：8位无符号8U，32位浮点32F等）；  
②像素存储区（uchar* data;）

>创建Mat矩阵--通过构造函数：

```c++
Mat img(Size(320,240), CV_8UC3);`
```
宽度(320)，高度(240)，数据类型8U(8位无符号)，3通道

>创建Mat矩阵--通过create函数：

```c++
Mat img;

img .create(Size(320,240), CV_32FC3);//32位浮点型
```

>创建载入图像文件：
```c++
Mat img = imread("lena.jpg", CV_LOAD_IMAGE_COLOR);
```

### 像素数据在内存中的组织方式：

**PS:内存是顺序排列的字节阵列**

<fancybox>![img](https://img.vim-cn.com/3e/ac56d209f474e08b9df75a04231a4547d3e240.jpg)</fancybox>

## 通过Mat方法访问图像像素

1. 3通道24位深度（表达每个通道占用8位）彩色图像像素的访问：

```c++
cv::Vec3b pixlsValue; //==表达这是一个3字节像素数据节点;

pixlsValue = img.at&lt;cv::Vec3b&gt;(y, x);
//y表达行号；x表达列号；尖括号<...>中的内容是C++模板，指定图像矩阵的数据类型；
```

2. 像素值的修改方法：

```c++
pixlsValue.val[0] = xxx;
pixlsValue.val[1] = xxx;
pixlsValue.val[2] = xxx;
```

3. 修改后的像素值返回到存储器：

```c++
img.at<cv::Vec3b>(y, x) = pixlsValue；其中，y和x为像素坐标;
```
## 通过C++指针方法访问图像像素

1. 图像行列数`Rows`和`Cols`，数据首地址 `uchar* data`，通道数`chaN`，每行总字节数byteR可通过矩阵类接口获得；

2. 每个像素的字节数`byteN= 位深度/8`；位深度24位彩色图像每个像素占用3字节；float类型单通道图像每个像素占用4字节；...

3. 每行总字节数`byteR = 每像素字节数 * 列数 = byteN * Cols`;

4. 第`row`行第`col`列像素向量的地址偏移量（相对像素数据首字节）：

```c++
pixlAddr = row * byteR + col * byteN ；
```

5. 像素向量首字节地址为 `(uchar *)&data [pixlAddr]`;

6. 如果是`uchar`型图像，则像素首地址为：

```c++
uchar  *pixlPTR = (uchar*) data [pixlAddr];
```

7. 如果是`float`型图像，则像素首地址为：

```c++
float *pixlPTR = (float*) data [pixlAddr];
```

8. 对于3通道8位（位深度24位）彩色图像(RGB)，则3个彩色通道像素存储位置为：`pixlPTR[0] = B` 、`pixlPTR[1] = G `、`pixlPTR[2] = R`；

9. 通过实验可以证明，使用C++指针访问像素，时间开销远低于调用`cv::Mat.at()`方法；

## 图像像素访问例程

{% codeblock lang:cpp 完整代码 %}
#include "stdafx.h"
#include &lt;Windows.h&gt;
#include &lt;opencv2/core.hpp&gt;
#include &lt;opencv2/imgproc.hpp&gt;
#include &lt;opencv2/highgui.hpp&gt;
#include &lt;opencv2/features2d.hpp&gt;
 
#ifdef _DEBUG
#pragma comment(lib, "opencv_world420d.lib")
#else
#pragma comment(lib, "opencv_world420.lib")
#endif

int main(int argc, char* argv[])
{
    char curPathName[384] = "";

    char FilePath[384] = "lena.jpg";

    if (strlen(curPathName) > 0)
         sprintf(FilePath, "%s\\lena.jpg", curPathName); //图片文件路径

    cv::Mat readImg = cv::imread(FilePath, cv::IMREAD_COLOR); //读取彩色图片: opencv420的IMREAD_COLOR与之前版本不同

    if (readImg.empty())
    {
         printf("error#41: 未能读取到图片，检查文件是否存在！回车退出！\n图片路径=%s\n", FilePath);
         fgets(FilePath, 127, stdin);

         return -1;

    }

    cv::imshow("readImg", readImg);//显示图片，窗口标题为readImg
    cv::Mat imgFloat;
    readImg.convertTo(imgFloat, CV_32F);

    //在C++中，访问矩阵中的像素有两种方法：通过矩阵方法和通过指针；通过指针是最快的访问方法
    cv::Vec3b colorValue; uchar greyValue;
    int channelNUMS = readImg.channels();

    uchar *pDATA = readImg.data;         //为了通过指针访问存储区，我们获取存储区的首指针
    int dataSTEP = readImg.step1();  //注意，当图像列数cols=512，3通道时，dataSTEP值为512 * 3 = 1536；
    int dataSTEP2 = imgFloat.step1();    //注意，当图像列数cols=512，3通道时，dataSTEP值为512 * 3 = 1536；

    int64 timeSTART = cv::getTickCount();

    for (int i = 0; i< imgFloat.rows / 2; i++) {
         for (int j = 0; j< readImg.cols; j++) {
             //我们使用条件编译开关来控制使用那一段代码
             #ifndef ACCESS_IAMGE_BY_PTR      //使用Mat的At()方法访问像素：花费时间几乎是指针方法的10倍；

             switch (channelNUMS)
             {
             case 1:
                  readImg.at&lt;uchar&gt;(i, j) = 255 - readImg.at&lt;uchar&gt;(i, j);

                  break;
             case 3:
                  colorValue = readImg.at&lt;cv::Vec3b&gt;(i, j);
                  colorValue.val[0] = 255 - colorValue.val[0];
                  colorValue.val[1] = 255 - colorValue.val[1];
                  colorValue.val[2] = 255 - colorValue.val[2];
                  readImg.at&lt;cv::Vec3b&gt;(i, j) = colorValue;

                  break;
             }

             #else              //使用指针方法访问像素：花费时间几乎是Mat的At()方法的1/10倍；
             if (pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 0] > 200 &&
                  pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 1] > 200 &&
                  pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 2] > 200)
             {
                  pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 0] = 64;
                  pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 1] = 64;
                  pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 2] = 0;
             }
             else
             {
                  pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 0] = 255 - pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 0];
                  pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 1] = 255 - pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 1];
                  pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 2] = 255 - pDATA[i * dataSTEP * sizeof(uchar) + j * channelNUMS + 2];
             }
         #endif ACCESS_IAMGE_BY_PTR
         }
    }

    int64 timeEND = cv::getTickCount(); //处理结束时间

    double usingTime = (double)(timeEND - timeSTART) / (double)cv::getTickFrequency();//统计矩阵操作花费的时间，单位ms；
    printf("INFO#128: 图像处理耗时：%.6f 秒\n", usingTime);

    cv::imshow("Nagtive Film", readImg);
    cv::imwrite("Nagtive_Film.bmp", readImg);       //保存处理后的图像为bmp文件
    cv::waitKey();//等待按下键盘，这是程序退出前让显示窗得以被显示的方法！

    return 0;
}
{% endcodeblock %}

>VS 2019实际运行效果如下：

<fancybox>![WOQ9@B(2}AH0A7L@J0GB9S1.png](https://i.loli.net/2020/03/01/gcSkP8OZIwGhQRU.png)</fancybox>


## 视频图像的特点

1. 视频源：摄像头和视频文件；
2. 视频图像格式：AVI、VCD、SVCD、DVD、MPG、WMV、RM、RMVB、MOV、MP4、MPEG4、3GP、H264等等。
3. 视频图像在硬盘中存储要求更高压缩比，压缩、解压缩处理的计算开销较大；
4. 无论什么格式视频图像，载入到内存后将被转换为像素矩阵，并存储到内存中。此时，像素访问方法将与静止图像没有差异。


### 通过C++调用OpenCV库载入图像

>这里使用C++和OpenCV库将视频载入内存，并将视频图像记录到另一个视频文件中

可以发现：  
+ 视频是一个图像序列；
+ 图像序列中的每幅图像仍以像素矩阵存储在内存中，访问方法与静止图像相同。
+ 使用OpenCV记录的视频文件，仅仅存储了经过编码后的视频流数据，还不符合视频文件格式标准，不能使用常规的播放器来播放，需要使用一些特殊的视频播放器（例如VLC）来播放。

```c++
//#include "stdafx.h"
#include &lt;stdio.h&gt;
#include &lt;iostream&gt;   // for standard I/O
#include &lt;Windows.h&gt;
#include &lt;opencv2/core.hpp&gt;
#include &lt;opencv2/imgproc.hpp&gt;
#include &lt;opencv2/highgui.hpp&gt;
#include &lt;opencv2/features2d.hpp&gt;
#include &lt;opencv2/video.hpp&gt;

#ifdef _DEBUG0
#pragma comment(lib, "opencv_world420d.lib")
#else
#pragma comment(lib, "opencv_world420.lib")
#endif


int main(int argc, char **argv)
{

    char curPathName[384] = "";

    //==【01】== 打开视频文件或摄像头
    char FilePath[384] = "HighWay_Video_001.Avi";

    if (strlen(curPathName) > 0)
         sprintf(FilePath, "%s\\HighWay_Video_001.Avi", curPathName);   //图片文件路径
                                                             //==【01】== 视频输入选择和打开：摄像头或视频文件
         cv::String  path = FilePath;

         if(argc > 1)
             path = argv[1];        //可通过命令行第二个参数指定视频文件名
         //path = "";              //====>>>>如果启用此行，将从摄像头中读取视频图像<<<<====
         cv::VideoCapture cap; //VideoCapture类实例化，使用缺省摄像头

         if(path.length() == 0)
             cap.open(0);       //打开第一个摄像头
         else
             cap.open(path);  //打开视频文件

         if(!cap.isOpened()) // check if we succeeded
             return -1;

    cv::Mat frame, greyFrame;

    //==【02】== 视频记录类实例化
         cv::VideoWriter outputVideo;                                        // Open the output
         int saveAVIenable = 0;            //非0时记录视频

         if(saveAVIenable){
             bool rtn = outputVideo.open("Result003.avi", cv::VideoWriter::fourcc('M', 'J', 'P', 'G'), 10,
                 cv::Size((int)cap.get(cv::CAP_PROP_FRAME_WIDTH),  (int)cap.get(cv::CAP_PROP_FRAME_HEIGHT) ) , true);
             if(!rtn)
                  return -2;
             }
 
    //==【03】== 创建一个运动视频背景提取对象：分离背景和运动对象： 暂时未使用！
         cv::Ptr<cv::BackgroundSubtractorMOG2> bgsubtractor= cv::createBackgroundSubtractorMOG2();

    //==【04】== 命名两个显示窗口
         cv::namedWindow("SRC", 0);
         cv::namedWindow("GREY", 0);

    //==【05】== 循环读取视频
         int frameNums  = 0;

    for(;;)
         {
         double t1 = (double)cv::getCPUTickCount();

         cap.read(frame);     //读取一个视频帧
         if(frame.empty())
             break;

         if(saveAVIenable)
             outputVideo.write(frame);   //写视频文件

         //==>> 取视频帧frame的一个矩形区，转换为灰度图像，存储到greyFrame
         cv::cvtColor(frame(cv::Rect(0, 150, frame.cols, frame.rows - 150)), greyFrame, cv::COLOR_RGB2GRAY);
         //==>> 将图像greyFrame的长宽分别缩小到1/2
         cv::resize(greyFrame, greyFrame, cv::Size(greyFrame.cols/2, greyFrame.rows/2));
         //==>> 使用7*7卷积窗，平滑灰度图像greyFrame
         cv::blur(greyFrame, greyFrame, cv::Size(7,7));
         // ==>> 计算处理时间
         double t2 = (double)cv::getCPUTickCount();
         printf("Line74 Video Play Delta Time = %.3fms\n", 1e0 * (t2 - t1)/(double)cv::getTickFrequency());

         // ==>> 显示处理结果
         imshow("SRC", frame);
         if(!greyFrame.empty())
             imshow("GREY", greyFrame);

         // ==>> 按下键盘q退出
         int keycode = cv::waitKey(30);
         if( keycode == 'q')
             break;

         frameNums++;
         }//for

    if(saveAVIenable)
         outputVideo.release();
    cap.release();

return 0;
}
```

>VS 2019实际运行效果如下：

<fancybox>![4)LH$EDIZVJNC((AMUD1TZC.png](https://i.loli.net/2020/03/06/3I7kzTpAmDMdueb.png)</fancybox>
