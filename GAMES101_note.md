# Timeline

> 计划为每周五节课+课后作业
>
> 课程链接：[Lecture 01 Overview of Computer Graphics_bilibili](https://www.bilibili.com/video/BV1X7411F744?p=1&vd_source=c34dccbda969c45c32e942a0bfb4bbab)
>
> 课后作业：http://games-cn.org/forums/topic/allhw/

- 第一周
  - [x] Lecture 01 Overview of Computer Graphics
  - [x] Lecture 02 Review of Linear Algebra
  - [x] Lecture 03 Transformation
  - [x] Lecture 04 Transformation Cont.
    - [x] Assignment 0
  - [x] Lecture 05 Rasterization 1 (Triangles)
    - [x] Assignment 1 透视投影
- 第二周
  - [x] Lecture 06 Rasterization 2 （Antialiasing and Z-Buffering）
  - [x] Lecture 07 Shading 1 (Illumination, Shading and Graphics Pipeline)
    - [x] Assignment 2 光栅化+抗锯齿
  - [x] Lecture 08 Shading 2 (Shading, Pipeline and Texture Mapping)
  - [x] Lecture 09 Shading 3 (Texture Mapping Cont.)
    - [x] Assignment 3 光照着色Blinn-Phong模型
  - [x] Lecture 10 Geometry 1 (Introduction)
- 第三周
  - [x] Lecture 11 Geometry 2 (Curves and Surfaces)
  - [x] Lecture 12 Geometry 3
    - [x] Assignment 4 贝塞尔曲线
  - [x] Lecture 13 Ray Tracing 1  (Whitted-Style Ray Tracing)
  - [ ] Lecture 14 Ray Tracing 2 (Acceleration & Radiometry)
  - [ ] Lecture 15
- 第四周
  - [ ] Lecture 16
  - [ ] Lecture 17
  - [ ] Lecture 18
  - [ ] Lecture 19
  - [ ] Lecture 20
- 第五周

  - [ ] Lecture 21

  - [ ] Lecture 22

---

复习知识点

- Rodrigues旋转公式
- 图形管线流程

# Course content

## Lecture 01 Overview of Computer Graphics

- 课程概览
  - 什么是CG
  - 为什么学习CG
  - 课程主题
  - 课程逻辑

## Lecture 02 Review of Linear Algebra

- A Swift and Brutal Introduction of Linear Algebra
  - Vectors
    - Addition
    - Multiplication
      - Dot(scalar)，<u>通过点乘光源和平面向量求出夹角、确定两个向量的接近程度</u>
      - Cross，满足$\vec x\times\vec y=\vec z$的坐标系为右手坐标系，<u>判定向量的左/右、内/外关系</u>，根据叉乘结果第三个轴上的正负来判断左右关系，通过判断左右进一步判断在几何形状中的内外关系
  - Matrices，向量的点乘/叉乘也可以写成矩阵形式
    - 通过变换矩阵改变摄像机视角

## Lecture 03 Transformation

- 3D transformation

  - modeling transformation
  - viewing transformation，涉及光栅化
  - 类比2D中对变换的操作，如齐次坐标系

- 2D transformation线性变换

  - scale缩放，即将几何图形上的每个点的坐标都分别乘以s倍
    $$
    \left[
    \begin{array}1
    x' \\
    y'
    \end{array}
    \right]
    =
    \left[
    \begin{array}2
    s & 0\\
    0 & s \\
    \end{array}
    \right]
    \left[
    \begin{array}3
    x \\
    y \\
    \end{array}
    \right]
    $$
    
  - reflect翻转，即分别将x与y坐标取反
  
  - shear切变， 在x方向上的切变
    $$
    \left[
    \begin{array}1
    x' \\
    y'
    \end{array}
    \right]
    =
    \left[
    \begin{array}2
    1 & a\\
    0 & 1 \\
    \end{array}
    \right]
    \left[
    \begin{array}3
    x \\
    y \\
    \end{array}
    \right]
    $$
  
  - rotate旋转，默认以原点为中心逆时针方向，旋转$\theta$角产生的变换，$R_{-\theta}=R_\theta^{-1}$
    $$
    R_\theta=
    \left[
    \begin{array}3
    \cos\theta & -\sin\theta\\
    \sin\theta & \cos\theta \\
    \end{array}
    \right]
    $$
  
- 非线性变换

  - 平移（**先应用线性变换，再平移**）
    $$
    \left[
    \begin{array}1
    x' \\
    y'
    \end{array}
    \right]
    =
    \left[
    \begin{array}2
    a & b\\
    c & d\\
    \end{array}
    \right]
    \left[
    \begin{array}3
    x \\
    y \\
    \end{array}
    \right]
    +
    \left[
    \begin{array}3
    t_x \\
    t_y \\
    \end{array}
    \right]
    $$

- homogeneous coordinates齐次坐标系下的线性变换与非线性变换

  - 2D点point：$(x,y,1)^T$

  - 2D向量vector：$(x,y,0)^T$

    > $v+v=v;p-p=v;p+v=p;p+p=p$，在保证向量平移不动性的同时保证了运算正确

  - 平移变换表示：
    $$
    \left[
    \begin{array}1
    x' \\
    y' \\
    \omega'
    \end{array}
    \right]
    =
    \left[
    \begin{array}2
    1 & 0 & t_x\\
    0 & 1 & t_y\\
    0 & 0 & 1
    \end{array}
    \right]
    \left[
    \begin{array}3
    x \\
    y \\
    1
    \end{array}
    \right]
    +
    \left[
    \begin{array}3
    x+t_x \\
    y+t_y \\
    1
    \end{array}
    \right]
    $$

  - 逆变换：原变换操作的逆过程，向量逐个左乘变换矩阵，也可以直接累乘变换矩阵后再乘以向量

- 复杂变换分解

  - 以几何形状的某个顶点为中心旋转&rarr;将该顶点平移到原点&rarr;绕原点旋转&rarr;将该顶点平移回原来位置

## Lecture 04 Transformation Cont.

- 3D transformation

  - 绕轴旋转
    $$
    R_x(\alpha)=
    \left[
    \begin{array}1
    1 & 0 & 0 & 0 \\
    0 & \cos\alpha & -\sin\alpha & 0\\
    0 & \sin\alpha & \cos\alpha & 0\\
    0 & 0 & 0 & 1
    \end{array}
    \right]
    R_y(\alpha)=
    \left[
    \begin{array}2
    \cos\alpha & 0 & \sin\alpha & 0 \\
    0 & 1 & 0 & 0\\
    -\sin\alpha & 0 & \cos\alpha & 0\\
    0 & 0 & 0 & 1
    \end{array}
    \right]
    R_z(\alpha)=
    \left[
    \begin{array}3
    \cos\alpha & -\sin\alpha & 0 & 0 \\
    \sin\alpha & \cos\alpha & 0 & 0\\
    0 & 0 & 1 & 0\\
    0 & 0 & 0 & 1
    \end{array}
    \right]
    $$

  - 任意旋转：将任意旋转分解为绕三个轴旋转的过程$R_{xyz}(\alpha,\beta,\gamma)=R_x(\alpha)R_y(\beta)R_z(\gamma)$，这里将$\alpha,\beta,\gamma$称为欧拉角Euler angles，即roll，pitch，yaw

  - Rodrigues旋转公式

  - Quaternions四元数，旋转的另一种表示方法，弥补了欧拉角在差值计算上的不足`如旋转20度的矩阵减去旋转10度的矩阵的结果不是旋转10度`

- View/Camera transformation, MVP(model, view, projection)

  - model transformation

  - view transformation

    |        相机参数        |   标记   |   描述   | 默认值 |
    | :--------------------: | :------: | :------: | :----: |
    |        Position        | $\vec e$ | 所在位置 | origin |
    | Look-at/Gaze direction | $\hat g$ | 面朝位置 |   -Z   |
    |           Up           | $\hat t$ | 侧朝位置 |   Y    |

    计算将某个相机位置变换到默认位置的矩阵时，可以计算默认位置到当前位置的矩阵，再取逆

    > 旋转矩阵是正交矩阵，$M_{view}^{-1}=M_{view}^T$

    视角的变换也可以转换为对其他所有物体的相对变换

- Projection transformation，将物体投影到$[-1,1]^3$中

  - Orthographic projection正交投影

    对于任意空间立方体$[l,r]\times[b,t]\times[f,n]$都可以映射为标准立方体$[-1,1]^3$`这里的f远和n近是延z轴负方向，越远值越小`

    - Translate，将立方体中心移到原点
    - Scale，缩放为标准立方体

  - Perspective projection透视投影

    - 欧氏几何中某平面的平行线在另一平面内的透视投影中是相交的；$(x,y,z,1)=(kx,ky,kz,k)=(xz,yz,z^2,z),k,z\neq0$

      - Squish，将透视投影转换为正交投影$M_{persp\rarr ortho}$

        > 近平面内所有点不会变化，远平面的z轴不会变化，远平面的中心点不会变化

        ![QQ截图20231015171150](D:\Tasks\图片\QQ截图20231015171150.png)

        - 利用相似三角形的性质缩放远平面的x，y坐标
        - 利用“近平面所有点不会变化，远平面中心点不会变化”两条性质求出矩阵$M_{persp\rarr ortho}$（第四行的正负影响画面翻转）
          $$
          M_{persp\rarr ortho}=
          \left[
          \begin{array}3
          \frac{n}{f} & 0 & 0 & 0\\
          0 & \frac{n}{f} & 0 & 0 \\
          0 & 0 & \frac{n+f}{f} & -n \\
          0 & 0 & -\frac{1}{f} & 0
          \end{array}
          \right]
          $$
          
      
      - 正交投影$M_{ortho}$
  
  <img src="D:\Tasks\图片\QQ截图20231015162140.png" alt="QQ截图20231015162140" style="zoom:67%;" />

## Lecture 05 Rasterization 1 (Triangles)

视锥参数：已知矩形参数l,r,b,t

- field-of-view (fovV)：透视投影中垂直的可视角度$\tan\frac{fovY}{2}=\frac{t}{|n|}$

- aspect ratio：长宽比r/t

将$[-1,1]^3$中的物体显示在屏幕上，即光栅化；单个像素pixel内颜色不会改变，由RGB三个值表示，位置由坐标(x,y)表示，中心位于(x+0.5,y+0.5)

- 视口变换：将立方体xy平面$[-1,1]^2$转换到$[0,width]\times[0,height]$，不考虑z轴。分别按长宽比例缩放后平移
  $$
  R_\theta=
  \left[
  \begin{array}3
  \frac{width}{2} & 0 & 0 & \frac{width}{2}\\
  0 & \frac{height}{2} & 0 & \frac{height}{2} \\
  0 & 0 & 1 & 0 \\
  0 & 0 & 0 & 1
  \end{array}
  \right]
  $$
  

光栅显示设备：

- Oscilloscope示波器：阴极射线管（Cathode Ray Tube，CRT），可以利用人眼的视觉暂留特性进行隔行扫描（这一次扫描奇数行，下一次扫描偶数行）

- Frame Buffer：将显存中的一块区域映射到显示器上

- Flat Panel Displays：

  > 计算器屏幕，手机屏幕

  - Liquid Crystal Display：LCD液晶显示器，通过液晶的排布影响光的极化（偏振）
  - Light emitting diode array：LED发光二极管
  - Electrophoretic Display：电子墨水屏

光栅显示：将多边形、网格顶点呈现到屏幕上

- 三角形特点：

  - 最基础的多边形，其他多边形都可以被拆分为三角形
  - 内部一定是平面，内外定义清晰
  - 给定顶点属性，可以推测出三角形内部任意点的渐变属性

- 三角形&rarr;像素：判断像素中心点与三角形的位置关系

  - sampling采样：将连续函数离散化，分别取离散值代入。定义二值函数inside(tri,x,y)=1代表点在三角形内部；判断函数：判断给定点$Q$是否都为三角形边向量的同一侧（都在向量$\vec{AB},\vec{BC},\vec{CA}$的左侧或右侧），根据与$Q$与顶点连线向量的叉乘方向判断。
  - edge cases：落在三角形边界上的像素点，自行判断归属
  - 确定需要检验的像素范围，而不是在填充三角形的过程中检验整个屏幕的像素

- 光栅化正方形像素会带来锯齿Jaggies问题，采样率对于信号来说不够高时信号会出现走样（如下图显示三角形时）

  | ![QQ截图20231016154423](D:\Tasks\图片\QQ截图20231016154423.png) | ![QQ截图20231016154637](D:\Tasks\图片\QQ截图20231016154637.png) |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |

##Lecture 06 Rasterization 2 (Antialiasing and Z-Buffering)

- Sampling：
  - Rasterization：采样2D坐标
  - Photograph：采样图像像素
  - Video：对时间采样
  - 采样artifacts：信号改变速度>采样速度
    - Jaggies锯齿，空间采样
    - Moire patterns摩尔纹，下采样时，如去掉奇数行和奇数列
    - Wagon wheel effect车轮效应，时间采样

- Antialiasing：在采样前进行滤波

  - Jaggies：采样前对图像做模糊处理，像素值从0/1离散值转变为连续值；先采样再做模糊处理会出现Blurred Aliasing

    <img src="D:\Tasks\图片\QQ截图20231019095926.png" alt="QQ截图20231019095926" style="zoom: 33%;" />

    <img src="D:\Tasks\图片\QQ截图20231019100153.png" alt="QQ截图20231019100153" style="zoom: 33%;" />

- Frequencies，如$\cos2\pi fx$中。参数$f=\frac{1}{T}$定义了函数的频率，$f$越大频率越高

  - Fourier Transform：分解成多个频率不同的正弦函数

    - 傅里叶奇数展开：任何周期函数都可以表达为正弦与余弦函数的线性组合。经过傅里叶展开，一个函数可以分解成不同频率的正弦

    - 傅里叶变换：给定时域函数$f(x)$经过傅里叶变换后，得到频域函数$F(\omega)=\int_{-\infin}^{\infin}f(x)e^{-2\pi i\omega x}dx$，再经过逆傅里叶变换可以得到原函数$f(x)$。

      > 简单理解时域函数为按时间x区分函数值，频率函数按频率区分函数

    <img src="D:\Tasks\图片\QQ截图20231019102705.png" alt="QQ截图20231019102705" style="zoom: 50%;" />

    > 高频信号被不充分采样后，采样结果可能显示出低频信号特征，即<u>**Aliases走样**</u>

  - Filtering：过滤特定频率

    - 时域图通过傅里叶变换转换为频域图（演示任何信号在不同频率的分布，离原点越远频率越高）

      <img src="D:\Tasks\图片\QQ截图20231019143115.png" alt="QQ截图20231019143115" style="zoom: 33%;" />

    - 过滤特定频率信号，右图通过逆傅里叶变换后得到左图

      | <img src="D:\Tasks\图片\QQ截图20231019150618.png" alt="QQ截图20231019150618" style="zoom:33%;" /> | <img src="D:\Tasks\图片\QQ截图20231019150651.png" alt="QQ截图20231019150651" style="zoom:33%;" /> |
      | :----------------------------------------------------------: | :----------------------------------------------------------: |
      |                           高通滤波                           |                           低通滤波                           |

    - Filtering = Convolution(= Averaging)：卷积也可视为一种加权平均

      对图像进行卷积操作等价于对源图像傅里叶变换得到的频域图乘以卷积核的频域图的结果经过逆傅里叶变换得到目标图像

      <img src="D:\Tasks\图片\QQ截图20231019151811.png" alt="QQ截图20231019151811" style="zoom:33%;" />

    - Sampling = Repeating Frequency Contents

      - 采样在频域上，就是重复原始信号的频谱。采样率不足时，相邻频谱会出现重合（走样Aliasing）

      - Antialiasing反走样

        <img src="D:\Tasks\图片\QQ截图20231019212429.png" alt="QQ截图20231019212429" style="zoom:33%;" />

        - 增加采样率

        - 模糊后采样，先进行低通滤波再进行采样

          <img src="D:\Tasks\图片\QQ截图20231019212625.png" alt="QQ截图20231019212625" style="zoom:33%;" />

          - 卷积，使用单像素box进行低通滤波，
          - 采样每个像素中心点
          
        - 像素值取连续的平均值（如一定比例的黑色），而不是二值
        
          - Multi-Sample Anti-Aliasing（MSAA）：计算单像素内平均值时，通过在像素内进一步采样均匀离散点，计算在三角形内部的点的占比来近似得到像素平均值
          - MSAA‘s cost：由于supersampling计算像素内平均值，增加了计算量
          
        - Fast Approximate AA（FXAA）：图像后处理，检测锯齿边界进行快速替换
        
        - Temporal（TAA）：复用上一帧数据
        
      - Super resolution / super sampling
      
        - Deep Learning Super Sampling（DLSS）

 ## Lecture 07 Shading 1 (Illumination, Shading and Graphics Pipeline)

- Visibility / occlusion

  - Painter's Algorithm: 按物体从后往前画，但实际情况更复杂
  - Z-Buffer：按像素绘制，每个像素记录每个物体的最小z-value，处理不了透明物体

- Shading着色：针对不同的物体应用不同的材质

  - **Blinn-Phong** Reflectance Model
    - Specular highlights：高光
    - Diffuse reflection：漫反射：光线被均匀反射到各个方向
      - Lambert's cosine law：根据光照方向与法向量夹角，计算着色点的单位面积上接收了多少能量
      - Light Fallof：点光源辐射，能量密度随距离衰减
    - Ambient lighting：间接光照

  计算局部一个点上的着色：视角方向$v$，平面法向量$n$，光照方向$l$，表面参数（color，shininess），不考虑阴影

  $L_d=k_d(I/r^2)\max(0,n\cdot l)$，$k_d$为漫反射颜色吸收系数（1为全吸收，0为不吸收全反射，为三通道系数时可定义着色点颜色），$I/r^2$为点光源辐射到达此处的能量

  漫反射的结果应与视角方向没有关系（向所有方向反射）

  <img src="D:\Tasks\图片\QQ截图20231028102629.png" alt="QQ截图20231028102629" style="zoom: 50%;" />

## Lecture 08 Shading 2 (Shading, Pipeline and Texture Mapping)

- Shading着色：针对不同物体应用不同材质

  - **Blinn-Phong** Reflectance Model
    - Specular highlights：高光，强度取决于视角是否接近镜面反射方向
    - Diffuse reflection
    - Ambient lighting：环境光照，不依赖于任何因素，设为某颜色常数，保证没有地方有阴影

  计算视角所接受到的高光：计算视角与光照方向的半程向量$h$与着色点平面法线的接近程度，来衡量高光强度

  $\textbf h=bisector(\textbf v, \textbf l)=\frac{\textbf v+\textbf l}{||\textbf v+\textbf l||}$

  $L_s=k_s(I/r^2)\max(0,\cos\alpha)^p=k_s(I/r^2)\max(0,\textbf n\cdot\textbf h)^p$，$k_s$为镜面反射系数，指数$p$圈定高光范围，增加范围外的衰减速度

  <img src="D:\Tasks\图片\QQ截图20231028144942.png" alt="QQ截图20231028144942" style="zoom:50%;" />

  $L_a=k_aI_a$，$k_a$为环境光系数

​	$L=L_a+L_d+L_s$![QQ截图20231028145451](D:\Tasks\图片\QQ截图20231028145451.png)

- Shading Frequencies着色频率
  - Flat shading平面着色：计算每个三角形的法线进行均匀着色
  - Gouraud shading顶点着色：计算每个顶点法线，利用插值计算着色三角形
    - 顶点法线=所有相邻面法线的加权平均$N_v=\frac{\sum_i w_iN_i}{||\sum_i w_iN_i||}$，$w_i$与相邻面面积有关
  - **Phong shading**像素着色：计算每个顶点的法线，利用插值计算三角形内部每个点的法线方向，再依据各自法线进行着色
    - Barycentric插值：使用重心坐标进行计算

<img src="D:\Tasks\图片\QQ截图20231028153524.png" alt="QQ截图20231028153524" style="zoom:50%;" />

- Graphics (Real-time Rendering) Pipeline图形/实时渲染管线，嵌入在硬件中

  - Input：输入三维空间中的一系列顶点
  - Vertex Processing：将顶点定位到屏幕空间[-1, 1]^3^中，生成顶点流，MVP变换，顶点着色
  - Triangle Processing：将三角形定位到屏幕空间中，生成三角形流
  - Rasterization：通过光栅化将空间中的三角形投影到屏幕上，生成Fragments流
  - Fragment Processing：对Fragment进行着色，判断可见
  - Framebuffer Operations：将Fragment对应到像素，最终显示出来

  <img src="D:\Tasks\图片\QQ截图20231028170455.png" alt="QQ截图20231028170455" style="zoom:50%;" />

- Shader程序

  - 编码vertex processing和fragment processing部分
  - Highly Complex 3D Scenes in Realtime：目前存在的集成式引擎，内含各类图形基础功能进行实时渲染
  - Graphics Pipeline Implementation: GPUs：图形管线的硬件实现，可编程着色器

- Texture Mapping纹理映射，定义任何一个点的不同属性

  > 不同的点有不同的漫反射系数，进而决定不同位置的颜色

  - 三维物体的表面都是二维的，纹理映射即将二维的纹理映射到三维物体的表面（一般约定为正方形）。其中3D物体到2D纹理的三角形映射关系默认已知（由美工处理）

  - 纹理坐标系：每个三角形对应到一个纹理坐标(u, v)，利用颜色可视化坐标位置，u越大越红，v越大越绿

    <img src="D:\Tasks\图片\QQ截图20231028164023.png" alt="QQ截图20231028164023" style="zoom: 50%;" />

- Interpolation Across Triagnles: Barycentric Coordinates
  - 根据顶点的纹理坐标，利用插值计算出三角形内部各点的纹理坐标

## Lecture 09 Shading 3 (Texture Mapping Cont.)

- Barycentric coordinates重心坐标：Interpolation across triangles，根据顶点属性，平滑地推断出三角形内部各点的属性

  - 建立Barycentric坐标系$(\alpha,\beta,\gamma),\alpha+\beta+\gamma=1$，三角形三个顶点为A, B, C，坐标$(x,y)=\alpha A+\beta B+\gamma C$

  - 已知坐标$(\alpha,\beta,\gamma)$可以推出该点所在位置世界坐标

    > 屏幕空间下推断出的重心坐标不能直接用于插值，需要推出其在三维空间下的重心坐标为透视插值矫正：
    >
    > 已知三角形顶点属性值$I_A,I_B,I_C$，深度值$z_A,z_B,z_C$，三角形内一点$P$屏幕空间重心坐标$\alpha,\beta,\gamma$
    >
    > - 求三维空间中$P$点深度值：
    >
    >   $z_P=\frac{1}{\frac{\alpha}{z_A}+\frac{\beta}{z_B}+\frac{\gamma}{z_C}}$
    >
    > - 求三维空间中$P$点属性值：
    >
    >   $I_P=(\alpha\frac{I_A}{z_A}+\beta\frac{I_B}{z_B}+\gamma\frac{I_C}{z_C})/\frac{1}{z_P}$

  - 已知该点位置世界坐标，可根据面积比值计算$(\alpha,\beta,\gamma)$坐标值，$S_{PBC}:S_{PAC}:S_{PAB}=\alpha:\beta:\gamma$

    <img src="D:\Tasks\图片\QQ截图20231028193253.png" alt="QQ截图20231028193253" style="zoom: 33%;" />

  - 三角形重心：$\alpha=\beta=\gamma=\frac{1}{3}$

    <img src="D:\Tasks\图片\QQ截图20231028194022.png" alt="QQ截图20231028194022" style="zoom: 33%;" />

  - 先在三维空间中做插值，再进行投影（投影会改变重心坐标）

- Applying texture

  - Texture Magnification：相对于模型，纹理的分辨率过小，此时像素所对应的纹素值texel并非整数

    - Nearest interpolation：对映射到的非整数，直接取最接近的texel值
    - Bilinear Interpolation：双线性插值（线性插值是两个点间的插值），对映射到的非整数，综合周围四个纹素点的位置进行插值
      - 1D：$lerp(x,v_0,v_1)=v_0+x(v_1-v_0)$
      - 2D：$u_0=lerp(s,u_{00},u_{10})\\u1=lerp(s,u_{01},u_{11})\\f(x,y)=lerp(t,u_0,u_1)$
    - Bicubic Interpolation：类似双线性插值，取周围14个纹素点进行插值

  - Texture Magnification (hard case)：相对于模型，纹理的分辨率过大，即采样率过低造成走样

    - Mipmap/Image pyramid：允许点查询/范围查询(fast, square, appro.)

      - 在加载纹理时，Mipmap对纹理进行$\log Size$次的预处理，得到各种level下的纹素均值

        <img src="D:\Tasks\图片\QQ截图20231028210623.png" alt="QQ截图20231028210623" style="zoom: 50%;" />

      - 实际查询中根据待查询范围所在level，直接得到对应平均值

      - 根据相邻像素点映射的纹素点间距离L来确定覆盖范围range所在层级$D=\log_2L$，进而确定对应纹素点的均值=D相邻整数层级$\lfloor D\rfloor,\lfloor D+1\rfloor$对应像素均值的插值，而在层级内部继续进行Bilinear插值，构成**Trilinear Interpolation三线性插值**

      ![QQ截图20231028205651](D:\Tasks\图片\QQ截图20231028205651.png)

      - 在图像远处会出现overblur现象，细节被模糊掉

    - Anisotropic Filtering各向异性过滤，独立缩小纹理的长宽，可以查询矩形区域

      <img src="D:\Tasks\图片\QQ截图20231028220434.png" alt="QQ截图20231028220434" style="zoom: 67%;" /><img src="D:\Tasks\图片\QQ截图20231028220623.png" alt="QQ截图20231028220623" style="zoom:50%;" />

    - EWA filtering：将不规则形状拆分为多个圆形进行查询

> 法线贴图，TBN矩阵

- Applications of textures

  - 在GPU中纹理可以视为memory + range query，即内存+点查询/范围查询

  - 环境贴图：来自于周围环境的所有光照，假设光源无限远，不会受位置影响

    <img src="D:\Tasks\图片\QQ截图20231104145338.png" alt="QQ截图20231104145338" style="zoom:50%;" />

    - Spherical Environment Map：将环境光记录在球面上，但展开成矩形时会出现扭曲现象

      <img src="D:\Tasks\图片\QQ截图20231104152053.png" alt="QQ截图20231104152053" style="zoom:33%;" />

    - Cube Map：使用球的外切立方体来记录环境光纹理映射，但会缺少某些方向的信息

      <img src="D:\Tasks\图片\QQ截图20231104152205.png" alt="QQ截图20231104152205" style="zoom:33%;" />

  - Bump Mapping法线贴图：纹理影响着色，通过凹凸贴图定义不同位置的不同属性，影响纹理沿法线方向的相对高度；在不改变基本几何形状的基础上，改变纹理高度即不同位置的法线方向来影响着色的明暗

    <img src="D:\Tasks\图片\QQ截图20231104153140.png" alt="QQ截图20231104153140" style="zoom:33%;" />

    - 定义局部坐标系，设原法线向量$n(p)=(0,0,1)$
    - 点$p$处求偏导
      - $\frac{\partial p}{\partial u}=c_1*[h(\textbf u+1)-h(\textbf u)] $
      - $\frac{\partial p}{\partial v}=c_2*[h(\textbf v+1)-h(\textbf v)] $
    - Perturbed nomal: $n=(-\frac{\partial p}{\partial u},-\frac{\partial p}{\partial v}, 1)$.normalized()

  - Displacement Mapping位移贴图：在凹凸贴图的基础上，真实的移动顶点位置改变几何形状，要求三角形足够细致，频率高于纹理细节（采样率足够）

    <img src="D:\Tasks\图片\QQ截图20231104155724.png" alt="QQ截图20231104155724" style="zoom:50%;" />

  - Dynamic Tessellation动态曲面细分（DirectX）：先进行法线贴图，后酌情对局部三角形做细分进而位移贴图

  - 三维纹理：利用3D噪声函数自动生成

  - Ambient Occlusion环境光遮蔽，实时计算阴影/预计算阴影贴图

  - 三维纹理和体积渲染


## Lecture 10 Geometry 1 (Introduction)

- Implicit隐式几何：满足特定关系的点集，如$x^2+y^2+z^2=1$，$f(x,y,z)=0$。并不能直观的推断出几何体形状，但便于表示、便于判断给定点与几何体的位置关系、易于计算光线与平面相交。

  - Algebraic Surfaces几何平面，使用数学公式但不直观，难以描述复杂模型

    <img src="D:\Tasks\图片\QQ截图20231104184626.png" alt="QQ截图20231104184626" style="zoom:33%;" />

  - Constructive Solid Geometry构造实体几何，基本几何的布尔运算

    <img src="D:\Tasks\图片\QQ截图20231104184552.png" alt="QQ截图20231104184552" style="zoom:33%;" />

  - Distance Functions距离函数，空间中任意一个点到物体表面的距离。将两个物体间的融合转换为各自距离函数SDF的融合，函数为0的位置即物体的表面

    <img src="D:\Tasks\图片\QQ截图20231104185502.png" alt="QQ截图20231104185502" style="zoom:33%;" />

  - Level Set Methods水平集方法，在水平集中根据函数值取平面；用于医学影像、物理模拟

    <img src="D:\Tasks\图片\QQ截图20231104185839.png" alt="QQ截图20231104185839" style="zoom: 50%;" />

  - Fractals分形、自相似

- Explicit显式几何：直接给出所有点的位置，或通过参数映射根据给定点集映射出目标点集$(x,v)\rarr(x,y,z)$，但难以判断给顶点与几何体的位置关系（内/外）

  - Point Cloud点云，利用点来表示物体，高密度的点构建面
  - Polygon Mesh多边形面，存储顶点
    - Wavefront Object File (.obj)格式，text文件定义顶点、法线、纹理坐标和顶点间的连接关系

## Lecture 11 Geometry 2 (Curves and Surfaces)

- Curves：摄像机路径，矢量字体

  - Bézier Curves贝塞尔曲线：通过控制点定义曲线，也可以视为一种插值方法

    - de Casteljau算法：给定三个点，如需要求$t=t_0$时刻点的位置，则分别求两条线上固定百分比处的点$\textbf b_0^1,\textbf b_1^1$，迭代进行二次连接后求出新线上百分比处的点$b_0^2$

      | <img src="D:\Tasks\图片\QQ截图20231104194859.png" alt="QQ截图20231104194859" style="zoom:33%;" /> | <img src="D:\Tasks\图片\QQ截图20231104195858.png" alt="QQ截图20231104195858" style="zoom: 33%;" /> |
      | ------------------------------------------------------------ | ------------------------------------------------------------ |

    - Bernstein多项式：$\textbf b^n(t)=\textbf b_0^n(t)=\sum\limits_{j=0}^n\textbf b_jB_j^n(t)$，是对1的多项式展开，即任意t时刻，$B_i^n(t)$的和都为1

      <img src="D:\Tasks\图片\QQ截图20231104204150.png" alt="QQ截图20231104204150" style="zoom:33%;" />

    - 性质

      - 终点和起点：$\textbf b(0)=\textbf b_0,\textbf b(1)=\textbf b_3$

      - 起始切线（对于四个控制点）：$\textbf b'(0)=3(\textbf b_1-\textbf b_0),\textbf b'(1)=3(\textbf b_3-\textbf b_2)$

      - Affine transformation仿射变换：对Bezier曲线做仿射变换等价于对控制点做仿射变换

        > 仿射变换区别于投影变换，仿射变换的过程中会保持直线间的直线性和各个点的顺序

      - Convex hull凸包性质：曲线一定在控制点形成的最小凸多边形内

    - Piecewise Bézier Curves：控制点数量多时，难以控制形状，因此采用逐段定义的方式（每段四个控制点），不同段之间的共用点上分别的起始切线方向相反且大小相等则能保证曲线**光滑**

      - $C^0$ continuity几何连续：$\textbf a_n=\textbf b_0$
      - $C^1$ continuity切线连续：$\textbf a_n=\textbf b_0=\frac{1}{2}(\textbf a_{n-1}+\textbf b_1)$，一阶导数连续

  - Splines样条：给定点集下构造的连续曲线，拥有一定的连续性

    - B-splines：Basis splines，基函数样条，对Bézier曲线的拓展，局部性，增强对曲线的控制能力
    - NURBS非均匀有理B样条

- Surfaces：

  - Bézier Surfaces贝塞尔曲面：由4x4个控制点，在两个方向上分别应用Bézier曲线（类似双线性插值），水平方向上的四个点得到曲线后，在特定时刻四条曲线上的点组成控制点得到竖直方向控制点。给定参数（u,v）得到曲面上的点

    <img src="D:\Tasks\图片\QQ截图20231104215123.png" alt="QQ截图20231104215123" style="zoom: 33%;" />

## Lecture 12 Geometry 3

- Mesh Operations: Geometry Processing几何操作

  - 网格细分subdivision：将一个网格细分为多个网格，得到更光滑的结果，实际为上采样

    分出更多三角形&rarr;调整三角形位置

    <img src="D:\Tasks\图片\QQ截图20231105150021.png" alt="QQ截图20231105150021" style="zoom: 33%;" />

    - Loop Subdivision (Triangle Mesh)，分别加权平均更新新/老顶点的位置

      | <img src="D:\Tasks\图片\QQ截图20231105152520.png" alt="QQ截图20231105152520" style="zoom:33%;" /> | <img src="D:\Tasks\图片\QQ截图20231105152743.png" alt="QQ截图20231105152743" style="zoom:50%;" /> |
      | ------------------------------------------------------------ | ------------------------------------------------------------ |
      |                                                              | <img src="D:\Tasks\图片\QQ截图20231105153125.png" alt="QQ截图20231105153125" style="zoom:33%;" /> |

    - Catmull-Clark Subdivision (General Mesh)，在Non-quad非四边形面中新引入的点为奇异点，经过细分后Non-quad面都被消除。第一次细分后引入Non-quad数量的奇异点，并消除所有Non-quad

      | ![QQ截图20231105154548](D:\Tasks\图片\QQ截图20231105154548.png) | ![QQ截图20231105154614](D:\Tasks\图片\QQ截图20231105154614.png) |
      | ------------------------------------------------------------ | ------------------------------------------------------------ |
      | ![QQ截图20231105155244](D:\Tasks\图片\QQ截图20231105155244.png) |                                                              |

      

  - 网格简化simplification：细分的逆过程，减少网格数量的同时保持形状，实际为下采样
    - Edge Collapsing：坍缩一条边，合并两个顶点，生成一个新顶点。通过计算局部最优解得到全局最优（贪心）
      - Quadric Error Metrics二次度量误差：
        - 新顶点的位置保证最小化到原本相邻面的距离平方和（L2距离）
        - 依次选择二次度量误差最小的边进行坍缩
      - 坍缩贪心流程
        - 计算每条边的二次度量误差并利用优先队列排序
        - 坍缩二次度量误差最小的边，并更新相邻边的二次度量误差和排序
        - 迭代循环

  - 网格正则化regularization：减小三角形间大小形状的偏差，都趋向接近

- Shadows，在光栅化的过程中绘制阴影，之前的着色过程中只考虑光源、视点和着色点之间的关系

  - Shadow mapping，一种Image-space算法，同时被光源与摄像机看到的点才不在阴影中，光源处的Shadow map分辨率较低时会出现走样现象

    - 点光源，硬阴影

      - Render from Light，记录看到的所有点的深度信息
      - Render from Eye，将看到的点投影回光源成像平面上计算深度，比较光源对应点深度图，深度一致则可视不一致则不可视

      <img src="D:\Tasks\图片\QQ截图20231105165139.png" alt="QQ截图20231105165139" style="zoom:33%;" />

    - 一定大小的光源，软阴影，模糊边界

      <img src="D:\Tasks\图片\QQ截图20231105173621.png" alt="QQ截图20231105173621" style="zoom:33%;" />

      - Umbra本影和Penumbra半影

        <img src="D:\Tasks\图片\QQ截图20231105173724.png" alt="QQ截图20231105173724" style="zoom: 50%;" />

## Lecture 13 Ray Tracing 1 (Whitted-Style Ray Tracing)

光栅化不善于解决全局效果（如软阴影，Glossy光泽反射，间接光照），光线追踪准确但很慢，光栅化实时但效果不好；采用离线渲染光线追踪的方法

- Light Rays
  - 光沿直线传播（实际上具有波的性质）
  - 光彼此不会发生碰撞（实际上波之间存在干扰）
  - 光从光源传播到摄像机（实际是可逆的过程）
  
- Ray Casting (Pinhole Camera Model)
  - 通过逐像素投射光线生成图像
  - 通过投射到光源来确认是否为阴影
  
- Recursive (Whitted-Style) Ray Tracing

  <img src="D:\Tasks\图片\QQ截图20231107152210.png" alt="QQ截图20231107152210" style="zoom: 33%;" />

  - Ray-Surface Interaction

    - 光线由原点和方向向量定义：$\textbf r(t)=\textbf o+t\textbf d\quad0\leq t\leq \infin$

    - 光线与球面的相交，Sphere $\textbf p$：$(\textbf p-\textbf c)^2-R^2=0$

      解$(\textbf o+t\textbf d-\textbf c)^2-R^2=0$，即求解一元二次方程，要求$t$为正实数

    - 光线与隐式表面相交，$\textbf p$：$f(\textbf p)=0$

      解$f(\textbf o+t\textbf d)=0$

    - 光线与显式表面（三角形）相交：

      1. 平面定义：平面上的一个点和法向量，$\textbf p$：$(\textbf p-\textbf p')\cdot\textbf N =0\quad ax+by+cz+d=0$

      2. 平面与光线相交：令$\textbf p=\textbf r(t)$

         解$(\textbf o+t\textbf d-\textbf p')\cdot\textbf N=0$，得到$t=\frac{(\textbf p'-\textbf o)\cdot\textbf N}{\textbf d\cdot\textbf N}\quad0\leq t<\infin$ 

      3. 判断交点是否在三角形内

      ---

      Moller Trumbore算法，将交点表示为重心坐标：$\textbf O+t\textbf D=(1-b_1-b_2)\textbf P_0+b_1\textbf P_1+b_2\textbf P_2$，求解n个式子n个变量的线性方程组，解$t,b_1,b_2$符合条件时（$0\leq t<\infin,b_1>0,b_2>0,1-b_1-b_2>0$）判定点在三角形内

    - 加速光线与平面求交过程，进一步判断光线与模型相交

      - Bounding Volumes，如果没有接触包围体则不会接触物体本身，接触包围体再测试是否解除物体本身

        <img src="D:\Tasks\图片\QQ截图20231107160517.png" alt="QQ截图20231107160517" style="zoom:33%;" />

      - Bounding Box，将长方体视为the intersection of 3 pairs of slabs，常用**轴对齐包围盒**（Axis-Aligned Bounding Box，**AABB**）

        <img src="D:\Tasks\图片\QQ截图20231107161003.png" alt="QQ截图20231107161003" style="zoom:33%;" />
        
      - 光线与AABB相交，2D情形下，分别计算与两个slab平面的交点$(t_\min,t_\max)$，计算两条线段的交集得到与AABB相交的区域
      
        <img src="D:\Tasks\图片\QQ截图20231107162155.png" alt="QQ截图20231107162155" style="zoom:50%;" />
      
      - 光线与AABB相交，3D情形下，光线进入AABB等价于进入所有三对slabs，离开AABB等价于离开任意一对slabs
      
        因此$t_{enter}=\max\{t_\min\},t_{exit}=\min\{t_\max\}$，即光线进入的时间为所有slabs中最迟进入的时间，光线离开的时间为所有slabs中最早离开的时间，$t_{enter}<t_{exit}$即判定光线与AABB相交
      
      - 时间$t$为负的情形
      
        - $t_{exit}<0$，box在光线后
        - $t_{exit}\ge0,\quad t_{enter}<0$，光线原点在box内，一定有交点
      
      - **总结光线与AABB相交当且仅当**：$t_{enter}<t_{exit}\&\&t_{exit}\ge 0$，相比于一般情况，轴对齐包围盒在计算上更加方便（直接取值x、y、z）

## Lecture 14 Ray Tracing 2 (Acceleration & Radiometry)

Using AABBs to accelerate ray tracing

- Uniform Spatial Partitions (Grids)

  - 预处理阶段建立加速网格
    - 找到bounding box
    - 划分格子
    - 标记与物体相交的格子
  - Ray-Scene相交
    - 按光线传播顺序遍历经过的网格
    - 对每个网格先检测是否有物体相交，再检测与物体是否相交
  - Grid Resolution
    - 格子不能太稀疏也不能太密集

  <img src="D:\Tasks\图片\QQ截图20231107193618.png" alt="QQ截图20231107193618" style="zoom: 33%;" />

- Spatial Partitions

  - Examples

    - Oct-Tree八叉树，将场景分割为八份，对与物体相交的区域递归分割直至区域内物体数量足够少；但数量随维度上升而增加
    - KD-Tree，只将场景分割为两份，依次不同维度划分
    - BSP-Tree，并非轴对齐，高维情况下难以计算

    <img src="D:\Tasks\图片\QQ截图20231107193730.png" alt="QQ截图20231107193730" style="zoom: 33%;" />

  - KD-Tree加速预处理，内部结点存储划分的轴向、划分位置、子节点位置，叶子结点存储相交的几何形体列表；难点在于难以判断几何形体与格子的相交关系，一个物体可能同时存在多个叶子结点中

    <img src="D:\Tasks\图片\QQ截图20231107195553.png" alt="QQ截图20231107195553" style="zoom: 33%;" />

- Object Partitions & Bounding Volume Hierarchy (BVH)，按物体划分而不是按空间划分，尽量减少重叠，一个物体只会出现在一个叶子结点中

  - 划分流程
    - 找到bounding box
    - 递归地将物体集合分为两个子集，并重新计算子集bounding box
    - 直至达到指定条件
  - 划分方法
    1. 按维度依次划分
    2. 选最长的维度划分
    3. 选处于中间位置的物体进行划分
  - 数据结构，结点表示视野中所有物体的子集
    - 内部结点：存储Bounding box，子节点
    - 叶子结点：存储Bounding box，物体列表
  - 光线求交过程
    - 递归的判断光线是否与结点bounding box相交，直至叶子结点
    - 判断是否与叶子结点中每个物体相交

  <img src="D:\Tasks\图片\QQ截图20231107203442.png" alt="QQ截图20231107203442" style="zoom: 33%;" />

  |      |                      Spatial Partitions                      |                       Object Partition                       |
  | :--: | :----------------------------------------------------------: | :----------------------------------------------------------: |
  | 特点 | 按空间区域划分，每个grid记录所包含的物体，光线与grid相交则递归判断是否与物体相交；一个物体可能被多个grid包含，且难以判断是否被grid包含 | 按物体划分，bounding box可能重叠，一个物体只会在一个box内，box范围取决于物体所以无需判断box与物体的相交关系 |

---

Basic radiometry辐射度量学，描述光照（Radiant flux辐射通量，intensity强度，irradiance辐射度，radiance辐射）

- Motivation

  - Blinn-Phong模型中对光强的定义light.intensity = 10的数字是什么含义，单位换算与衰减过程并不精确
  - Whitted style光追的效果并不真实

- Radiant Energy and Flux (Power)

  - Radiant energy辐射能是电磁辐射能量，单位是焦耳$Q[J=Joule]$
  - Radiant flux (power)辐射通量是单位时间内发射、反射、通过的能量，单位是瓦特$\Phi\equiv\frac{dQ}{dt}\quad[W= Watt][lm=lumen]^*$

- Light Measurements

  - Radiant Intensity：辐射强度是单位立体角内由点光源发出的能量，单位是candela，$I(\omega)\equiv\frac{d\Phi}{d\omega}\quad[\frac{W}{sr}][\frac{lm}{sr}=cd=candela]$，其中sr为立体角的单位，各向同性点光源下光强$I=\frac{\Phi}{4\pi}$

    > 立体角：在三维空间中的角度定义为在球面上覆盖的面积/球面总面积，$\Omega=\frac{A}{r^2}$，**球面有$4\pi$立体角**（对比与二维情形下的弧度定义，角度覆盖的弧长/圆周长）
    >
    > <img src="D:\Tasks\图片\QQ截图20231107215254.png" alt="QQ截图20231107215254" style="zoom:33%;" />
    >
    > 微分立体角：依靠一个轴向定义两个角度$\theta,\phi$，决定向量方向$\omega$，计算单位矩形的面积$dA=(rd\theta)(r\sin\theta d\phi)=r^2\sin\theta d\theta d\phi$，$d\omega=\frac{dA}{r^2}=\sin\theta d\theta d\phi$，经过球面积分后$\Omega=\int_0^{2\pi}\int_0^\pi\sin\theta d\theta d\phi=4\pi$
    >
    > <img src="D:\Tasks\图片\QQ截图20231107215625.png" alt="QQ截图20231107215625" style="zoom:33%;" />

  - Irradiance

  - Radiance

  <img src="D:\Tasks\图片\QQ截图20231107214007.png" alt="QQ截图20231107214007" style="zoom: 33%;" />

