# <center> 提高识别率解决方案
## 1. Tesseract 现存问题
&emsp;我们以下图为例：
![image](https://wanyonggangdage.github.io/Images/13.png)
对整张图进行识别的结果如下：
```
GEMEDICAL SYSTEMS by QIANFOSHAN HOSPITAL

 

N EEL 1 Lol P e
Ex: 999/ME150308CT2008 AR
R LS
113 EER
SRy A1) R R
(1 FIPEAIK
B
EER TS
3R
N
R §
S 4
9 9
kv 120
mé 220
RIS

P R LY
L[S l I | | |
P13 LU TS

[
```

我们可以发现病人编号并没有被很好的识别出来。

根据图像特点，文字分为左右两侧，左侧左对齐，右侧右对齐，我们先将图像拆分为左右两部分：
![image](https://wanyonggangdage.github.io/Images/14.PNG)
识别该图像识别结果如下：
```
QIANFOSHAN HOSPITAL
WANG CHANG MEI
(AR
3L

Mar 08 2015

08:41:33 AM

EPRAIN

EER TS
18
1B

 

——
L LR
```

识别效果并不理想，通过如下方法，对上图进行拆分：

```Python
def SplitImageVertical(im,stepHeight):
    x = im.shape[0]
    ims = []
    for i in range(int(x/stepHeight)):
        low = i*stepHeight
        high = (i+3)*stepHeight
        if high > x-1:
            high = x -1
        ims.append(im[low:high,:])
    return ims
```

拆分成高度较小的几张图进行识别，识别结果如下：

```
QIANFOSHAN HOSPITAL
RER e I e
AT TR

1261639
YRR 14WHNULHHNUMI:I
FABWJul0319?1
1261639

Mar 08 2015
08:41:33 4M1261639
Maroszms
08:41:33P.M
512)(512mmvv‘.vli

08:41:33 AM
EPRAIN

EER TS
.b'IH(b'I.!

Mag=1.94
FL:
NMag:1.94
FL:
ROT:gI_l_l WW:300W|.:35;l_l WW:300W|.:35I_l_l WW:300W|.:35
```

我们可以看到其识别效果还是不错的。估计Tesseract对于多行文本的识别有点问题，对于顶部的几行文本识别效果好。但由于步长不太容易确定。拆分较多张图片性能也是一个问题。

## 2. 先提取文字区域，在分别进行识别以提高文字识别率
### 区域分割问题
&emsp;利用Tesseract 现有方法，得到识别出的文字区域，识别结果如下图：  
 ![image](https://wanyonggangdage.github.io/Images/16.PNG)

 去掉一些干扰区域： 高度大于宽度的区域，如果识别字符串较长，也可以将宽度小于2倍高度的区域全部去掉，去掉干扰区域后如下图：  
  ![image](https://wanyonggangdage.github.io/Images/17.PNG)

在区域分割的基础之上对所有的小的区域进行分别识别，识别结果如下：
```
LineResult:GEMEDICM
LineResult:IR
LineResult:QIANFOSHAN
LineResult:HUSPITAI.
LineResult:WANG
LineResult:LHAND
LineResult:999!ME150308CD008
LineResult:F43WJu1031971
LineResult:1261639
LineResult:Marus
LineResult:SN327
LineResult:08:41:33
LineResult:512)(512
LineResult:STND
LineResult:SFOV
LineResult:PRI T L
LineResult:300WL'35
```

识别效果较好，但对于A号所在区域的识别反而退化了，并且几次执行下来的结果相对稳定，如下：
直接进行识别结果：
```
Ex: 999/ME150308CT2008 AR
```
区域分割后的识别结果如下：
```
999!ME150308CD008
```
以上问题我们可以将两次的识别结果拼接到一起进行正则过滤来解决问题。

### 区域连接问题
&emsp; 对于如下一张图，我们又会碰到另外的一个问题：  
  ![image](https://wanyonggangdage.github.io/Images/18.PNG)

如上图所示，P 号部分被切分成了两部分，对于一行的区域，我应该识别为一行，并且应该应该去掉内部的空格，以便我们后续的处理。