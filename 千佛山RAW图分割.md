# <center> 点线分割图处理
## 1. 原始图像如下：
![image](https://wanyonggangdage.github.io/Images/7.png)
## 2. 进行二值化处理以后：
![image](https://wanyonggangdage.github.io/Images/8.PNG)
&ensp;&ensp;经过观察我们可以发现，图片被点状直线分割，先从竖直方向上看，水平方向取一像素宽度，将取出的竖线，以10像素为一组进行分割，创建一个新的图像，宽度等于原图像，高度为原来的 1/10 ，当分组内包含非0的像素点，将对应像素点设为 255，否则设为0. 
经过处理后的图像如下：
![image](https://wanyonggangdage.github.io/Images/9.PNG)
&ensp;&ensp;我们可以看到在竖直方向上虚线被我们转换成了实线。在竖直方向上求和，取最大值，记录下最大值的点的水平坐标。
同理在水平方向上我们应用相同算法：  
![image](https://wanyonggangdage.github.io/Images/10.PNG)  
&ensp;&ensp;如上图我们可以看出水平方向上虚线被转化成了实线。在水平方向上求和，取最大值，记录下最大值的竖直方向上的点的坐标。

根据如上步骤求得的坐标位置，在原图像上画出边界分割线：
![image](https://wanyonggangdage.github.io/Images/11.PNG)  