# 使用YOLOv4进行自定义数据集训练的流程及结果

## **一、安装与配置**

-  git clone Darknet github

```shell
git clone https://github.com/AlexeyAB/darknet
```

- 修改Makefile

​	进入 darknet_master 文件架修改 Makefile，将GPU, CUDNN, CUDNN_HALF, OPENCV 修改为1，预设值为0

![image-20240128141023014](https://raw.githubusercontent.com/djz47/test/main/202401281410050.png)

- 进行编译

```shell
cd darknet
make
```

##  二、项目创建

- 先将数据集图片暂存至临时文件夹中

- 创建如图的文件结构

  ![image-20240128142907410](https://raw.githubusercontent.com/djz47/test/main/202401281429440.png)

- **使用labelimg给图片数据集进行标注**
  1. 将`/labelimg/data/predefined_classes.txt`中修改为自定义数据集中的类
  2. 运行labelimg进行打标，AD左右切换图片，W创建标注框，将标注的VOC文件存储到项目临时文件夹中

- **使用数据集增强功能包进行数据集增强**
  1. 将数据集的标注和标注好的标注文件分别存储到功能包`DataAugForObjectDetecton`中的`/data/Annotations`和`/data/images`中
  2. 进入`DataAugForObjectDetecton.py`文件，到程序入口处修改`need_aug_num = 10`,这是每张图片进行扩增的数量
  3. 运行`DataAugForObjectDetecton.py`文件，在Dataset文件夹下可以找到扩增后的标签与数据集
  4. 将标签与数据集移动到项目中对应的文件夹

- 划分训练集、测试集和验证集

  运行`spit.py`文件，调节其中的参数可以改变三者的比例大小

- VOC标签转YOLO

  运行`voc_lavel.py`文件，转化后的标签将存储在label文件夹中

- 写入2007_test.txt(2007_train.txt 2007_val.txt)

  运行`write_name.py`文件，可以进行文件的写入

- 修改`yolov4-tiny.cfg`文件

  ```python
  classes #改为自定义的类别数
  filters = (classes + 5)*3
  ```

## 三、模型训练与验证操作

- 相关操作指令

  ```shell
  ./darknet detector train [.data] [.cfg] [.weight]
  # 模型训练
  ```

  说明：
   `.data`是.data文件的路径。

  `.cfg`是.cfg文件的路径。

  `.weight`是预训练权重文件的路径，可以是.weight文件，也可以是.backup文件。

  ```shell
  ./darknet detector train [.data] [.cfg] [.weight]
  #检测图片
  ./darknet detector demo [.data] [.cfg] [.weight] [-thresh 0.25] [xxx.mp4]
  # 检测视频
  -dont_show -ext_output [.txt] result.txt
  #批量检测
  ./darknet detector demo [.data] [.cfg] [.weight] [-thresh 0.25] [http://192.168.1.108:8080/video?dummy=x.mjpg -i 0]
  #检测网络摄像头
  ./darknet detector map [.data] [.cfg] [.weight]
  #评价模型
  ```

## 四、本次任务模型训练结果

- 类别划分

  本次训练划分了七个类别

  ```
  corn
  corn_plant
  cucumber
  cucumber_plant
  watermelon
  rice
  wheat
  ```

- 训练过程损失曲线

  ![chart_yolov4-tiny](https://raw.githubusercontent.com/djz47/test/main/202401281500174.png)

- 图片验证结果![2024-01-28_13-34](https://raw.githubusercontent.com/djz47/test/main/202401281500712.png)