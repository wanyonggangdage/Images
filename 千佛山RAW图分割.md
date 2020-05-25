# <center> 点线分割图处理
## 1. 原始图像如下：
![image](https://wanyonggangdage.github.io/Images/7.png)
## 2. 进行二值化处理以后：
![image](https://wanyonggangdage.github.io/Images/8.PNG)
&ensp;&ensp;经过观察我们可以发现，图片被点状直线分割，先从竖直方向上看，水平方向取一像素宽度，将取出的竖线，以10像素为一组进行分割，