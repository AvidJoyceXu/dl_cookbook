# Config
```Shell
pip install -U "ray[default]"
export PATH=/home/xly/.local/bin:$PATH
```
# Debug
- ray process that `ray stop` fails to kill

# Overview
For data scientists and machine learning practitioners, Ray lets you scale jobs without needing infrastructure expertise:
- Easily parallelize and distribute ML workloads across multiple nodes and GPUs.
- Leverage the ML ecosystem with native and extensible integrations.
For ML platform builders and ML engineers, Ray:
- Reduces friction between development and production by enabling the same Python code to scale seamlessly from a laptop to a large cluster.
# Cluster setting up
## manually: `ray start`
```Shell
ray start --head --dashboard-host='0.0.0.0'
```
![[Pasted image 20240818150306.png]]
## clusterize: `ray up, ray down, ray attach`
- https://docs.ray.io/en/latest/cluster/vms/user-guides/launching-clusters/on-premises.html#manual-cluster-launcher
# Installation
- `pip install ray`
# Support Lang:
Python, Java, C++
# Ray Core Basics
```Python
import ray
ray.init()
```
## Run functions as remote tasks in the cluster
```Python
@ray.remote
def foo(x):
	return x*x
# remote call return a future
futures = [foo.remote(x) for x in range(4)]
# use `ray.get` to fetch future value
print(ray.get(futures))
```

Then, you **call that function with `.remote()`** instead of calling it normally. This remote call returns a future, a so-called Ray _object reference_, that you can then fetch with `ray.get`:
## Remote Actor
```Python
# Define the Counter actor.
@ray.remote
class Counter:
    def __init__(self):
        self.i = 0

    def get(self):
        return self.i

    def incr(self, value):
        self.i += value

# Create a Counter actor.
c = Counter.remote()

# Submit calls to the actor. These calls run asynchronously but in
# submission order on the remote actor process.
for _ in range(10):
    c.incr.remote(1)

# Retrieve final actor state.
print(ray.get(c.get.remote()))
# -> 10
```
## Passing an object
`ray.put` : produce an object store, which can be passed as substitutes for argument values into remote functions
# Tasks
Ray enables arbitrary functions to be **executed asynchronously on separate Python workers**. These asynchronous Ray functions are called “tasks”.
Ray enables tasks to specify their **resource requirements** in terms of CPUs, GPUs, and custom resources.
These **resource request**s are used by the **cluster scheduler** to **distribute tasks across the cluster** for parallelized execution.

