# OPENCV笔记 

## [一、实用指令总结](https://blog.csdn.net/Vici__/article/details/100714822)

### 1、图片加载、显示和保存

```python
cv2.imread(filename, flags) #读取加载图片

cv2.imshow(winname, mat) #显示图片

cv2.waitKey()  #等待图片的关闭，可设置参数，为多少毫秒后自动关闭

cv2.imwrite(filename, img) # 保存图片
```
### 2、图像显示窗口创建与销毁

```python
cv2.namedWindow(winname, 属性)：#创建一个窗口

cv2.destroyWindow(winname)：销毁某个窗口

cv2.destroyAllWindows()：#销毁所有窗口
```

- **winname作为窗口的唯一标识，如果想使用指定窗口显示目标图像，需要让cv2.imshow(winname)中的winname与窗口的winname需要保持一致。**

  **窗口创建时可以添加的属性如下：**

```
cv2.WINDOW_NORMAL：窗口大小可以改变（同cv2.WINDOW_GUI_NORMAL）
cv2.WINDOW_AUTOSIZE：窗口大小不能改变
cv2.WINDOW_FREERATIO：窗口大小自适应比例
cv2.WINDOW_KEEPRATIO：窗口大小保持比例
cv2.WINDOW_GUI_EXPANDED：显示色彩变成暗色
cv2.WINDOW_FULLSCREEN：全屏显示
cv2.WINDOW_OPENGL：支持OpenGL的窗口
```


例如：

```python
# 第二个参数为窗口属性
cv2.namedWindow(winname="title", cv2.WINDOW_NORMAL)
```

### 3、图片的常用属性的获取

- **img.shape：**打印图片的高、宽和通道数（当图片为灰度图像时，颜色通道数为1，不显示）
- **img.size：**打印图片的像素数目
- **img.dtype：**打印图片的格式

### 4、生成指定大小的矩形区域（**ROI**）

​	一个图片img，它的某个像素点可以用 img[x, y, c] 表示（x，y为坐标，c为通道数）同理，这个图片的某个矩形区域可以表示为：img[x1:x2, y1:y2, c]（相当于截下一块矩形，左上角坐标为（x1, y1），右下角坐标为（x2, y2））其中 c 一般取值为0，1，2（BGR）代表第几个颜色通道，可以省略不写 img[x, y] 

**实例一：生成一个大小为（300，400）颜色通道为3的红色图片** 

    import cv2
    import numpy as np
     
    imgzero = np.zeros(shape=(300, 400, 3), dtype=np.uint8)
     
    imgzero[:, :] = (0, 0, 255) # (B, G, R)
    cv2.imshow("imgzero",imgzero)
    cv2.waitKey()

**实例二：从一张图片上截取一个矩形区域**

    import cv2
    import numpy as np
     
    img = cv2.imread("cat.jpg")
    # 输出（50，100）上的像素值
    num = img[50, 100]
    print(num)
     
    # 截取部分区域并显示
    region = img[50:100, 50:100]
    cv2.imshow("img", region)
    cv2.waitKey()
### 5、图片颜色通道的分离与合并

- **cv2.split(m)：**将图片m分离为三个颜色通道
- **cv2.merge(mv)：**将三个颜色通道合并为一张图片

### 6、两张图片相加，改变对比度和亮度

- **cv2.add(src1, src2)**：普通相加

- **cv2.addWeighted(src1, alpha, src2, w2，beta)**：带权相加
         src1：第一张图片
         alpha：第一张图片权重
         src2：第二张图片
         beta：第二张图片权重
         gamma：图1与图2作和后添加的数值。
         dst：输出图

### 7、像素运算(1)加减乘除

```python
def add_demo(m1, m2):
    dst = cv2.add(m1, m2)
    cv2.imshow("add", dst)
 
def subtract_demo(m1, m2):
    dst = cv2.subtract(m1, m2)
    cv2.imshow("subtract", dst)
 
def multiply_demo(m1, m2):
    dst = cv2.multiply(m1, m2)
    cv2.imshow("multiply", dst)
 
def divide_demo(m1, m2):
    dst = cv2.divide(m1, m2)
    cv2.imshow("divide", dst)
```

