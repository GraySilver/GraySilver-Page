## 二项分布和Beta分布



#### 二项分布

在概率论和统计学中，二项分布是n个独立的[是/非]试验中成功的次数的离散概率分布，其中每次试验的成功概率为<img src="http://latex.codecogs.com/gif.latex?p"/>。举两个例子就很容易理解二项分布的含义了：

- 抛一次硬币出现正面的概率是0.5(<img src="http://latex.codecogs.com/gif.latex?p"/>)，抛10(n)次硬币，出现k次正面的概率。
- 掷一次骰子出现六点的概率是1/6，投掷6次骰子出现k次六点的概率。

在上面的两个例子中，每次抛硬币或者掷骰子都和上次的结果无关，所以每次实验都是独立的。二项分布是一个离散分布，k的取值范围为从0到n，只有n+1种可能的结果。

`scipy.stats.binom`为二项分布，下面用它计算抛十次硬币，出现k次正面的概率分布。

```python
n = 10
k = np.arange(n+1)
pcoin = stats.binom.pmf(k, n, 0.5)
print(pcoin)
```

> array([ 0.00097656,  0.00976563,  0.04394531,  0.1171875 ,  0.20507813,        0.24609375,  0.20507813,  0.1171875 ,  0.04394531,  0.00976563,        0.00097656])

```python
pl.stem(k, pcoin, basefmt="k-")
pl.margins(0.1)
```

