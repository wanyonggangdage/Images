# <center> OCR 预处理方法
## 1. 综述
OCRMain 中对于图像的预处理算法包括：Basic、InvertIntensityDectect、SubtractBackground、IncreaseContrast、EdgeDetect、HollowCharacter、HollowCharacterInvert。其中 Basic 算法是不对图像进行处理，空心字相关算法HollowCharacter、HollowCharacterInvert 在原有程序中为Opencv 实现，不做关注。
## 2. Basic 算法

``` C#
  //no more processing 
            if (algo == OCRAlgo.Basic)
            {
                Rotate(ref image, rvalue * 100);
                ConvertInputTo16BitGray(ref image);
            }
``` 

我们可以看到，在原有程序中，Basic算法，先将图像旋转，使文字转正，在将图像转换为16位Gray图像进行识别。

## 3. InvertIntensityDectect 算法
实现该算法的子算法包括：ThresholdSegment、ConvertInputToBinary、Invert、RemoveNoises、Rotate、ConvertInputTo16BitGray、ResizeImage。
### ThresholdSegment 
