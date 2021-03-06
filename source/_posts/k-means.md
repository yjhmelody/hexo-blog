---
title: k-means
date: 2017-08-08 16:18:09
tags: [ML, Clusters]
categry: [CS]
---

公式排版目前没法解决唉，博客写个数学公式怎么这么揪心，好不容易解决了公式显示问题，但排版又很难控制

# k-means

## 基本原理

这个星期学无监督学习(unsuperviser-learning), 最基本的是k-means算法.
k-means属于`原型聚类`:假设聚类结构能通过一组原型刻画. 而聚类本身是根据数据相似度来划分的,即"距离".我这里不展开讲距离计算, 姑且用欧几里德距离来理解，即平时最常见的公式.
{% raw %}

我们先写出k-means的最小化平方误差: 给定样本集合 $D = {x_1, x_2, \dots, x_m}$,

k-means对聚类所得的簇划分 $C = {C_1, C_2, \dots, C_k}$
的最小平方误差是 $E = \sum_{i=1}^k \sum_{x \in C_i} ||x - \mu_i||_2^2 $,

其中 $\mu_i = \frac{1}{|C_i|} \sum_{x \in C_i}x$是簇$ C_i $ 的均值向量
{% endraw %}

可以看出来, 求该式子的最优解并不容易, 因为需要考察每个样本可能的划分情况(`NP难问题`),
所以k-means 采用贪心策略, 通过迭代优化求近似解.

感觉学完以上内容, 脑子里好像有了这个算法的轮廓了, 但仔细一想, 还有很多地方需要斟酌.
k-means是划分为k个类别, 那一开始怎么划分呢?
我们想到一开始需要选k个样本当作中心点来计算距离, 根据这k个位置就求得每个样本离哪个位置最接近. 这样,第一次聚类就完成了, 但是由于这次聚类造成每个类别的中心点发生了偏移,

我们需要重新计算这时候的中心点,而由于发生了偏移,我们又需要重新计算所有样本最接近的... 如何反复,直到中心点(即均值向量)不变, 便完成了聚类. 

如此说来, 这个算法还是蛮朴素的, 但是还有需要斟酌的地方.
如何选初始点呢？万一2个点靠得近，或者是离群点, 岂不是可能会出现很极端的聚类(比如一个人一个类别).个人觉得这有时候很难避免, 但可以通过多次随机选取初始点并评估各个聚类的效果(最小平方误差),来减少这种情况.

嗯,好像对算法的轮廓又清晰了, 但可能又有疑问, 到底如何选择k值呢?这里应该要具体问题具体分析了.如果只是想得到更好的性能, 把应该把k值从小到大递增来比较选择最优, 但这不一定给你带来实际上的最大收益.比如你是服装商, 把消费者划分了不同尺码的类别来针对客户进行推荐商品, 那到底多细才最优呢,反正我是不知道, 这得根据实际情况和专家经验来决策,而k-means在这里不充当决策者, 更像是打下手的.

扯远了,总之对于k-means, 知道k值我觉得往往很关键.

## 算法伪代码

这样子算法应该比较清晰了,我下来列出(抄上)伪代码, 参考自周志华的西瓜书:

{% raw %}
输入: 
样本集 $D = \{x_1, x_2, ..., x_m\}$, 聚类簇数 k

过程:
从D中随机选择k个样本作为初始均值向量 $\{\mu_1, \mu_2, ..., \mu_k\}$
repeat 
	$C_i = \emptyset$
	for j = 1, 2, ..., m do
		计算$x_j$与各均值向量 \mu_i(1 \leq i \leq k)的距离: $d_ji = ||x_i - \mu_i||_2$;
		根据距离最近的均值向量确定x_j的簇标记: $\lambda_j = argmin_{i \in \{1,2,...,k\}}d_ij$;
		将样本$x_j$划入相应的簇: $C_{\lambda_j} = C_{\lambda_j} \bigcup\{x_j\}$
	end for

	for i = 1, 2, ..., k do
		计算新均值向量: $\mu_i^` = \frac{1}{|C_i|}\sum_{x \in C_i|}x$;
		if $\mu_i^ \ne\mu_i$ then
			更新 $\mu_i$
		else
			保持当前均值向量不变
		end if

	end for

util 当前均值向量都没有更新

输出: 簇划分 $C = \{C_1, C_2, ..., C_k\}$
{% endraw %}

## 扯淡

暂时先写到这里,第一次写latex,第一次写博客,写的不好请见谅,以上大段文字
有些是建立在自己的理解写的,如果有错误,希望能不吝赐教.写的比较浅,其实也没什么意思啊,就是梳理下自己跟其他同学的思路.