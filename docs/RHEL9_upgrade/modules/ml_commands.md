# Module Commands Reference

Lmod is the module system used on Kestrel RHEL 9. Here are the essential commands for managing modules.

## Basic Commands

### `module reset`

Reset your module environment to the login state.

```bash
module reset
```

This unloads all modules except defaults (Core/26.05, DefApps) and avoids environment conflicts. Use this before switching toolchains or workflows.

### `module avail` or `ml avail`

List modules visible in your current environment.

```bash
module avail
module avail python      # List only modules matching 'python'
```

**Note:** `module avail` only shows modules compatible with currently loaded modules. Core modules are always visible.

### `module spider`

Search for all available modules, including those not currently visible.

```bash
module spider               # List everything
module spider python        # Find all python versions
module spider python/3.12   # Show details for specific version
```

`module spider` is essential for discovering hidden modules and their dependencies.

### `module load` or `ml`

Load a module into your environment.

```bash
module load gcc/14.2.0
ml gcc/14.2.0             # Shorthand
ml gcc/14.2.0 fftw cmake  # Load multiple modules
```

## Module Listing

### `module list` or `ml`

Show currently loaded modules.

```bash
module list
ml      # Shorthand
```

### `module show`

Display what a module does (environment variables, PATH changes, etc.).

```bash
module show gcc/14.2.0
module show fftw/3.3.10
```

## Module Removal

### `module unload` or `ml -`

Unload a specific module.

```bash
module unload gcc/14.2.0
ml - gcc/14.2.0          # Shorthand
```

### `module purge`

Unload all modules (except system defaults).

```bash
module purge
```

**Caution:** This may break your shell. Use `module reset` instead for a safe reset.

## Module Switching

### `module swap`

Replace one module with another.

```bash
module swap gcc/14.2.0 oneapi/2025.3.1
module swap PrgEnv-gnu PrgEnv-intel     # Within CPE
```

This is safer than `unload` + `load` in sequence.

## Additional Commands

### `module help`

Get help on a module (if the module provides help text).

```bash
module help gcc
```

### `module keyword`

Search module descriptions.

```bash
module keyword fortran
module keyword MPI
```

### `module save` / `module restore`

Save your current module configuration and restore it later.

```bash
module save my-gcc-setup     # Save current setup
module restore my-gcc-setup  # Restore it later
ml --default                  # Restore last saved
```

## Common Workflows

### Switch to User GCC Toolchain

```bash
module reset
module load gcc/14.2.0
module load openmpi/5.0.5
module load fftw cmake
```

### Switch to User oneAPI Toolchain

```bash
module reset
module load oneapi/2025.3.1
module load fftw cmake
```

### Switch to CPE

```bash
module reset
module load cpe-stack/25.03
module load PrgEnv-gnu
```

### Return to Defaults

```bash
module reset
```

## Troubleshooting

**Problem:** Cannot find a module

**Solution:** Use `module spider` to search:

```bash
module spider hdf5
```

**Problem:** Module won't load due to dependencies

**Solution:** Check dependencies:

```bash
module spider hdf5/1.14.5
```

Then load dependencies first.

**Problem:** Mixed toolchains causing conflicts

**Solution:** Reset and start fresh:

```bash
module reset
module load [your-preferred-toolchain]
```

## Shortcuts

Lmod provides convenient shortcuts:

| Full Command | Shorthand |
| --- | --- |
| `module` | `ml` |
| `module avail` | `ml avail` |
| `module load foo` | `ml foo` |
| `module unload foo` | `ml -foo` |
| `module show foo` | `ml show foo` |
| `module list` | `ml` (no args) |

