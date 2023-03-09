# yolov5_TensorRT_C++_VS2019
最近准备在工控机端利用TensorRT部署Yolov5，代码从https://github.com/yaoyi30/yolov5_TensorRT_C-  这位大佬的网址下载的，在自己电脑上部署时需要注意一些细节，在此记录如下

# 环境要求

VS2019（Release x64）

CUDA 11.3

CUDNN 8.6

TensorRT-8.5.1.7.Windows10.x86_64.cuda-11.8.cudnn8.6

Opencv452

注意：Tensorrt下载上面要求的cudnn版本一定要与cuda下的cudnn一致，否则可能会报错

# 属性表配置

**Opencv**

VC++目录 -> 包含目录：D:\Library\opencv452\build\include;

VC++目录 -> 库目录：D:\Library\opencv452\build\x64\vc15\lib;

链接器 -> 输入 -> 附加依赖项：opencv_world452.lib

**CUDA**

VC++目录 -> 包含目录：C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.3\include;

VC++目录 -> 库目录：C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.3\lib\x64;

链接器 -> 输入 -> 附加依赖项：C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.3\lib\x64\*.lib;

**TensorRT**

VC++目录 -> 包含目录：D:\Library\TensorRT-8.5.1.7\include;

VC++目录 -> 库目录：D:\Library\TensorRT-8.5.1.7\lib;

链接器 -> 输入 -> 附加依赖项：nvinfer.lib;nvinfer_plugin.lib;nvonnxparser.lib;nvparsers.lib;

**其他配置**

属性表->配置属性->C/C++->预处理器->预处理器定义：NDEBUG; _CONSOLE; _CRT_SECURE_NO_WARNINGS;NOMINMAX;

VC++目录 -> 包含目录：.\common

# 疑难问题

（1）编译时报如下错误：

![](.\images\logging.png)

该错误可能是因为TensorRT版本问题，只需要修改logging.h下的代码即可

 void log(Severity severity, const char* msg)  override
    {
        LogStreamConsumer(mReportableSeverity, severity) << "[TRT] " << std::string(msg) << std::endl;
    }

改为：

  void log(Severity severity, const char* msg) **noexcept** override
  {
        LogStreamConsumer(mReportableSeverity, severity) << "[TRT] " << std::string(msg) << std::endl;
   }

（2）在编译时经常报一些dll的库，如缺少cublasLt64_10.dll、zlibwapi.dll等

只需要根据需求在网上把这些库下载解压到

C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.3\bin即可，注意版本问题，本机电脑上一般是64位

# **优化技巧**

上述版本是比较简单的yolov5，可以根据需求对其进行优化，在此提出几点优化策略：

（1）预处理、归一化可以用CUDA编写，归一化也可以放到网络里面，这样减少CPU预处理的耗时；

（2）网络输出可以将三个输出Concat在一起，减少耗时；也可以将置信度和类别得分相乘再输出onnx

（3）Yolov5版本的NMS后处理可以放到网络里面，但目前只知道再TensorRT上可以。

## 联系

**有问题可以提issues 或者加qq群:722237896 询问**

