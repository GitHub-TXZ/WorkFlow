# 边缘主体感知的医学影像分割工作流

## 简介
医学图像分割是计算机辅助诊断（CAD）系统中用于诊断和预后许多疾病（如肿瘤、中风和皮肤癌）的基本步骤。在临床实践中，医学图像由具有丰富专业知识的医生和放射科医生手动分割，这既费时又繁琐，并且专家之间的观察者间差异很大因此，在本研究中，我们的目标是开发一种基于 CNN 的分割方法，用于自动和准确的医学图像分割。
本方法开发了一种用于医学图像分割的新型二维主体边缘感知网络（称为 BEA-Net），该网络具有多尺度短期连接功能。BEA-Net中的一个多尺度短期连接模块可以捕获多尺度信息，并且在更少的参数下替换编码器和解码器深层中的普通卷积层。为了从多层卷积特征中分别学习有效的体和边缘特征，提出了一个新的主体生成模块和一个新的边缘生成模块。为了充分利用多层信息，基于所提出的体和边缘生成模块，设计了与编码器对称的并行体和边缘解码器。此外，利用主体和边缘解码器的融合和深度监督来实现更好的分割。
所开发的工作流。


### 核心特性
测试数据为开源数据集AISD， 数据量共计397个3D体积，包含在不同中心的不同扫描参数和协议下的多种NCCT数据，选取官方发布的397个体积作为训练集，其余52个作为测试集。Dice平均值达到57.86%, HD95平均值达到了35.51 mm， 符合任务书/支撑材料中的指标。

---

## 环境要求
### 硬件环境
- **CPU**: Intel(R) Xeon(R) Platinum 8373C CPU @ 2.60GHz  
- **内存**: 251GB  
- **GPU**: NVIDIA GeForce RTX 3090  
- **存储**: 15.7TB HDD  

### 软件环境
- **Python**: 3.10.13  
- **Pytorch**: 2.0.1  
- **Torchvision**: 0.8.2
- **Java**: 17.0.10
- **Nextflow**: 24.10.1  

---

## 数据说明
### 输入数据
- 测试图像文件夹：`test_input`
- 数据预处理：图像已完成裁剪、重采样、标准化等预处理流程，确保分割任务的一致性。

### 输出结果
- 分类结果文件：`test_output`  
- 内容格式：包含分割掩码输出。

---

## 快速开始
### 环境安装（如果直接有安装pytorch的环境，可以忽略这一步）
1. 创建并激活Python虚拟环境：
   ```bash
   conda create -n local_test python=3.10
   conda activate local_test
   ```
2. 安装依赖：
   ```bash
   pip install torch==2.0.1 torchvision==0.8.2 torchaudio==0.7.2
   pip install -r requirements.txt
      ```
   或者手动安装文件头部少数依赖包即即可（建议）


