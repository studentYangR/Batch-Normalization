# Batch-Normalization
## Batch Normalization说明
Batch Normalization作为最近一年来DL的重要成果，已经广泛被证明其有效性和重要性。
  《Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift》介绍了Batch Normalization。
   BatchNorm的功能是实现在深度神经网络训练过程中使得每一层神经网络的输入保持相同分布的。主要用于满足IID独立同分布假设：假设训练数据和测试数据是满足相同分布的，这是通过训练数据获得的模型能够在测试集获得好的效果的一个基本保障。
   随着深度学习中神经网络的深度加深，训练起来越来愈困难。收敛也越来越慢。这是深度学习十分本质的问题。很多论文都是解决这个问题的，比如ReLU激活函数，再比如Residual Network，BN本质上也是解释并从某个不同的角度来解决这个问题的。
## Internal Covariate Shift问题
  在论文中说明了Mini-Batch SGD相对于One Example SGD的两个优势：梯度更新方向更准确；并行计算速度快；（为什么要说这些？因为BatchNorm是基于Mini-Batch SGD的，所以先夸下Mini-Batch SGD，当然也是大实话）；然后吐槽下SGD训练的缺点：超参数调起来很麻烦。（作者隐含意思是用BN就能解决很多SGD的缺点）。
  接着引入`covariate shift`的概念：如果ML系统实例集合<X,Y>中的输入值X的分布老是变，这不符合IID假设，网络模型很难稳定的学规律，这不得引入迁移学习才能搞定吗，我们的ML系统还得去学习怎么迎合这种分布变化。对于深度学习这种包含很多隐层的网络结构，在训练过程中，因为各层参数不停在变化，所以每个隐层都会面临covariate shift的问题，也就是在训练过程中，隐层的输入分布老是变来变去，这就是所谓的`Internal Covariate Shift`，`Internal`指的是深层网络的隐层，是发生在网络内部的事情，而不是`covariate shift`问题只发生在输入层。
  BatchNorm的基本思想：能不能让每个隐层节点的激活输入分布固定下来呢？避免`Internal Covariate Shift`。
  BN思想源于图像处理中对输入图像进行白化(Whiten)处理：对输入数据分布变换到0均值，单位方差的正态分布——那么神经网络会较快收敛。
  推论：图像是深度神经网络的输入层，做白化能加快收敛，那么其实对于深度网络来说，其中某个隐层的神经元是下一层的输入，意思是其实深度神经网络的每一个隐层都是输入层，不过是相对下一层来说而已，那么能不能对每个隐层都做白化呢？这就是启发BN产生的原初想法，而BN也确实就是这么做的，可以理解为对深层神经网络每个隐层神经元的激活值做简化版本的白化操作。<br>
## BatchNorm的本质思想
   针对于传统神经元只对输入x进行标准化处理，例如减去均值，除标准差。以降低样本间的差异性。BN不仅仅只对输入层的输入数据x进行标准化，还对每个隐藏层的输入进行标准化.如下图所示。 <br>
  ![BN](https://img-blog.csdn.net/20170721163449112?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2hpdGVzaWxlbmNl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
- BN的基本思想：
  * 因为深层神经网络在做非线性变换前的激活输入值（就是那个x=WU+B，U是输入）随着网络深度加深或者在训练过程中，其分布逐渐发生偏移或者变动，之所以训练收敛慢，一般是整体分布逐渐往非线性函数的取值区间的上下限两端靠近（对于Sigmoid函数来说，意味着激活输入值WU+B是大的负值或正值），所以这导致反向传播时低层神经网络的梯度消失，这是训练深层神经网络收敛越来越慢的本质原因。<br>
  * 而BN就是通过一定的规范化手段，把每层神经网络任意神经元这个输入值的分布强行拉回到均值为0方差为1的标准正态分布，其实就是把越来越偏的分布强制拉回比较标准的分布，这样使得激活输入值落在非线性函数对输入比较敏感的区域，这样输入的小变化就会导致损失函数较大的变化，意思是这样让梯度变大，避免梯度消失问题产生，而且梯度变大意味着学习收敛速度快，能大大加快训练速度。<br>
``对于每个隐层神经元，把逐渐向非线性函数映射后向取值区间极限饱和区靠拢的输入分布强制拉回到均值为0方差为1的比较标准的正态分布，使得非线性变换函数的输入值落入对输入比较敏感的区域，以此避免梯度消失问题。``<br>


  
  
