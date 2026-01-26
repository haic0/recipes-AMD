## AMD GPU Support
Recommended approaches by hardware type are:


MI300X/MI325X/MI355X  with fp8: Use FP8 checkpoint for optimal memory efficiency.

- **MI300X/MI325X/MI355X with `fp8`**: Use FP8 checkpoint for optimal memory efficiency.
- **MI300X/MI325X/MI355X with `bfloat16`**
  
#### Step by Step Guide
Please follow the steps here to install and run Qwen3-Next-80B-A3B-Instruct models on AMD MI300X GPU.
#### Step 1
Pull the latest vllm docker:
```shell
docker pull vllm/vllm-openai-rocm:v0.14.1
```
Launch the Rocm-vllm docker: 
```shell
docker run -d -it --ipc=host --network=host --privileged --cap-add=CAP_SYS_ADMIN --device=/dev/kfd --device=/dev/dri --device=/dev/mem --group-add video --cap-add=SYS_PTRACE --security-opt seccomp=unconfined -v /:/work -e SHELL=/bin/bash  --name Qwen3-next vllm/vllm-openai-rocm:v0.14.1
```
### Step 2: Log in to Hugging Face
Log in to your Hugging Face account:
```shell
hf auth login
```

### Step 3: Start the vLLM server

Run the vllm online serving
### BF16 
```shell
VLLM_ALLOW_LONG_MAX_MODEL_LEN=1 vllm serve Qwen/Qwen3-Next-80B-A3B-Instruct --tensor-parallel-size 4 --max-model-len 32768  --no-enable-prefix-caching 
```
### FP8
```shell
VLLM_ALLOW_LONG_MAX_MODEL_LEN=1 vllm serve Qwen/Qwen3-Next-80B-A3B-Instruct-FP8 --tensor-parallel-size 4 --max-model-len 32768  --no-enable-prefix-caching 
```
#### Step 4 Run Benchmark

Open a new terminal and run the following command to execute the benchmark script inside the container.
```shell
docker exec -it Qwen3-next vllm bench serve \
  --model "Qwen/Qwen3-Next-80B-A3B-Instruct" \
  --dataset-name random \
  --random-input-len 8192 \
  --random-output-len 1024 \
  --request-rate 10000 \
  --num-prompts 16 \
  --ignore-eos \
  --trust-remote-code 
```

