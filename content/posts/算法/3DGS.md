+++
title = '3DGS学习'
date = '2025-07-02T15:38:47+08:00'
categories = ["3DGS"]
tags = ["3DGS"]
# draft = true
+++

## 学习记录

## 部署指南

项目地址：[3DGS](https://github.com/graphdeco-inria/gaussian-splatting)

需要注意的是，新版本代码存可能存在问题，导致训练出来的结果为一团白色点云或黑色点云，并且训练过程中```loss```不下降，所以不要使用新版本的代码

[issue记录1](https://github.com/graphdeco-inria/gaussian-splatting/issues/1050)

[issue记录2](https://github.com/graphdeco-inria/gaussian-splatting/issues/751)

因此，可使用旧版本的代码，链接如下：

项目地址：[3DGS old-ersion](https://github.com/camenduru/gaussian-splatting)

### 安装步骤

1. 克隆项目

```
// 保证所依赖的第三方库也切换到指定分支，否则可能还会出现奇怪的问题
git clone https://github.com/camenduru/gaussian-splatting.git --recursive
```

成功后显示如下：

```
Cloning into 'gaussian-splatting'...
remote: Enumerating objects: 603, done.
remote: Total 603 (delta 0), reused 0 (delta 0), pack-reused 603 (from 1)
Receiving objects: 100% (603/603), 2.09 MiB | 1.18 MiB/s, done.
Resolving deltas: 100% (347/347), done.
Submodule 'SIBR_viewers' (https://gitlab.inria.fr/sibr/sibr_core) registered for path 'SIBR_viewers'
Submodule 'submodules/diff-gaussian-rasterization' (https://github.com/graphdeco-inria/diff-gaussian-rasterization) registered for path 'submodules/diff-gaussian-rasterization'
Submodule 'submodules/simple-knn' (https://gitlab.inria.fr/bkerbl/simple-knn.git) registered for path 'submodules/simple-knn'
Cloning into '/home/xxx/Project-Python/gaussian-splatting/SIBR_viewers'...
warning: redirecting to https://gitlab.inria.fr/sibr/sibr_core.git/
remote: Enumerating objects: 3293, done.        
remote: Counting objects: 100% (322/322), done.        
remote: Compressing objects: 100% (174/174), done.        
remote: Total 3293 (delta 171), reused 280 (delta 148), pack-reused 2971 (from 1)        
Receiving objects: 100% (3293/3293), 9.98 MiB | 206.00 KiB/s, done.
Resolving deltas: 100% (2039/2039), done.
Cloning into '/home/xxx/Project-Python/gaussian-splatting/submodules/diff-gaussian-rasterization'...
remote: Enumerating objects: 329, done.        
remote: Total 329 (delta 0), reused 0 (delta 0), pack-reused 329 (from 1)        
Receiving objects: 100% (329/329), 111.68 KiB | 794.00 KiB/s, done.
Resolving deltas: 100% (216/216), done.
Cloning into '/home/xxx/Project-Python/gaussian-splatting/submodules/simple-knn'...
remote: Enumerating objects: 37, done.        
remote: Counting objects: 100% (37/37), done.        
remote: Compressing objects: 100% (34/34), done.        
remote: Total 37 (delta 18), reused 0 (delta 0), pack-reused 0 (from 0)        
Submodule path 'SIBR_viewers': checked out '14199886ae3f42358092c16ada909c1e0b1cba20'
Submodule path 'submodules/diff-gaussian-rasterization': checked out '8064f52ca233942bdec2d1a1451c026deedd320b'
Submodule 'third_party/glm' (https://github.com/g-truc/glm.git) registered for path 'submodules/diff-gaussian-rasterization/third_party/glm'
Cloning into '/home/xxx/Project-Python/gaussian-splatting/submodules/diff-gaussian-rasterization/third_party/glm'...
remote: Enumerating objects: 59935, done.        
remote: Counting objects: 100% (217/217), done.        
remote: Compressing objects: 100% (91/91), done.        
remote: Total 59935 (delta 170), reused 126 (delta 126), pack-reused 59718 (from 2)        
Receiving objects: 100% (59935/59935), 71.36 MiB | 1.17 MiB/s, done.
Resolving deltas: 100% (45400/45400), done.
Submodule path 'submodules/diff-gaussian-rasterization/third_party/glm': checked out '5c46b9c07008ae65cb81ab79cd677ecc1934b903'
Submodule path 'submodules/simple-knn': checked out '44f764299fa305faf6ec5ebd99939e0508331503'
```

2. 安装```submodule```中的模块

```
cd ./submodules/diff-gaussian-rasterization
python setup.py install


cd ./submodules/simple-knn
python setup.py install
```

如果出现下面的错误

```
FAILED: /home/xxx/Project-Python/gaussian-splatting/submodules/simple-knn/build/temp.linux-x86_64-cpython-311/simple_knn.o 
/usr/local/cuda-12.6/bin/nvcc --generate-dependencies-with-compile --dependency-output /home/xxx/Project-Python/gaussian-splatting/submodules/simple-knn/build/temp.linux-x86_64-cpython-311/simple_knn.o.d -I/home/xxx/Project-Python/gaussian-splatting/.venv/lib/python3.11/site-packages/torch/include -I/home/xxx/Project-Python/gaussian-splatting/.venv/lib/python3.11/site-packages/torch/include/torch/csrc/api/include -I/usr/local/cuda-12.6/include -I/home/xxx/Project-Python/gaussian-splatting/.venv/include -I/home/xxx/.local/share/uv/python/cpython-3.11.11-linux-x86_64-gnu/include/python3.11 -c -c /home/xxx/Project-Python/gaussian-splatting/submodules/simple-knn/simple_knn.cu -o /home/xxx/Project-Python/gaussian-splatting/submodules/simple-knn/build/temp.linux-x86_64-cpython-311/simple_knn.o -D__CUDA_NO_HALF_OPERATORS__ -D__CUDA_NO_HALF_CONVERSIONS__ -D__CUDA_NO_BFLOAT16_CONVERSIONS__ -D__CUDA_NO_HALF2_OPERATORS__ --expt-relaxed-constexpr --compiler-options ''"'"'-fPIC'"'"'' -DTORCH_API_INCLUDE_EXTENSION_H '-DPYBIND11_COMPILER_TYPE="_gcc"' '-DPYBIND11_STDLIB="_libstdcpp"' '-DPYBIND11_BUILD_ABI="_cxxabi1016"' -DTORCH_EXTENSION_NAME=_C -D_GLIBCXX_USE_CXX11_ABI=1 -gencode=arch=compute_86,code=compute_86 -gencode=arch=compute_86,code=sm_86 -std=c++17
/home/xxx/Project-Python/gaussian-splatting/submodules/simple-knn/simple_knn.cu:23: warning: "__CUDACC__" redefined
   23 | #define __CUDACC__
      | 
<command-line>: note: this is the location of the previous definition
/home/xxx/Project-Python/gaussian-splatting/submodules/simple-knn/simple_knn.cu:23: warning: "__CUDACC__" redefined
   23 | #define __CUDACC__
      | 
<command-line>: note: this is the location of the previous definition
/home/xxx/Project-Python/gaussian-splatting/submodules/simple-knn/simple_knn.cu(90): error: identifier "FLT_MAX" is undefined
    me.minn = { FLT_MAX, FLT_MAX, FLT_MAX };
                ^

/home/xxx/Project-Python/gaussian-splatting/submodules/simple-knn/simple_knn.cu(154): error: identifier "FLT_MAX" is undefined
   float best[3] = { FLT_MAX, FLT_MAX, FLT_MAX };
```

解决方案：打开```./submodules/simple-knn/simple_knn.cu```文件，引用```float.h```头文件，如下所示：

```
#define BOX_SIZE 1024

#include "cuda_runtime.h"
#include "device_launch_parameters.h"
#include "simple_knn.h"
#include <cub/cub.cuh>
#include <cub/device/device_radix_sort.cuh>
#include <vector>
#include <cuda_runtime_api.h>
#include <thrust/device_vector.h>
#include <thrust/sequence.h>
#define __CUDACC__
#include <cooperative_groups.h>
#include <cooperative_groups/reduce.h>
#include <float.h>

namespace cg = cooperative_groups;

struct CustomMin
{
	__device__ __forceinline__
		float3 operator()(const float3& a, const float3& b) const {
		return { min(a.x, b.x), min(a.y, b.y), min(a.z, b.z) };
	}
};
```

3. 下载测试数据


```
https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/datasets/input/tandt_db.zip
```

4. 训练

```
python train.py -s ./tandt_db/db/playroom
```

结果如下所示，显存不够，崩溃了：

```
Optimizing 
Output folder: ./output/fe22bd57-3 [02/07 15:33:01]
Tensorboard not available: not logging progress [02/07 15:33:01]
Reading camera 225/225 [02/07 15:33:02]
Loading Training Cameras [02/07 15:33:02]
Loading Test Cameras [02/07 15:33:05]
Number of points at initialisation :  37005 [02/07 15:33:05]
Training progress:  23%|██████████████████████▊                                                                           | 7000/30000 [06:31<29:44, 12.89it/s, Loss=0.0299219]
[ITER 7000] Evaluating train: L1 0.016766435094177724 PSNR 31.630204391479495 [02/07 15:39:43]

[ITER 7000] Saving Gaussians [02/07 15:39:43]
Training progress:  36%|██████████████████████████████████▉                                                              | 10800/30000 [12:12<29:36, 10.81it/s, Loss=0.0235506]Traceback (most recent call last):
  File "/home/xxx/Project-Python/gaussian-splatting/train.py", line 216, in <module>
    training(lp.extract(args), op.extract(args), pp.extract(args), args.test_iterations, args.save_iterations, args.checkpoint_iterations, args.start_checkpoint, args.debug_from)
  File "/home/xxx/Project-Python/gaussian-splatting/train.py", line 117, in training
    gaussians.densify_and_prune(opt.densify_grad_threshold, 0.005, scene.cameras_extent, size_threshold)
  File "/home/xxx/Project-Python/gaussian-splatting/scene/gaussian_model.py", line 394, in densify_and_prune
    self.densify_and_split(grads, max_grad, extent)
  File "/home/xxx/Project-Python/gaussian-splatting/scene/gaussian_model.py", line 372, in densify_and_split
    self.prune_points(prune_filter)
  File "/home/xxx/Project-Python/gaussian-splatting/scene/gaussian_model.py", line 293, in prune_points
    optimizable_tensors = self._prune_optimizer(valid_points_mask)
                          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/home/xxx/Project-Python/gaussian-splatting/scene/gaussian_model.py", line 278, in _prune_optimizer
    stored_state["exp_avg"] = stored_state["exp_avg"][mask]
                              ~~~~~~~~~~~~~~~~~~~~~~~^^^^^^
torch.OutOfMemoryError: CUDA out of memory. Tried to allocate 372.00 MiB. GPU 0 has a total capacity of 7.69 GiB of which 355.06 MiB is free. Process 5248 has 5.09 MiB memory in use. Including non-PyTorch memory, this process has 7.29 GiB memory in use. Of the allocated memory 5.26 GiB is allocated by PyTorch, and 1.85 GiB is reserved by PyTorch but unallocated. If reserved but unallocated memory is large try setting PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True to avoid fragmentation.  See documentation for Memory Management  (https://pytorch.org/docs/stable/notes/cuda.html#environment-variables)
Training progress:  36%|██████████████████████████████████▉                                                              | 10800/30000 [12:12<21:42, 14.74it/s, Loss=0.0235506]
```