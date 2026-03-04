# What is Pixi?

[Pixi](https://pixi.prefix.dev/latest/) is a package management tool that attempts to unify the workflows of existing package managers such as [conda](conda.md) or [pip](https://pip.pypa.io/en/stable/) for a smoother and more robust user experience. Pixi uses the [rattler](https://github.com/prefix-dev/rattler-build/tree/main) library, a high-performance implementation of core conda functionalities (such as dependency tree solving) written in [Rust](https://rust-lang.org), leading to Pixi being significantly faster than "traditional" or "pure" conda. Pixi facilitates the management of project-specific environments which may contain a mix of packages from Python and other languages. Pixi handles both *environment creation* and *package installation*, replacing the need to use conda for the former and pip for the latter. 

# Using Pixi on Kestrel

Pixi is available as a module on both the CPU and GPU nodes on Kestrel:

```
$ ml help pixi
------------------ Module Specific Help for "pixi/0.65.0" ------------------
Name   : Pixi
Version: 0.65.0 (built 27 February 2026)
Source : https://github.com/prefix-dev/pixi
Docs   : https://pixi.prefix.dev

Pixi is a cross-platform, multi-language package manager and workflow tool 
built on the foundation of the conda ecosystem. It provides developers with 
an exceptional experience similar to popular package managers like cargo or 
npm, but for any language.
```

Pixi is mainly designed to create environments for a specific project/working directory. Two minimal examples (one for CPU nodes, and another for GPU nodes) of creating a Pixi environment and running a script from each are provided below. NLR HPC users are encouraged to consult the [Pixi documentation](https://pixi.prefix.dev) for more information on how to get the most from Pixi.

## Minimal environment example on Kestrel - CPU

The following scripts represent a minimal example of using the Pixi module to 1. create a simple Pixi environment containing the `numpy` package (named `numpy-workspace`) and then 2. execute a Python script that performs a matrix multiplication 10 times (`numpy-test.py`). **Be sure to run this on a CPU node:**

??? "Example: Using Pixi to create an environment and execute `numpy-test.py`"
    Ensure that `numpy-test.py` (found in the next drop-down menu) exists one directory above `numpy-workspace` for this example.

    ```bash
    #!/bin/bash
    # Load Pixi module
    ml pixi
    # Initialize Pixi environment
    pixi init numpy-workspace
    # Note that we navigate to the Pixi environment folder to add packages and eventually execute the Python script
    cd numpy-workspace
    # Add numpy and Python as dependencies
    pixi add numpy python=3.11
    # The Python script we wish to execute is found one directory above 'numpy-workspace'
    pixi run python ../numpy-test.py

    # Optional - cleanup numpy-workspace and PIXI_CACHE_DIR
    #echo "Removing numpy-workspace..."
    #cd .. && rm -rf numpy-workspace
    #echo "Removing PIXI_CACHE_DIR..."
    #rm -rf $PIXI_CACHE_DIR
    ```

??? "`numpy-test.py`"
    ```python
    import numpy as np
    from time import time
    from time import sleep
    import os

    print(f"Running Python script using the Pixi environment '{os.getcwd()}'")

    # create random arrays as input data
    asize = pow(10, 6)
    array_a = np.float32(np.random.rand(asize))
    array_b = np.float32(np.random.rand(asize))
    array_c = np.float32(np.random.rand(asize))

    matrix_a = ([array_a], [array_b], [array_c])
    matrix_b = ([array_c], [array_b], [array_a])

    # numpy - CPU
    nloops = 10
    t0 = time()
    for i in np.arange(nloops):
        np.multiply(matrix_a, matrix_b)
    cpu_time = time()-t0
    print("numpy on CPU required", round(cpu_time, 2), "seconds for multiplying two matrices each of size", 3*asize, "a total number of", nloops, "times.")
    ```

Note that the Python script intended to be run by this environment (`numpy-test.py`) is executed from the `numpy-workspace` folder via `pixi run python ../numpy-test.py`. After the environment is created and you navigate to the environment folder, providing the call to Python with the `pixi run ...` prefix will use the version of Python and its associated packages from the `numpy-workspace` environment. 

## Minimal environment example on Kestrel - GPU

The following script represents a minimal example of using the Pixi module to 1. create a simple Pixi environment containing a GPU-enabled version of `torch` (named `cuda-workspace`) and then 2. run a simple Python command that verifies whether this environment's `torch` can see a GPU device. **Be sure to run this on a GPU node:**

??? "Example: Using Pixi to create a GPU-enabled PyTorch environment on Kestrel"
    ```
    #!/bin/bash
    # Load Pixi module
    ml pixi
    # Initialize Pixi environment
    pixi init cuda-workspace
    # Note that we navigate to the Pixi environment folder to add packages and eventually execute the Python script
    cd cuda-workspace

    # Manually create pixi.toml
    cat <<EOF > pixi.toml
    [workspace]
    channels = ["https://prefix.dev/conda-forge"]
    name = "pytorch-conda-forge"
    platforms = ["linux-64",]

    [system-requirements]
    cuda = "12.4"

    [dependencies]
    pytorch-gpu = "*"
    cuda-version = ">=12.4"
    cowpy = "*"
    python = "3.11.*"
    EOF
    pixi run cowpy "MUUUUUUDA!"
    pixi run python -c "import torch; print('Can pixi find a GPU? -->', torch.cuda.is_available(), '\n', 'Using CUDA version:', torch.version.cuda)"
    ```

Note that in this example, we specify `cuda = "12.4"` under `[system-requirements]` in the `pixi.toml`. This will allow Pixi to install a GPU-enabled version of PyTorch; without this, Pixi would install a CPU-only version of `torch`. Additionally, when creating environments from a custom `pixi.toml`, note that anything under `[dependencies]` is functionally equivalent to `pixi add <package1> <package2> ... <packageN>` as written in the [CPU example above](#minimal-environment-example-on-kestrel---cpu). At the time of writing, the GPU drivers on Kestrel are compatible with `cuda/12.4+`, so we pin `cuda-version = ">=12.4"` as a dependency accordingly as an extra insurance that we pull a compatible version of PyTorch.

!!! warning "A note on performant, multi-node PyTorch on Kestrel's GPU nodes"
    Note that installing PyTorch with the aim for good communication performance across multiple GPU nodes on Kestrel requires special considerations that are not covered in this page. See [our dedicated documentation on the topic](../../Machine_Learning/index.md#installing-pytorch-on-kestrel-with-multi-node-and-gpu-support) for more information.

## Package caching location

On Kestrel, the Pixi modules are designed to cache downloaded packages to `/scratch/${USER}/.cache/rattler` by default. This saves users storage space in their `/home` or `/projects` folders, though this may be overridden by users by modifying and exporting the `PIXI_CACHE_DIR` environment variable after loading the module.

To save space in your personal `/scratch`, you may safely run `rm -rf /scratch/${USER}/.cache/rattler` at any time to clear this cache directory.

## Tips and tricks for using Pixi on Kestrel

**Coming soon!**

# Useful links

- [Managing Python Environments with Pixi-From Laptop to HPC](https://nrel-my.sharepoint.com/:v:/g/personal/chschwin_nrel_gov/IQAr0Zstq-bQSZ5nkfcd-eXDAT_GOjhkcPXWHa6S2XRympU) (NLR HPC Tutorial series - requires access to CSC Tutorials Teams channel)
- [Switching from Conda/Mamba to Pixi](https://pixi.prefix.dev/latest/switching_from/conda/) (external site)
- [PyTorch installation with Pixi](https://pixi.prefix.dev/latest/python/pytorch/) (external site)
- [Building custom packages with Pixi](https://pixi.prefix.dev/latest/build/getting_started/) (external site)