- `remote` method will immediately return an **object ref (a future)** and then **create a task that will be executed on a worker process**.
在内存开辟一片空间，用于存放future；开启一个worker，用于计算result；当`ray.get`被call时，blocking地计算result
## Specifying required resources
```Python
# Specify required resources.
@ray.remote(num_cpus=4, num_gpus=2)
def my_function():
    return 1


# Override the default resource requirements.
my_function.options(num_cpus=3).remote()
```
## Passing object refs to Ray tasks
```Python
@ray.remote
def function_with_an_argument(value):
    return value + 1


obj_ref1 = my_function.remote()
assert ray.get(obj_ref1) == 1

# You can pass an object ref as an argument to another Ray task.
obj_ref2 = function_with_an_argument.remote(obj_ref1)
assert ray.get(obj_ref2) == 2
```
- As the second task depends on the output of the first task, Ray will not execute the second task until the first task has finished.
- If the two tasks are **scheduled on different machines**, the output of the first task (the value corresponding to `obj_ref1/objRef1`) will be **sent over the network to the machine** where the second task is scheduled.
## Waiting for Partial Results
- know which ones have finished executing without blocking on all of them.
```Python
object_refs = [slow_function.remote() for _ in range(2)]
# Return as soon as one of the tasks finished execution.
ready_refs, remaining_refs = ray.wait(object_refs, num_returns=1, timeout=None)
```
## Multiple returns
```Python
# However, you can configure Ray tasks to return multiple Object Refs.
@ray.remote(num_returns=3)
def return_multiple():
    return 0, 1, 2

object_ref0, object_ref1, object_ref2 = return_multiple.remote()
assert ray.get(object_ref0) == 0
assert ray.get(object_ref1) == 1
assert ray.get(object_ref2) == 2
```
## Cancelling tasks
- call `ray.concel()` on the returned Object ref.
```Python
@ray.remote
def blocking_operation():
    time.sleep(10e6)


obj_ref = blocking_operation.remote()
ray.cancel(obj_ref)

try:
    ray.get(obj_ref)
except ray.exceptions.TaskCancelledError:
    print("Object reference was cancelled.")
```
## Fault Tolerence
 - By default, Ray will [retry](https://docs.ray.io/en/latest/ray-core/fault_tolerance/tasks.html#task-retries) failed tasks due to system failures and specified application-level failures. You can change this behavior by setting `max_retries` and `retry_exceptions` options in [`ray.remote()`](https://docs.ray.io/en/latest/ray-core/api/doc/ray.remote.html#ray.remote "ray.remote") and [`.options()`](https://docs.ray.io/en/latest/ray-core/api/doc/ray.remote_function.RemoteFunction.options.html#ray.remote_function.RemoteFunction.options "ray.remote_function.RemoteFunction.options"). See [Ray fault tolerance](https://docs.ray.io/en/latest/ray-core/fault-tolerance.html#fault-tolerance) for more details.
## Task Events
By default, Ray **traces** the execution of tasks, reporting task status events and profiling events that the Ray Dashboard and [State API](https://docs.ray.io/en/latest/ray-observability/user-guides/cli-sdk.html#state-api-overview-ref) use.
You can change this behavior by setting `enable_task_events` options in [`ray.remote()`](https://docs.ray.io/en/latest/ray-core/api/doc/ray.remote.html#ray.remote "ray.remote") and [`.options()`](https://docs.ray.io/en/latest/ray-core/api/doc/ray.remote_function.RemoteFunction.options.html#ray.remote_function.RemoteFunction.options "ray.remote_function.RemoteFunction.options") to disable task events, which **reduces the overhead of task execution**, and the amount of data the task sends to the Ray Dashboard. **Nested tasks don’t inherit the task events settings from the parent task**. You need to **set the task events settings for each task separately**.
- example of nested remote functions
```Python
import ray


@ray.remote
def f():
    return 1


@ray.remote
def g():
'''
>>> ray.get(g.remote())
[ObjectRef(b1457ba0911ae84989aae86f89409e953dd9a80e),
 ObjectRef(7c14a1d13a56d8dc01e800761a66f09201104275),
 ObjectRef(99763728ffc1a2c0766a2000ebabded52514e9a6),
 ObjectRef(9c2f372e1933b04b2936bb6f58161285829b9914)]
'''
    # Call f 4 times and return the resulting object refs.
    return [f.remote() for _ in range(4)]


@ray.remote
def h():
'''
>>> ray.get(h.remote())
[1, 1, 1, 1]
'''
    # Call f 4 times, block until those 4 tasks finish,
    # retrieve the results, and return the values.
    return ray.get([f.remote() for _ in range(4)])
```
# Debugging tools
- https://docs.ray.io/en/latest/ray-observability/user-guides/cli-sdk.html#state-api-overview-ref
```Shell
# This API is only available when you download Ray via `pip install "ray[default]"`
ray summary tasks
```

# Actors
Actors extend the Ray API from functions (tasks) to classes. An actor is essentially a **stateful worker** (or a service). When a new actor is instantiated, **a new worker is created**, and **methods of the actor are scheduled on that specific worker** and can **access and mutate the state of that worker.**
```Python
import ray

@ray.remote
class Counter:
    def __init__(self):
        self.value = 0

    def increment(self):
        self.value += 1
        return self.value

    def get_counter(self):
        return self.value

# Create an actor from this class.
counter = Counter.remote()
```
The `ray.remote` decorator indicates that instances of the `Counter` class will be actors. Each actor runs in its own Python process.
- `ray list actors`
check actor states
## Properties: calling the actor
- Methods called on different actors can execute in parallel, and methods called on the same actor are executed **serially in the order that they are called**. Methods on the same actor will **share state** with one another, as shown below.
- actor has their own states!
```Python
# Create ten Counter actors.
counters = [Counter.remote() for _ in range(10)]

# Increment each Counter once and get the results. These tasks all happen in
# parallel.
results = ray.get([c.increment.remote() for c in counters])
print(results)

# Increment the first Counter five times. These tasks are executed serially
# and share state.
results = ray.get([counters[0].increment.remote() for _ in range(5)])
print(results)
```
## Passing around actor handles
- actor handles can be passed into other tasks
- create an action handle using `class.remote()`
```Python
import time

@ray.remote
def f(counter): # counter is an action handle
    for _ in range(10):
        time.sleep(0.1)
        counter.increment.remote()
```

## Cancelling Actor Tasks
### Unscheduled task
- cancel the scheduling
- When successfully cancelled at this stage, invoking `ray.get(actor_task_ref)` produce a [`TaskCancelledError`](https://docs.ray.io/en/latest/ray-core/api/doc/ray.exceptions.TaskCancelledError.html#ray.exceptions.TaskCancelledError "ray.exceptions.TaskCancelledError").
### Running Actor Tasks
- Ray offers no mechanism for interruption.
### Running Async Actor Tasks
- not so sure
- https://docs.python.org/3/library/asyncio-task.html#task-cancellation
### Cancellation Guarantee
Ray attempts to cancel Tasks on a _best-effort_ basis, meaning **cancellation isn’t always guaranteed**. For example, if the cancellation request doesn’t get through to the executor, the Task might not be cancelled.
- You can **check if a Task was successfully cancelled** using `ray.get(actor_task_ref)`.
### Fault Tolerance
- By default, Ray actors won't be restarted and actor tasks won’t be retried when actors crash unexpectedly.
- Change this behavior by **setting `max_restarts` and `max_task_retries` options** in [`ray.remote()`](https://docs.ray.io/en/latest/ray-core/api/doc/ray.remote.html#ray.remote "ray.remote") and [`.options()`](https://docs.ray.io/en/latest/ray-core/api/doc/ray.actor.ActorClass.options.html#ray.actor.ActorClass.options "ray.actor.ActorClass.options")

### Actors, Workers and Resources
- Workers are treated differently for tasks and actors. Any “Ray worker” is either 
	1. used to execute multiple Ray tasks 
	or
	3. is started as a dedicated Ray actor.
- **Actor**: A Ray Actor is also a “Ray worker” but is instantiated at runtime (upon `actor_cls.remote()`). All of its methods will run on the same process, using the same resources (designated when defining the Actor). Note that unlike tasks, the **python processes that runs Ray Actors are not reused** and **will be terminated** when the Actor is deleted.
- Tasks are scheduled more flexibly, and that if you don’t need the stateful part of an actor, you’re mostly better off using tasks.
## Task Events
- By default, Ray traces the execution of actor tasks, reporting task status events and profiling events that Ray Dashboard and [State API](https://docs.ray.io/en/latest/ray-observability/user-guides/cli-sdk.html#state-api-overview-ref) use.
# Objects
- We refer to these objects as **remote objects** because they can be **stored** anywhere in a Ray cluster, and we use **object refs** to **refer to** them.
- Object refs can be created in two ways.
> 1. They are returned by remote function calls.
> 2. They are returned by [`ray.put()`](https://docs.ray.io/en/latest/ray-core/api/doc/ray.put.html#ray.put "ray.put").

> [!Note]
> Remote objects are imutable.
>  This allows remote objects to be replicated in multiple object stores without needing to synchronize the copies.

## Passing Object Arguments
- Ray object references can be freely passed around a Ray application
- Objects are tracked via **_distributed reference counting_**, and their data is automatically freed once all references to the object are deleted.
### Passing as a top-level argument
- Ray will de-reference the object. This means that Ray will **fetch the underlying data** for all top-level object reference arguments, **not executing the task until** the object data becomes fully available.
### Passing as a nested argument
- nested: not passing an object reference directly
- When an object is passed within a **nested object**, for example, **within a Python list**, Ray will _not_ de-reference it. This means that the task will **need to call `ray.get()` on the reference** to fetch the concrete value.
- if the task never calls `ray.get()`, then the object value never needs to be transferred to the machine the task is running on.
### Passing as Closure Capture
- use case: have a bundle of objects passed in multiple methods, and want to spare the efforts of repeated argument definition
- defining a task that **closes over an object ref** will **pin** the object via **reference-counting**, so the object will not be evicted until the job completes.
```Python
import ray

# Put the values (1, 2, 3) into Ray's object store.
a, b, c = ray.put(1), ray.put(2), ray.put(3)


@ray.remote
def print_via_capture():
    """This function prints the values of (a, b, c) to stdout."""
    print(ray.get([a, b, c]))


# Passing object references via closure-capture. Inside the `print_via_capture`
# function, the global object refs (a, b, c) can be retrieved and printed.
print_via_capture.remote()
# -> prints [1, 2, 3]
```

## Nested Objects
- eg: `ray.put([ray.put(1)]`
- build **composite objects** that **themselves hold reference**s to further sub-objects.
# Environment Dependencies
- very important!
Your Ray application may have dependencies that exist outside of your Ray script.
Ray expects these “dependencies” to exist on each Ray node. If these are not present, you may run into issues such as `ModuleNotFoundError`, `FileNotFoundError` and so on.
## Specify the container image using Cluster Launcher
- prepare your dependencies on the cluster in advance (e.g. using a container image) using the Ray [Cluster Launcher](https://docs.ray.io/en/latest/cluster/vms/getting-started.html#vm-cluster-quick-start),
- https://docs.ray.io/en/latest/cluster/vms/references/ray-cluster-configuration.html#cluster-config
### Cluster YAML configuration Options
The cluster configuration is defined within a YAML file that will be used by the Cluster Launcher to **launch the head node**, and by the Autoscaler to **launch worker nodes**.
Once the cluster configuration is defined, you will need to use the [Ray CLI](https://docs.ray.io/en/latest/cluster/cli.html#ray-cluster-cli) to perform any operations such as **starting and stopping the cluster.**
## Runtime environments
> A **runtime environment** describes the dependencies your Ray application needs to run, including [files, packages, environment variables, and more](https://docs.ray.io/en/latest/ray-core/handling-dependencies.html#runtime-environments-api-ref). It is installed dynamically on the cluster at runtime and cached for future use (see [Caching and Garbage Collection](https://docs.ray.io/en/latest/ray-core/handling-dependencies.html#runtime-environments-caching) for details about the lifecycle).

In contrast with the base cluster environment, a runtime environment will only be **active for Ray processes**. (For example, if using a runtime environment specifying a `pip` package `my_pkg`, the statement `import my_pkg` will fail if called outside of a Ray task, actor, or job.)

Runtime environments also allow you to **set dependencies per-task, per-actor, and per-job** on a long-running Ray cluster.
```Python
import ray

runtime_env = {"pip": ["emoji"]}

ray.init(runtime_env=runtime_env)

@ray.remote
def f():
  import emoji
  return emoji.emojize('Python is :thumbs_up:')

print(ray.get(f.remote()))
'''
Python is 👍
'''
```
# Clusterize API
## `ray.init()`
To explicitly connect to an existing local cluster, use this as follows. A ConnectionError will be thrown if no existing local cluster is found.
```Python
try:
	ray.init(address="auto")
except ConnectionError:


```
## Register the cluster in YAML
- how to register the cluster in Ray
```Python
import ray
ray.cluster_resources()
'''
>>> ray.cluster_resources()

{'accelerator_type:A100': 1.0, 'node:__internal_head__': 1.0, 'node:115.27.161.222': 1.0, 'CPU': 384.0, 'memory': 587283528704.0, 'object_store_memory': 200000000000.0, 'GPU': 2.0}
'''
```
## Ray utilities
## Manage a list of Actors
```Python
def ray.util.ActorPool(actors)
'''
@param: actors - List of Ray actor handles to use in this pool
'''

```
## Cuda related utilities
- check current GPU
`torch.cuda.device_count()
