# Mastering shell scripts

```Shell
#!/bin/bash 
# Shebang line: setting the interpreter

export CUDA_VISIBLE_DEVICES=$((OMPI_MCA_orte_ess_node_rank % 2))

export START="${START:-mpirun -n 4}" # START is default to be 'mpirun -n 4'

$@ # This part runs any command-line arguments passed to the script. It allows you to execute any command after setting the environment variable, making the wrapper flexible.
```