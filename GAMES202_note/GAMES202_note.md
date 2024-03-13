# Timeline

- 第一周
  - [x] Lecture 01 Introduction and Overview
  - [x] Lecture 02 Recap of CG Basics
    - [x] Assignment 0 配置glsl环境，vscode
  - [x] Lecture 03 Real-time Shadows 1
  - [x] Lecture 04 Real-time Shadows 2
    - [x] Assignment 1 完成PCSS
  - [x] Lecture 05 Real-time Environment Mapping
- 第二周
  - [x] Lecture 06 Real-time Environment Mapping (Precomputed Radiance Transfer)
  - [x] Lecture 07 Real-time Global Illumination (in 3D)
    - [ ] Assignment 2 PRT预计算和实际渲染
  - [x] Lecture 08 Real-time Global Illumination (screen space)
    - [ ] Assignment 3 SSR实现
  - [x] Lecture 09 Real-time Global Illumination (screen spcae cont.)
  - [x] Lecture 10 Real-time Physically-based Materials (surface models)
- 第三周
  - [ ] Lecture 11
  - [ ] Lecture 12
  - [ ] Lecture 13
    - [ ] Assignment 4 Kulla-Conty实现
  - [ ] Lecture 14

---

复习：

- PCSS和VSSM

# Course content

## Lecture 01 Introduction and Overview

**Real-Time High Quality Rendering**

- 速度：实时渲染每秒超过30帧，低于30帧为interactive
- 互动：实时互动
- 真实感：先进方法保证渲染更加真实
- 渲染：将3D场景渲染成图像
- Topic：阴影，全局光照，基于物理着色，实时光线追踪，参与介质渲染，图像空间反射，非真实感渲染NPR，时域抗锯齿TAA和超采样

---

**Science != Technology**

Science = Knowledge, Technology = engineering skills

实时渲染=fast&approximate离线渲染+系统性工程

工业界的实时渲染技术远超学术界

---

**Motivation**

真实渲染非常慢，需要在合理近似下追求最快速度

---

**Evolution**

1997年最终幻想7，1999年cs，2009年刺客信条2，生化危机5，2018年战神

技术里程碑：<u>可编程图形管线</u>带来质的改变，<u>预计算方法</u>（球面谐波函数），<u>interactive光追</u>（CUDA+OptiX，cuda计算后经过快速降噪）

## Lecture 02 Recap of CG Basics

**Graphics Pipeline**

- Vertex Processing：输入3D空间的一堆顶点，输出Vertex流；MVP
- Triagnle Processing：根据顶点在屏幕空间的分布，输出Triangle流
- Rasterization：根据三角形在屏幕空间的分布，输出Fragment流；抗锯齿
- Fragment Processing：对Fragments进行着色，输出Shaded Fragments；纹理映射
- Framebuffer Operations：写入Framebuffer显示图像

**OpenGL**

CPU端一系列调动GPU管线的APIs，跨平台，与语言无关，其他（DirectX只支持Windows，Vulkan等）

版本非常碎片化，C语言风格

油画过程：

- 摆放物体：确定模型`Vertex buffer object (VBO)，将模型的顶点、法线、纹理坐标包装成VBO送入GPU`，模型变换`glTranslate, glMultMatrix等GpenGL函数`
- 摆放画架：视图变换`gluPerspective`，创建framebuffer
- 贴上画布：使用同一画架可以画出多幅图像`Multiple rendering target`，一个framebuffer可以输出不同纹理`pass一趟渲染`，将不同的内容属性传递到后续fragments中
- 画布绘画：使用vertex / fragment着色器，针对顶点为每个像素生成fragment，进行自定义MVP操作和深度z-buffer depth测试
- 贴另外的画布继续画

总结：

- 确定物体、相机、MVP、插值对象等
- 确定framebuffer和输入输出纹理
- 确定vertex / fragment着色器
- 渲染

Multiple pass多趟渲染

**OpenGL Shading Language (GLSL)**，vertex+fragment

在GPU上写汇编&rarr;开发一门语言

DirectX中的High Level Shading Language(HLSL)，vertex+pixel

初始化：创建着色器，编译着色器，将着色器添加到program，建立彼此链接，使用program

