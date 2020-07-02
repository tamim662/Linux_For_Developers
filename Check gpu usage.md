# Intel gpu usage check:

### For Intel GPU's there exists the intel-gpu-tools, so firstly install it :
```bash
  $ sudo apt-get install intel-gpu-tools
```
To show gpu usage:
```bash
  $ sudo intel_gpu_top
```
# Nvidia
### For Nvidia GPUs there is a tool nvidia-smi that can show memory usage, GPU utilization and temperature of GPU.
To show gpu usage:
```bash
  $ nvidia-smi -l 2
```
it gives you updates every 2 seconds.
