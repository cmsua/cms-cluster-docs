# HPC Usage

## Login

Simply having an account with HPC permissions is not sufficient to login to the cluster. To do so, please request access via a system administrator.

Then, login to the cluster with the following command:

```bash
ssh username@10.116.25.113
# The static IP is in-place due to issues with OIT DNS
# The actual command is below:
# ssh username@hpc.cms.physics.ua.edu
```

## Resources

### Data Storage

We provide three networked volumes for your convenience:

- `/home/your-name` is the generic home directory. Assume that space is limited.
- `/bighome/your-name` is based on HDDs, and intended for large objects. While we don't set per-user quotas, we do have an overall cluster limit.
- `/scratch/your-name` is based on SSDs. Note that this directory should **not be used for long-term storage**, and will be **wiped regularly**.

Note that your `bighome` and `scratch` directories are availible under `$BIGHOME` and `$SCRATCH`.

### Network Ports

Ports 30000-30500 are exposed on all nodes.

## Using Jupyter

We will create a Jupyterlab setup in a Conda environment. For the purposes of instruction, we will install **Miniforge** and create a sample Conda environment.

```bash
# Install Miniforge
curl https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh
chmod +x Miniforge3-Linux-x86_64.sh
./Miniforge3-Linux-x86_64.sh

# Create Sample Environment
conda create --name JupyterTutorial

# Activate the sample environment
conda activate JupyterTutorial

# Install Jupyter, NodeJS (for slurm extension)
conda install jupyter nodejs

# Install Jupyter Slurm Extension
jupyter labextension install jupyterlab-slurm
```

Next, we will create a script to launch a Jupyter environment, to be executed via `srun`. Note that the port below, `30000`, may not be availible on your particular node. See the [Network Ports](#network-ports) section above for potentially availible ports.

```bash
echo '#!/bin/bash
conda activate JupyterTutorial
jupyter-lab --no-browser --ip 0.0.0.0 --port 30000' > ./jupyter.sh
chmod +x ~/jupyter.sh
```


Next, use `srun` to launch the notebook.

```bash
# Start a notebook with 2 CPUs and 16GB of RAM
srun -t 6-09:59:59 --cpus-per-task=2 --ntasks=1 --mem-per-cpu=8G --pty ~/jupyter.sh

# Start a notebook with 16 CPUs, 64GB of RAM, and an A100 (80GB)
srun -t 6-09:59:59 --cpus-per-task=16 --ntasks=1 --mem-per-cpu=4G --gres=gpu:a100-80gb:1 --pty ~/jupyter.sh

# Start a notebook with 32 CPUs, 64GB of RAM, and 2 GPUs
srun -t 6-09:59:59 --cpus-per-task=32 --ntasks=1 --mem-per-cpu=2G --gres=gpu:2 --pty ~/jupyter.sh

# Start a notebook with 32 CPUs, 64GB of RAM, and an A100 (40GB)
srun -t 6-09:59:59 --cpus-per-task=32 --ntasks=1 --mem-per-cpu=2G --gres=gpu:a100-40gb:1 --pty ~/jupyter.sh
```