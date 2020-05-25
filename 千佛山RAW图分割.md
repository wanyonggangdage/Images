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


## 3. 进行图像切割：

&emsp;我们采用如下方式对图像进行分割，以水平方向为例：我们得到了一系列的分割线水平坐标，我们记录下以最后一个分割线坐标为起点，到下一个分割线坐标为终点的数组。同理竖直方向上我们也可以得到一个数组序列。然后根据得到的两个数组序列进行图像分割。
分割后的结果如下图所示：
![image](https://wanyonggangdage.github.io/Images/12.PNG)
## 4. 代码及其说明：
```Python
import cv2
import numpy as np
import os
import glob
from os import path

class ImageProcess:
    
    def SplitImage(self,image,destFolder,width,height,itemPixel,magnifyCount):
        # 二值化图像
        _,im = cv2.threshold(image,254,255,cv2.THRESH_BINARY)
        # 初始化一张像素点值全为 0 的图像
        a = np.zeros(shape = (height//itemPixel,width))
        # 竖直方向上分组处理
        for i in range(height//itemPixel):
            region = np.sum(im[i*itemPixel:(i+1)*itemPixel,:] ,axis =0 ) 
            for m in range(width):
                if region[m] > 0 :
                    a[i][m] = 255 
        # 竖直方向上求和取最大值
        mm = np.sum(a,axis = 0)
        maxValue = np.amax(mm)
        m = np.zeros(shape =(width))
        for i in range(width):
            if mm[i] == maxValue:
                m[i] = 1
                cv2.line(im,(i,0),(i,height-1),255)
        # 竖直方向区域分组       
        verticalRegions = []
        start = 0
        for i in range(width -1):
            if m[i] != m[i+1]:
                verticalRegions.append([start,i])
                start = i+1
        # 移除过小不准确的分组        
        removeItems = []        
        for item in verticalRegions:
            if item[1] - item[0] < 40 :
                removeItems.append(item)
        
        for item in removeItems:
            verticalRegions.remove(item)      
                
        a = np.zeros(shape = (height,width//itemPixel))
       
        # 水平方向上的处理
        #Horizontal
        for i in range(width//itemPixel):
            region = np.sum(im[:,i*itemPixel:(i+1)*itemPixel] ,axis =1 ) 
            for m in range(height):
                if region[m] > 0 :
                    a[m][i] = 255  
        self.ShowImage(a) 
        self.SaveImage('C:\\10.PNG', a)            
        mm = np.sum(a,axis = 1)
        maxValue = np.amax(mm)
        m = np.zeros(shape =(height))
        for i in range(height):
            if mm[i] == maxValue:
                m[i] = 1
                cv2.line(im,(0,i),(width-1,i),255) 
        
        self.ShowImage(im) 
        self.SaveImage('C:\\11.PNG', im)       
        horizontalRegions = []
        start = 0
        for i in range(height -1):
            if m[i] != m[i+1]:
                horizontalRegions.append([start,i])
                start = i+1
        removeItems = []        
        for item in horizontalRegions:
            if item[1] - item[0] < 40 :
                removeItems.append(item) 
        
        for item in removeItems:
                horizontalRegions.remove(item)                                               
        
        files = glob.glob(destFolder+'//*')
        for f in files:
            os.remove(f)
        
        # 图像分割
        imageCount = 1
        for item in verticalRegions:
            vim = im[:,item[0]:item[1]]
            for hitem in horizontalRegions:
                sim = vim[hitem[0]:hitem[1],:]
                dim = cv2.resize(sim,((hitem[1]-hitem[0]+1)*magnifyCount,(item[1]-item[0]+1)*magnifyCount),interpolation = cv2.INTER_LANCZOS4)
                #print('x:',(hitem[1]-hitem[0]+1)*magnifyCount)
                #print('x:',(item[1]-item[0]+1)*magnifyCount)
                x = (hitem[1]-hitem[0]+1)*magnifyCount
                y = (item[1]-item[0]+1)*magnifyCount
                dim = np.array(dim)  
                #sprint(x,y)
                self.SaveImage(destFolder+'\\'+str(imageCount)+'.tif',dim)
                imageCount= imageCount + 1
                      
        
    def ShowImage(self,image):   
        cv2.imshow('Image',image) 
        cv2.waitKey()
        cv2.destroyAllWindows()  
  
    def SaveImage(self,path,image):
        cv2.imwrite(path,image)

def DealOneImage(filePath,width,height,itemPixels,magnifyCount):
    print('Start process:'+filePath)  
    imProc = ImageProcess()
    fd = open(filePath, 'rb')
    rows = height
    cols = width
    f = np.fromfile(fd, dtype=np.uint8,count=rows*cols)
    im = f.reshape((rows, cols)) #notice row, column format
    fd.close()
    imProc.ShowImage(im)
    imProc.SaveImage('C:\\2.png', im)
    filePath = filePath.strip('.raw')
    if path.exists(filePath) == False:
        os.mkdir(filePath)
    imProc.SplitImage(im, filePath, width, height,itemPixels,magnifyCount)
    print('Process successfully')

def main():
    #print('程序开始执行')
    #files = glob.glob('E:\\OCRData\\Qianfoshan_RAW\\2560X2083X8\\*.raw')
    #for f in files:
        #DealOneImage(f,2083,2560,10,3)
    #print('finish')
    DealOneImage('C:\\2.raw',2083,2560,10,3)
    
# 增加调用main()函数
if __name__ == '__main__':
    main()


    
```



