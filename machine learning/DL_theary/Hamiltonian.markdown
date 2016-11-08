# ��������Ϲ��ܶ���

Author: Leoeon	
Date: 2016.09.15

>[Henry W Lin and Max Tegmark. Why does deep and cheap learning work so well? arXiv preprint arXiv:1608.08225 , 2016.](https://arxiv.org/abs/1608.08225)

## ��������Ч��

### ����Ŀ��

��������дΪ
$$ f(\vec{y}) = \hat{\sigma}_n \hat{W}_n \cdots \hat{\sigma}_1 \hat{W}_1 \vec{y} $$

�
$$ H_x(\vec{y}) \stackrel{def}{=} -\ln{p(\vec{y}|x)} $$
$$\mu_x \stackrel{def}{=} -\ln{p(x)}$$
��
$$p(x|\vec{y}) = \frac{1}{Z(\vec{y})} e^{-(H_x(\vec{y})+\mu_x)} = \hat{\sigma} (-(H_x(\vec{y})+\mu_x))	\\
\left(Z(\vec{y}) = \int e^{-(H_x(\vec{y})+\mu_x)} dx \right) $$
��ֻҪ��������ܹ���$-(H_x(\vec{y})+\mu_x)$�����ټ�һ����ԪΪsoftmax����

### ���⾫�����

��$\sigma(x) = \sigma + \sigma'x + \sigma''x^2 +O(u^3)$
�ã�$$\frac{1}{8\sigma''\lambda^2}[ \sigma(\lambda u+\lambda v) + \sigma(-\lambda u-\lambda v) - \sigma(\lambda u-\lambda v) - \sigma(-\lambda u+\lambda v) ]
= uv(1+O(\lambda^2 u^2+\lambda^2 v^2))$$
�������Ͽ��������������⾫�ȱ�ʾ�˻�
![Multiplication](./picture/Hamiltonian/Multiplication.JPG)

΢����
$$ H_x(\vec{y}) = H + \sum_i H'_i y_i + \sum_{ij} H''_{ij} y_i y_j + \sum_{ijk} H'''_{ijk} y_i y_j y_k + \cdots $$
�������Ͽ����������ʾ����$H_x(\vec{y})$

### ��Ч

��������ʹ�ò�����������

- �ͽ�� $H_x(\vec{y})$ ֻ��չ�������������̫�߽���
- �����ԣ� ֻ�ж̳����ã��ܶ�$H^{(k)}$ Ϊ0
- �Գ��ԣ� �ܶ�$H^{(k)}$ ���������໥����




## �ֲ�

����������Ⱥ���ۣ��ӵͲ����Ϣ�г���������ƣ�ͳ�Ƴ������Ϊ�߲����Ϣ�������ȡֱ�����衣