![](http://graysliver.oss-cn-shenzhen.aliyuncs.com/1_1.png)

下面是投掷6次骰子，出现6点的概率分布。

```python
n = 6
k = np.arange(n+1)
pdice = stats.binom.pmf(k, n, 1.0/6)
print(pdice)
```

> array([  3.34897977e-01,   4.01877572e-01,   2.00938786e-01,         5.35836763e-02,   8.03755144e-03,   6.43004115e-04,         2.14334705e-05])

```python
pl.stem(k, pdice, basefmt="k-")
pl.margins(0.1)
```

![](http://graysliver.oss-cn-shenzhen.aliyuncs.com/1_2.png)

#### Beta分布

对于硬币或者骰子这样的简单实验，我们事先能很准确地掌握系统成功的概率。然而通常情况下，系统成功的概率是未知的。为了测试系统的成功概率<img src="http://latex.codecogs.com/gif.latex?p"/>，我们做n次试验，统计成功的次数k，于是很直观地就可以计算出<img src="http://latex.codecogs.com/gif.latex?p = k/n"/>。然而由于系统成功的概率是未知的，这个公式计算出的<img src="http://latex.codecogs.com/gif.latex?p"/>只是系统成功概率的最佳估计。也就是说实际上<img src="http://latex.codecogs.com/gif.latex?p"/>也可能为其它的值，只是为其它的值的概率较小。

例如有某种特殊的硬币，我们事先完全无法确定它出现正面的概率。然后抛10次硬币，出现5次正面，于是我们认为硬币出现正面的概率最可能是0.5。但是即使硬币出现正面的概率为0.4，也会出现抛10次出现5次正面的情况。因此我们并不能完全确定硬币出现正面的概率就是0.5，所以<img src="http://latex.codecogs.com/gif.latex?p"/>也是一个随机变量，它符合Beta分布。

Beta分布是一个连续分布，由于它描述概率<img src="http://latex.codecogs.com/gif.latex?p"/>的分布，因此其取值范围为0到1。 Beta分布有<img src="http://latex.codecogs.com/gif.latex?\alpha"/>和<img src="http://latex.codecogs.com/gif.latex?\beta"/>两个参数，其中<img src="http://latex.codecogs.com/gif.latex?\alpha"/>为成功次数加1，<img src="http://latex.codecogs.com/gif.latex?\beta"/>为失败次数加1。

连续分布用概率密度函数描述，下面绘制实验10次，成功4次和5次时，系统成功概率<img src="http://latex.codecogs.com/gif.latex?p"/>的分布情况。可以看出<img src="http://latex.codecogs.com/gif.latex?k=5"/>时，曲线的峰值在<img src="http://latex.codecogs.com/gif.latex?p=0.5"/>处，而<img src="http://latex.codecogs.com/gif.latex?k=4"/>时，曲线的峰值在<img src="http://latex.codecogs.com/gif.latex?p=0.4"/>处。

```python
n = 10
k = 5
p = np.linspace(0, 1, 100)
pbeta = stats.beta.pdf(p, k+1, n-k+1)
plot(p, pbeta, label="k=5", lw=2)

k = 4
pbeta = stats.beta.pdf(p, k+1, n-k+1)
plot(p, pbeta, label="k=4", lw=2)
xlabel("$p$")
legend(loc="best");
```

![](http://graysliver.oss-cn-shenzhen.aliyuncs.com/1_3.png)

下面绘制<img src="http://latex.codecogs.com/gif.latex?n=10, k=4"/>和<img src="http://latex.codecogs.com/gif.latex?n=20, k=8"/>的概率分布。可以看出峰值都在<img src="http://latex.codecogs.com/gif.latex?p=0.4"/>处，但是<img src="http://latex.codecogs.com/gif.latex?n=20"/>的山峰更陡峭。也就是说随着实验次数的增加，<img src="http://latex.codecogs.com/gif.latex?p"/>取其它值的可能就越小，对<img src="http://latex.codecogs.com/gif.latex?p"/>的估计就更有信心，因此山峰也就更陡峭了。

```PYTHON
n = 10
k = 4
p = np.linspace(0, 1, 100)
pbeta = stats.beta.pdf(p, k+1, n-k+1)
plot(p, pbeta, label="n=10", lw=2)

n = 20
k = 8
pbeta = stats.beta.pdf(p, k+1, n-k+1)
plot(p, pbeta, label="n=20", lw=2)
xlabel("$p$")
legend(loc="best")
```

![](http://graysliver.oss-cn-shenzhen.aliyuncs.com/1_4.png)

#### 用pymc模拟

假设我们的知识库中没有Beta分布，如何通过模拟实验找出<img src="http://latex.codecogs.com/gif.latex?p"/>的概率分布呢？pymc是一个用于统计估计的库，它可以通过 **先验概率** 和 **观测值** 模拟出 **后验概率** 的分布。下面先解释一下这两个概率：

- 先验概率：在贝叶斯统计中，某一不确定量p的先验概率分布是在考虑"观测数据"前，能表达p不确定性的概率分布。
- 后验概率：在考虑相关证据或数据后所得到的不确定量p的概率分布。

拿前面抛硬币的实验来说，如果在做实验之前能确信硬币出现正面的概率大概在0.5附近的话，那么它的先验概率就是一个以0.5为中心的山峰波形。而如果是某种特殊的硬币，我们对其出现正面的概率完全不了解，那么它的先验概率就是一个从0到1的平均分布。为了估计这个特殊硬币出现正面的概率，我们做了20次实验，其中出现了8次正面。通过这个实验，硬币出现正面的可能性的后验概率就如上图中的绿色曲线所示。

pymc库可以通过先验概率和观测值模拟出后验概率的分布，这对于一些复杂的系统的估计是很有用的。下面我们看看如何用pymc来对这个特殊硬币出现正面的可能性进行估计：

- 首先`pcoin`是这个特殊硬币出现正面的概率，由于我们没有任何先验知识，因此它的先验概率是一个从0到1的平均分布(Uniform)。
- 假设我们做了20次实验，其中8次为正面。根据前面的介绍可知，出现正面的次数符合二项分布(Binomial)，并且这个二项分布的概率<img src="http://latex.codecogs.com/gif.latex?p"/>为`pcoin`。这个通过`value`参数指定了实验的结果。因此`experiment`虽然是一个二项分布，但是它已经不能取其它值了。

```python
import pymc
pcoin = pymc.Uniform("pcoin", 0, 1)
experiment = pymc.Binomial("experiment", 20, pcoin, value=8)
```

接下来通过`MCMC`对象模拟`pcoin`的后验概率。MCMC是Markov chain Monte Carlo(马尔科夫蒙特卡洛)的缩写，它是一种用马尔可夫链从随机分布取样的算法。通过调用`MCMC`对象的`sample()`，可以对`pcoin`的后验概率分布进行取样。这里30000为取样次数，5000表示不保存头5000次取样值。这时因为MCMC算法通常有一个收敛过程，我们希望只保留收敛之后的取样值。

```python
mc = pymc.MCMC([pcoin])
mc.sample(30000, 5000)
```

> [****************100%******************]  30000 of 30000 complet

通过MCMC对象`trace()`可以获得某个不确定量的取样值。下面的程序获得`pcoin`的25000次取样值，并用`hist()`显示其分布情况。由结果可知`pcoin`的分布与前面介绍的Beta分布一致。

```python
pcoin_trace = mc.trace("pcoin")[:]
hist(pcoin_trace, normed=True, bins=30);
plot(p, pbeta, "r", label="n=20", lw=2)
```

![](http://graysliver.oss-cn-shenzhen.aliyuncs.com/1_5.png)

```python
pcoin_trace.shape
```

> (25000,)