**The Rendering Equation**，渲染中最重要的等式

描述光路：$L_o(p,w_o)=L_e(p,w_o)+\int_{H^2}f_r(p,w_i\rarr w_o)L_i(p,w_i)\cos\theta_idw_i$`光线=自发光+BRDF*入射光`

real-time rendering (RTR)中BRDF在原BRDF的基础上加了$\cos\theta_i$权重：$L_o(p,w_o)=\int_{\Omega^+}L_i(p,w_i)f_r(p,w_i,w_o)\cos\theta_iV(p,w_i)dw_i$`光线=入射光*BRDF*能见度`

环境光照：表示来自所有方向的入射光——cube map或sphere map

传入fragment前使用伽马矫正（调整图像亮度从而改善在不同设备上的表现，显示器的亮度响应不是线性而是非线性，伽马矫正先后进行乘方和反向乘方运算）

> 关键字：
>
> - attribute：结构属性
>
> - uniform：全局属性
> - varying：要被插值的属性
>
> 其中attribute和uniform关键字修饰的变量由Material类构造器和loadOBJ初始化中传值

## Lecture 03 Real-time Shadows 1

**Shadow Mapping**

2-Pass（light pass + camera pass），light pass生成Shadow map（SM），camera pass利用SM；图像空间算法，生成SM后不再需要场景图形，但存在自遮挡和走样情况

|              light pass：从光源出发生成深度纹理              |               camera pass：从眼睛出发渲染图像                |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| <img src="D:\Tasks\GAMES_note\images\20231129015812.png" alt="20231129015812" style="zoom:33%;" /> | <img src="D:\Tasks\GAMES_note\images\20231129015730.png" alt="20231129015730" style="zoom:33%;" /> |

- 自遮挡：light pass生成深度纹理的过程中，会因精度问题出现自遮挡现象，在光源与法线垂直（平行于地面）时最明显；每个像素都记录一个深度，出现下方地板的深度被上方地板遮挡的情况，导致判断该处被遮挡

  <img src="D:\Tasks\GAMES_note\images\20231129101055.png" alt="20231129101055" style="zoom:33%;" />

  - 添加可变的bias减小自遮挡效果（同比EPSILON的效果），容忍深度差值在一定范围内的遮挡，但会出现detached shadow问题（即实际模型的部分阴影缺失）
  - second-depth shadow mapping

- 实时渲染不相信复杂度

- 走样：two-pass的采样率不足

**Inequalities in Calculus**

<img src="D:\Tasks\GAMES_note\images\20231129141719.png" alt="20231129141719" style="zoom:33%;" />

RTR更关心近似相等：$\int_\Omega f(x)g(x)dx\approx\frac{\int_\Omega f(x)dx}{\int_\Omega dx}\cdot\int_\Omega g(x)dx$，左边分母为归一化常数，仅当$g(x)$积分域有限且起伏不大时可视为近似

对渲染方程做近似：$L_o(p,w_o)=\int_{\Omega^+}L_i(p,w_i)f_r(p,w_i,w_o)\cos\theta_iV(p,w_i)dw_i\approx\frac{\int_{\Omega^+}V(p,w_i)dw_i}{\int_{\Omega^+}dw_i}\cdot\int_{\Omega^+}L_i(p,w_i)f_r(p,w_i,w_o)\cos\theta_idw_i$

即visibility * shading，仅当积分域有限（点光源/方向光源），起伏不大（diffuse BSDF/恒定radiance面光源），因此glossy BRDF并不合适，公式同样可见于环境光遮蔽Ambient Occlusions中

**Percentage closer soft shadows (PCSS)**

Shadow mapping生成硬阴影，面光源会导致软阴影的形成

|                    硬阴影（Hard Shadows）                    |                    软阴影（Soft Shadows）                    |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| <img src="D:\Tasks\GAMES_note\images\20231129151049.png" alt="20231129151049" style="zoom:33%;" /> | <img src="D:\Tasks\GAMES_note\images\20231129151113.png" alt="20231129151113" style="zoom:33%;" /> |

