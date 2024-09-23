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



## Data Storage

We provide three networked volumes for your convenience:

- `/home/your-name` is the generic home directory. Assume that space is limited.
- `/bighome/your-name` is based on HDDs, and intended for large objects. While we don't set per-user quotas, we do have an overall cluster limit.
- `/scratch/your-name` is based on SSDs. Note that this directory should **not be used for long-term storage**, and will be **wiped regularly**.

Note that your `bighome` and `scratch` directories are availible under `$BIGHOME` and `$SCRATCH`.