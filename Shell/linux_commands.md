# Linux Commands
## `ps` and `jobs`
`ps` helps locate the PID and verify process details, but it does not replace job control commands (`bg`, `fg`, `jobs`). 

> Once you have the PID, you can bring the job to the foreground using `kill -CONT` to continue a stopped job and `kill -STOP` to stop it. 

Use `jobs` for job control **within a shell session** and `ps` to find PIDs for **system-wide process management**.

 `fg` 
You can bring a job to the foreground with `fg %<jobid>` or resume it in the background with `bg %<jobid>`.
