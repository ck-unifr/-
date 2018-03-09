# 盗版图检测

* 主要目的: 检测网络上的盗版图片.

* 难点一: 如何判断一张图片是否盗用了正版的图片.
* 难点二: 如果已知一张图片为盗图,那么具体到底是盗用了哪一张图片.
* 难点三: 如何使运算很有效.


步骤一:
训练一个分类器用于判断一张图片是否是盗版图片.
方法: 使用GAN.用正版图片和盗版图片作为训练集去训练一个GAN.训练好的GAN包含一个discriminator和一个generator,我们可以用discriminator去判断一张图片是否是盗图.


步骤二:
训练一个分类器可以将一堆图片分成$M$类.例如,人物,场景,等等.
方法: 使用CNN.因为是传统的图片分类方法所以可以用transfer learning(用已经训练好的VGG net结构,然后在我们的数据集上做fine-tuning).


步骤三
训练一个距离检测器,用于给出两张图片的相似程度.
方法:使用 [Siamese Networks](hackernoon.com/one-shot-learning-with-siamese-networks-in-pytorch-8ddaab10340e).在传统的classification问题中,通常是每一类我们有很多的训练样本.但是有些情况下,例如人脸识别,我们有很多类别,但是没一个类别的样本很少.对于这样的问题解决方案就是使用[Siamese Networks](hackernoon.com/one-shot-learning-with-siamese-networks-in-pytorch-8ddaab10340e). 具体训练是对于我们数据库里有的图片,用和它对应的盗版图片和其他的图片训练一个Siamese Network.最终目的是要让任意一张图片和它所对应的盗版图片距离很小,和其他的图片距离很大.


步骤四:
对于网络上的任意给定的图片x,首先用步骤一的discriminator去判断它是不是盗图.然后用步骤二的CNN去判断x是属于那一类.
如果x是属于第m类.那么对于所有第m类内的图片y_i,用步骤三的Siamese Network去计算x和y_i的距离,然后按距离从小到大排列.那么前k个距离最小的y_i就是我们认为被x盗用的图片.


* 关于难点一,解决方案是步骤一的GAN.
* 关于难点二,解决方案是步骤二的CNN .
* 关于难点三,目前想法主要还是通过并行计算,例如Apache Spark.
