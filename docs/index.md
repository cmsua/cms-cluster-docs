# Getting Started

## Account Creation

Create an account with the cluster at [Account Console](https://auth.cms.physics.ua.edu/realms/ua-cms/account/), or ask an administrator to create one on your behalf.

Once an account has been created, it will not have access to any resources until granted such by an administrator.

## FPGA Access

FPGAs can be accessed via [JupyterHub](https://jupyter.cms.physics.ua.edu). There are two notebook options availible that support FPGAS:

- MATLAB with FPGA (1x Alveo U200)
- MATLAB without FPGA

Currently, the cluster has **0** FPGAs installed. If all FPGAs are in use, your notebook will not launch.

Each notebook is provided with MATLAB Online, as well as VNC access to the Desktop version via the "Desktop" option.

To launch MATLAB in the Desktop option, from a terminal window, run `DISPLAY=:1 /opt/matlab/bin/matlab`

### Installation of HDL Toolbox / Coder FPGA Support Libraries

During the extension install process, the Xilinx support libraries are written to the `$HOME/Documents` folder, which is overwritten by your user data volume. As such, to enable the support packages, run the following command:

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