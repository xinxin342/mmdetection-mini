# mmdetection-mini
mmdetection的mini版本，主要包括一阶段目标检测器，结构和
mmdetection完全一致，系统通过从头构建整个框架来熟悉所有细节

## 0 为何而生
很多人可能有疑问：mmdetection那么好用，你为啥要自己又写一遍呢？没事干？
其实不然，基于我目前的理解，出于以下几点原因：

- **学习目的**
mmdetection无疑是非常优异的目标检测框架，但是其整个框架代码其实非常多。我希望通过从0构建整个
结构，来彻底熟悉整个框架，而不是仅仅熟悉算法部分。只有自己写一遍才能发现一些容易忽略的细节
- **方便注释**
这一点，我相信很多人都碰到过。主要原因是mmdetection发展太快了，更新特别频繁，比如今天我拉了最新分支
加入了我的一些理解注释，过了两天，一更新就发现完全变了，此时再pull就出现很多冲突。天天解决这些冲突其实蛮累的。
所以我自己写一个mmdetection，然后加入注释，并且实时同步mmdetection到最新版，不仅可能清楚每次更新的
所有细节，还可以不影响注释
- **新特性方便增加**
如果自己想实现一些mmdetection里面没有的新特性，就非常方便了。比如我要在debug模式下进行可视化分析，
如果直接加到mmdetection上面，会改动一些代码，一旦pull又有冲突。由于同步mmdetection过程是手动的，新增特征
也不会出现冲突

## 1 介绍

完全基于mmdetection框架结构,简称mmdet最简学习版，**基于最简实现，第一原则就是简洁，不会加入一些乱七八糟的功能，一步一步构建一阶段目标检测器**。
主要目的为在从0构建整个框架的基础上，掌握整个目标检测实现细节。 并且方便新增自己想要实现的部分。

由于只有一张显卡，故不支持分布式训练。

总结：本项目目的是学习,希望通过从0构建每一行代码，来熟悉每个部分，而且自己写的框架，后续我新增一些新特性也非常容易

**更新可能是快时慢。在掌握每个细节后才会增加新代码，欢迎有兴趣的朋友共同学习，也欢迎提出意见。**

## 2 提交日志
[文档链接](./docs/changelog.md)

## 3 已实现模型
- [x] retinanet
- [x] yolov3
- [x] darknet-yolov3
- [x] darknet-yolov4
- [x] darknet-tiny_yolov3
- [x] darknet-tiny_yolov4
- [x] yolov5(s/m/l/x全部支持)

## 4 模型仓库
[文档链接](./docs/model_zoo.md)


## 5 安装说明
[文档链接](./docs/install.md)


## 6 统一数据集
   由于coco训练集图片太多了，跑到论文效果需要非常多时间，而本框架目的主要目的是快速验证
思想和算法(代码和mmdetection一致，应该没有错误)，故对主要以voc为主：
- coco
- voc2012和voc2007
- wider face


## 7 使用说明
### 7.1 训练、测试和demo使用说明

开启训练过程和mmdetection完全一致，例如：

```python
python train.py ../configs/retinanet/retinanet_r50_fpn_coco.py
```

开启测试过程和mmdetection完全一致，例如：

```python
# 评估   
python test.py ../configs/retinanet/retinanet_r50_fpn_coco.py ../tools/work_dirs/retinanet_r50_fpn_coco/latest.pth --eval bbox
# 显示
python test.py ../configs/retinanet/retinanet_r50_fpn_coco.py ../tools/work_dirs/retinanet_r50_fpn_coco/latest.pth --show
```

开启demo过程和mmdetection完全一致，例如：

```python
python image_demo.py demo.jpg ../configs/retinanet/retinanet_r50_fpn_coco.py ../tools/work_dirs/retinanet_r50_fpn_coco/latest.pth
```

### 7.2 darknet权重转化为mmdetection

转化脚本在tools/darknet里面

使用方法就是参考模型仓库文档里面的链接，将对应的权重下载下来，然后设置path就可以转化成功

例如tiny_yolov3权重：

1. 首先到https://github.com/AlexeyAB/darknet 对应的tiny_yolov3链接处下载对应权重

2. 打开tools/darknet/tiny_yolov3.py代码，修改tiny_yolov3_weights_path为你的下载的权重路径

3. 运行tiny_yolov3.py即可生成pth权重

4. 然后就可以直接训练或者测试了


### 7.3 yolov5权重转化为mmdetection

转化脚本在tools/darknet里面。以yolov5s为例

1. https://github.com/ultralytics/yolov5/releases/tag/v3.0 处下载yolo5s.pt或者直接运行convert_yolov5_weights_step1.py脚本，会自动下载
2. 运行convert_yolov5_weights_step1.py脚本，但是不好意思，你不能直接在我写的路径下运行，你需要将本脚本copy到yolov5工程目录下运行，并且必须pytorch版本大于等于1.6，原因是其保存的权重包括了picker对象，如果不放在相同路径下无法重新加载
3. 利用上一步所得权重，然后运行tools/darknet/convert_yolov5_weights_step2.py(在本框架中运行)，得到最终转化模型
4. 然后修改configs/yolo/rr_yolov5_416_coco.py对应的路径就可以进行前向测试或者mAP计算了

支持yolov5所有模型

## 8 mmdetection-mini独有特性

- loss分析工具 tools/loss_analyze.py
- anchor分析工具 tools/anchor_analyze.py
- 模型感受野自动计算工具 tools/receptive_analyze.py
- 前向推理时间分析工具 tools/inference_analyze.py
- 特征图可视化工具tools/featuremap_analyze
- darknet权重和mmdetection模型相互转化工具 tools/darknet
- 数据分析工具(hw ratio/hw scale/anchor kmean)tools/dataset_analyze
- 正样本可视化，需要开启debug模式
- 支持darknet系列模型权重在mmdetection中训练，目前支持4个主流模型yolov3/v4和tiny-yolov3/v4
- coco数据可视化工具，包括显示所有label和仅仅显示gt bbox格式，显示效果极佳(即使是voc数据，也推荐先转化为coco)
- 支持任意数据格式转coco类CocoCreator
- yolov5转化工具tools/darknet/convert_yolov5_weights_step2.py


## 9 mmdetection-mini工具汇总
- voc2coco工具 tools/convert/voc2coco
- 数据浏览工具 tools/browse_dataset


## 笔记(持续更新)

[第一篇：mmdetection最小复刻版(一)：整体概览](https://www.zybuluo.com/huanghaian/note/1742545)  
或者 [知乎文章](https://zhuanlan.zhihu.com/p/252616317)   
[第二篇：mmdetection最小复刻版(二)：RetinaNet和YoloV3分析](https://www.zybuluo.com/huanghaian/note/1742594)    
或者 [知乎文章](https://zhuanlan.zhihu.com/p/259487104)  
[第三篇：mmdetection最小复刻版(三)：神兵利器](https://www.zybuluo.com/huanghaian/note/1743266)    
或者 [知乎文章](https://zhuanlan.zhihu.com/p/259963010)  
[第四篇：mmdetection最小复刻版(四)：独家yolo转化内幕](https://www.zybuluo.com/huanghaian/note/1744915)      
[第五篇：mmdetection最小复刻版(五)：yolov5转化内幕](https://www.zybuluo.com/huanghaian/note/1745145)  


## 帮助

如果使用本框架有啥问题，想立刻联系我或者参与讨论的，可以加我微信，我拉你进入讨论群，微信号：hhahuanghaian .
请注明：mmdetection-mini，否则不会通过！  




       