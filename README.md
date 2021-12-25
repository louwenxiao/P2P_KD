# P2P_KD

## 工作描述

本工作主要是将知识蒸馏（不完全是）应用于P2P中，降低通信开销，提高在non-IID数据上的精度。

### 传统方法以及存在问题

传统的P2P模型训练是：传输本地模型（或者模型剪枝）给邻居，client接收到邻居模型后，在本地进行聚合，更新本地模型。但是存在问题：1.巨大的通信开销；2.数据的non-IID问题。

### 我们的方法

所以针对上述两个问题，我们提出一个方法：将模型分成两部分，特征提取器 和 分类器。特征提取器的作用是将输入的数据转换成特征，作为分类器的输入。

整个训练过程是：1.client在本地数据集上训练，更新分类器和特征提取器（使用交叉熵损失函数）；2.使用特征提取器提取部分数据特征，通过分类器计算数据特征的logit，传输 （数据特征、logit和label 以及 特征提取器参数）给邻居。3.client接收到邻居的数据后，通过FedAvg更新特征提取器参数，训练邻居的数据更新分类器（使用交叉熵+KL散度损失函数）。


## 代码描述

worker_config.json文件：client的基本信息，包括地址、带宽、监听端口等；

server.py为主函数；

training_module文件夹：数据集划分、模型等信息；

control_algorithm文件夹：主要用于控制P2P拓扑结构

communication_module文件夹：主要用于client之间的通信，在我们的实验中，通过socket+pytorch仿真

client_module文件夹：关于client的各种设置，主要函数为client.py，如训练过程整体描述。


## 参数设置
实验设置

| dataset/model         | test_acc | lr/min_lr | decay_rate | local_iteration | epoch | momentum | weight_decay |
| --------------------- | -------- | --------- | ---------- | --------------- | ----- | -------- | ------------ |
| CIFAR10/AlextNet      | 0.8      | 0.1/0.001 | 0.99       | 50              | 500   | -1       | 0.00         |
| CIFAR10/VGG9          | 0.85     | 0.1/0.001 | 0.99       | 50              | 500   | -1       | 0.00         |
| EMNIST/CNN            |          |           |            |                 |       |          |              |
| CIFAR100/ResNet9      |          |           |            |                 |       |          |              |
| tinyImageNet/ResNet50 |          |           |            |                 |       |          |              |


