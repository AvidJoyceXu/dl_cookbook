# Play with process

```Shell
# Check all processes
top # Examine for the column whether it is zombie

# Check zombie process
ps aux | grep Z

# Kill zombie process
kill -9 ${PID}
```
## kill zombie process in `nvidia-smi`
The difference between `kill` and `kill -9` lies in how they terminate a process:

1. **`kill`**: This command sends the default signal, `SIGTERM` (signal number 15), to the specified process. `SIGTERM` requests the process to terminate gracefully, allowing it to clean up resources, save data, or perform any necessary shutdown procedures. Processes can choose to ignore this signal.
    
2. **`kill -9`**: This command sends the `SIGKILL` signal (signal number 9) to the process. `SIGKILL` forces the process to terminate immediately without giving it a chance to clean up. This signal cannot be ignored or handled by the process, making it a more aggressive way to kill a process.
    

In summary, use `kill` for a graceful termination and `kill -9` when a process does not respond to the standard kill command.