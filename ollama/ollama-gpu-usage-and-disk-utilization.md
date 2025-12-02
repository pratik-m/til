# TIL: Ollama's GPU Libraries and Disk Space

## The Problem

Ollama was taking up 8.8GB under `/usr/local/lib/ollama` with three separate GPU library folders:

- `rocm` (AMD GPU support)
- `cuda_v12` (NVIDIA GPU support)
- `cuda_v13` (NVIDIA GPU support)

## My System Specs

```bash
fastfetch --structure os:kernel:de:cpu:gpu --logo none
```

```
OS: Arch Linux x86_64
Kernel: Linux 6.17.8-arch1-1
CPU: AMD Ryzen 7 7730U (16) @ 4.55 GHz
GPU: AMD Barcelo [Integrated]
```

## What Are These Libraries?

- **ROCm** - AMD's GPU compute platform for AMD GPUs
- **CUDA v12/v13** - NVIDIA's GPU compute platform for NVIDIA GPUs

Ollama ships with all three to work out-of-the-box on any hardware, but you only need the one matching your GPU.

## What I Deleted

Since I have an AMD integrated GPU and no NVIDIA card:

```bash
sudo rm -rf /usr/local/lib/ollama/cuda_v12
sudo rm -rf /usr/local/lib/ollama/cuda_v13
```

**Result**: Freed up ~5-6GB of disk space

## Was ROCm Even Being Used?

**TL;DR: No, it wasn't. Confirmed via logs.**

After deleting all GPU libraries (including ROCm), my model (`llama3.2:1b`) still ran fine. I verified what Ollama was actually using:

### Checking GPU Usage

```bash
# Monitor GPU percentage
watch -n 1 cat /sys/class/drm/card1/device/gpu_busy_percent
```

Result: GPU stayed at ~24% (just desktop compositing), no spikes during inference.

### Checking Ollama Logs

```bash
journalctl -u ollama -f
```

**Key findings from the logs:**

```
library=cpu layers.requested=-1 layers.model=17 layers.offload=0
load_backend: loaded CPU backend from /usr/local/lib/ollama/libggml-cpu-haswell.so
load_tensors:          CPU model buffer size =  1252.41 MiB
```

**Translation:**

- `layers.offload=0` - Zero layers offloaded to GPU
- `library=cpu` - Using CPU backend with AVX2 optimizations
- No GPU/ROCm initialization messages whatsoever

### Using OLLAMA_DEBUG

Running with debug flag shows the same:

```bash
OLLAMA_DEBUG=1 ollama run llama3.2:1b
```

Output explicitly states:

```
"new model will fit in available system memory for CPU inference, loading"
library=cpu layers.offload=0
load_backend: loaded CPU backend from /usr/local/lib/ollama/libggml-cpu-haswell.so
```

## Conclusion

Integrated AMD GPUs (especially older architectures like Barcelo) may not be well-supported by ROCm for AI workloads, so Ollama might fall back to CPU anyway. For small models like `llama3.2:1b`, CPU performance may be acceptable, making the GPU libraries unnecessary overhead.

If you want GPU acceleration and it's not working, you might need to install ROCm system-wide or use CPU-optimized models instead.
