---
title: 利用OpenCV实现对车流量的统计
categories:
    - [学习笔记, C++]
    - [学习笔记, OpenCV]
tags: 'OpenCV, 图像处理'
keywords: 'OpenCV, 图像处理'
abbrlink: 48ab4a6e
date: 2020-07-14 00:07:07
---

博主又有几个月没消息了。去哪里了呢？是的，考研闲暇之余，短时间摸鱼后，他，又灰溜溜地回来了。

博主最近在干啥？详见[一枚考研狗的执着](https://blog.cwxyr.me/archives/195/)

![](https://cdn2.sublimerui.top/2020/07/14/7b9c439ccbd61.jpg)

<!-- more -->

闲话少絮。开始正题——OpenCV的车流量统计。

## 调试平台

+ OpenCV 4.2
+ VS 2019

## 汽车识别原理——背景/前景分割算法

如今，检测和提取车辆时候，常用的方法有MOG2算法和KNN算法。MOG算法是以高斯混合模型（GMM）为基础的背景/前景分割算法。它是以2004年和2006年Z.Zivkovic的两篇文章为基础的。这个算法的一个特点是它为每一个像素选择一个合适数目的高斯分布。其主要原理为：在一个固定位置和角度固定的视频或图像中，提取分割图像或视频中运动的成分。此算法使用背景建模的方式，将整张图片或一帧视频分为前景和后景。此算法运行时，会将动态的前景与静止的后景相减，得出结果即为徐提取的运动物体的图像。

K最近邻算法（KNN）是属于机器学习的一种算法。其主要原理为：给定一个已训练数据集，对新的输入实例，在训练数据集中找到与该实例最邻近（注：衡量邻近的标准以具体选取的某个特征而言，例如下面示意图中使用的特征为欧式距离）的K个实例，这K个实例的多数属于某个类，则判定该输入实例同属此类。如下图所示：训练者取k值，计算以欧氏距离k为半径的圆内其他类别的个数，图中中心小红点以k为半径的圆内三角形个数最多，则判定中心小红点为三角形。

![KNN](https://cdn2.sublimerui.top/2020/07/14/cec057608abab.png)

## 整体结构

### 整体流程框架图

本程序的主要运行流程为：程序运行开始，首先从文件中获取上次保存的光流量检测矩形框数据（顶点坐标和矩形的长宽）；其后，分别初始化背景提取对象，使用MOG2和KNN两种算法。与此同时，建立一个鼠标回调函数，用于捕获鼠标左键（绘制矩形框）、中键（取消操作）和右键（保存矩形框数据到文件）的操作。

此后，程序进入主循环状态。程序循环从视频中获取一帧的图像，先进行压缩处理，以提高后续运算速度。之后，将这一帧图片从RGB转为灰度图片。为了祛除灰度后可能出现的小毛刺杂点，再进行平滑滤波处理。此后，分别通过MOG2和KNN算法提取前景，并将提取后的视频显示出来。与此同时，获取每个矩形框中积分后的亮度和。

最后，两个算法中，分别将实时得到的亮度和与所预设的阈值进行比较，当满足条件后，便认为一辆汽车通过矩形框，使得计数器加一。如此重复，统计整个视频中的车流量。

![流程图](https://cdn2.sublimerui.top/2020/07/14/adbf3842b3b5f.png)

### 主要参数

1. `int detectTHD`

> 此参数为预设的亮度阈值。确定是否有汽车经过检测框中，其需要联合上一帧积分亮度和本次积分亮度后综合做出决定。

2. `cv::Size newSize(frame.cols / 2, frame.rows / 2);`

> 此参数可以储存一帧视频缩小后的大小。

3. `cv::Mat showMat;`

> 此参数用于储存最终显示的图像矩阵。

### 主要函数

1. `static void onMouse(int event, int x, int y, int flags, void\*)`

> 此函数主要用于检测鼠标左键、滑轮（中键）和右键的一些操作，用于绘制矩形检测框。

2. `cv::resize(frame, newframe, newSize);`

> 此函数主要用于缩小原视频比例，提高计算机运算速度。

3. `bgMOG2-\>apply(greyFrame, mog2RES, update_bg_model ? -1 : 0);`以及`bgKNN-\>apply(greyFrame, KNN, update_bg_model ? -1 : 0);`

> apply函数主要用于两种算法的前景提取。此后，前景提取后的这帧视频保存于bgMOG2和bgKNN之中。

4. `cv::rectangle(showMat, myLanneRect.at(k), cv::Scalar(255, 255, 255), 3);`

> 此函数主要用于前景提取后在其上面绘制矩形框。

5. `cv::integral(subMat, sumMat, CV_32S);`

> 计算车道矩形框亮度积分图。

6. `cv::putText();`

> 此函数可以按照要求，在视频图像上显示文字和统计数字。

## 测试结果

1. **功能测试**

进行车流量统计前，应当首先根据矩形框中出现车辆时，估算其亮度平均值作为亮度阈值，使其设定为一个较为合适的值，增强检测的灵敏性。由下图可知，当程序运行后，通过手动标记车道检测区域，得到2个矩形检测区。每次获得矩形中的积分亮度结果，当上从亮度结果大于阈值，且此次亮度结果小于阈值时，使车辆统计结果加一。

![功能测试](https://cdn2.sublimerui.top/2020/07/14/6bb48c736d423.png)


2. **算法处理时间**

由测试结果可知，相较于MOG2算法，KNN算法在运算处理上花费更多的时间，KNN算法时间约为MOG2时间的2倍。同时，算法处理总时间也相对较长，除了两个算法所带来的开销外，仍有其他附加代码所花费的时间。

![时间处理](https://cdn2.sublimerui.top/2020/07/14/eddddc18068b8.png)

3. **缩放图片前后比较**

程序中，可以使用`cv::resize();`函数进行图片的压缩，缩放图片对于检测速度有较大的影响。不使用缩放时，将视频中原始一帧的图像进行计算，经测试发现，处理速度很慢，显示的图片有明显的脱帧和卡顿现象，CPU占用率相当高，与压缩后（上图）相比，无论使用MOG2还是KNN算法，其处理时间均成倍增加。下图为不使用缩小图片尺寸条件下的处理时间。

![时间对比](https://cdn2.sublimerui.top/2020/07/14/9ea91e56e17a6.png)

4. **矩形检测区亮度阈值的影响**

阈值设置的合理性也是车流量检测准确性的一个重要指标。过高或过低的阈值均不能很好地反映车辆的经过和实现的统计。阈值过低，将会把视频中环境干扰噪声和其他运动对象（如三轮车和行人）当做汽车统计，使得统计结果偏大；同理，阈值过高，将很难检测到车辆的通过，当车辆进过矩形检测区时，无法实现车辆的统计。本程序中，经过检测矩形框内平均值的大致估算，将阈值`detectTHD`设置为900000。

下图中显示了当阈值设定过小时的状况。右端矩形框（右车道）经过了一辆电动车，程序误认为汽车，并错误地将统计结果L1的值从1加为2。

![阈值过小](https://cdn2.sublimerui.top/2020/07/14/42ebf686fb258.png)

同理，当阈值设置过大，也会造成统计的不准确。下图显示了当阈值设定过大时的状况。可以发现，即使是车辆经过了矩形框，车辆统计变量L0和L1仍为0。

![阈值过大](https://cdn2.sublimerui.top/2020/07/14/138c1e1078634.png)

## 结论

由以上分析可知，汽车流量的统计可以借助设定矩形区域内的亮度阈值来确定。为识别运动对象（汽车）的状态，可使用背景提取算法，如本软件中使用到的MOG2和KNN算法。通过比较不同算法间的处理时间，我们应当合理选择一种耗时短且提取车辆准确性高的一种算法。此外，识别统计车流量较为重要的一环便是设置合理的亮度阈值，亮度阈值设置的合理性直接关系到车流量统计准确性。

## 附：程序源代码

**此项目Github地址：** <https://github.com/cwxyr/traffic-detection>

```c++
#include "stdafx.h"
#include <Windows.h>
#include <string>
#include <opencv2/core.hpp>
#include <opencv2/imgproc.hpp>
#include <opencv2/highgui.hpp>
#include <opencv2/features2d.hpp>
#include <opencv2/video.hpp>

#ifdef _DEBUG
#pragma comment(lib, "opencv_world420d.lib") 
#else
#pragma comment(lib, "opencv_world420.lib") 
#endif

//===【鼠标事件回调函数】===
int detectTHD = 900000;		//亮度阈值：有车辆经过的；
std::vector<int>		myLanneLightSum_Last;			//车道亮度和：上一帧的
std::vector<int>		myLanneVihicleCnt;				//车道车辆计数器

std::vector<cv::Rect>		myLanneRect;			//车道矩形框；显示为红色；
std::vector<cv::Point>		myMousePoints;		//鼠标点向量；显示为蓝色；
int	myMouseEventBusy = 0;							//鼠标回调事件忙:简单的资源锁
static void onMouse(int event, int x, int y, int flags, void*)
{
	myMouseEventBusy = 1;
	cv::Point  mPoint;
	cv::Rect mRect;
	switch (event)
	{
	case cv::EVENT_LBUTTONDOWN:		//左键按下:增加myMousePoints中的点数
		mPoint = cv::Point(x, y);
		myMousePoints.push_back(mPoint);	//将当前鼠标点推送到向量中；
		if (myMousePoints.size() > 4)
			myMousePoints.erase(myMousePoints.begin());	//保证myMousePoints向量中节点数不大于4；
		break;
	case cv::EVENT_RBUTTONDOWN:		//右键键按下：将myMousePoints中的4个点推送到矩形框向量myLanneRect
		if (myMousePoints.size() == 4)
		{
			int Xmin = 100000; int Ymin = 100000;
			int Xmax = 0; int Ymax = 0;
			for (int k = 0; k < 4; k++)
			{
				Xmin = std::min(Xmin, myMousePoints.at(k).x);
				Ymin = std::min(Ymin, myMousePoints.at(k).y);
				Xmax = std::max(Xmax, myMousePoints.at(k).x);
				Ymax = std::max(Ymax, myMousePoints.at(k).y);
			}//for k   <<< === 用四个点构成矩形框的参数
			mRect = cv::Rect(Xmin, Ymin, Xmax - Xmin, Ymax - Ymin);		//构成矩形框
			myLanneRect.push_back(mRect);
			myLanneLightSum_Last.push_back(0);
			myLanneVihicleCnt.push_back(0);
			myMousePoints.clear();  //清除鼠标点向量

		}///if
		break;
	case cv::EVENT_MBUTTONDOWN:		//中间键键按下：删除myMousePoints中的一个点；myMousePoints为空时，删除myLanneRect中的节点；
		printf("EVENT_MBUTTONDOWN\n");
		if (myMousePoints.size() > 0)
			myMousePoints.pop_back();
		else
		{
			myLanneRect.pop_back();
			myLanneLightSum_Last.pop_back();
			myLanneVihicleCnt.pop_back();
		}
		break;

	}/////switch
myMouseEventBusy = 0;
return;
}

int main(int argc, char* argv[])
{
	char errorMSG[256];
	char curPathName[384] = ""; char curModulerPath[384] = "";
	GetCurrentDirectory(383, curModulerPath); 
	printf("Line39: curModulerPath = %s\n", curModulerPath);

	//=======读取标记的矩形框文件内容到myLanneRect：=======
	#ifndef READ_RECT_FILE
		FILE *pFILE = fopen("MarkRect.txt", "r");
		if (pFILE != NULL)
		{
			cv::Rect  mRect;
			while (fgets(errorMSG, 255, pFILE) != NULL)
			{
				int rtn = sscanf(errorMSG, "%d %d %d %d", &mRect.x, &mRect.y, &mRect.width, &mRect.height);
				if (rtn == 4) {
					myLanneRect.push_back(mRect);
					myLanneLightSum_Last.push_back(0);
					myLanneVihicleCnt.push_back(0);
					}
			}
			fclose(pFILE);
		}///if
	#endif // !READ_RECT_FILE

	std::string  imgName = "video-02.mp4";
	char FilePath[384];
	if (strlen(curPathName) > 0)
		sprintf(FilePath, "%s\\%s", curPathName, imgName.c_str());	//图片文件路径
	else
		sprintf(FilePath, "%s", imgName.c_str());	//图片文件路径

	//==【01】== 打开视频文件或摄像头
		cv::VideoCapture cap; //VideoCapture类实例化，使用缺省摄像头

		if (0 && "UsingCam")
			cap.open(0);
		else
			cap.open(FilePath);
		if (!cap.isOpened()) // check if we succeeded
		{
			printf("error#73: 打开设备或文件失败，检查是否存在！回车退出！\n路径=%s\n", FilePath);
			fgets(FilePath, 127, stdin);
			return -1;
		}
		cv::Mat frame, newframe, greyFrame, floatFrame, lastFrame, frame2, mog2RES, KNN, out_frame, avgFrame;
		std::vector<cv::Mat> diffIMGvec;

	//==【02】== 创建运动视频背景提取对象：用于分离背景和运动对象
		cv::Ptr<cv::BackgroundSubtractorMOG2> bgMOG2 = cv::createBackgroundSubtractorMOG2();
		cv::Ptr<cv::BackgroundSubtractorKNN> bgKNN = cv::createBackgroundSubtractorKNN();
		bgMOG2->setVarThreshold(30);
		bool update_bg_model = true;
	//==【03】== 命名几个显示窗口
		cv::namedWindow("RawWnd", cv::WINDOW_NORMAL);
		cv::setMouseCallback("RawWnd", onMouse, &newframe);		//设置鼠标事件回调函数（"RawWnd"窗口的）：同时传递彩色图像指针；
		cv::namedWindow("Out_KNN", cv::WINDOW_NORMAL);
		cv::namedWindow("Out_MOG2", cv::WINDOW_NORMAL);

	int frameNums = 0;
	for (;;)
	{
		frame.rows = 0;
		double t1 = (double)cv::getCPUTickCount();  //开始统计时间
		cap.read(frame);
		if (frame.rows == 0)
			break;
		cv::Size newSize(frame.cols / 2, frame.rows / 2);  //压缩图像，将其尺寸缩小
		cv::resize(frame, newframe, newSize);
		cv::cvtColor(newframe, greyFrame, cv::COLOR_RGB2GRAY);  //转换为灰度图
		cv::blur(greyFrame, greyFrame, cv::Size(3, 3));		//使用平滑运算
	
		double t2 = (double)cv::getCPUTickCount();
		bgMOG2->apply(greyFrame, mog2RES, update_bg_model ? -1 : 0);   //使用MOG2算法提取前景
		double t3 = (double)cv::getCPUTickCount();  //获取处理时间

		double t4 = (double)cv::getCPUTickCount();
		bgKNN->apply(greyFrame, KNN, update_bg_model ? -1 : 0);			//使用KNN算法提取前景
		double t5 = (double)cv::getCPUTickCount();  //获取处理时间
		printf("MOG2 Time = %.3fms\n", 1e0 * (t3 - t2) / (double)cv::getTickFrequency());
		printf("KNN Time = %.3fms\n", 1e0 * (t5 - t4) / (double)cv::getTickFrequency());
		printf("Total Time = %.3fms\n", 1e0 * (t5 - t1) / (double)cv::getTickFrequency());
		printf("--------------------\n");

		if (!mog2RES.empty())  //计算MOG2算法下矩形框的积分亮度值
		{
			cv::Mat showMat;
			mog2RES.copyTo(showMat);
			if (myMouseEventBusy == 0)
			{
				for (int k = 0; k < myLanneRect.size(); k++)
				{
					cv::rectangle(showMat, myLanneRect.at(k), cv::Scalar(255, 255, 255), 3);
					cv::Mat subMat = mog2RES(myLanneRect.at(k));	//再MOG2的前景提取结果中，取车道标记矩形框区域为subMat矩阵
					cv::Mat sumMat;		//积分图 == subMat的积分矩阵
					cv::integral(subMat, sumMat, CV_32S);		//设置积分矩阵的数据类型为uint；
					int sumValue = (int)sumMat.at<int>((int)sumMat.rows - 1, (int)sumMat.cols - 1);  //获取积分图右下角的值，就是矩形框内亮度和；
					sprintf(errorMSG, "sum = %d;", sumValue);
					cv::putText(showMat, errorMSG, cv::Point(myLanneRect.at(k).x, myLanneRect.at(k).y + 4), 0.2, 1, cv::Scalar(255, 0, 0), 2);//显示矩形框内的亮度和；

				}//for k
			}////if
			cv::imshow("Out_MOG2", showMat);
		}

		if (!KNN.empty())   //计算KNN算法下矩形框的积分亮度值
		{
			cv::Mat showMat;
			KNN.copyTo(showMat);
			if (myMouseEventBusy == 0)
			{
				for (int k = 0; k < myLanneRect.size(); k++)
				{

					cv::rectangle(showMat, myLanneRect.at(k), cv::Scalar(255, 255, 255), 3);
					cv::Mat subMat = KNN(myLanneRect.at(k));	//再KNN的前景提取结果中，取车道标记矩形框区域为subMat矩阵
					cv::Mat sumMat;		//积分图 == subMat的积分矩阵
					cv::integral(subMat, sumMat, CV_32S);		//设置积分矩阵的数据类型为uint；
					int sumValue = (int)sumMat.at<int>((int)sumMat.rows - 1, (int)sumMat.cols - 1);  //获取积分图右下角的值，就是矩形框内亮度和；
					sprintf(errorMSG, "sum = %d;", sumValue);
					cv::putText(showMat, errorMSG, cv::Point(myLanneRect.at(k).x, myLanneRect.at(k).y + 4), 0.2, 1, cv::Scalar(255, 0, 0), 2);//显示矩形框内的亮度和；
				}//for k
			}////if
			imshow("Out_KNN", showMat);
		}			

		//===>>> 显示原始图像：显示车道标记信息 + 矩形框内亮度和 + 车流量统计
		#ifndef SHOW_RAW_MAT
			cv::Mat showMat;
			newframe.copyTo(showMat); //矩阵复制
			sprintf(errorMSG, "mL=add Point; mR=add Rect; mM=delete Point;");
			cv::putText(showMat, errorMSG, cv::Point(8, 32), 0.2, 1, cv::Scalar(255, 0, 0), 2);//显示提示信息；
			//==>> 显示车道矩形框为红色 + 车流量统计 + 车流量显示
			if (myMouseEventBusy == 0)
			{
				for (int k = 0; k < myLanneRect.size(); k++)
				{
					cv::rectangle(showMat, myLanneRect.at(k), cv::Scalar(0, 0, 255), 3);
					cv::Mat subMat = mog2RES(myLanneRect.at(k));	//再MOG2的前景提取结果中，取车道标记矩形框区域为subMat矩阵
					cv::Mat sumMat;		//积分图 == subMat的积分矩阵
					cv::integral(subMat, sumMat, CV_32S);		//设置积分矩阵的数据类型为int，计算车道矩形框内亮度积分图；
					int sumValue = (int)sumMat.at<int>((int)sumMat.rows - 1, (int)sumMat.cols - 1);  //获取积分图右下角的值，就是矩形框内亮度和；
					sprintf(errorMSG, "sum = %d;", sumValue);
					cv::putText(showMat, errorMSG, cv::Point(myLanneRect.at(k).x, myLanneRect.at(k).y + 4), 0.2, 1, cv::Scalar(255, 255, 0), 2);//显示矩形框内的亮度和；
					//===>>> 车流量统计：
					if (myLanneLightSum_Last.at(k) > detectTHD && sumValue <= detectTHD)
					{
						//:: 车辆通过了矩形框：上一帧亮度和大于阈值，本帧亮度和小于阈值；车辆计数器自加；
						myLanneVihicleCnt.at(k)++;
						myLanneLightSum_Last.at(k) = sumValue;
					}
					else 
						myLanneLightSum_Last.at(k) = sumValue;  //存储当前亮度和到myLanneLightSum_Last				
				}//for k

				//===>> 车流量统计结果显示
				cv::Mat topareaMat = showMat(cv::Rect(0, 0, showMat.cols, 75));		//最顶部48行置0；
				topareaMat *= 255;

				std::string strVihicleCnt = "VihicleCnt: ";
				for (int k = 0; k < myLanneRect.size(); k++)
				{
					sprintf(errorMSG, "L%d = %d;", k, myLanneVihicleCnt.at(k));
					strVihicleCnt += errorMSG;
				}
				cv::putText(showMat, strVihicleCnt.c_str(), cv::Point(8, 64), 0.2, 1, cv::Scalar(0, 0, 255), 2); //流量统计显示到彩色图片上

			}////if
			 //==>> 显示正在标记的坐标点为蓝色：
			if (myMouseEventBusy == 0)
			{
				for (int k = 1; k < myMousePoints.size(); k++)
				{
					cv::line(showMat, myMousePoints.at(k - 1), myMousePoints.at(k), cv::Scalar(255, 0, 0), 15);
				}//for k
				if(myMousePoints.size() == 4)
					cv::line(showMat, myMousePoints.at(0), myMousePoints.at(3), cv::Scalar(255, 0, 0), 2);
			}////if

			imshow("RawWnd", showMat);
		#endif // SHOW_RAW_MAT
		int keycode = cv::waitKey(100);		//等待100ms
		if (keycode == 'q')
			break;
		else if (keycode == ' ')
		{
			update_bg_model = !update_bg_model;
			printf("Learn background is in state = %d\n", update_bg_model);
		}
		else if (keycode == 'w')
		{
			//写文件：记录标记的矩形框到文件中：
			#ifndef WRITE_RECT_FILE
			FILE *pFILE = fopen("MarkRect.txt", "w");
			if (pFILE != NULL)
			{
				for (int k = 0; k < myLanneRect.size(); k++) {
					fprintf(pFILE, "%d %d %d %d\n", myLanneRect.at(k).x, myLanneRect.at(k).y, myLanneRect.at(k).width, myLanneRect.at(k).height);
				}
				fclose(pFILE);
			}///if
			#endif // !WRITE_RECT_FILE

		}
		frameNums++;
		Sleep(50);
	}//for 
	cap.release();
	return 0;
}
```
