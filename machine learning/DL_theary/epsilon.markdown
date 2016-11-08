# $\epsilon$������

Author: Leoeon    
Date:2016.10.20

>[Shiyu Liang and R Srikant. Why deep neural networks? arXiv preprint arXiv:1610.04161 , 2016.](https://arxiv.org/abs/1610.04161)

������$\tilde{f}(x)$��Ϻ���$f(x)$��
���� $|f(x) - \tilde{f}(x)| \leq \varepsilon$

## ����	

��$\varepsilon$�������Ϻ�����

- �ֶι⻬������
$\Theta(\log{\frac{1}{\varepsilon}})$�㣬$\mathcal{O}(polylog(\frac{1}{\varepsilon})$����Ԫ
- �ֶι⻬������$o(\log{\frac{1}{\varepsilon}})$�㣬$\Omega(poly(\frac{1}{\varepsilon})$����Ԫ
- ��΢͹������	$\Omega(\log{\frac{1}{\varepsilon}})$����Ԫ




## ��������������

| ��Ϻ��� | ���� | ��Ԫ���� |
|-|-|-|
| $f(x) = x^2$ $(x\in[0,1])$ | $O(\log{\frac{1}{\varepsilon}})$ | $O(\log{\frac{1}{\varepsilon}})$ |
| $f(x) = \suml_{i=0}^p a_i x^i$ $(x\in[0,1],\suml_{i=0}^p |a_i| \leq 1)$ | $O(p+\log{\frac{p}{\varepsilon}})$ | $O(p\log{\frac{p}{\varepsilon}})$ |
| $f(x)$ $(x\in[0,1])$ (��$\forall n \in [\lceil\log{\frac{2}{\varepsilon}}\rceil+1]$��$||f^{(n)}||_{\infty} \leq n!$) | $O(\log{\frac{1}{\varepsilon}})$	| $O((\log{\frac{1}{\varepsilon}})^2)$ |
| \tabularl{$f = \prod_{i=1}^k h_i$ $(x\in[0,1])$ \\ (��$\forall n \in [\lceil4k\log{4k}+4k+2\log{\frac{2}{\varepsilon}}\rceil+1]$��$||h_i^{(n)}||_{\infty} \leq n!$)} | $O(k\log{k}+\log{\frac{1}{\varepsilon}})$	| $O((k\log{k})^2+(\log{\frac{1}{\varepsilon}})^2)$ |
| \tabularl{$f(x)=h_1(h_2(\cdots(h_k(x))))$ $(x\in[0,1])$ \\ (��$\forall n \in [\lceil\log{\frac{2}{\varepsilon}}\rceil+1]$��$||h_i^{(n)}||_{\infty} \leq n!$��$h_i:[0,1]\rightarrow[0,1]$)} | $O(k\log{k}\log{\frac{1}{\varepsilon}}+\log{k}(\log{\frac{1}{\varepsilon}})^2)$ | $O(k\log{k}\log{\frac{1}{\varepsilon}}+k^2(\log{\frac{1}{\varepsilon}})^2+(\log{\frac{1}{\varepsilon}})^4)$ |


## ����

��ǿ͹���� 
��$<\nabla f(x)-\nabla f(y), x-y> \geq \lambda||x-y||^2_2$����$(x\in[0,1])$��
Ҫ��$N \geq L(\frac{\mu}{16\varepsilon})^{\frac{1}{2L}}$