- Percentage Closer Filtering (PCF)

  - 起初用于阴影边缘的反走样Antialiasing
  - 后用于软阴影生成，对阴影比较结果做滤波（不是对shadow map做滤波，而是对two-pass后得到的0/1图做滤波）
  - 原本对于一个像素，只取一个fragment的比较结果（0/1）来判断是否处于阴影，现在一个像素取一个7x7范围的fragment比较结果作平均（0~1）visibility以判断阴影程度

    > 类似与MSAA，PCF是在生成shadow map时在阴影纹理上根据位置不同进行适应性大小的超采样

  滤波器的size越大，阴影的边缘越平滑，足够大时能够达到软阴影的效果，需要确定不同位置的size大小

  | <img src="D:\Tasks\GAMES_note\images\20231129153512.png" alt="20231129153512" style="zoom: 50%;" /> | <img src="D:\Tasks\GAMES_note\images\20231129153725.png" alt="20231129153725" style="zoom:33%;" /> |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |

  Filter size与blocker distance相关，即遮挡物与阴影平面的距离

  $w_{Penumbra}=(d_{Receiver}-d_{Blocker})\cdot w_{Light}/d_{Blocker}$

- Percentage closer soft shadows (PCSS)

  - Blocker search：在light pass中得到一定区域遮挡物的平均深度（遍历小于着色点深度的遮挡物）

    > 这里确定滤波器size之前又要确定遮挡物平均深度图的区域size，这里的大小取决于<u>光源面积</u>以及阴影平面<u>receiver到光源的距离</u>

  - Penumbra estimation：根据遮挡物平均深度确定滤波器size，在已知遮挡物深度的情况下，问题转化为PCF问题

  - Percentage Closer Filtering

## Lecture 04 Real-time Shadows 2

**PCSS**：$V(x)=\sum\limits_{q\in\mathcal N(p)}w(p,q)\cdot\Chi^+[D_{SM}(q)-D_{scene}(x)]$

**Filtering in shadow map**：$\Chi^+\{[w\ast D_{SM}](q)-D_{scene}(x)\}$

**Filtering in result image**：$\sum\limits_{x\in\mathcal Y}w(p,q)V(q)$

PCSS的速度限制在于第一步和第三步对区域遍历的耗时，这里使用区域采样来替代遍历结果，再降噪

---

**Variance Soft Shadow Mapping (VSSM)**

相比于PCSS对纹素的遍历，VSSM利用正态分布得到近似值

- 计算区域内均值
  - MIPMAP
  - Summed Area Tables (SAT)
- 计算区域内方差
  - $Var(X)=E(X^2)-E^2(X)$，在生成shadow map的同时生成一个square-depth map

<img src="D:\Tasks\GAMES_note\images\20231129200614.png" alt="20231129200614" style="zoom: 50%;" />

利用建立起的概率分布函数来计算处于某个深度以下所占比例（Cumulative Distribution Function, CDF），来确定该平均深度blocker下不同深度纹素的阴影程度，不再需要采样

利用切比雪夫不等式$P(x>t)\le\frac{\sigma^2}{\sigma^2+(t-\mu)^2}$，用近似值代替得到相近效果（方差较小且filter size较小，不够小的情况下细分filter size后分别进行近似和取样操作）

- 生成shadow map
  - 同步记录square depth map
  - 利用MIPMAP或SAT
- Run time
  - 获得区域内平均深度O(1)，平均平方深度O(1)，切比雪夫O(1)计算出visibility

对于<u>第一步</u>blocker search中记录区域中深度小于着色点深度的blocker平均深度z~occ~

对于<u>第三步</u>直接计算出深度大于着色点深度的比例，得到可见度

假设区域内总计$N$个blocker中有$N_1$个深度大于着色点，$N_2$个深度小于着色点

$\frac{N_1}{N}z_{unocc}+\frac{N_2}{N}z_{occ}=z_{Avg}$，可得到区域内的平均深度，而其中$\frac{N_1}{N}=P(x>t)$可以利用切比雪夫不等式进行近似操作，将$z_{unocc}$非遮挡物的平均深度认为等价于着色点深度$t$

- MIPMAP：快速得到一个区域内的均值，但不够准确
- Summed Area Table (SAT)：利用前缀和计算出固定区域内的数值和，进而将下图拓展到二维情况，复杂度O(mn)，其中行与行/列与列之间的计算可以并行

