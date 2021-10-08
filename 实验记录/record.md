本次任务选用paddlepaddle完成cifar10图像分类。

本次任务选用paddlepaddle完成cifar10图像分类。
1. 安装paddlepaddle：https://www.paddlepaddle.org.cn/install/quick?docurl=/documentation/docs/zh/develop/install/pip/windows-pip.html#cpu
2. 使用官网提供的baseline跑一下实验结果
![dc2cadbd45aefcb030b797db5775a70e.png](../_resources/ef0ec73668f546039db5272515ca3d08.png)![0f8de64af4843cb4239e2095d8bf6826.png](../_resources/373d94b1e70747d886f84687050f5ae5.png)
可以看到loss在前几个epoch快速下降，accuracy不断提升并最终稳定在0.70附近。
3. 观察修改输入输出通道对模型效果的影响
输入输出通道前后对比：
之前：
`self.conv1 = paddle.nn.Conv2D(in_channels=3, out_channels=32, kernel_size=(3, 3))
self.conv2 = paddle.nn.Conv2D(in_channels=32, out_channels=64, kernel_size=(3,3))
self.conv3 = paddle.nn.Conv2D(in_channels=64, out_channels=64, kernel_size=(3,3))`
之后：
`self.conv1 = paddle.nn.Conv2D(in_channels=3, out_channels=12, kernel_size=(3, 3))
self.conv2 = paddle.nn.Conv2D(in_channels=12, out_channels=24, kernel_size=(3,3))
self.conv3 = paddle.nn.Conv2D(in_channels=24, out_channels=64, kernel_size=(3,3))`
![b3b83c0a51177bec37109551eba02599.png](../_resources/99c27446d39f4f26a031a5054487e249.png)
![8d74b6f973b0380ab7449b3dcbadcac0.png](../_resources/db4aa36f3e184b8d930ee9d989989479.png)
发现loss高于原模型，同时accuracy也比原模型要低
再尝试一次：
![ac1512f75235e4c9e601387a8210897f.png](../_resources/68560088a3c14844b3b207b153797530.png)
![a22beff2e851b1177e324dddcb20cbfd.png](../_resources/cdbb686936364d189621979c523cef6a.png)
发现loss变化波动较大，accuracy虽然中间达到过0.7317，但是后面又降低了下来，可能是出现了过拟合的情况。
中间很好奇对性能的要求,发现占用了50%的CPU资源以及6GB的内存
![b885a39bc4eea0396e0029196d7ef7f4.png](../_resources/4682eb3aeae948d2aea71f04866ecbfd.png)
![24685891d18312c3669be3568930d5b3.png](../_resources/bd0ae5d95579481eb9441a2fa5260c46.png)
4. 将batch_size从32调整为64
![f8e3dc27ca2459e371bb779cc6e4ed0e.png](../_resources/f2aff9c47a0343a8b999bf593d0387cb.png)
![43f98449f57e5b524bceb52aaa098bba.png](../_resources/c9b794b616164b0185d18884bca5bab1.png)
发现与原模型相比没有很大的提升
5. 将learning_rate调整从0.001调整为0.004
![3b37fcbbb5ad36864420efb9c4192a3a.png](../_resources/69c3455cb70047beab6c4416a3c095fd.png)
![b9c23df3f1a7f6f5949d39fb93e2768b.png](../_resources/c889db72303a43ef8d12407be0a3e1c4.png)
发现准确率低了很多
尝试调整为0.002
![10dfedc16b35785e6b9d2533cd5100f7.png](../_resources/d6ea4d5b10ec407394dc918785e66c72.png)
![109550514359a12c1bec76a0b3b8e70a.png](../_resources/5c285941ff304219994a40b3023faef1.png)
与原模型效果没有什么差别
尝试调整为0.00001，为了观察效果，增加了epoch_num
![e7d990c0593657c883ebf42ef757c334.png](../_resources/3386220689074b27bcf6278eb7a650a7.png)
![3dbba4a5b07f3cf675c93df09604202c.png](../_resources/64704caefeb14d8abee9aa6844689a7c.png)
发现准确率稳步上升，但是到后面的轮次的时候上升趋势放缓，因此放弃了通过增加轮次来提升accuracy值使其突破0.75。
6. 将batch_size调整为2
![1ea57fbdfd51c57e63fad155eb10ec3c.png](../_resources/7fef156e897342ff8937ef72a7c85906.png)
![a3c7f01a3ea51a3bfc2ce6a8afe43a55.png](../_resources/5fce44d83eaf4d0cb5870875e80d7d1d.png)
发现一开始就达到了较理想的情况，后面变化不大。