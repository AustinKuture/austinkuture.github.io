---
layout: wiki
title: 基于Ray局域网推理计算
categories: Anything
description: 基于Ray局域网推理计算
keywords: 基于Ray局域网推理计算
---



# 基于Ray局域网推理计算

```
硬件：GPU主机、Mac 电脑
系统环境：ubuntu、Mac OS
软件版本：v2.45.0
```

Ray 是一个面向分布式计算的开源框架，专为简化大规模计算任务的并行化和分布式执行而设计，广泛应用于机器学习、强化学习、超参数优化和通用分布式计算场景。其核心特点是 **高性能、低延迟、易用性**，支持 Python 和 Java 接口，并兼容常见的机器学习库（如 PyTorch、TensorFlow）。

在局域网中利用远程 GPU 主机（IP：192.168.1.130）的 A6000 资源，并通过 Mac（IP：192.168.1.80）提交深度学习任务（如 YOLOv11），可以按照以下步骤使用 Ray 框架：



### **1. 环境准备**
#### **远程 GPU 主机（192.168.1.130）**
1. **安装依赖**
   - 安装 NVIDIA 显卡驱动、CUDA Toolkit 和 cuDNN。
   - 安装 Python 和 PyTorch（或 TensorFlow），确保支持 GPU：
     ```bash
     pip install torch  # 或 tensorflow
     python -c "import torch; print(torch.cuda.is_available())"  # 验证 GPU 可用
     ```
2. **安装 Ray**
   ```bash
   pip install ray
   ```

#### **Mac 电脑（192.168.1.80）**
1. **安装 Ray**
   ```bash
   pip install ray
   ```



### **2. 启动 Ray 集群**
在远程 GPU 主机上启动 Ray 集群（作为**主节点**）：
```bash
ray start --head --port=6379  # 启动主节点，端口 6379 是默认的 Ray 端口
```
> 注意：确保局域网内防火墙允许以下端口：
- `6379`（Ray 节点通信）
- `8265`（仪表板，可选）



### **3. 从 Mac 连接集群**

在 Mac 上连接到远程集群：
```python
import ray
ray.init(address="ray://192.168.1.130:6379")  # Mac 作为客户端提交任务到集群
```



### **4. 编写分布式任务（以 YOLOv11 为例）**

假设你使用 PyTorch 实现 YOLOv11，需要将模型加载到 GPU 并执行推理。

#### **修改代码以利用 GPU**
```python
import torch
import ray

# 在 GPU 节点上定义任务
@ray.remote(num_gpus=1)  # 要求每个任务使用 1 个 GPU
def run_yolo_inference(image_path):
    import torch
    model = torch.hub.load('ultralytics/yolov11', 'yolov11s')  # 加载预训练模型
    results = model(image_path)  # 推理
    return results.save()  # 返回结果路径

# 提交任务到远程 GPU 节点
result = ray.get(run_yolo_inference.remote("path/to/image.jpg"))
print(result)
```



### **5. 关键配置和注意事项**

#### **GPU 资源调度**
- 使用 `@ray.remote(num_gpus=1)` 确保任务分配到 GPU 节点。
- 若远程主机有多块 GPU，可通过 `CUDA_VISIBLE_DEVICES` 控制使用哪些 GPU：
  ```bash
  CUDA_VISIBLE_DEVICES=0 ray start --head  # 仅暴露第一个 GPU
  ```

#### **数据传输**
- 如果输入数据（如图像）存储在 Mac 上，需先传输到远程节点，或共享存储（如 NFS/Samba）。
- 可通过 `ray.put()` 将小数据上传到集群内存，或直接读取远程节点上的文件。

#### **调试和监控**
- 访问 Ray 仪表板（默认在主节点的 `8265` 端口）：
  ```
  http://192.168.1.130:8265
  ```



### **6. 完整示例**

#### **远程 GPU 主机**
```bash
# 启动主节点（确保已安装 Ray 和 PyTorch）
ray start --head --num-gpus=1
```

#### **Mac 电脑**
```python
# client.py
import ray
ray.init(address="ray://192.168.1.130:6379")

@ray.remote(num_gpus=1)
def gpu_task():
    import torch
    return torch.cuda.is_available()

print(ray.get(gpu_task.remote()))  # 应输出 True，表示任务在 GPU 节点上运行
```



### **7. 常见问题**

#### **1. 无法连接到集群**
- 确保 IP 和端口正确，检查防火墙设置。
- 使用 `nmap 192.168.1.130 -p 6379` 测试端口是否开放。

#### **2. 任务未使用 GPU**
- 检查任务是否声明 `num_gpus=1`。
- 在远程节点运行 `nvidia-smi` 观察 GPU 使用情况。

#### **3. Mac 与远程主机的 Python 版本不一致**
- 确保 Ray 版本一致（建议升级到最新版）：
  ```bash
  pip install --upgrade ray
  ```



### **8. 扩展方案**

- **多节点扩展**：可在更多 GPU 机器上运行 `ray start --address=192.168.1.130:6379` 加入集群。
- **资源隔离**：通过 `runtime_env` 指定容器或虚拟环境，隔离不同任务依赖。

