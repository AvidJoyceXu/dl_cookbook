# Commands
```Shell
make prebuild # enter docker container

make run RUN_ARGS="--benchmarks=dlrm-v2,3d-unet,bert --scenarios=offline --fast --config_ver=high_accuracy"

# is a combination of
make generate_engines RUN_ARGS="..."
make run_harness RUN_ARGS="..."

# run accuracy check
make run_harness RUN_ARGS="..." --test_mode=AccuracyOnly

# fast run for dev
make run RUN_ARGS="--fast"
```
You can either call `make generate_engines`, or `make run` first for your specified workload. Afterwards, to run the engine, just use `make run_harness` instead of `make run`.
Don't call `make clean` abruptly!
# Logs
Logs are saved to `build/logs/[timestamp]/[system ID]/...` every time `make run_harness` is called.

