# Play with docker

## Check Docker Root
```Shell
> docker info | grep "Docker Root Dir"
WARNING: bridge-nf-call-iptables is disabled
WARNING: bridge-nf-call-ip6tables is disable
Docker Root Dir: /var/lib/docker
```

## Check the pulled Docker image
```Shell
docker images
```
## Debug docker
```Shell
dockerd
systemctl status docker
```


## Useful Commands
 - https://docs.docker.com/reference/cli/docker/container/run/
```Shell
docker ps
docker exec -it mlperf-inference-xly-x86_64-24490 /bin/bash
docker rm (-f) mlperf-inference-xly-x86_64-24490
# notice how to specify GPUs visible to a docker container
docker run --gpus '"device=0,1,2,3"' --runtime=nvidia --rm -it -w /work         -v /home/xly/inference_results_v4.0/closed/NVIDIA:/work -v /home/xly:/mnt//home/xly         --cap-add SYS_ADMIN --cap-add SYS_TIME         -e NVIDIA_VISIBLE_DEVICES=all         -e HISTFILE=/mnt//home/xly/.mlperf_bash_history         --shm-size=32gb         --ulimit memlock=-1         -v  /home/xly/tmp/timezone:/etc/timezone:ro -v /etc/localtime:/etc/localtime:ro         --security-opt apparmor=unconfined --security-opt seccomp=unconfined         --name mlperf-inference-xly-x86_64-24490 -h mlperf-inference-xly-x86-64-24490 --add-host mlperf-inference-xly-x86_64-24490:127.0.0.1         --cpuset-cpus 0-191         --user 1023 --net host --device /dev/fuse         -v /home/xly/mlperf_inference_data/:/home/xly/mlperf_inference_data/          -e MLPERF_SCRATCH_PATH=/home/xly/mlperf_inference_data/         -e HOST_HOSTNAME=sc1                  mlperf-inference:xly-x86_6
```
## Inspect docker
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
## What is Docker container
- https://www.docker.com/resources/what-container/

## Set Proxy in a Docker container
- enter a running container
- nano /etc/environment
```
http_proxy="http://your_proxy_address:port/"
https_proxy="http://your_proxy_address:port/"
ftp_proxy="http://your_proxy_address:port/"
no_proxy="localhost,127.0.0.1,.yourdomain.com"
```
