#### 基本概念：

[散列表](https://baike.baidu.com/item/散列表)（Hash table，也叫哈希表），是根据关键码值\(Key value\)而直接进行访问的[数据结构](https://baike.baidu.com/item/数据结构)。也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做[散列函数](https://baike.baidu.com/item/散列函数)，存放记录的[数组](https://baike.baidu.com/item/数组)叫做[散列表](https://baike.baidu.com/item/散列表)。

给定表M，存在函数f\(key\)，对任意给定的关键字值key，代入函数后若能得到包含该关键字的记录在表中的地址，则称表M为哈希\(Hash）表，函数f\(key\)为哈希\(Hash\) 函数。

* 若关键字为**k**，则其值存放在**f\(k\)**的存储位置上。由此，不需比较便可直接取得所查记录。称这个对应关系**f**为散列函数，按这个思想建立的表为散列表。

* 对不同的关键字可能得到同一散列地址，即**k1≠k2**，而**f\(k1\)=f\(k2\)**，这种现象称为碰撞（英语：Collision）。具有相同函数值的关键字对该散列函数来说称做同义词。综上所述，根据散列函数**f\(k\)**和处理碰撞的方法将一组关键字映射到一个有限的连续的地址集（区间）上，并以关键字在地址集中的“像”作为记录在表中的存储位置，这种表便称为散列表，这一映射过程称为散列造表或散列，所得的存储位置称散列地址。

* 若对于关键字集合中的任一个关键字，经散列函数映象到地址集合中任何一个地址的概率是相等的，则称此类散列函数为均匀散列函数（Uniform Hash function），这就是使关键字经过散列函数得到一个“随机的地址”，从而减少碰撞。

#### 常用方法：

[散列函数](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B0)能使对一个数据序列的访问过程更加迅速有效，通过散列函数，[数据元素](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E5%85%83%E7%B4%A0)将被更快地定位。实际工作中需视不同的情况采用不同的[哈希函数](https://baike.baidu.com/item/%E5%93%88%E5%B8%8C%E5%87%BD%E6%95%B0)，通常考虑的因素有：

· 计算[哈希函数](https://baike.baidu.com/item/%E5%93%88%E5%B8%8C%E5%87%BD%E6%95%B0)所需时间

· 关键字的长度

· 哈希表的大小

· 关键字的分布情况

· 记录的查找频率

* 直接寻址法
* 数字分析法
* 平方取中法
* 折叠法
* 随机数法
* 除留余数法

#### 处理冲突：

1.[开放寻址法](https://baike.baidu.com/item/%E5%BC%80%E6%94%BE%E5%AF%BB%E5%9D%80%E6%B3%95)：Hi=\(H\(key\) + di\) MOD m,i=1,2，…，k\(k&lt;=m-1），其中H\(key）为[散列函数](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B0)，m为[散列表](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E8%A1%A8)长，di为增量序列，可有下列三种取法：

1.1. di=1,2,3，…，m-1，称线性探测再散列；

1.2. di=1^2,-1^2,2^2,-2^2，⑶^2，…，±（k\)^2,\(k&lt;=m/2）称二次探测再散列；

1.3. di=[伪随机数](https://baike.baidu.com/item/%E4%BC%AA%E9%9A%8F%E6%9C%BA%E6%95%B0)序列，称伪随机探测再散列。

2. 再[散列法](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E6%B3%95)：Hi=RHi\(key\),i=1,2，…，k RHi均是不同的

[散列函数](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B0)，即在同义词产生地址冲突时计算另一个散列函数地址，直到冲突不再发生，这种方法不易产生“聚集”，但增加了计算时间。

3. 链地址法（拉链法）

4. 建立一个公共溢出区




