<!-- @format -->

# How to make onnx file

````
在本机 /home/nack/桌面/deeplearning/yolov5文件夹下
删除上次训练数据集文件夹，保留python所有文件
将图片与标签存入Annotations、images文件夹
运行split_train_val.py
修改classes后运行text_to_yolo.py
#运行clauculate_anchors.py
#在models文件夹修改yolov5s.yaml，根据clauculate_anchors.py生成的anchors四舍五入修改myvoc.yaml中的nc与anchors
#在data文件夹修改myvoc.yaml中的路径使用绝对路径，nc与names
运行python3 train.py --weights weights/yolov5s.pt  --cfg models/yolov5s.yaml  --data data/myvoc.yaml --epoch 50 --batch-size 16 --img 640   --device 0开始训练。
在服务器，先运行text_to_yolo.py
运行python3 train.py --weights weights/yolov5s.pt  --cfg models/yolov5s.yaml  --data data/myvoc.yaml --epoch 50 --batch-size 16 --img 640   --device 0
将得到的best.pt复制在yolov5-export目录下，
运行python3 export.py --weights best.pt --img 640 --batch 1 --include onnx得到onnx文件
````

# How to make rknn file

```
在本机/桌面/deeplearning/rknpu2/examples/rknn_yolov5_demo/convert_rknn_demo
/yolov5文件夹下
将生成的onnx文件放入onnx_models文件夹。
运行onnx2rknn.py
在rknn_models生成rknn模型
```

# 在车上 rknn_ros 文件夹下

```
#在/src文件下，修改yolov5_postprocess.cc中的lables，anchor
#在/include文件下，修改yolov5_postprocess.h修改#define OBJ_CLASS_NUM ：：
#重新编译
在/models/RK3588文件夹下，放入生成的rknn模型
在/launch/yolov5.launch文件夹下，修改权重名称，置信度等参数。
成功。
```

