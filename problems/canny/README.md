## Repository Tree
```
.
├── canny.cpp         //源码框架
├── canny.hpp         //头文件
├── canny_test.cpp    //测试源码
├── src.txt           //测试输入
├── dst.txt           //标准输出
├── tcl_script.tcl    //tcl文件
└── README.md         //说明文件
```
## Canny

1. 框架代码说明

    框架代码是算法的C-model，
    您需要自行设计可综合co-sim的设计。  
    但请注意：**您必须保持顶层函数不变。**

2. 项目要求

    在设计完成后，
    您可以使用我们提供的`canny_test.cpp`文件进行基本功能测试。
    但是，由于在评分时会统一使用评分系统内部的`canny_test.cpp`，
    因此请不要将设计写在`canny_test.cpp`中，
    `canny_test.cpp`文件也无需提交。
    **在HLS优化的过程中，请务必将优化指令设置为pragma写入c++代码中。**
    比赛不需要生成最终bit文件，仅考察算法实现功能和综合报告的性能，能完成co-sim即可。
		
3. 算法说明

    Canny边缘检测器是一种边缘检测算子，
    它使用多阶段算法检测图像中的边缘。

4. 函数原型

    ```c++
    void fast_accel(DTYPE* img_in, int threshold, DTYPE* img_out, int rows, int cols)
    ```
    - `img_in`: 图像输入
    - `img_out`: 图像输出
    - `imag_sample`: 阈值
    - `rows`: 图像长度（行数）
    - `cols`: 图像宽度（列数）

    在本项目中，输入的图像已经被转化为了一个大小为![](https://render.githubusercontent.com/render/math?math=128\times128)的一维数组。
    输入矩阵是按行展开的。
    输出矩阵是算法得到的k个特征点在图像中的位置。
    如果该位置是特征点，则该位置的值为255；如果不是，则该位置的值为0。
    例如，算法得到的一张![](https://render.githubusercontent.com/render/math?math=4\times4)大小图像的特征点坐标为![](https://render.githubusercontent.com/render/math?math=(0,1))和![](https://render.githubusercontent.com/render/math?math=(2,3))，那么dst应为

    <div align="center">
    <img src="https://render.githubusercontent.com/render/math?math=\huge%20\begin{matrix}%200%26255%260%260\\0%260%260%260\\0%260%260%26255\\0%260%260%260\end{matrix}">
    </div>

    分16行输出：
    <div align="center">
    <img src="https://render.githubusercontent.com/render/math?math=\huge%20\begin{matrix}%200%26255%260%260%260%260%260%260%260%260%260%26255%260%260%260%260\end{matrix}">
    </div>

    **本题在使用滤波器前一律进行opencv的BORDER_DEFAULT填充，使得滤波后的图像和滤波前大小一致。**

    关于canny算法本身，以下为算法4个主要步骤的大致介绍：

    1.使用![](https://render.githubusercontent.com/render/math?math=3\times3)的高斯滤波来平滑图像，标准差![](https://render.githubusercontent.com/render/math?math=\sigma)=1.5。

    2.使用![](https://render.githubusercontent.com/render/math?math=3\times3)的Sobel滤波器找寻图像的梯度强度。

    Sobel的X方向和Y方向上的算子![](https://render.githubusercontent.com/render/math?math=I_x)和![](https://render.githubusercontent.com/render/math?math=I_y)分别为：
    <div align="center">
    <img src="https://render.githubusercontent.com/render/math?math=\huge%20I_x=\begin{bmatrix}-1%260%26%2B1\\-2%260%26%2B2\\-1%260%26%2B1\end{bmatrix},">
    <img src="https://render.githubusercontent.com/render/math?math=\huge%20I_y=\begin{bmatrix}-1%26-2%26-1\\0%260%260\\%2B1%26%2B2%26%2B1\end{bmatrix}">
    </div>

    则图像某点X方向梯度![](https://render.githubusercontent.com/render/math?math=G_x)、Y方向梯度![](https://render.githubusercontent.com/render/math?math=G_y)和梯度强度![](https://render.githubusercontent.com/render/math?math=G)之间满足
    
    <div align="center">
    <img src="https://render.githubusercontent.com/render/math?math=\huge%20G=\sqrt{G_x^2%2BG_y^2}">
    </div>
    
    上式中平方为对应元素相乘，不是矩阵乘法。
    
    此外，计算梯度方向![](https://render.githubusercontent.com/render/math?math=a)：
    
    <div align="center">
    <img src="https://render.githubusercontent.com/render/math?math=\huge%20a=arctan{\frac{G_y}{G_x}}">
    </div>
    
    3.对梯度强度![](https://render.githubusercontent.com/render/math?math=G)在![](https://render.githubusercontent.com/render/math?math=3\times3)范围内进行非最大值抑制。

    4.使用双阈值法对梯度强度![](https://render.githubusercontent.com/render/math?math=G)进行边界求解，其中阈值上界取80，阈值下界取30。如果边缘像素的梯度值高于高阈值，则将其标记为强边缘像素，直接视为边缘像素；如果边缘像素的梯度值小于高阈值并且大于低阈值，则将其标记为弱边缘像素。通过查看弱边缘像素及其8个邻域像素，只要其中一个为强边缘像素，则该弱边缘点就可以保留为真实的边缘。

5. 提交程序

    您仅需提交cpp文件和h文件。
    请将除`canny_test.cpp`以外的所有cpp和h文件
    压缩为一个压缩包`canny.zip`，
    在页面下方您的解答处点击“**上传代码**”按钮进行提交，
    稍待片刻即可在文本框下方得知您的设计是否通过了基本功能测试。
    延迟、面积等性能指标则可在历史提交记录中点击版本号详细查看。
  
6. 评分规则

    - 如果最大频率![](https://render.githubusercontent.com/render/math?math=F_{max}<100\MHz)， 视为未通过基本功能

    - 如果最大频率![](https://render.githubusercontent.com/render/math?math=100\MHz%20\le%20F_{max}<300\MHz)，使用下列公式计算性能
      <div align="center">
      <img src="https://render.githubusercontent.com/render/math?math=\huge%20\frac{T_{clock}\times%20F_{max}}{\tau_{Simulation}}">
      </div>

    - 如果最大频率![](https://render.githubusercontent.com/render/math?math=F_{max}%20\ge%20300\MHz)，使用下列公式计算性能
      <div align="center">
      <img src="https://render.githubusercontent.com/render/math?math=\huge%20\frac{T_{clock}\times300\MHz}{\tau_{Simulation}}">
      </div>
    
    其中,
    ![](https://render.githubusercontent.com/render/math?math=F_{max})是设计的最高运行频率，
    ![](https://render.githubusercontent.com/render/math?math=T_{clock})是TCL中设定的时钟频率，
    ![](https://render.githubusercontent.com/render/math?math=\tau_{Simulation})是Vitis HLS软件Cosimulation所用时间。
    性能公式计算出的数值越大，性能越好。

    **注意：**
    设计的频率范围建议在100MHz和300MHz之间。
    如果频率低于100MHz，则未能满足最低性能指标，无法得到基本分10分；
    如果设计的最高运行频率高于300MHz，则在上方公式中也只会取300MHz作为最高频率。
