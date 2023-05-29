# PyTorch中GPU的调用原理

## 1 现代深度学习框架概述

深度学习框架是用于支持深度学习算法的软件工具集，它们提供了各种功能和工具，使得开发者可以更加高效地构建、训练和部署深度学习模型。常见的深度学习框架包括TensorFlow、Keras、Caffe、PyTorch、Theano、CNTK、MXNet、PaddlePaddle、Deeplearning4j、ONNX等，它们在发展关系上或是继承、竞争、补充等，在不同层级的应用领域分别具有不同的优势和特点。其基本原理是将深度学习的部分常见组件包装为组件并提供给用户进行模型开发，常见的组件包括各种网络层、神经元结构及激活函数，如全连接层、卷积层、池化层、RNN、LSTM、ReLU、sigmoid等。此外，它们还提供了各种优化器和损失函数，如随机梯度下降(SGD)、Adam、交叉熵损失函数、均方差损失函数等。通过使用这些工具，开发者可以快速地构建和训练复杂的深度学习模型。

深度学习框架的应用领域非常广泛，包括计算机视觉、自然语言处理、语音识别、推荐系统等。其中，计算机视觉是最常见的应用领域之一。例如，图像分类、目标检测、人脸识别等任务都可以使用深度学习框架来搭建模型解决。此外，自然语言处理也是另一个重要的应用领域，可以使用深度学习框架来构建文本分类、情感分析、机器翻译等模型。它的价值在于提高了开发效率和模型性能。通过使用这些框架，开发者可以快速地构建和训练复杂的深度学习模型，同时还可以利用各种优化器和损失函数来提高模型的准确性和泛化能力。此外，深度学习框架还提供了可视化工具，可以帮助开发者更好地理解和调试模型，并且由于其广泛的应用领域和强大的性能，深度学习框架已经成为了人工智能领域不可或缺的一部分。

## 2 PyTorch中GPU的调用原理

