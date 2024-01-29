# 使用YOLOv5进行自定义数据集训练的流程及结果

参考资料：https://docs.ultralytics.com/yolov5/tutorials/train_custom_data/

## 一、安装与配置

- git clone YOLOv5

```shell
git clone git@github.com:ultralytics/yolov5.git
```

- 新建python虚拟环境，避免版本相互干扰，我建立虚拟环境的指令如下：

```shell
conda create -n py39_yolov5 python=3.9
```

- ##### 安装相关依赖

```shell
pip install -r requirements.txt 
```

## 二、项目创建与配置

- 创建如图所示的文件结构

![image-20240129135431740](https://raw.githubusercontent.com/djz47/test/main/202401291354773.png)

- + 其中`Annotations`存放VOC格式的标签，如果直接创建的是YOLO格式的标签则此文件夹非必要，直接将标签存放到`labels`文件夹中即可
  + `image`文件夹中存放的是数据集中的图片内容
  + `ImageSets`文件夹是将`image`文件夹中的图片以及`labels`内的标签按照train、test、val划分开来，每种存储在对应的文件夹中
  + `labels`存放的是YOLO类型的标签

- **使用labelimg给图片数据集进行标注**
  - 将`/labelimg/data/predefined_classes.txt`中修改为自定义数据集中的类
  - 运行labelimg进行打标，AD左右切换图片，W创建标注框，将标注的VOC文件存储到项目临时文件夹中

- **使用数据集增强功能包进行数据集增强**
  - 将数据集的标注和标注好的标注文件分别存储到功能包`DataAugForObjectDetecton`中的`/data/Annotations`和`/data/images`中
  - 进入`DataAugForObjectDetecton.py`文件，到程序入口处修改`need_aug_num = 10`,这是每张图片进行扩增的数量
  - 运行`DataAugForObjectDetecton.py`文件，在Dataset文件夹下可以找到扩增后的标签与数据集
  - 将标签与数据集移动到项目中对应的文件夹

- **划分训练集、测试集和验证集**
  - 运行`spit.py`文件，调节其中的参数可以改变三者的比例大小
- **VOC标签转YOLO**
  - 运行`voc_lavel.py`文件，转化后的标签将存储在labels文件夹中

- **将`images`和`labels`中的文件按照`train`、`test`、`val`划分**
  - 运行`set_dir.py`,修改其中的地址，可以按照`spit.py`中生成的`.txt`文件自动从`images`和`labels`中找到对应文件并将其复制到`ImageSets`文件夹中的指定位置

- **`data`文件的配置**

  - 新建`.yaml`文件，具体内容如下：

    ```yaml
    train : datasets/winter_task5_YOLOv5/ImageSets/train
    val : datasets/winter_task5_YOLOv5/ImageSets/val
    test : datasets/winter_task5_YOLOv5_/ImageSets/test
    # 各数据集的路径
    nc: 7	#类别数
    names: # 类别名	["corn","corn_plant","cucumber","cucumber_plant","watermelon","rice","wheat"]
    ```

- **`cfg`文件的配置**

  - 复制`yolov5/models`文件夹下的`yolov5*.yaml`文件，具体选择结合训练要求

  - 修改`yolov5*.yaml`文件中第一行`nc`的值，修改为自己的类别数

    ```yaml
    # Parameters
    nc: 7 # number of classes
    depth_multiple: 0.33 # model depth multiple
    width_multiple: 0.50 # layer channel multiple
    anchors:
      ......
    ```

	## 三、项目的训练与检验

- 相关操作指令

  ```shell
  python train.py --data <path_of_data.yaml> --cfg <path_of_cfg.yaml> --weight <path_of_pretrained_model>
  # --weight参数可以不加如果没有预训练模型
  ```

  说明：
   `path_of_data.yaml`是data文件的路径。

  `path_of_cfg.yaml`是.cfg文件的路径。

  `path_of_pretrained_model`是预训练权重文件的路径

  ```python
  #模型检验
  python detect.py --weights <model> --source <arg>
  #python detect.py --source 0  # webcam
                            img.jpg  # image
                            vid.mp4  # video
                            screen  # screenshot
                            path/  # directory
                           'path/*.jpg'  # glob
                           'https://youtu.be/LNwODJXcvt4'  # YouTube
                           'rtsp://example.com/media.mp4'  # RTSP, RTMP,HTTP stream
  ```

## 四、`wandb`可视化工具的使用

- [配置`wandb`](https://xugaoxiang.com/2021/01/27/yolov5-wandb/)

- [`wandb`的进阶使用方法](https://zhuanlan.zhihu.com/p/522355820)

## 五、本次任务模型训练结果

- 类别划分

​		本次训练划分了七个类别

```
corn
corn_plant
cucumber
cucumber_plant
watermelon
rice
wheat
```

- 训练输出图像

  ![results](https://raw.githubusercontent.com/djz47/test/main/202401291447241.png)![labels_correlogram](https://raw.githubusercontent.com/djz47/test/main/202401291447173.jpg)![labels](https://raw.githubusercontent.com/djz47/test/main/202401291446046.jpg)![labels](https://raw.githubusercontent.com/djz47/test/main/202401291446132.jpg)![confusion_matrix](https://raw.githubusercontent.com/djz47/test/main/202401291446651.png)![train_batch0](https://raw.githubusercontent.com/djz47/test/main/202401291445581.jpg)

更多图像可以在[我的仓库](https://github.com/djz47/Homework/tree/master/%E8%A7%86%E8%A7%89task5/winter_task5_YOLOv5)查看

- 验证结果

  ![aug_3_watermelon07](https://raw.githubusercontent.com/djz47/test/main/202401291456803.jpg)