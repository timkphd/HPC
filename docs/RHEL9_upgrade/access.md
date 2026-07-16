# Accessing Kestrel RHEL9 

## RHEL9 Login Nodes

To submit jobs to RHEL9 compute nodes, you *must* be connected to an upgraded login node. Use the following login nodes to access RHEL9 resources:

- CPU login node: `kl3.hpc.nlr.gov`
- GPU login node: `kl5.hpc.nlr.gov`
- DAV node: `kd7.hpc.nlr.gov` 

## Example login

The following example describes how to access the RHEL9 CPU login node.

1\. SSH to the appropriate login node.

```bash
ssh kl3.hpc.nlr.gov

cat /etc/redhat-release
lscpu | grep 'Model name'
```

Example output:

```text
Red Hat Enterprise Linux release 9.4 (Plow)
Model name: Intel(R) Xeon(R) Platinum 8470
```

2\. Verify the currently loaded default modules.

```bash
module list
```

Example output:

```text
Currently Loaded Modules:
   1) Core/26.05   2) application/26.05   3) DefApps
```

3\. Verify the installed Slurm version.

```bash
srun --version
```

```text
slurm 25.05.6
```

4\. Launch an interactive job. A compute node running RHEL 9 is assigned automatically.

```bash
salloc --time=20:00 --account=<allocation handle> --nodes=1
```

Example output:

```text
salloc: Granted job allocation 13338420
salloc: Waiting for resource configuration
salloc: Nodes x1005c0s0b0n0 are ready for job
```

Then verify OS on the compute node:

```bash
cat /etc/redhat-release
```
