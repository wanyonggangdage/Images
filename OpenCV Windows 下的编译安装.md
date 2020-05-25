# <center>OpenCV Windows 下的编译安装

## 1. OpenCV源码的下载
1. 进入OpenCV 官网 [http://opencv.org](http://opencv.org)  
![image](https://wanyonggangdage.github.io/Images/1.PNG)
2. 选择Sources 进行下载
3. 将下载到的 opencv-4.3.0.zip 解压到 opencv-4.3.0
4. 下载 CMake [https://github-production-release-asset-2e65be.s3.amazonaws.com/537699/d0aef900-8948-11ea-8326-edb98a32f346?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20200523%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20200523T094534Z&X-Amz-Expires=300&X-Amz-Signature=451df9d8023d7d1d3f8bda1548064b8a0ed49634b5b0933976d4c3f9ac1b4ded&X-Amz-SignedHeaders=host&actor_id=0&repo_id=537699&response-content-disposition=attachment%3B%20filename%3Dcmake-3.17.2-win64-x64.msi&response-content-type=application%2Foctet-stream](https://github-production-release-asset-2e65be.s3.amazonaws.com/537699/d0aef900-8948-11ea-8326-edb98a32f346?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20200523%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20200523T094534Z&X-Amz-Expires=300&X-Amz-Signature=451df9d8023d7d1d3f8bda1548064b8a0ed49634b5b0933976d4c3f9ac1b4ded&X-Amz-SignedHeaders=host&actor_id=0&repo_id=537699&response-content-disposition=attachment%3B%20filename%3Dcmake-3.17.2-win64-x64.msi&response-content-type=application%2Foctet-stream)
5. 将下载到的 cmake-3.17.2-win64-x64.msi 进行安装。
6. 打开如下界面的 Cmake 主程序。  
![image](https://wanyonggangdage.github.io/Images/2.PNG)
7. 选择源代码所在目录，也就是我们压缩文件夹解压缩后的目录。
![image](https://wanyonggangdage.github.io/Images/3.PNG)
9. 如上图所示，除了源代码目录，我们还需要指定一个编译的目标目录。
10. 然后点击 Generate 按钮，第一次编译需要选择编译工具。一定要选取本机已安装的编译工具。
11. 经过一段时间后编译完成。
12. 下载opencv_contrib 源代码文件 [https://codeload.github.com/opencv/opencv_contrib/zip/4.3.0](https://codeload.github.com/opencv/opencv_contrib/zip/4.3.0)
13. 将下载的 opencv_contrib-4.3.0.zip 解压到 opencv_contrib-4.3.0 目录中。
14. 二次编译，将扩展部分编译到输出目录。
![image](https://wanyonggangdage.github.io/Images/4.PNG)
17. 将 OPENCV_EXTRA_MODULES_PATH 设置为 opencv_contrib 中 modules
18. 点击config 按钮，无报错点击 Generat
19.  编译完成后，我们在资源管理器下查看上面设置的文件输出目录，发现目录下多出了很多文件，其实就是OpenCV.sln工程文件。
 ![image](https://wanyonggangdage.github.io/Images/5.PNG)
21.  用VS打开OpenCV.sln工程，编译生成Debug和Release库
![image](https://wanyonggangdage.github.io/Images/6.PNG)
22.  编译生成debug版本的库，记得在此之前要选择编译的平台信息，这就是编译生成debug版本和release版本的区别，也可以选择release，因为自己的工程可能要用到相应的动态链接库

  





## 详细部分可参阅 [https://www.cnblogs.com/jliangqiu2016/p/5597501.html](https://www.cnblogs.com/jliangqiu2016/p/5597501.html)