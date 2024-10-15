# Features
1. The generated TensorRT engine files are not portable across platforms or TensorRT versions. Plans are specific to the exact GPU model they were built on (in addition to the platforms and the TensorRT version) and must be rebuilt on the specific GPU in case you want to run them on a different GPU.
https://forums.developer.nvidia.com/t/the-engine-plan-file-is-generated-on-an-incompatible-device-expecting-compute-6-1-got-compute-8-6-please-rebuild/257903/4

Error log
```
# Using Ray to distribute computation across clusters
(SDXLCore pid=59182) [E] 6: The engine plan file is generated on an incompatible device, expecting compute 7.0 got compute 8.0, please rebuild. [repeated 3x across cluster]
```
- cluster: sc1(V100 * 4 + A100 * 2), sc4(V100 * 4)
	- the generated tensor-RT engine is in A100 (A for ampere, V for volta)

> Compute Capability (7.0, 8.0)
> - **Compute 7.0** refers to NVIDIA's **Volta architecture** (e.g., the Tesla V100 GPU).
> - **Compute 8.0** refers to the **Ampere architecture** (e.g., the A100 GPU).
> The **compute capability** version is critical in determining the GPU's support for certain programming features, precision types (like mixed precision or tensor cores), and optimizations available in frameworks like CUDA.