<img src="D:\Tasks\GAMES_note\images\20231129213851.png" alt="20231129213851" style="zoom:33%;" />

![20231129214638](D:\Tasks\GAMES_note\images\20231129214638.png)

**Moment Shadow Mapping**

VSSM的高斯分布并不总是准确，在一些不符合正态分布的场景下（多峰值）会出现较大偏差，切比雪夫不等式也存在误差；偏暗的误差能够容忍，但不能容忍偏白的误差（light leaking光漏）

使用更高阶的moments来表示分布，得到更准确的结果

- VSSM使用了两阶的moments矩（EX^2^）
- 保留前m阶的矩时能够表示$\frac{m}{2}$个台阶，因此通常使用四阶函数

使用四阶的Moment shadow mapping效果更好，解决了VSSM的漏光现象，但计算更加耗时

## Lecture 05 Real-time Environment Mapping

**Distance Field Soft Shadows**

对于任意一个点都给出一个到其他物体的最小距离（带符号无方向），**有符号距离场**（Signed Distance Field, SDF），**最优传输**（Optimal Transport）

<img src="D:\Tasks\GAMES_note\images\20231213145708.png" alt="20231213145708" style="zoom:33%;" />

同GAMES101的Lecture10中的插值应用，在运动blender中利用距离函数得到中间态；同理也可以blender任意距离函数

<img src="D:\Tasks\GAMES_note\images\20231213150234.png" alt="20231213150234" style="zoom: 33%;" />

- Ray marching，在SDF中进行光线追踪判断相交

  之前的<u>路径追踪</u>中判断光线相交是通过bvh的box来判断空间直线的相交情况，而在使用<u>距离场</u>的情形下是以光线为视角根据所在点的SDF数值作为步长（某一点SDF的数值代表与其他物体的最小距离，即代表该距离内没有物体），向光线方向移动该步长后递归过程直至到达物体表面（SDF=0）

- 软阴影生成，决定遮挡范围

  根据SDF的值计算出安全的偏转角度，该偏转角度越小则可见度越小，$\frac{k\cdot SDF(p)}{||p-o||}$其中k决定阴影的软硬程度

  | <img src="D:\Tasks\GAMES_note\images\20231213160139.png" alt="20231213160139" style="zoom:33%;" /> | <img src="D:\Tasks\GAMES_note\images\20231213160658.png" alt="20231213160658" style="zoom:33%;" /> |
  | :----------------------------------------------------------: | :----------------------------------------------------------: |
  
- 无限分辨率字体生成

  将字体矢量轮廓转换为SDF表示，利用插值平滑处理边缘

- 优缺点：

  - 优点：快速，高质量

  - 缺点：预计算，大量存储空间（可以利用hierarchy节省空间）

---

**Environment Lighting**

存储方式：球形和正方形纹理；Image-Based Lighting (IBL)，适用于无限远光源，据此渲染一个点，之前的方法采取蒙特卡洛采样，对每个shading point进行大量采样非常慢，现在将采样过程转换为查询该点指定方向环境光模糊纹理

同样使用近似，其中$\Omega$为滤波器立体角范围，相当于在此范围内做滤波操作，使查询得到模糊结果相当于对未模糊纹理上多次采样

$L_o(p,w_o)=\int_{\Omega^+}L_i(p,w_i)f_r(p,w_i,w_o)\cos\theta_i dw_i\approx\frac{\int_{\Omega^+}L_i(p,w_i)dw_i}{\int_{\Omega^+}dw_i}\cdot\int_{\Omega^+}f_r(p,w_i,w_o)\cos\theta_idw_i$

<img src="D:\Tasks\GAMES_note\images\20231213193250.png" alt="20231213193250" style="zoom:33%;" />

<u>The Split Sum</u>：

- 分离出的左式由采样计算转换为查询纹理
- 分离出的右式进一步使用Schlick近似，将问题维度由三维降为二维，预计算为纹理形式存储（同一种材质同一张图），同样将采样转换为查询$\int_{\Omega^+}f_r(p,\omega_i,\omega_o)\cos\theta_id\omega_i\approx R_0\int_{\Omega^+}\frac{f_r}{F}(1-(1-\cos\theta_i)^5)\cos\theta_id\omega_i+\int_{\Omega^+}\frac{f_r}{F}(1-\cos\theta_i)^5\cos\theta_id\omega_i$

