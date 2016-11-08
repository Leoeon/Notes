#机器学习展平流形

Author: Leoeon    
Date: 2015.12.30

>[Pratik Prabhanjan Brahma, Dapeng Wu, and Yiyuan She. Why deep learning works: A manifold disen-tanglement perspective. 2015.](http://ieeexplore.ieee.org/document/7348689/)

原始数据为嵌在高维空间中的低维流型，且该流型很可能非常复杂。
机器学习提取出特征，实质为将流型尽可能在低维空间中展平。

深度学习能自动提取特征，即为自动适应各类复杂流型，自动进行展平。
优于传统机器学习中人工设计特征，局限于有限的流型。

流型展平程度判断依据：
- 流型上任意两点间测地线长度与两点间欧几里得空间长度比较，越接近则流型越平。
- 由流型上每个点切空间与周围点切空间求得该点曲率，曲率越小则流型越平。