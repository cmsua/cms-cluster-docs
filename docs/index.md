# Getting Started

## Account Creation

Create an account with the cluster at [Account Console](https://auth.cms.physics.ua.edu/realms/ua-cms/account/), or ask an administrator to create one on your behalf.

Once an account has been created, it will not have access to any resources until granted such by an administrator.

## JupyterHub (FPGA, GPU) Access

GPUs and FPGAs can be accessed via [JupyterHub](https://jupyter.cms.physics.ua.edu) while connected to the UA network. Connection can be either via an Ethernet cable in a UA-building, or via the [UA VPN](https://oit.ua.edu/services/internet-networking/).

There are two notebook options available that support FPGAs:

- MATLAB with FPGA (1x Alveo U200)
- MATLAB without FPGA

Currently, the cluster has **3** FPGAs installed. If all FPGAs are in use, your notebook will not launch. Each FPGA notebook is provided with MATLAB Online, as well as VNC access to the Desktop version via the "Desktop" option. To launch MATLAB in the Desktop option, from a terminal window, run `DISPLAY=:1 /opt/matlab/bin/matlab`

Additionally, there are four notebooks that support GPUs:

- PyTorch with GPU
- PyTorch without GPU
- TensorFlow with GPU
- TensorFlow without GPU

There is currently **1** GPU in the cluster:

- 1x NVIDIA A100 (40GB)
- 2x NVIDIA A100 (80GB) (Temporarily Removed)

### Installation of HDL Toolbox / Coder FPGA Support Libraries

During the extension install process, the Xilinx support libraries are written to the `$HOME/Documents` folder, which is overwritten by your user data volume. As such, to enable the support packages, run the following command while launching a MatLab environment:

```bash
wget https://www.mathworks.com/mpm/glnxa64/mpm \
  && chmod +x mpm \
  && ./mpm install \
  --release=r2024b \
  --destination=/opt/matlab \
  --products Deep_Learning_HDL_Toolbox_Support_Package_for_Xilinx_FPGA_and_SoC_Devices HDL_Coder_Support_Package_for_Xilinx_FPGA_and_SoC_Devices
```

This only needs to be ran once per-user.

## Docker Registry

When deploying images to run on the cluster, the cluster's [Docker Registry](https://registry.cms.physics.ua.edu) should be used.

Note that when authenticating the registry via the command-line, the password prompt should be filled with a token generated from the registry's web GUI, not your actual password.