> $R(\theta)=R_0+(1-R_0)(1-\cos\theta)^5,\quad R_0=\big(\frac{n_1-n_2}{n_1+n_2}\big)^2$，其中R0为基础反射率
>
> $f=\frac{F\cdot G\cdot D}{4(n,i)(n,o)}$

<img src="D:\Tasks\GAMES_note\images\20231213214811.png" alt="20231213214811" style="zoom:33%;" />



## Lecture 06 Real-time Environment Mapping (Precomputed Radiance Transfer)

**Shadow from Environment Lighting**

难以处理环境光阴影问题，环境光视为多光源，而多光源求解阴影十分复杂，使用环境光遮蔽会出现不真实的结果

工业方案：以一个主要光源生成阴影

Precomputed radiance transfer

将乘积的积分$\int_\Omega f(x)g(y)\mathrm dx$视为滤波，本质上也是向量点乘，低频==函数平滑 / 变化起伏较小，且只保留两个函数中较低频的部分（一函数比另一函数高频的部分不会被保留）

> 基函数：$f(x)=\sum\limits_i c_i\cdot B_i(x)$，如函数$f(x)$可以被分解为一系列函数$B_i(x)$，如傅里叶序列，多项式序列

---

- Real-time environment lighting

  - Spherical Harmonics球面谐波函数（SH）

    一系列2维定义在球面的基函数$B_i(\omega)$，类比一维的傅里叶级数，每个基函数都由Legendre勒让德多项式构成，系数$c_i=\int_\Omega f(\omega)B_i(\omega)d\omega$（利用基函数间的正交性直接对原函数滤波得到），当然该积分过程耗费时间所以实际使用中也放在预计算过程中

    > 对基函数构成的函数$f(x)$求基函数$B_i(x)$系数的过程称为投影，利用此性质可以对函数$f(x)$进行重建
    >
    > 如只利用前四阶的SH进行重建，则重建出频率最高为四阶的部分，高频部分则被丢弃，同比傅里叶级数中级数越高越接近

    <img src="D:\Tasks\GAMES_note\images\20231214152504.png" alt="20231214152504" style="zoom:33%;" />

    由此推到BRDF函数上，也可以对shading point处的BRDF利用球面谐波函数描述（给定出射方向，以入射方向为自变量则BRDF函数可以定义在球面上，不过diffuse材质是视角无关的），对于diffuse材质的BRDF可以视为低通滤波器，与环境光乘积积分，为适应BRDF的低频环境光的SH也只需要使用前三阶的部分

  - Precomputed radiance transfer (diffuse)

    将渲染方程分为两部分lighting*light transport，将lighting部分L(i)表述为基函数形式，而light transport在给定场景下不变也可表述为基函数形式$L(o)=\rho\int\limits_\Omega L(i)V(i)\max (0,\textbf n\cdot\textbf i)\mathrm d\textbf i\approx\rho\sum l_i\int\limits_\Omega B_i(i)V(i)\max (0,\textbf n\cdot\textbf i)\mathrm d\textbf i\approx \rho\sum l_iT_i$，将light transport部分作为在基函数空间中被投影的对象，**最终将预计算过程转换为两个向量的点乘**

  最终渲染/阴影过程从查询操作简化为两个向量之间的点乘，在预计算过程中根据环境光贴图和阴影贴图分别计算向量（固定相机位置），实际使用中进行点乘

## Lecture 07 Real-time Global Illumination (in 3D)

**Precomputed Radiance Transfer (PRT)**

- Diffuse case

  $L_o(p,\omega_o)=\int_{\Omega^+}L_i(p,\omega_i)f_r(p,\omega_i,\omega_o)\cos\theta_iV(p,\omega_i)\mathrm d\omega_i=\sum\limits_p\sum\limits_qc_pc_q\int_{\Omega^+}B_p(\omega_i)B_q(\omega_i)\mathrm d\omega_i$

  由于其正交性，二维求和会被降为一维（即只计算p=q的情况）

