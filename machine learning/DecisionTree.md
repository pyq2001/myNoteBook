# DecisionTree

![image-20230425104515450](../image/DecisionTree/image-20230425104515450.png)

![image-20230425111425778](../image/DecisionTree/image-20230425111425778.png)

## 决策树的构建原理

决策树是树状结构，它的每个叶结点对应着一个分类，非叶结点对应着在某个属性上的划分，根据样本在该属性上的不同取值将其划分为若干子集。ID3、C4.5和CART算法都采用贪心(即非回溯）方法，以自顶向下递归的分治方式构造，随着树的构建，训练集递归地被划分为子集。

![image-20230425112453886](../image/DecisionTree/image-20230425112453886.png)

![image-20230425122212129](../image/DecisionTree/image-20230425122212129.png)

![image-20230425122403361](../image/DecisionTree/image-20230425122403361.png)

![image-20230425122614629](../image/DecisionTree/image-20230425122614629.png)

![image-20230425125432797](../image/DecisionTree/image-20230425125432797.png)

![image-20230425130046114](../image/DecisionTree/image-20230425130046114.png)

![image-20230425130012254](../image/DecisionTree/image-20230425130012254.png)

![image-20230425131417527](../image/DecisionTree/image-20230425131417527.png)