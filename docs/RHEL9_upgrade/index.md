# Kestrel RHEL9 Upgrade

Kestrel is currently undergoing a phased-in operating system (OS) upgrade from Red Hat Enterprise Linux (RHEL) 8.8 to RHEL 9.4 (RHEL9). At the time of writing in July 2026, most nodes on Kestrel are still using the original RHEL8 OS, but over time more compute nodes will be upgraded to RHEL9 until the entire system is running the newer OS.

The migration from RHEL8 to RHEL9 provides the system with necessary security updates, a critical kernel upgrade to enable the latest versions of various software applications, upgraded CUDA drivers for GPU nodes, and comes with significant changes to the [software module](./modules/index.md) system. 

!!! Note
    The experience of loading and using application modules that are maintained under `/nopt` is *not* expected to change between RHEL8 and RHEL9 on Kestrel. However, **users who compile their own software should especially familiarize themselves with how to navigate the [new RHEL9 module stack](./modules/index.md)**.
 
## Software Release

| Item | Value |
| --- | --- |
| Software Product | RHEL Compute |
| Red Hat Enterprise Linux | 9.4 (ESR) |
| Kernel Release | 5.14.0-427.13.1 |
| HPE Cray Programming Environment | 25.3 |
| NVIDIA Driver | 565.57.01 |
| Slurm | 25.05.6 |

## Notes

- On RHEL8, applications were installed under `/nopt/nrel/apps/`. A new directory, `/nopt/nlr/apps`, has been created for this transition.
- Slurm for RHEL9 is installed under `/nopt/slurm/latest` instead of `/nopt/slurm/current`.
- The latest FastX server is running on `kd7.hpc.nlr.gov` and can be accessed with the FastX client or at <https://kd7.hpc.nlr.gov>.

