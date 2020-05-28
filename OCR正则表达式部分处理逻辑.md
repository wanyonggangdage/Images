# <center> OCR 正则表达式部分处理逻辑
## 自动生成正则表达式部分匹配方案

1. 用于测试的数据如下：  
原始字符串：
```
S/HE150309CT30Z4 M7SVIMap 18 1938
```

配置的识别规则及生成的正则表达式：
![image](https://wanyonggangdage.github.io/Images/22.PNG)  

2. 得到配置表达式：
```
~~ME~~5-6~~CT~~3-6~~~~
```   
3. 去除掉原始字符内的空格字符，结果如下：
```
S/HE150309CT30Z4M7SVIMap181938
```  
4. 接下来程序分两种情况进行处理，首先第一种正则表达式可以完成匹配：
我们把初始字符串改为：
```
S/HE150309CT3024 M7SVIMap 18 1938
```  
也就是将'Z' 换成 ‘2’ ，经过正则表达式匹配后的字符串：  
```
HE150309CT3024
```  
下一步纠正前缀中缀后缀中的错误字符，我们自动生成的正则表达式如下：
```
([MHNm]\s*[EFGZS]\s*)(\d{5,6})\s*([C0OQGc]\s*[T7F1IlYt]\s*)(\d{3,7})\s*
```  

如果前缀中包含多个匹配的前缀，换成如下数据：  
![image](https://wanyonggangdage.github.io/Images/24.PNG)   

生成的正则表达式为：
```
([MHNm]\s*[EFGZS]\s*|[C0OQGc]\s*[DP0O]\s*)(\d{5,7})\s*([C0OQGc]\s*[T7F1IlYt]\s*)(\d{3,8})\s*
```  

我们可以看到前缀包含 ME||CD 两个可能的前缀类型，对于 ME类型，会先生成正则表达式：
```
([MHNm]\s*[EFGZS]\s*)(\d{5,7})\s*([C0OQGc]\s*[T7F1IlYt]\s*)(\d{3,8})\s*
``` 
去匹配，如果匹配成功，将替换前缀设为 ME。 如果匹配不成功，会生成正则表达式：
```
([C0OQGc]\s*[DP0O]\s*)(\d{5,7})\s*([C0OQGc]\s*[T7F1IlYt]\s*)(\d{3,8})\s*
```  
去匹配，匹配成功，将替换的前缀设为 CD。


我们可以看到，生成的正则表达式相当规整，一共有4个分组（每个分组的内容写在一个小括号内，\s 匹配任何空白字符，包括空格，制表符，换行符等），分别代表 前缀，数字段，中缀，数字段。程序处理逻辑也很简单，保留数字段内容，将前缀，中缀，后缀 替换成配置的内容。我们可以得到如下结果：
```
ME150309CT3024
``` 
5. 数字中包含字母，如 2 被识别成了 Z：
原始字符串：
```
S/HE150309CT30Z4 M7SVIMap 18 1938
```

一旦经过自动生成的正则表达式匹配不成功，先拆分出前缀的正则表达式：
```
([MHNm]\\s*[EFGZS]\\s*)
```  
利用该正则先去匹配出前缀，再去匹配数字段，可以匹配成功，再去匹配中缀，匹配成功，匹配中缀后的数字段，匹配不成功，进入如下处理过程：

- 利用下表进行替换操作，将字母替换为数字，发现第一个无法替换的字母时停止，当数字超过数字段最大字符数时也要停止替换。另外如果包含后缀，匹配到后缀所在位置也要停止替换。得到的数字个数符合数字长度要求，匹配成功，不符合匹配不成功。
- 用前缀，中缀，后缀字符替换掉匹配出的前缀，中缀，后缀字符。

```
                                                    {"A", "4"},
                                                    {"B", "8"},
                                                    {"C", "0"},
                                                    {"D", "0"},
                                                    {"E", ""},
                                                    {"F", "7"},
                                                    {"G", "6"},
                                                    {"H", ""},
                                                    {"I", "1"},
                                                    {"J", "3"},
                                                    {"K", ""},
                                                    {"L", ""},
                                                    {"M", ""},
                                                    {"N", ""},
                                                    {"O", "0"},
                                                    {"P", "9"},
                                                    {"Q", "0"},
                                                    {"R", "8"},
                                                    {"S", "5"},
                                                    {"T", "7"},
                                                    {"U", "0"},
                                                    {"V", ""},
                                                    {"W", "4"},
                                                    {"X", "4"},
                                                    {"Y", ""},
                                                    {"Z", "2"},
                                                    {"a", ""},
                                                    {"b", "6"},
                                                    {"c", ""},
                                                    {"d", "6"},
                                                    {"e", ""},
                                                    {"f", ""},
                                                    {"g", ""},
                                                    {"h", ""},
                                                    {"i", "1"},
                                                    {"j", ""},
                                                    {"k", ""},
                                                    {"l", "1"},
                                                    {"m", ""},
                                                    {"n", ""},
                                                    {"o", "0"},
                                                    {"p", "9"},
                                                    {"q", "9"},
                                                    {"r", ""},
                                                    {"s", "5"},
                                                    {"t", "1"},
                                                    {"u", ""},
                                                    {"v", ""},
                                                    {"w", ""},
                                                    {"x", ""},
                                                    {"y", ""},
                                                    {"z", "2"}
```  


## 手动输入正则表达式的匹配方案
1. 用于测试的数据如下：  
原始字符串：
```
S/HE150309CT30Z4 M7SVIMap 18 1938
```
用于匹配的正则表达式：
```
([MHNm]\s*[EFGZS]\s*)(\d{5,6})\s*([C0OQGc]\s*[T7F1IlYt]\s*)(\d{3,7})\s*
```  
![image](https://wanyonggangdage.github.io/Images/23.PNG)  

可见识别并为成功，手动正则，这是简单的正则筛选没有替换逻辑。

## 姓名匹配方案

### 匹配步骤

- 移除不相关字符
- 用空格拆分拿到的字符串
- 去掉疑似年龄和性别的部分
- 依次循环下面配置文件中的所有配置项，依据中间的 grammar 部分，找到输入字符串中匹配的项的项，用第三项 substiteChar 替换找到字符部分。（如果字符串包含两个及以上的该字符，只替换正则识别出的位置的字符）。
- 名字之间的空格拆分和错误字符替换都是以该方式实现的。
### 识别示例

对于名字拆分,识别初始数据如下：
```
F 66 LiLei
```
- 首先该字符串会被拆分为三部分，'F' '66' 'LiLei','F'和'66' 疑似性别和年龄的部分会首先被移除掉，剩余 'LiLei'部分。
- 我们从下面配置文件中找到如下配置部分：  
```C#
<patientNameSubstitutionEntry>
      <inputChar>L</inputChar>
	  <grammar>L[a-zA-Z]+L[a-zA-Z]+</grammar>
      <substituteChar> L</substituteChar>
</patientNameSubstitutionEntry>
```
- grammar 部分删选出第二个 L ，用 ' L' 替换第二个L，经过此步骤，名字中的两个子被拆开。

对于错误字符替换，识别初始数据如下：
```
0iuJu
```
- 对于0 我们找到了如下两个配置项，但是正则表达式匹配上的只有第一个，因此我们将其替换为 Q。
```HTML
<patientNameSubstitutionEntry>
      <inputChar>0</inputChar>
	  <grammar>^0[a-zA-Z]+</grammar>
      <substituteChar>Q</substituteChar>
</patientNameSubstitutionEntry>
<patientNameSubstitutionEntry>
      <inputChar>0</inputChar>
	  <grammar>[a-zA-Z]+0[a-zA-Z]*</grammar>
      <substituteChar>O</substituteChar>
</patientNameSubstitutionEntry>
```
- 对于 J 我们找到了如下两个配置项，首先，字母 J 会被第一个配置命中，将其替换为 ' J',这样因为已经有了前导空格，所以下一个配置不会被命中。
``` HTML
<patientNameSubstitutionEntry>
      <inputChar>J</inputChar>
	  <grammar>[a-zA-Z]+J[a-zA-Z]+</grammar>
      <substituteChar> J</substituteChar>
</patientNameSubstitutionEntry>
<patientNameSubstitutionEntry>
      <inputChar>J</inputChar>
	  <grammar>[a-zA-Z]+J[a-zA-Z]*</grammar>
      <substituteChar>I</substituteChar>
</patientNameSubstitutionEntry>
```  

对于无法识别的情况，识别初始数据如下：
``` 
Qiu Jurrr
```  
- 替换完成后，会经过如下代码，去除掉非法拼音的部分
``` C#
 // process "^"
                    for (int i = 0; i < parts.Length; ++i)
                    {
                        if (parts[i].Count((f => (f == 'A' || f == 'a'))) == 2
                            ||( matchGrammar_vs_OCRdString("[^BbPpMmFfDdTtNnLlGgKkHhZzCcSsYyWwIi]A$", parts[i]) != ""
                            && matchGrammar_vs_OCRdString("[^(GU)]A$", parts[i]) != ""
                            && matchGrammar_vs_OCRdString("[^(KU)]A$", parts[i]) != ""
                            && matchGrammar_vs_OCRdString("[^(HU)]A$", parts[i]) != "")
                            || matchGrammar_vs_OCRdString("INGA$|ingA$", parts[i]) != ""
                            || matchGrammar_vs_OCRdString("A[FfMm]$", parts[i]) != "")
                        {
                            int place = parts[i].LastIndexOf("A");
                            if (place > 0)
                            {
                                parts[i] = parts[i].Remove(place, 1).Insert(place, reserveNameSep ? "^" : " ");
                            }
                        }
                    }
```

- 去除完以后，对于每部分使用以下正则表达式进行匹配，如匹配内容不为空，直接跳出人名匹配，该次匹配失败。
``` C#
    static string[] patient_name_filter_grammar = new string[]{
                                                    "^[iIuU][a-zA-Z]+",
                                                    "([\\w])\\1",
                                                    //"[^a-zA-Z]",
                                                    "[bBcCdDfFhHjJkKlLmMpPqQrRsStTwWxXyYzZ]$",
                                                    "[Ii][^aAeEuUnNoO]",
                                                    "[aA][^iIoOnN]",
                                                    "[eE][^iInNrR]",
                                                    "[oO][^uUnN]",
                                                    "[uU][^iIeEnNaAoO]",
                                                    "[^cCsSzZ][hH]"
        };
```  
- 也就是说上面几个正则表达式，去除人名中不合法部分，下面正则可以一票否决是否该字符串是否为一个合法的人名。

### 姓名匹配，用到的循环较多，性能上待商榷。

### 用到配置文件 patientNameSubstitutions.xml 内容如下：

``` HTML
<?xml version="1.0" encoding="utf-8"?>
<patientNameSubstitutionEntries xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <entries>
	<patientNameSubstitutionEntry>
      <inputChar>0</inputChar>
	  <grammar>^0[a-zA-Z]+</grammar>
      <substituteChar>Q</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>0</inputChar>
	  <grammar>[a-zA-Z]+0[a-zA-Z]*</grammar>
      <substituteChar>O</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>1</inputChar>
	  <grammar>[a-zA-Z]+1[a-zA-Z]*</grammar>
      <substituteChar>I</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>2</inputChar>
	  <grammar>^2[a-zA-Z]+</grammar>
      <substituteChar>Z</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>3</inputChar>
	  <grammar>^3[a-zA-Z]+</grammar>
      <substituteChar>B</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>4</inputChar>
	  <grammar>[a-zA-Z]*4[a-zA-Z]*</grammar>
      <substituteChar>A</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>5</inputChar>
	  <grammar>^5[a-zA-Z]+</grammar>
      <substituteChar>S</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>6</inputChar>
	  <grammar>[a-zA-Z]*6[a-zA-Z]*</grammar>
      <substituteChar>G</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>7</inputChar>
	  <grammar>^7[a-zA-Z]+</grammar>
      <substituteChar>T</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>8</inputChar>
	  <grammar>^8[a-zA-Z]+</grammar>
      <substituteChar>B</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>9</inputChar>
	  <grammar>^9[a-zA-Z]+</grammar>
      <substituteChar>q</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>B</inputChar>
	  <grammar>[a-zA-Z]+B[a-zA-Z]+</grammar>
      <substituteChar> B</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>b</inputChar>
	  <grammar>[a-zA-Z]+b[a-zA-Z]+</grammar>
      <substituteChar> b</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>C</inputChar>
	  <grammar>[a-zA-Z]+C[a-zA-Z]+</grammar>
      <substituteChar> C</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>c</inputChar>
	  <grammar>[a-zA-Z]+c[a-zA-Z]+</grammar>
      <substituteChar> c</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>CH</inputChar>
	  <grammar>[a-zA-Z]+CH[a-zA-Z]+</grammar>
      <substituteChar> CH</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>ch</inputChar>
	  <grammar>[a-zA-Z]+ch[a-zA-Z]+</grammar>
      <substituteChar> ch</substituteChar>
    </patientNameSubstitutionEntry>
    <patientNameSubstitutionEntry>
      <inputChar>D</inputChar>
	  <grammar>^DI[AO]NG</grammar>
      <substituteChar>Q</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>D</inputChar>
	  <grammar>[a-zA-Z]+D[a-zA-Z]+</grammar>
      <substituteChar> D</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>d</inputChar>
	  <grammar>[a-zA-Z]+d[a-zA-Z]+</grammar>
      <substituteChar> d</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>D</inputChar>
	  <grammar>[a-zA-Z]+D$</grammar>
      <substituteChar>O</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>E</inputChar>
	  <grammar>^EH[a-zA-Z]+</grammar>
      <substituteChar>Z</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>E</inputChar>
	  <grammar>^E[^rRnN]+</grammar>
      <substituteChar>B</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>F</inputChar>
	  <grammar>[a-zA-Z]+F[a-zA-Z]+</grammar>
      <substituteChar> F</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>f</inputChar>
	  <grammar>[a-zA-Z]+f[a-zA-Z]+</grammar>
      <substituteChar> f</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>F</inputChar>
	  <grammar>^FONG</grammar>
      <substituteChar>R</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>G</inputChar>
	  <grammar>[^N]+G[a-zA-Z]+</grammar>
      <substituteChar> G</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>g</inputChar>
	  <grammar>[^n]+g[a-zA-Z]+</grammar>
      <substituteChar> g</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>H</inputChar>
	  <grammar>H$</grammar>
      <substituteChar>N</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>H</inputChar>
	  <grammar>[^SsZzCc]+H[a-zA-Z]+</grammar>
      <substituteChar> H</substituteChar>
    </patientNameSubstitutionEntry> 
	<patientNameSubstitutionEntry>
      <inputChar>I</inputChar>
	  <grammar>^I[a-zA-Z]+</grammar>
      <substituteChar>l</substituteChar>
    </patientNameSubstitutionEntry>	
	<patientNameSubstitutionEntry>
      <inputChar>J</inputChar>
	  <grammar>[a-zA-Z]+J[a-zA-Z]+</grammar>
      <substituteChar> J</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>J</inputChar>
	  <grammar>[a-zA-Z]+J[a-zA-Z]*</grammar>
      <substituteChar>I</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>j</inputChar>
	  <grammar>[a-zA-Z]+j[a-zA-Z]+</grammar>
      <substituteChar> j</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>K</inputChar>
	  <grammar>[a-zA-Z]+K[a-zA-Z]+</grammar>
      <substituteChar> K</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>k</inputChar>
	  <grammar>[a-zA-Z]+k[a-zA-Z]+</grammar>
      <substituteChar> k</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>L</inputChar>
	  <grammar>L[a-zA-Z]+L[a-zA-Z]+</grammar>
      <substituteChar> L</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>l</inputChar>
	  <grammar>[a-zA-Z]+l[a-zA-Z]+</grammar>
      <substituteChar> l</substituteChar>
    </patientNameSubstitutionEntry>
    <patientNameSubstitutionEntry>
      <inputChar>M</inputChar>
	  <grammar>M$</grammar>
      <substituteChar>N</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>M</inputChar>
	  <grammar>[a-zA-Z]+M[a-zA-Z]+</grammar>
      <substituteChar> M</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>m</inputChar>
	  <grammar>[a-zA-Z]+m[a-zA-Z]+</grammar>
      <substituteChar> m</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>MN</inputChar>
	  <grammar>^MN</grammar>
      <substituteChar>MIN</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>O</inputChar>
	  <grammar>^O[^Uu]</grammar>
      <substituteChar>Q</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>P</inputChar>
	  <grammar>HUPNG$</grammar>
      <substituteChar>A</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>P</inputChar>
	  <grammar>[a-zA-Z]+P[a-zA-Z]+</grammar>
      <substituteChar> P</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>p</inputChar>
	  <grammar>[a-zA-Z]+p[a-zA-Z]+</grammar>
      <substituteChar> p</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>Q</inputChar>
	  <grammar>[a-zA-Z]+Q[a-zA-Z]+</grammar>
      <substituteChar> Q</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>q</inputChar>
	  <grammar>[a-zA-Z]+q[a-zA-Z]+</grammar>
      <substituteChar> q</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>R</inputChar>
	  <grammar>[a-zA-Z]+R[oO]$</grammar>
      <substituteChar>A</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>R</inputChar>
	  <grammar>[a-zA-Z]+R[a-zA-Z]+</grammar>
      <substituteChar> R</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>S</inputChar>
	  <grammar>[a-zA-Z]+S[a-zA-Z]+</grammar>
      <substituteChar> S</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>s</inputChar>
	  <grammar>[a-zA-Z]+s[a-zA-Z]+</grammar>
      <substituteChar> s</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>SH</inputChar>
	  <grammar>[a-zA-Z]+SH[a-zA-Z]+</grammar>
      <substituteChar> SH</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>sh</inputChar>
	  <grammar>[a-zA-Z]+sh[a-zA-Z]+</grammar>
      <substituteChar> sh</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>T</inputChar>
	  <grammar>^TH[a-zA-Z]+</grammar>
      <substituteChar>Z</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>T</inputChar>
	  <grammar>[a-zA-Z]+T[a-zA-Z]+</grammar>
      <substituteChar> T</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>t</inputChar>
	  <grammar>[a-zA-Z]+t[a-zA-Z]+</grammar>
      <substituteChar> t</substituteChar>
    </patientNameSubstitutionEntry>
    <patientNameSubstitutionEntry>
      <inputChar>VV</inputChar>
	  <grammar>VV[a-zA-Z]+</grammar>
      <substituteChar>W</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>V</inputChar>
	  <grammar>^V[a-zA-Z]+</grammar>
      <substituteChar>Y</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>W</inputChar>
	  <grammar>[a-zA-Z]+W[a-zA-Z]+</grammar>
      <substituteChar> W</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>w</inputChar>
	  <grammar>[a-zA-Z]+w[a-zA-Z]+</grammar>
      <substituteChar> w</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>X</inputChar>
	  <grammar>[a-zA-Z]+X[a-zA-Z]+</grammar>
      <substituteChar> X</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>x</inputChar>
	  <grammar>[a-zA-Z]+x[a-zA-Z]+</grammar>
      <substituteChar> x</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>Y</inputChar>
	  <grammar>[a-zA-Z]+Y[a-zA-Z]+</grammar>
      <substituteChar> Y</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>y</inputChar>
	  <grammar>[a-zA-Z]+y[a-zA-Z]+</grammar>
      <substituteChar> y</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>Z</inputChar>
	  <grammar>[a-zA-Z]+Z[a-zA-Z]+</grammar>
      <substituteChar> Z</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>z</inputChar>
	  <grammar>[a-zA-Z]+z[a-zA-Z]+</grammar>
      <substituteChar> z</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>ZH</inputChar>
	  <grammar>[a-zA-Z]+ZH[a-zA-Z]+</grammar>
      <substituteChar> ZH</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>zh</inputChar>
	  <grammar>[a-zA-Z]+zh[a-zA-Z]+</grammar>
      <substituteChar> zh</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>ll</inputChar>
	  <grammar>ll</grammar>
      <substituteChar>N</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>l</inputChar>
	  <grammar>[a-zA-Z]+l[a-zA-Z]*</grammar>
      <substituteChar>I</substituteChar>
    </patientNameSubstitutionEntry>
	<patientNameSubstitutionEntry>
      <inputChar>O</inputChar>
	  <grammar>[IAE]NO$</grammar>
      <substituteChar>G</substituteChar>
    </patientNameSubstitutionEntry>
    <patientNameSubstitutionEntry>
      <inputChar>^</inputChar>
      <grammar>^</grammar>
      <substituteChar> </substituteChar>
    </patientNameSubstitutionEntry>
  </entries>
</patientNameSubstitutionEntries>
```  