* 计算非概率型分布间的KL散度，首先需要将两个频率分布直方图转换为概率分布。假设这两个数据分别为 `data1` 和 `data2`，可以通过将其每个直方柱的高度除以直方图总数得到概率分布。

```python
python import numpy as np 
def histogram_to_probability(data): 
	bins = len(data) 
	total = np.sum(data) 
	return [i / total for i in data] 
prob1 = histogram_to_probability(data1) 
prob2 = histogram_to_probability(data2)
```
计算KL散度代码：

```python
python from scipy.stats import entropy 
kl_divergence = entropy(prob1, prob2) 
print(f"KL散度为：{kl_divergence}") 
```