- Glossy case

  $L(\textbf o)=\int\limits_{\Omega}L(\textbf i)V(\textbf i)\rho(\textbf i,\textbf o)\max(0,\textbf n\cdot\textbf i)d\textbf i\approx \sum l_iT_i(\textbf o)$，将渲染转换为vector-matrix的乘法，存储激增

  <img src="D:\Tasks\GAMES_note\images\20231215104718.png" alt="20231215104718" style="zoom:50%;" />

**Interreflections and Caustics**

Interreflections：LE，LGE，LGGE...，在环境光中考虑反射次数，runtime与transport复杂度无关，transport复杂度只影响预计算过程

<img src="D:\Tasks\GAMES_note\images\20231215110035.png" alt="20231215110035" style="zoom:33%;" />

caustics（焦散）

<img src="D:\Tasks\GAMES_note\images\20231215110148.png" alt="20231215110148" style="zoom:33%;" />

球面谐波函数SH基本只适合描述低频的函数，预计算PRT要求静态场景、静态材质

**Haar Wavelet**

一系列基函数，2D定义在图像块上，投影时很多基函数的系数都为0，存储时只存系数最大的n个项系数进行非线性近似，支持全频率的表示，但不支持旋转光照，需要重新计算

描述平面的基函数&rarr;使用cubemap表示环境光

<img src="D:\Tasks\GAMES_note\images\20231215150441.png" alt="20231215150441" style="zoom:33%;" />

---

**Real-time Global Illumination (in 3D)**

任何被光源直接照射的点作为次级光源

- **Reflective Shadow Maps (RSM)**

  - 使用次级光源照亮着色点p需要知道哪些信息：

    - 有哪些表面被直接照射，其中像素可以视为一个个表面

    - 这些表面对p的影响如何加权

  - 需要在点p位置不定的情况下确认次级光源的影响，假定反射物的材质都为diffuse

  - 难以计算着色点对次级光源的可见性（复杂度n^2^），这里不算了

  - RSM中并非考虑所有像素的影响，根据可见性（难），朝向和距离。采用采样方式加速进程

  - 需要存储的顶点信息：深度、世界坐标、法线、通量flux

  - 优缺点

    - 优点：易于实现
    - 缺点：复杂度依赖于光源个数，无法考虑可见性

- **Light Propagation Volumes (LPV)**

  - 计算任意着色点处的间接光照，都需要知道该点处来自任意方向的光照
    - 假设radiance沿直线传播且不变（平方衰减的是intensity）
    - 使用3D格子（体素）传播radiance
  - 实现
    - 确认哪些点接收直接光照而成为次级光源（RSM）
    - 将次级光源的光照传入场景中的点云（2阶SH记录格子中向外传播的radiance）
    - 在场景中传播radiance（根据每个格子的SH向指定方向传播radiance，四五次传播后稳定）
    - 渲染（对任意着色点，得到所在格子的SH）
  - 缺陷
    - 漏光，遮挡物的宽度小于格子时无法阻挡光线

- **Voxel Global Illumination (VXGI)**

  - two-pass

    - light pass：存储每个体素中的间接光照分布和法线分布

    <img src="D:\Tasks\GAMES_note\images\20231218102927.png" alt="20231218102927" style="zoom:33%;" />

    - camera pass：对于glossy材质，向反射方向追踪cone，基于反射方向上不同大小的cone分别在建立好的体素层次结构中找到对应，并计算对应radiance贡献；对于diffuse材质，则需要追踪数个cone

    <img src="D:\Tasks\GAMES_note\images\20231218103143.png" alt="20231218103143" style="zoom:33%;" />

  - 效果很好，开销很大

## Lecture 08 Real-time Global Illumination (screen space)

全局光照前屏幕空间中获取的信息，对应Image space

**Screen Space Ambient Occlusion (SSAO)** 环境光遮蔽AO

增强物体立体感，易于实现，对于全局光照的近似

- 在不知道间接光照的情况下，假设任意着色点所有方向上的间接光照为一个常数（布林冯着色模型中应用）

