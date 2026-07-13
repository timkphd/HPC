# Modules on RHEL9

On Kestrel RHEL 9, modules are deployed and organized differently than on Kestrel RHEL 8. The basic concepts remain the same, but structure, discovery, and loading behavior are intentionally different to improve compatibility, reproducibility, and long-term maintainability.

The module system on this cluster is Lmod 8.7.37, a modern Lua-based environment module system with hierarchical modules, dependency awareness, and improved error handling.

## Default modules at login

When you log in to Kestrel, default modules include:

- `Core/26.05` 
- `DefApps`

`DefApps` is a convenience module that ensures both Core and applications are loaded on login or after `module reset`.


## Three Environment Types

On Kestrel RHEL 9, users typically work in one of these three environment types:

### 1. Module-Based Environment (Site Installed Software)

Use Lmod modules to access software installed by NLR/HPC admins, including:

- NLR-provided compiler toolchains (`gcc`, `oneapi`, `llvm`)
- CPE stack (`cpe-stack` with `PrgEnv-*`)
- Core modules (tools available independent of compiler choice)
- Research applications (`application/26.05`)

This is the environment to use when you want centrally installed and supported software.

```bash
module reset
module load gcc/14.2.0        # or oneapi/2025.3.1
module avail
```

Or for CPE:

```bash
module reset
module load cpe-stack/25.03
module load PrgEnv-gnu
module avail
```

**Important:** Do not mix CPE packages with NLR-provided toolchains in the same session.

### 2. Miniforge Environment (User-Defined Conda/Mamba)

Use `miniforge3` when you need your own user-managed software stack (Python/R/data-science packages, custom versions, isolated dependencies).

```bash
module reset
module load miniforge3/26.1.1-3
conda create -n myenv python=3.12
conda activate myenv
```

You can use `conda` or `mamba` inside this environment to install user-required software.

### 3. Container Environment (Apptainer Images)

Use Apptainer when you want to run software from pre-built container images.

```bash
module reset
module load apptainer/1.4.1-runonly
apptainer exec myimage.sif python --version
```

This is useful for portable and reproducible application stacks without directly installing software into your shell environment.

## Quick Start by Environment Type

### A. Module-Based Environment

```bash
module reset
module load application/26.05
module avail
```

### B. Miniforge Environment

```bash
module reset
module load miniforge3/26.1.1-3
conda activate myenv
```

### C. Container Environment

```bash
module reset
module load apptainer/1.4.1-runonly
apptainer exec myimage.sif <command>
```

## Key Principles

1. **Choose one environment type first:** module-based, miniforge, or container.
2. **No mixing of compiler ecosystems:** never combine CPE packages with NLR-provided toolchains in the same session.
3. **Clear separation:** applications are built for specific toolchains; check dependencies before loading.
4. **Discovery:** use `module spider` to find all versions and dependencies before loading.
5. **Cleanup:** use `module reset` before switching environment types.

## Critical: Do Not Mix CPE and NLR Toolchains

This WILL cause conflicts and unexpected behavior:

```bash
#  DO NOT DO THIS
module load gcc/14.2.0
module load cpe-stack/25.03    # Conflict!
```

**Choose one workflow per session:**

| Workflow | Start Command |
| --- | --- |
| Module-based (NLR toolchains) | `module reset; module load gcc/14.2.0` or `module load oneapi/2025.3.1` |
| Module-based (CPE) | `module reset; module load cpe-stack/25.03` |
| Miniforge | `module reset; module load miniforge3/26.1.1-3` |
| Container | `module reset; module load apptainer/1.4.1-runonly` |

If you accidentally mix them, reset and start over:

```bash
module reset
```

## MPI-Enabled Software

MPI implementations are available in both workflows:

- **NLR toolchains:** Load openmpi, mpich, or intelmpi alongside your compiler
- **CPE:** MPI is integrated in each PrgEnv

**👉 See:** [MPI-Enabled Software](mpi-software.md) for details

## Module Commands Reference

| Command | Purpose |
| --- | --- |
| `module reset` | Return to login state |
| `module avail` | List visible modules |
| `module spider <name>` | Find all versions (global search) |
| `module load <name>` | Load a module |
| `module unload <name>` | Unload a module |
| `module list` | Show current modules |
| `module show <name>` | Display module details |
| `module swap <a> <b>` | Switch modules |

**👉 See:** [Module Commands](ml_commands.md) for complete reference

## Additional Resources

- [User-Provided Toolchains](user-toolchains.md) - Build with gcc, oneapi, or llvm
- [Cray Programming Environment](cpe.md) - Use the CPE stack
- [MPI-Enabled Software](mpi-software.md) - Parallel computing libraries
- [Module Commands](ml_commands.md) - Command reference
- [Container guide](../../Documentation/Development/Containers/index.md) - Apptainer and container workflows
- [Installing Software](install_software.md) - Build and install custom software
- [Legacy Software](old_software.md) - RHEL 8 compatibility notes
