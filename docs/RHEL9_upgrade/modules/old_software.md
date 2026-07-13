# Testing RHEL 8 Applications on RHEL 9

As part of RHEL 8 to RHEL 9 migration, the recommended approach is to rebuild and validate applications on RHEL 9.

### Best Practice

Rebuild the application on RHEL 9 and create a new modulefile targeting the RHEL 9 stack.

### Testing Existing RHEL 8 Builds

For compatibility evaluation, existing RHEL 8 applications can be exposed by loading:

```bash
module load application/rhel8
```

This extends module search paths to include software built on RHEL 8.

### Module Dependency Considerations

RHEL 8 applications may rely on modules with changed names, versions, or hierarchy in RHEL 9. Some modulefiles may need updates.

Common issues:

- Renamed dependencies
- Different compiler or MPI hierarchies
- Supporting library version changes
- Missing/deprecated dependencies

Example dependency update:

```lua
depends_on("old_dependency/1.0")
```

May need to become:

```lua
depends_on("new_dependency/2.0")
```

### Validation Checklist

After loading the application module, verify:

- The module loads without errors.
- Required dependencies are resolved.
- Executables are available in the user environment.
- Basic functionality tests complete successfully.

The compatibility module is a temporary mechanism. Production deployments should use software rebuilt and validated on RHEL 9.