[Pytorch](https://pytorch.org/get-started/locally/)是一个基于Python的开源深度学习框架，其功能包括自动求导、动态计算图构建和GPU加速等。相比于TensorFlow和Caffe等其他深度学习框架，PyTorch更加灵活、易于使用、更加高效并且社区活跃，PyTorch的动态计算图构建方式使得用户可以更加自由地构建和修改神经网络模型，而不必受到静态计算图的限制，提供了简单易用的API和丰富的文档，使得初学者可以快速上手并进行实验，支持GPU加速，并且在CPU和GPU之间的数据传输方面也进行了优化，从而提高了训练速度和效率，拥有庞大的开发者社区，不断推出新的功能和改进，同时也支持用户进行贡献和交流。以下将以PyTorch为主线来分析现代深度学习框架中GPU的调用原理，具体的源码及分析内容均基于PyTorch官方[GitHub仓库](https://github.com/pytorch/pytorch/commit/af70fe9f3e4018531722ab2d9d3ede9150adf2e7)的main分支的`af70fe9`版本。

### 2.1 GPU的数据构造及模型加载

使用GPU进行模型的训练和推理时，通常需要使用`[model|data].to(device)`或`[model|data].cuda()`以将数据及模型加载至GPU，PyTorch内部集成了来自cuda、MKL以及多个计算库的多个模块，因此关于最底层的内存分配、调度以及计算并行化并不需要由PyTorch自行完成，但是PyTorch作为多平台的深度学习框架，其需要基础构建机制以充分利用不同平台的计算特性。以下将介绍PyTorch中最基础的`Tensor`数据类型是如何产生以及被作用到GPU设备上的，神经网络模型是如何使用被加载到GPU上的。

#### 2.1.2 GPU数据构造原理

PyTorch中最基础的数据类型为`Tensor`，网络中所有可学习的参数类型`Parameter`都由其派生而来，包括用户定义的不参与到网络当中的变量，虽然不进入DAG图网络，但仍然是`Tensor`数据类型。从源码中可知，`Tensor`的基类为`_TensorBase`，但是该类在源码中并没有对应的类，其原因是`_TensorBase`类需要在PyTorch的初始化过程中才会产生。

```python
# torch/_tensor.py
class Tensor(torch._C._TensorBase):
    def _typed_storage(self):...
    ...
    def backward(
        self, gradient=None, retain_graph=None, create_graph=False, inputs=None
    ):...
```

PyTorch中初始化栈的第一层会调用`torch/csrc/stub.cpp`中的`PyInit__C`函数，

```c
// torch/csrc/stub.c
PyMODINIT_FUNC PyInit__C(void)
{
  return initModule();
}
```

其中`initModule`函数完成了PyTorch的大部分初始化工作，其中关于`Tensor`部分的初始化工作包括以下内容：

- 注册百余个基础函数，包含了cuda、MKL、DataLoader、mps等多个方面，并将全部加入到`static std::vector<PyMethodDef> methods`管理。

  ```c++
  // torch/csrc/Module.cpp
  static PyMethodDef TorchMethods[] = {
      {"_initExtension", THPModule_initExtension, METH_O, nullptr},
      {"_autograd_init", THPAutograd_initExtension, METH_NOARGS, nullptr},
      {"_add_docstr", THPModule_addDocStr, METH_VARARGS, nullptr},
      {"_init_names", THPModule_initNames, METH_O, nullptr},
      {"_has_distributed", THPModule_hasDistributed, METH_NOARGS, nullptr},
      ...
  }
  ...
  static std::vector<PyMethodDef> methods;
  ...
  THPUtils_addPyMethodDefs(methods, TorchMethods);
  THPUtils_addPyMethodDefs(methods, DataLoaderMethods);
  THPUtils_addPyMethodDefs(methods, torch::autograd::python_functions());
  THPUtils_addPyMethodDefs(methods, torch::multiprocessing::python_functions());
  THPUtils_addPyMethodDefs(methods, torch::mps::python_functions());
  ...
  ```

- 注册符号`torch._C`并将`methods`中的百余个函数注册到其中，初始化其中的一些重要模块，如`dtype`、`device`、`layout`等等。

  ```c++
  // torch/csrc/Module.cpp
  static struct PyModuleDef torchmodule = {PyModuleDef_HEAD_INIT, "torch._C", nullptr, -1, methods.data()};
  ASSERT_TRUE(module = PyModule_Create(&torchmodule));
  ASSERT_TRUE(THPGenerator_init(module));
  ASSERT_TRUE(THPException_init(module));
  THPSize_init(module);
  THPDtype_init(module);
  THPDTypeInfo_init(module);
  THPLayout_init(module);
  THPMemoryFormat_init(module);
  THPQScheme_init(module);
  THPDevice_init(module);
  THPStream_init(module);
  ASSERT_TRUE(THPVariable_initModule(module));
  ...
  ```

- 在`THPVariable_initModule`中，`_TensorBase`作为对象加入`_C`模块当中。

  ```python
  # torch/csrc/autograd/python_variable.cpp
  Py_INCREF(&THPVariableType);
  PyModule_AddObject(module, "_TensorBase", (PyObject*)&THPVariableType);
  torch::autograd::initTorchFunctions(module);
  torch::autograd::initTensorImplConversion(module);
  torch::utils::validate_numpy_for_dlpack_deleter_bug();
  return true;
  ```

以上初始化过程仅是在Python环境视角下`Tensor`类型的产生过程，实际上该数据类型具有复杂的血缘关系。

<img src="https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/57e23f09b76f45188fcbf91f3c705cfd.png" alt="Tensor类血缘关系" style="zoom:50%;" />

当用户创建一个`Tensor`类型的变量时，PyTorch首先会调用`torch/csrc/autograd/python_torch_functions_manual.cpp`中的THPVariable_tensor函数，该函数用于接受不同类型的（list、tuple、np.array等）输入来构造`Tenso`对象，在该函数的末尾调用了`torch/csrc/utils/tensor_new.cpp`中的`tensor_ctor`以为新的Tensor对象分配内存、设置值以及相应的属性。其中最重要的函数为`internal_new_from_data`，从源码中可以看出，该函数接受解析后的参数，返回了一个新的`Tensor`对象。

```cpp
// torch/csrc/utils/tensor_new.cpp
Tensor tensor_ctor(
    c10::DispatchKey dispatch_key,
    at::ScalarType scalar_type,
    PythonArgs& r){
    ...
    bool type_inference = r.isNone(1);
    bool pin_memory = r.toBool(3);
    bool args_requires_grad = r.toBool(4);
    auto new_tensor = internal_new_from_data(
        typeIdWithDefault(r, 2, dispatch_key),
        r.scalartypeWithDefault(1, scalar_type),
        r.deviceOptional(2),
        data,
        /*copy_variables=*/true,
        /*copy_numpy=*/true,
        /*type_inference=*/type_inference,
        pin_memory); 
}
```

当用户使用类似于如下的语句创建一个要分配至GPU的变量时，`internal_new_from_data`函数中的`device_opt`将会指向指定的GPU设备上，并使用`to`函数将创建好的实例分配到设备上。

```python
data = torch.Tensor([1., 2.]).cuda()
```

`.to(...)`函数的调用栈如下：

```python
# aten/src/ATen/core/TensorMethods.h
inline Tensor Tensor::to(Device device, ScalarType dtype, bool non_blocking, bool copy) const {
    return dispatch_type().to(*this, device, dtype, non_blocking, copy);
}
# torch/csrc/autograd/generated/VariableType_2.cpp
Tensor VariableType::to(const Tensor & self, Device device, ScalarType dtype, bool non_blocking, bool copy) const {
  auto result = TypeDefault::to(self, device, dtype, non_blocking, copy);
  return result;
}
# build/aten/src/ATen/TypeDefault.cpp
Tensor TypeDefault::to(const Tensor & self, Device device, ScalarType dtype, bool non_blocking, bool copy) const {
    return at::native::to(/* native_actuals */ self, device, dtype, non_blocking, copy);
}
# aten/src/ATen/native/TensorConversions.cpp
Tensor to(const Tensor& self, Device device, ScalarType dtype, bool non_blocking, bool copy) {
  device = ensure_has_index(device);
  if (self.device() == device && self.dtype() == dtype && !copy) {
    return self;
  }
  return to_impl(self, self.options().device(device).dtype(dtype), non_blocking);
}
```

#### 2.1.2 GPU模型加载原理

PyTorch中的模型可以视为一种参数的容器，一般继承自`nn.modules.Module`类，这个类在源码当中有如下属性：

```python
# torch/nn/modules/module.py
class Module:
    dump_patches: bool = False
    _version: int = 1
    training: bool # 本网络是否正在训练。
    _parameters: Dict[str, Optional[Parameter]] # 在训练过程中会随着 BP 而更新的参数，梯度更新的对象。
    _buffers: Dict[str, Optional[Tensor]] # 在训练过程中不会随着 BP 而更新的参数
    _non_persistent_buffers_set: Set[str]
    _backward_pre_hooks: Dict[int, Callable]
    _backward_hooks: Dict[int, Callable]
    _is_full_backward_hook: Optional[bool]
    _forward_hooks: Dict[int, Callable]
    _forward_hooks_with_kwargs: Dict[int, bool]
    _forward_pre_hooks: Dict[int, Callable]
    _forward_pre_hooks_with_kwargs: Dict[int, bool]
    _state_dict_hooks: Dict[int, Callable]
    _load_state_dict_pre_hooks: Dict[int, Callable]
    _state_dict_pre_hooks: Dict[int, Callable]
    _load_state_dict_post_hooks: Dict[int, Callable]
    _modules: Dict[str, Optional['Module']] # 本网络下属的子模块，采取迭代的方式进行定义
    call_super_init: bool = False
    _compiled_call_impl : Optional[Callable] = None
```

其中最值得注意的参数you`_parameters`以及`_buffers`，前者是在训练过程中被不断更新的参数，后者是一些不会随着BP过程而更新但需要被保存的参数，如batchNorm中的moving mean以及varrience，其优化不通过BP过程。由于一个模型的具体状态基本就由这两个参数决定，因此在将模型加载到GPU上只需要将这两个变量拷贝到对应的设备上即可。具体而言，当模型调用`.cuda()`函数时，函数内部会调用`._apply()`方法对每个module进行递归操作，分别对`_paramters`和`buffers`中参数中的值调用`fn`函数进行遍历处理，而没有对其他`Module`中的成员进行操作，其中`fn`就是将数据复制到对应设备上的函数。

```python
# torch/nn/modules/module.py
def cuda(self: T, device: Optional[Union[int, device]] = None) -> T:
    return self._apply(lambda t: t.cuda(device))

def _apply(self, fn):
    # 递归进行参数复制
    for module in self.children():
        module._apply(fn)
    # 遍历_parameters
    for key, param in self._parameters.items():
        ...
    # 遍历_buffers
    for key, buf in self._buffers.items():
    	if buf is not None:
        	self._buffers[key] = fn(buf)
```

### 2.2 GPU函数的调度

GPU上的函数一般使用CUDA进行编程，CUDA是一个异构模型，其程序运行在一个由CPU及GPU组成的异构系统之上，他们之间由总线分开，通常CPU机器内存被称为`Host`而GPU机及其内存被称为`device`，CUDA架构下的程序也分为`host`和`device`两个部分，二者分别在CPU及GPU上运行，并且可以进行数据复制。通常一个CUDA程序的执行流程如下：

> 1. 分配host内存空间并且初始化数据
> 2. 分配device显存空间
> 3. 将要计算的数据从Host内存之上复制到device显存之上
> 4. 调用CUDA核函数在device上完成用户指定的运算
> 5. 将计算后GPU内存上的结果复制到Host内存上
> 6. 释放device和host上分配的内存

![CUDA_processing_flow_(En)](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/CUDA_processing_flow_(En).PNG)

在CUDA中，核函数主要分为三种，分别为`__global__`、`__devie__`以及`__host`核函数，这三种核函数有其限定的调用范围，对应着CUDA中常见的三种运行场景。

|   限定符   |    执行    |                  调用                  |
| :--------: | :--------: | :------------------------------------: |
| __global__ | 设备端执行 | 可以从主机调用也可以从某些特定设备调用 |
| __device__ | 设备端执行 |               设备端调用               |
|  __host__  | 主机端执行 |                主机调用                |

正式基于这样的核函数机制，使得程序可以在HOST端通过`__global__`函数调用GPU上的函数。

#### 2.2.1 PyTorch的函数调度原理

实际上，PyTorch中一个算子既可以运行在CPU上也可以运行在GPU上，而选择调用CPU端的算子函数或GPU端的算子实现，是由HOST端决策的。这种决策需要考虑到多个因素，为了实现自动函数调度，PyTorch实现了类似于C++中`vtable`的`dispatcher`机制。

`dispatcher`为每个算子维护其对应的函数指针表，其中每个`dispatcher key`对应着其实现方法，而`dispatcher key`的计算方法不仅有关于工作的环境，也考虑了`autograd`、`TLS`、`tracer`、`XLA`等更加丰富的因素，在数据形式上，这种函数的调度方法需要维护一个二维查找表，查找的维度分别为算子以及对应的`dispatcher key`，目前一些常见的`dispatcher key`有`XALRPreAutograd`、`Tracer`、`Private Use`、`CPU` 以及`CUDA`等，该表可表示为下图所示：

<img src="https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/1850883-20211106210225747-821296730.jpg" alt="1850883-20211106210225747-821296730" style="zoom:80%;" />

当在一个特定的`dispatcher key`上实现了对应的算子时，程序就可以通过该`dispatcher key`执行预定的方法，如上图中在CPU上定义的`mul`方法。这些方法共有三种注册方式，分别为`catchall`、`fallback`以及特定内核的实现，其中`catchall`指的是为所有的`dispatcher key`注册相同的算子，而`fallback`指的是为所有的算子指定同一个`dispatcher key`，三者之间的优先级为`特定实现` >  `catchall` > `fallback`。

<img src="https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/1850883-20211109230508275-1642852144.png" alt="1850883-20211109230508275-1642852144" style="zoom: 50%;" />

#### 2.2.2 GPU函数的调度过程

基于CUDA的编程架构，对于在GPU上的函数可以使用类似于如下代码的方法进行编写，模型在使用到对应算子时会自动根据`dispacher key`的情况调用对应的函数。

```c++
template <typename F>
__global__ void kernel_forward(const F *__restrict__ const __w, const F *__restrict__ const __k, F *__restrict__ const x,
                               const F eps, const int B, const int C, const int T){
    const int i = blockIdx.y;
    const int ij = (B * C) / BF;
    const int t = threadIdx.x << 2;

    __shared__ F ww[Tmax];
    __shared__ F kk[Tmax * BF];
    F4(ww, t) = F4(__w, t + T * (i % C));
    
    #pragma
    for (int j = 0; j < BF; j++) {
        F4(kk, t + Tmax * j) = F4(__k, t + T * (i + ij * j));
    }
    __syncthreads();

    float4 s[BF];
    #pragma
    for (int j = 0; j < BF; j++) {
        s[j] = {eps, eps, eps, eps};
    }
    const F *__restrict__ const w = ww + T - t - 4;
    for (int u = 0; u <= t; u++) {
        #pragma
        for (int j = 0; j < BF; j++) {
            const F x = kk[u + Tmax * j];
            s[j].x += w[u + 3] * x;
            s[j].y += w[u + 2] * x;
            s[j].z += w[u + 1] * x;
            s[j].w += w[u + 0] * x;
        }
    }
    #pragma
    for (int j = 0; j < BF; j++) {
        const F *__restrict__ const k = kk + Tmax * j;
        s[j].y += w[t + 3] * k[t + 1];
        s[j].z += w[t + 2] * k[t + 1];
        s[j].z += w[t + 3] * k[t + 2];
        s[j].w += w[t + 1] * k[t + 1];
        s[j].w += w[t + 2] * k[t + 2];
        s[j].w += w[t + 3] * k[t + 3];
        F4(x, t + T * (i + ij * j)) = s[j];
    }
}
```



## 3 总结

现代深度学习框架中对于GPU的使用很大程度上依赖于如CUDA、MKL、OpenML等基于硬件优化的计算库，而其本身并不直接关注如何调度GPU的显存及计算资源，以PyTorch为例，其更多地关注了以下几个方面：

1. 如何使用有效的策略管理异构计算模型（CPU与GPU）之间的数据，包括数据的构造、拷贝及引用操作。
2. 如何高效地集成不同计算模型之间的计算优势，将CPU的串行复杂运算与GPU的并行核运算相结合。

## 参考

1. http://blog.ezyang.com/2020/09/lets-talk-about-the-pytorch-dispatcher/
2. https://www.cnblogs.com/rossiXYZ/p/15518457.html
3. https://zhuanlan.zhihu.com/p/54896021

