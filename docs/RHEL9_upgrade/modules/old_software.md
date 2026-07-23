# Testing RHEL8 Applications on RHEL9

As part of the migration from RHEL8 to RHEL9, the recommended approach is to rebuild and validate applications on RHEL9.

### Best Practice

Rebuild the application on RHEL9 and create a new modulefile targeting the RHEL9 stack.

### Testing Existing RHEL8 Builds

For compatibility evaluation, existing RHEL8 applications can be exposed by loading:

```bash
module load application/rhel8
```

This extends module search paths to include software built on RHEL8.

### Module Dependency Considerations

RHEL8 applications may rely on modules with changed names, versions, or hierarchy in RHEL9. Some modulefiles may need updates.

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

The compatibility module is a temporary mechanism. Production deployments should use software rebuilt and validated on RHEL9.