### 8、像素运算(2)均值&方差

```python
def demo(img):
    # 均值
    M1 = cv2.mean(img)
    print(M1)
    # 均值和方差
    M1, dev1 = cv2.meanStdDev(img)
    print(M1)
    print(dev1)
```

### 9、像素运算(3)逻辑运算——与、或、非、异或

```python
def logic_demo(m1, m2):
    dst = cv2.bitwise_and(m1, m2)
    cv2.imshow("bitwise_and", dst)
    dst = cv2.bitwise_or(m1, m2) 
    cv2.imshow("bitwise_or", dst)
    dst = cv2.bitwise_not(m1, m2)
    cv2.imshow("bitwise_not", dst)
    dst = cv2.bitwise_xor(m1, m2)
    cv2.imshow("bitwise_xor", dst)
```

### 10、彩色空间转换(修改编码方式)

- **cv2.cvtColor**

**原型：**cvtColor(src,code,dst=None,dstCn=None)

**作用：**将一幅图像从一个色彩空间转换到另一个色彩空间

**参数：**code，转换的色彩空间![image-20240127095352985](https://raw.githubusercontent.com/djz47/test/main/opencv_cvt_color.png)

### 11、cv2.inRange(hsv, lower_hsv, upper_hsv)

**利用cv2.inRange函数设阈值，这里注意用的颜色空间是hsv**

![image-20240127095531254](https://raw.githubusercontent.com/djz47/test/main/hsv%E5%AF%B9%E7%85%A7%E8%A1%A8.png)

```python
#实例
mask = cv2.inRange(hsv, lower_hsv, upper_hsv)
#hsv:hsv编码方式的图片，lower_hsv, upper_hsv为根据上表设定的数组
```

### 12、均值模糊、中值模糊，高斯模糊，双边模糊

- **cv2.blur**

原型：**blur(src,ksize,dst=None,anchor=None,borderType=None)**

作用：对图像进行算术平均值模糊

参数：ksize，卷积核的大小。dst，若填入dst，则将图像写入到dst矩阵。

- **cv2.medianBlur**

原型：**mediaBlur(src,ksize,dst=None)**

作用：对图像进行中值模糊

- **cv2.GaussianBlur**

原型：**GaussianBlur(src, ksize, sigmaX, dst=None, sigmaY=None, borderType=None)**
作用：对图像进行**高斯模糊**
参数：sigmaX，X方向上的方差，一般设为0让系统自动计算

- **cv2.bilateralFilter**

原型：**bilateralFilter(src, d, sigmaColor, sigmaSpace, dst=None, borderType=None)**
作用：对图像进行**双边模糊**
参数：**int d**: 表示在过滤过程中每个像素邻域的直径范围。如果这个值是非正数，则函数会从第五个参数sigmaSpace计算该值。 
**double sigmaColor**: 颜色空间过滤器的sigma值，这个参数的值月大，表明该像素邻域内有越宽广的颜色会被混合到一起，产生较大的半相等颜色区域。 （这个参数可以理解为值域核的）
**double sigmaSpace**: 坐标空间中滤波器的sigma值，如果该值较大，则意味着越远的像素将相互影响，从而使更大的区域中足够相似的颜色获取相同的颜色。当d>0时，d指定了邻域大小且与sigmaSpace无关，否则d正比于sigmaSpace. （这个参数可以理解为空间域核的）

### 13、二值化（cv2.threshold） 

原型：**threshold(src,thresh,maxval,type,dst=None)**

作用：将图像的每个像素点进行**二值化**

参数：thresh，阈值（最小值）。maxval，二值化的最大取值。
 type，二值化类型，一般设为0，也可以取以下的值：

![image-20240127100510599](https://raw.githubusercontent.com/djz47/test/main/cv2_threshold.png)

实例：

```python
#全局二值化
def threshold_demo():
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    ret, binary = cv2.threshold(gray, 0, 255, cv2.THRESH_BINARY | cv2.THRESH_OTSU)
    cv2.ADAPTIVE
    print("threshold value : %s\n" % ret)
    cv2.imshow("binary_global", binary)
 
threshold_demo()
```

```python
# 局部二值化
def local_threshold_demo():
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    binary = cv2.adaptiveThreshold(gray, 255, cv2.ADAPTIVE_THRESH_MEAN_C, cv2.THRESH_BINARY, 25, 10)
    #print("threshold value : %s\n" % ret)
    cv2.imshow("binary_local", binary)
```

### 14.模板匹配

模板匹配，就是在整个图像区域发现与给定子图像匹配的小块区域，需要**模板图像T**和待检测图像-**源图像S**；

工作方法：在待检测的图像上，从左到右，从上倒下计算模板图像与重叠子图像匹配度，匹配度越大，两者相同的可能性越大。

函数：**matchTemplate(image, templ, method, result=None, mask=None)**

参数：

**image**：源图像S；
**templ**：模板图像T，一般是源图像S中的一小块；
**method**：模板匹配算法（cv.TM_SQDIFF_NORMED最小时最相似，其他最大时最相似）

![image-20240127101127630](https://raw.githubusercontent.com/djz47/test/main/matchTemplate.png)

**实例及运行结果图片**

```python
import cv2
import numpy as np
from matplotlib import pyplot as plt
 
 
def template_demo():
    tpl = cv2.imread("sample.jpg")
    target = cv2.imread("target.jpg")
    cv2.imshow("tpl", tpl)
    cv2.imshow("target", target)
    methods = [cv2.TM_SQDIFF_NORMED, cv2.TM_CCORR_NORMED, cv2.TM_CCOEFF_NORMED]  # 三种模板匹配方法
    th, tw = tpl.shape[:2]
    
    for md in methods:
        print(md)
        result = cv2.matchTemplate(target, tpl, md)  # 得到匹配结果
        min_val, max_val, min_loc, max_loc = cv2.minMaxLoc(result)
        if md == cv2.TM_SQDIFF_NORMED:  # cv.TM_SQDIFF_NORMED最小时最相似，其他最大时最相似
            tl = min_loc
        else:
            tl = max_loc
 
        br = (tl[0] + tw, tl[1] + th)
        cv2.rectangle(target, tl, br, (0, 0, 255), 2)  # tl为左上角坐标，br为右下角坐标，从而画出矩形
        cv2.imshow("match-"+np.str(md), target)
 
template_demo()
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![image-20240127101300001](https://raw.githubusercontent.com/djz47/test/main/202401271013.png)

### 15.图像梯度

- **cv2.Sobel**

​	Sobel算子是高斯平滑和微分操作的结合体，所以他的抗噪声能力很好。他计算的是一阶导数，可以自己定义x方向或者y方向。

原型： **Sobel(src,ddepth,dx,dy,dst=None,ksize=None,scale=None,delta=None,borderType=None)**

作用：对图像进行Sobel算子计算。检测出其边缘。

参数：dx，x方向上的导数阶数；dy，y方向上的导数阶数。

- **cv2.scharr**

​	是Sobel的优化版，在使用3*3卷积核时这个优于Sobel，其它尺寸的卷积核用Sobel就行。

原型：**Scharr(src, ddepth, dx, dy, dst=None, scale=None, delta=None, borderType=None, /)**

- **cv2.Laplacian**

​	Laplacian算子是个二阶微分。下面两个卷积核，靠上的是4邻域的，靠下的是8邻域的。函数默认为8邻域。

原型:**Laplacian(src,ddepth,dst=None,ksize=None,scale=None,delta=None,borderType=None)**

作用：检测图像边缘。

参数：ddepth，图像位深度，对于灰度图来说，其值为：cv2.CV_8U。ksize，希望使用的卷积核的大小。scale，是缩放导数的比例常数。
实例：

```python
import cv2 as cv
import numpy as np
 
 
def laplace_demo(image):  # 二阶导数，边缘更细
    dst = cv2.Laplacian(image,cv2.CV_32F)
    lpls = cv2.convertScaleAbs(dst)
    cv2.imshow("laplace_demo", lpls)
 
 
src = cv.imread("../images/lena.jpg")
cv.imshow("lena",src)
laplace_demo(src)
cv.waitKey(0)
cv.destroyAllWindows()
```

### 16.VideoCapture 类

- **cv2.VideoCapture**

原型：**VideoCapture(*args,**kwargs)**

作用：初始化VideoCapture类并利用构造函数读入该视频的当前帧。

参数：一般仅填入一个，即文件名。如果填入整数，则打开对应的捕获设备ID。若为0，则打开默认摄像头。

- **VideoCapture.get**

原型：**VideoCapture.get(self,propld)**

作用：返回该视频的propld所指定的属性

参数：propld，为需要读取的视频属性参数位，一般以cv2.CAP_PROP_ 开头

|         参数名称          |                        作用                        |
| :-----------------------: | :------------------------------------------------: |
|   CV_CAP_PROP_POS_MSEC    | 当前视频文件的时间位置（返回毫秒）或视频捕获时间戳 |
|  CV_CAP_PROP_POS_FRAMES   |              从0开始的解码/捕获时间帧              |
| CV_CAP_PROP_POS_AVI_RATIO |  返回视频文件的相关位置：0，视频开始。1，视频结束  |
|  CV_CAP_PROP_FRAME_WIDTH  |                   视频流中的帧宽                   |
| CV_CAP_PROP_FRAME_HEIGHT  |                   视频流中的帧高                   |
|      CV_CAP_PROP_FPS      |                        帧率                        |
|    CV_CAP_PROP_FOURCC     |               返回解码方式中的四字符               |
|  CV_CAP_PROP_FRAME_COUNT  |                  视频文件的总帧数                  |
|    CV_CAP_PROP_FORMAT     |        由retrieve()函数返回的矩阵对象的格式        |
|     CV_CAP_PROP_MODE      |          用于预测当前捕获模式的后端专用值          |
|  CV_CAP_PROP_BRIGHTNESS   |             图像的亮度（仅用于摄像头）             |
|                           |                                                    |
|   CV_CAP_PROP_CONTRAST    |            图像的对比度（仅用于摄像头）            |
|  CV_CAP_PROP_SATURATION   |            图像的饱和度（仅用于摄像头）            |
|      CV_CAP_PROP_HUE      |             图像的色调（仅用于摄像头）             |
|     CV_CAP_PROP_GAIN      |              图像增益（仅用于摄像头）              |
|   CV_CAP_PROP_EXPOSURE    |               曝光度（仅用于摄像头）               |
|  CV_CAP_PROP_CONVERT_RGB  |      用于预测图像是否应该被转换为RGB的布尔位       |
| CV_CAP_PROP_RECTIFICATION |                  立体相机的纠正位                  |

- **VideoCapture.isOpened**

参数：无

作用：判断设备/文件是否读取成功，若成功，返回True

- **VideoCapture.release**

参数：无

作用：关闭文件/摄像头

- **VideoCapture.read**

参数：无

返回值：bool，numpy.array

作用：读取该文件/摄像头的下一帧，成功与否由bool返回值决定，返回的帧矩阵为第二个参数

### 17.VideoWriter类

- **cv2.VideoWriter**

原型：**cv2.VideoWriter(*args,**kwargs)**

参数：第一个，写入的视频文件名。第二个，由cv2.VideoWriter_fourcc返回的视频制式特定代码，通常有XVID，MPEG等。第三个，该视频的fps。第四个，一个tuple，为该视频的宽、高。

- **VideoWriter.write**

原型：**VideoCapture.write(image)**

作用：将当前帧内容写入视频文件

参数：image，写入的当前帧

# 二、常用数据增强指令

### 1.旋转

```python
#先生成一个旋转矩阵，参数分别是旋转中心，旋转度数和放缩倍数
M = cv2.getRotationMatrix2D((img_w/2,img_h/2), 120, 1)
#进行旋转
rotated = cv2.warpAffine(img, M, (img_w, img_h))
cv2.imshow('rotated', rotated)
```

### 2.镜像

```python
#水平镜像
h_flip=cv2.flip(img,1)
cv2.imshow("Flipped Horizontally",h_flip)
#垂直镜像
v_flip=cv2.flip(img,0)
cv2.imshow("Flipped Vertically",v_flip)
#水平垂直镜像
hv_flip=cv2.flip(img,-1)
cv2.imshow("Flipped Horizontally & Vertically",hv_flip)
```

### 3. 缩放

```python
#缩放
height,width=img.shape[:2]
res=cv2.resize(img,(2*width,2*height)) #宽和高各放大2倍
cv2.imshow("large",res)    
```

### 4.高斯滤波

通俗的讲，高斯滤波就是对整幅图像进行加权平均的过程，每一个像素点的值，都由其本身和邻域内的其他像素值经过加权平均后得到。高斯滤波的具体操作是：用一个模板（或称卷积、掩模）扫描图像中的每一个像素，用模板确定的邻域内像素的加权平均灰度值去替代模板中心像素点的值

```python
import cv2
# ----------------------读取图片-----------------------------
img = cv2.imread('gauss_noise.png')
# ----------------------高斯滤波-----------------------------
result_5 = cv2.GaussianBlur(img, (5, 5), 0)  # 5x5
result_9 = cv2.GaussianBlur(img, (9, 9), 0)  # 9x9（卷积层越大越模糊）
# ----------------------显示结果-----------------------------
cv2.imshow('origion_pic', img)
cv2.imshow('5x5_filtered_pic', result_5)
cv2.imshow('9x9_filtered_pic', result_9)
cv2.waitKey(0)
```

### 5.膨胀

​	膨胀操作是将图像中的物体区域进行扩展的过程。以下是一个使用膨胀操作的示例代码：

```python
import cv2
import numpy as np

# 读取图像文件
image = cv2.imread('image.jpg', cv2.IMREAD_GRAYSCALE)

# 定义膨胀操作的结构元素
kernel = np.ones((3, 3), np.uint8)

# 进行膨胀操作
dilated_image = cv2.dilate(image, kernel, iterations=1)
```

在上述示例中，我们使用 `dilate` 函数对灰度图像进行膨胀操作。 `kernel` 参数定义了膨胀操作的结构元素，可以是矩形、椭圆或自定义形状。 `iterations` 参数表示膨胀的次数，较大的值将产生更明显的膨胀效果。

### 6.腐蚀

腐蚀操作是将图像中的物体区域进行收缩的过程。以下是一个使用腐蚀操作的示例代码：

```javascript
import cv2
import numpy as np

# 读取图像文件
image = cv2.imread('image.jpg', cv2.IMREAD_GRAYSCALE)

# 定义腐蚀操作的结构元素
kernel = np.ones((3, 3), np.uint8)

# 进行腐蚀操作
eroded_image = cv2.erode(image, kernel, iterations=1)
```

在上述示例中，我们使用 `erode` 函数对灰度图像进行腐蚀操作。 `kernel` 参数定义了腐蚀操作的结构元素，可以是矩形、椭圆或自定义形状。 `iterations` 参数表示腐蚀的次数，较大的值将产生更明显的腐蚀效果。

### 7.叠加

```python
import cv2
import numpy as np
 
img1 = cv2.imread('4.jpg')
img2 = cv2.imread('1.jpg')
 
imgadd = cv2.add(cv2.resize(img1,(200,200)),cv2.resize(img2,(200,200)))
cv2.imshow('imgadd',imgadd)
cv2.waitKey(0)
```

