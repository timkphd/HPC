# MPI-Enabled Software

MPI implementations and MPI-enabled libraries are available in both NLR-provided toolchain and CPE workflows.

## MPI Libraries Available

MPI-enabled software modules use a `-mpi` suffix. Like compiler-dependent modules, MPI-enabled modules are not visible by default. They appear after loading an MPI implementation (for example, `openmpi`, `mpich`, or `intelmpi`).

## Discovering MPI Variants

### Find all versions of a package

```bash
ml spider hdf5
```

Example output:

```text
hdf5:
--------------------------------------------
Versions:
  hdf5/1.14.5           (serial version)
  hdf5/1.14.5-mpi       (MPI-parallel version)
```

### Check dependencies for the MPI variant

```bash
ml spider hdf5/1.14.5-mpi
```

Example output showing dependencies:

```text
hdf5:
--------------------------------------------
You will need to load all module(s) on one of the lines below before the
'hdf5/1.14.5-mpi' module is available to load.

  gcc/14.2.0  openmpi/5.0.5
  oneapi/2025.1.3  oneapi/mpi-2021.14.0
  oneapi/2025.1.3  openmpi/5.0.5
```

## NLR-Provided Toolchains: Loading MPI Software

### With GCC and OpenMPI

```bash
module reset
module load gcc/14.2.0
module load openmpi/5.0.5
module avail hdf5
```

Output showing MPI variant now visible:

```text
----------- [ gcc/14.2.0, openmpi/5.0.5 ] -----------
  hdf5/1.14.5-mpi
----------- [ gcc/14.2.0 ] -----------
  hdf5/1.14.5
```


## CPE: Loading MPI Software

CPE comes with integrated MPI implementations per `PrgEnv`. For example, with `PrgEnv-gnu`:

```bash
module reset
module load cpe-stack/25.03
module load PrgEnv-gnu
module avail      # Shows MPI-aware libraries
```

The MPI library (typically `cray-mpich`) is already included in the `PrgEnv`.

## Discovery Strategy

To determine whether software is available, use `module spider`. It lists all versions and configurations, including modules hidden from `module avail` in the current environment.

To find prerequisites for a specific configuration, run `module spider` with the full module name and version:

```bash
module spider fftw/3.3.10
module spider netcdf/4.9.2-mpi
```

## Important Notes

1. **MPI selection affects visibility:** Loading an MPI implementation changes which packages are visible.
2. **Both NLR toolchains and CPE support MPI:** Choose your workflow (NLR or CPE) first, then add MPI.
3. **Use `module spider` to check dependencies:** Always verify what MPI library a package expects before loading it.
