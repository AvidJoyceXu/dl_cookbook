# Useful Commands
`docker ps
`docker exec -it mlperf-inference-xly-x86_64-24490 /bin/bash`
# Inspect docker
`docker inspect <container_id>`

```Shell
"HostConfig": {
            "Binds": [
                "/tmp/timezone_tmp:/etc/timezone:ro",
                "/etc/localtime:/etc/localtime:ro",
                "/home/xly/mlperf_inference_data/:/home/xly/mlperf_inference_data/",
               "/home/xly/inference_results_v4.0/closed/NVIDIA:/work",
                "/home/xly:/mnt//home/xly"
            ],
```
# What is Docker container
- https://www.docker.com/resources/what-container/

