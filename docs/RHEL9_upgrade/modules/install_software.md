# Installing Software

Build on compute nodes, not login nodes.

Note: The CPU stack is loaded by default on DAV nodes. If software is intended for DAV use, install it on the CPU Compute nodes.

## Testing Modulefiles

1. Load the module directly from the modulefile.

   ```bash
   module use $(pwd)/modules
   module load softwareA/version
   ```

   Alternatively, from any directory:

   ```bash
   module use {kestreltype}/software/softwareA/modules
   module load softwareA/version
   ```

3. Verify the module is loaded.

   ```bash
   module list
   ```

4. Verify environment changes.

   ```bash
   module show softwareA/version
   ```

   Confirm expected variables (for example, `PATH`, `LD_LIBRARY_PATH`, and application-specific variables).

5. Test the software.

   ```bash
   softwareA --version
   ```

6. Clean up.

   ```bash
   module unload softwareA/version
   module unuse $(pwd)/modules
   ```
