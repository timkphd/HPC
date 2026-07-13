# Cray Programming Environment (CPE)

The Cray Programming Environment (CPE) is a complete, pre-built software stack provided by HPE Cray. It includes integrated compilers, libraries, and tools optimized for Cray systems. CPE offers a distinct alternative to using individually selected toolchains.

## What is CPE?

CPE is a pre-validated software environment where:

- Multiple compilers are available (Cray, GNU, Intel, AOCC)
- MPI implementations are pre-built and integrated
- Libraries and tools are tested together
- Performance is optimized for the system

## Loading CPE

To use CPE, start with a clean environment and load the CPE stack:

```bash
module reset
module load cpe-stack/25.03
```

This single command loads a complete, validated environment. All subsequent `module avail` commands show only CPE-compatible packages.

## Available CPE Compilers

Once CPE is loaded, you can switch between compiler environments within CPE:

```bash
module load PrgEnv-cray
module load PrgEnv-gnu
module load PrgEnv-intel
module load PrgEnv-aocc      # AMD AOCC compiler
```

Each `PrgEnv` includes:
- A compiler suite
- Optimized MPI implementation (cray-mpich)
- Associated libraries and tools

Example: view available libraries under PrgEnv-gnu:

```bash
module load PrgEnv-gnu
module avail
```

## Discovering CPE Modules

List all visible CPE modules:

```bash
module avail
```

Find all versions of a specific package:

```bash
module spider fftw
```

Show dependencies for a specific module:

```bash
module spider fftw/3.3.10
```

## ⚠️ CPE Does Not Mix with NLR Toolchains

**Critical:** Do not load CPE modules alongside NLR-provided toolchains (gcc, oneapi, llvm).

These are mutually exclusive:

```bash
# ❌ DO NOT DO THIS
module load cpe-stack/25.03
module load gcc/14.2.0    # This will cause conflicts!
```

**Choose one workflow:**
- **CPE workflow:** `cpe-stack/25.03` + `PrgEnv-*`
- **User toolchain workflow:** `gcc`, `oneapi`, or `llvm` (but not CPE)

If you accidentally mix them, reset and start over:

```bash
module reset
```

## Using Core Modules with CPE

Core modules (cmake, git, python, gdb, etc.) are available with CPE:

```bash
module load cpe-stack/25.03
module load cmake/3.31.11
module load git/2.52.0
```

## Switching PrgEnv within CPE

You can switch between compiler environments within a single CPE session:

```bash
module load cpe-stack/25.03
module load PrgEnv-gnu
# ... compile with GNU ...

module swap PrgEnv-gnu PrgEnv-intel
# ... now compile with Intel ...
```

Using `module swap` preserves the CPE stack while changing compilers.

## More Information

For complete CPE documentation, refer to the HPE Cray documentation or consult `man` pages after loading CPE:

```bash
module load cpe-stack/25.03
man intro       # General introduction
man ftn         # Fortran compiler
man cc          # C/C++ compiler
```

## Quick Reference

| Task | Command |
| --- | --- |
| Load CPE | `module load cpe-stack/25.03` |
| Switch to GNU | `module load PrgEnv-gnu` |
| Switch to Intel | `module load PrgEnv-intel` |
| List modules | `module avail` |
| Find package | `module spider fftw` |
| Show dependencies | `module spider fftw/3.3.10` |
| Reset | `module reset` |

## When to Use CPE vs NLR Toolchains

Use CPE if:

- You want a pre-validated, integrated environment
- You need Cray-specific optimizations
- You want guaranteed compatibility between all packages

Use user toolchains if:

- You need specific versions of gcc or Intel oneAPI
- You want flexibility in component selection
- You're porting code built with specific toolchain versions
