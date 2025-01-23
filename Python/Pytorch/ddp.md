# `torch.nn.parallel.DistributedDataParallel`

## DDP Mechanism
- Data parallelism
  
DDP splits the input data across multiple GPUs.

Each GPU computes gradients independently based on its local mini-batch of data.

- model parallelism

The model is replicated across all the GPUs, and each replica performs its part of the computation.

- gradient synchronization
After every backward pass, DDP automatically synchronizes gradients across all the GPUs using an efficient all-reduce operation.

## Multi-GPU run with DDP
```Python
### Setup DDP ###
ddp = int(os.environ.get('RANK', -1)) != -1
if ddp:
    assert device_type == 'cuda'
    init_process_group(backend='nccl')
    ddp_rank = int(os.environ['RANK'])
    ddp_local_rank = int(os.environ['LOCAL_RANK'])
    ddp_world_size = int(os.environ['WORLD_SIZE'])
    device = f'cuda:{ddp_local_rank}'
    torch.cuda.set_device(device)
    master_process: bool = ddp_rank == 0
else:
    # Vanilla, non-ddp run
    ddp_rank = 0
    ddp_local_rank = 0
    ddp_world_size = 1
    master_process = True
```
### Parameters
- `RANK` and `LOCAL_RANK`

| **Aspect** | **`RANK` (Global Rank)** | **`LOCAL_RANK` (Local Rank)** | 
| --- | --- | --- | 
| **Definition** | Unique rank of the process in the global distributed setup. | Rank of the process on the local node (per machine). | 
| **Scope** | Global (across all nodes and GPUs in the distributed setup). | Local (specific to the node and GPU within that node). | 
| **Value Range** | 0 to `total_processes - 1` (across all nodes and GPUs). | 0 to `num_gpus_per_node - 1` (within each node). | 
| **Purpose** | Identifies the process globally across all distributed workers. | Identifies the specific GPU on a local node that a process is using. | 
| **Use Case** | For global communication and synchronization (e.g., gradients). | For setting the device (GPU) for a process. |
- `WORLD_SIZE`

### `torchrun`

A utility provided by PyTorch to facilitate the launching and management of distributed training jobs across multiple GPUs or nodes.

```bash
torchrun --nproc_per_node=2 --nnodes=1 --node_rank=0 train.py
torchrun --nproc_per_node=<num_gpus_per_node> --nnodes=<num_nodes> --node_rank=<node_rank> <your_script.py> <args>
```

It automatically **sets environment variables**, such as the rank of each process and the total number of processes (world size), and **spawns processes**.