- 但并不是所有方向上的作为常数的间接光照都能被接收visibility function

  > 分离可见项$L_o(p,w_o)=\int_{\Omega^+}L_i(p,w_i)f_r(p,w_i,w_o)\cos\theta_iV(p,w_i)\mathrm dw_i\approx\frac{\int_{\Omega^+}V(p,w_i)\mathrm dw_i}{\int_{\Omega^+}dw_i}\cdot\int_{\Omega^+}L_i(p,w_i)f_r(p,w_i,w_o)\cos\theta_i\mathrm dw_i$
  >
  > $左项=k_A=\frac{\int_{\Omega^+}V(p,\omega_i)\cos\theta_i\mathrm d\omega_i}{\pi},\quad 右项=L_i(p)\cdot\frac{\rho}{\pi}\cdot\pi=L_i(p)\cdot\rho$

- 在计算着色点visibility的过程中，需要考虑castray的范围，超出一定范围内的遮挡不再考虑；在screen space场景下计算着色点visibility时，利用采样深度值z-buffer来判断可见（采样点的深度值>camera pass在该点中获得的深度值时判断该点不可见），只有红点数量过半（半数不可见）时才计算AO（实际计算的是半球，而采样的为整个球，因此计算AO时红点数先减去样本数/2再计算visibility，在无法线信息情况下），有法线信息下为HBAO（并考虑了不同深度差距的情况）

<img src="D:\Tasks\GAMES_note\images\20231218194312.png" alt="20231218194312" style="zoom:33%;" />

- 采样过程中设置少量采样数再对结果进行降噪

## Lecture 09 Real-time Global Illumination (screen spcae cont.)

**Screen Space Directional Occlusion (SSDO)**

在SSAO基础上改进，考虑更实际情况下的间接光照（而不是视为来源于所有方向的常量）

使用RSM的思想，即利用屏幕空间内次级光源的位置作为参考

同样在着色点处半球取样，在一定距离内发生碰撞的线视为发生间接光照，未发生碰撞视为发生直接光照（与AO相反，DO假设间接光照来源于近距离，AO假设间接光照来源于无限远）

$L_o^{dir}(p,\omega_o)=\int_{\Omega^+,V=1}L_i^{dir}(p,\omega_i)f_r(p,\omega_i,\omega_o)\cos\theta_i\mathrm d\omega_i$

$L_o^{indir}(p,\omega_o)=\int_{\Omega^+,V=0}L_i^{indir}(p,\omega_i)f_r(p,\omega_i,\omega_o)\cos\theta_i\mathrm d\omega_i$

- 缺陷来源于屏幕空间的局限，丢失信息；范围有限，只能考虑小范围内的间接光照（即小范围内的次级光源）

**Screen Space Reflection (SSR)**

在屏幕空间内进行光线追踪，而不需要知道物体信息（反射出的图像很大程度上都能在屏幕空间内找到对应）

- 基本任务
  - 在屏幕空间下对给定的光线进行求交
  - 计算交点对着色点的贡献

利用raymarch来进行光线追踪，在光线路径上取样，比较深度值判断是否相交（仍然是基于屏幕空间，当取样的点的深度大于屏幕空间中该点的深度时判断为相交，如图中绿点）

<img src="D:\Tasks\GAMES_note\images\20231219143801.png" alt="20231219143801" style="zoom:33%;" />

取样的方式并不是均匀取样，而是可变长度的取样，利用深度Mipmap跳过大量不会相交的距离；下图中的蓝色方块为mipmap所记录的固定间隔下纹素的最小（在图二中判断相交后再在上一级判断相交）

<img src="D:\Tasks\GAMES_note\images\20231219145818.png" alt="20231219145818" style="zoom:33%;" />

在实际前进过程中步长不断变化，在没有相交的情况下提升level（更换更粗糙的mipmap），在相交时降低level（更精细），而在计算反射brdf过程中可以复用空间信息（直接利用周围以计算好的反射值而非重新计算），对于glossy材质的范围采样同样可以采用split sum中的思想，对范围内的值做模糊操作只需一次查询操作（但屏幕空间内做滤波非常局限，深度值的滤波结果缺乏意义，联合双边滤波）

<img src="D:\Tasks\GAMES_note\images\20231219162129.png" alt="20231219162129" style="zoom:33%;" />

