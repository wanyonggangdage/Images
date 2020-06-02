# <center> OCR 预处理方法
## 1. 综述
OCRMain 中对于图像的预处理算法包括：Basic、InvertIntensityDectect、SubtractBackground、IncreaseContrast、EdgeDetect、HollowCharacter、HollowCharacterInvert。其中 Basic 算法是不对图像进行处理，空心字相关算法HollowCharacter、HollowCharacterInvert 在原有程序中为Opencv 实现，不做关注。
## 2. Basic 算法



## 3. InvertIntensityDectect 算法
实现该算法的子算法包括：ThresholdSegment、ConvertInputToBinary、Invert、RemoveNoises、Rotate、ConvertInputTo16BitGray、ResizeImage。
### ThresholdSegment 
