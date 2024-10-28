# Understand mpi run commands
## Example
example: `mpirun --verbose -n 4 --map-by socket --display-map -hostfile $(pwd)/sc-hosts-gpu -- $PROF $(pwd)/gpu-wrapper.sh`


### Breakdown of the Command
- `mpirun`: The command used to run MPI applications.

- `--verbose`: This option enables detailed output during the execution of the MPI processes. It provides information about the execution, which can be useful for debugging.

- `-n 4`: This specifies that 4 MPI processes should be launched.

- `--map-by socket`: This option controls how MPI processes are distributed across the hardware. In this case, it means that processes will be mapped to different CPU sockets. This can help optimize performance by reducing cross-socket communication.

- `--display-map`: This option instructs mpirun to print a map of **how processes are assigned to resources**. It shows **which processes are running on which nodes and cores**, helping users verify that the mapping is as intended.

- `-hostfile $(pwd)/sc-hosts-gpu`: This s**pecifies a hostfile** that contains **a list of hosts (machines)** on which the MPI processes will run. The $(pwd) command returns the current working directory, so this points to a hostfile named sc-hosts-gpu in that directory.

- `--`: This indicates **the end of mpirun options** and the beginning of the command to be executed.

- `$PROF`: This could be an environment variable or a command that may modify the execution of gpu-wrapper.sh, such as profiling settings.

- `$(pwd)/gpu-wrapper.sh`: This specifies the path to the gpu-wrapper.sh script that will be executed by each MPI process.

## Multi-node run
` mpirun -hostfile <hostfile>`
### hostfile format
```Shell
node1 slots=4
node2 slots=4
node3 slots=2
# node1, node2, and node3 are the hostnames (or IP addresses) of each node.
# slots=<num> indicates how many processes can run concurrently on each host (like specifying the number of CPU cores).
```