- 快速进行glossy和specular的反射，质量高，完全考虑了遮挡问题
- 缺陷同样来源于屏幕空间的局限，且难以处理diffuse材质，需要大量采样

## Lecture 10 Real-time Physically-based Materials (surface models)

**Physically-Based Rendering (PBR)**：包含材质、光照、摄像机和光线传播而不仅仅是材质，实时渲染中PBR材质远远落后于离线渲染，但PB往往并不PB；最关键的在于保证速度的同时提升质量

- 平面模型，如microfacet模型和Disney principled BRDFs，但都不PB
- 体积模型，聚焦在快速、大致还原，如云、头发和皮肤等

**Microfacet BRDF** $f(i,o)=\frac{F(i,h)G(i,o,h)D(h)}{4(n,i)(n,o)}$

- Normal Distribution Function (NDF)：描述NDF的一系列模型包括Beckmann，GGX等，以半球形式存储法线信息

  - Beckmann NDF：$D(h)=\frac{e^{-\frac{\tan^2\theta_h}{\alpha^2}}}{\pi\alpha^2\cos^4\theta_h}$，类似于高斯分布，其中$\alpha$为平面粗糙程度，$\theta_h$为半程向量h与法线的夹角
  - GGX (Trowbridge-Reitz)：同样类似于高斯分布，但特点在于拖尾，高光的边缘会出现光晕的现象

  <img src="D:\Tasks\GAMES_note\images\20231219213710.png" alt="20231219213710" style="zoom:33%;" />

  <img src="D:\Tasks\GAMES_note\images\20231219214812.png" alt="20231219214812" style="zoom:33%;" />

  - Extending GGX：Generalized Trowbridge-Reitz (GTR)，会有更长的拖尾

- Shadowing-Masking Term / Geometry term G，衡量微表面上产生的自遮挡问题，在grazing angle下尤为严重，light pass中产生的遮挡称为shadowing，camera pass中产生的遮挡称为masking；G项的特征：入射光垂直表面时不存在自遮挡问题，在grazing angle下会陡降到0

  - Smith shadowing-masking term，假设法线分布进而推测shadowing和masking项

  <img src="D:\Tasks\GAMES_note\images\20231219222331.png" alt="20231219222331" style="zoom:33%;" />

  白炉测试，在不同roughness下测试亮度，微表面越粗糙（法线分布散）反射光越会被自遮挡/弹射，在只考虑一次反射的情况下粗糙表面会损失大量能量，这里采用基于模拟的方法不切实际（非常慢）

  <img src="D:\Tasks\GAMES_note\images\20231220094100.png" alt="20231220094100" style="zoom:33%;" />

  被遮挡=发生弹射&rarr;**Kulla-Conty近似**：补全多次反射丢失的能量，在着色点处对球形积分$E(\mu_o)=\int^{2\pi}_0\int^1_0f(\mu_o,\mu_i,\phi)\mu_i\mathrm d\mu_i\mathrm d\phi$，假设入射能量为1，得到损失的能量$=1-E(\mu_o)$

  因此需要构建一个补充函数，满足对称性的同时积分等于$1-E(\mu_o)$：$c(1-E(\mu_i))(1-E(\mu_o))$

  得到$f_{ms}(\mu_o,\mu_i)=\frac{(1-E(\mu_o))(1-E(\mu_i))}{\pi(1-E_{avg})},E_{avg}=2\int^1_0E(\mu)\mu\mathrm d\mu$，但还需计算$E_{avg}$，采用预计算/查表

  <img src="D:\Tasks\GAMES_note\images\20231220101654.png" alt="20231220101654" style="zoom:33%;" />

  在有颜色情况下会引起额外能量损失，计算平均菲涅尔项$F_{avg}$

  - 被直接看到的能量：$F_{avg}E_{avg}$
  - k次反射后被看到的能量：$F_{avg}^k(1-E_{avg})^k\cdot F_{avg}E_avg$

  求和后得到$\frac{F_{avg}E_{avg}}{1-F_{avg}(1-E_{avg})}$

## Lecture 11 Real-time Physically-based Materials (surface models cont.)

**Shading Microfacet Models using Linearly Transformed Cosines (LTC)**