### 运行流程
1. **准备数据**：将待分割的医学影像放入`test_input`文件夹。
2. **下载训练好的权重**：为了使用我们的模型，你需要下载预先训练好的权重。请使用以下链接和提取码进行下载：
- **百度网盘链接**：[点击这里下载](https://pan.baidu.com/s/1sR8NUNE8aylamIDDFvJjiA) 
- **提取码**：`fr23`
3. **启动推理**：
   - 使用Nextflow执行工作流：
     ```bash
     nextflow run main.nf
     ```
   - 或直接运行Python脚本（建议）：
     ```bash
     python bea_net.py
     ```
4. **查看结果**：分割结果存储在`test_output/bea_net_result.nii.gz`文件中，执行以下命令即可查看：
   ```bash
    sudo apt-get install fsl
    fslview bea_net_result.nii.gz
   ```
或者使用ITK-SNAP软件打开（建议）

---

## 测试数据集
- **数据集名称**：AISD
- **数据规模**：397张3D NCCT扫描 
- **分割类别**：正常组织 or 缺血组织  
- **实验结果**：公开的测试集上（52个数据），Dice平均值达到57.86%, HD95平均值达到了35.51 mm。

---



# 基于CNN和Transformer的脑卒中病灶分割工作流

## 简介
脑卒中是全球最常见的脑血管疾病，也是第三大死因。急性缺血性中风 (AIS) 占所有中风病例的 75-85%，是由于流向大脑的血流减少导致脑细胞受损所致。在临床实践中，通过脑卒中病灶分割获得的脑卒中病灶的大小和位置对于AIS的诊断、治疗决策和预后至关重要。与 MRI 相比，非增强 CT (NCCT) 具有快速采集能力和低成本，是 AIS 病变测量的主流成像方式。然而，由于其低对比度、噪声和伪影，NCCT 上的 AIS 病灶分割具有挑战性。 AIS病灶的手动分割仍然是NCCT上病灶体积测量的标准方法，尽管它既耗时又繁琐。 NCCT 上的自动化、准确的中风病灶分割方法在临床上是可取的。
因此，本方法提出了一种新的混合 CNN 和 Transformer 网络，具有循环特征交互和双边差异学习，用于 NCCT 扫描上的 AIS 病灶分割。为了有效捕获 CNN 和 Transformer 特征，本方法分别设计了CNN 编码器和 Transformer编码器。为了解决 Transformer 的弱归纳偏差，通过引入卷积模块设计了一个新的 Transformer（称为 Hybridformer）块，使模型能够更有效地收敛。为了实现 CNN 和 Transformer 特征之间的有效交互，设计了一个带有基于注意力的 CNN-to-Transformer 和 Transformer-to-CNN 模块的循环特征交互模块。为了有效利用临床先验知识来增强AIS病灶分割，我们设计了双边差异学习模块，可以学习高级语义空间中左脑和右脑之间的差异，从而省略复杂的配准或对齐操作。



### 核心特性
测试数据为开源数据集AISD， 数据量共计397个3D体积，包含在不同中心的不同扫描参数和协议下的多种NCCT数据，选取官方发布的397个体积作为训练集，其余52个作为测试集。Dice平均值达到61.63%, HD95平均值达到了32.73 mm， 符合任务书/支撑材料中的指标。

---

## 环境要求
### 硬件环境
- **CPU**: Intel(R) Xeon(R) Platinum 8373C CPU @ 2.60GHz  
- **内存**: 251GB  
- **GPU**: NVIDIA GeForce RTX 3090  
- **存储**: 15.7TB HDD  

### 软件环境
- **Python**: 3.10.13  
- **Pytorch**: 2.0.1  
- **Torchvision**: 0.8.2
- **Java**: 17.0.10
- **Nextflow**: 24.10.1  

---

## 数据说明
### 输入数据
- 测试图像文件夹：`test_input`
- 数据预处理：图像已完成裁剪、重采样、标准化等预处理流程，确保分割任务的一致性。

### 输出结果
- 分类结果文件：`test_output`  
- 内容格式：包含分割掩码输出。

---

## 快速开始
### 环境安装（如果直接有安装pytorch的环境，可以忽略这一步）
1. 创建并激活Python虚拟环境：
   ```bash
   conda create -n local_test python=3.10
   conda activate local_test
   ```
2. 安装依赖：
   ```bash
   pip install torch==2.0.1 torchvision==0.8.2 torchaudio==0.7.2
   pip install -r requirements.txt
      ```
   或者手动安装文件头部少数依赖包即即可（建议）


### 运行流程
1. **准备数据**：将待分割的医学影像放入`test_input`文件夹。
2. **下载训练好的权重**：为了使用我们的模型，你需要下载预先训练好的权重。请使用以下链接和提取码进行下载：
- **百度网盘链接**：[点击这里下载](https://pan.baidu.com/s/1zKRKW_IXLncR0ciQsFr_4g) 
- **提取码**：`bv4j`
3. **启动推理**：
   - 使用Nextflow执行工作流：
     ```bash
     nextflow run main.nf
     ```
   - 或直接运行Python脚本（建议）：
     ```bash
     python clseg-net.py
     ```
4. **查看结果**：分割结果存储在`test_output/bea_net_result.nii.gz`文件中，执行以下命令即可查看：
   ```bash
   sudo apt-get install fsl
   fslview clseg_net_result.nii.gz
   ```
或者使用ITK-SNAP软件打开（建议）

---

## 测试数据集
- **数据集名称**：AISD
- **数据规模**：397张3D NCCT扫描 
- **分割类别**：正常组织 or 缺血组织  
- **实验结果**：公开的测试集上（52个数据），Dice平均值达到61.63%, HD95平均值达到了32.73 mm。

---
