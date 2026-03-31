# Cache Eviction

Available scoped cache eviction through `cache clear`:

```bash
vp cache clear
vp cache clear <task-name>
vp cache clear --filter <filter-query> <task-name>
vp cache clear --filter <filter-query> --filter <filter-query> <task-name>
```

Examples:

```bash
vp cache clear
vp cache clear build
vp cache clear --filter @my/abc build
vp cache clear --filter @my/* --filter ./packages/ui build
```

## Semantics

- `vp cache clear`: evict all cache entries.
- `vp cache clear build`: evict entries for task name `build` across workspace.
- `vp cache clear --filter @my/abc build`: evict entries for task name `build` only in packages matched by `@my/abc`.
- `vp cache clear --filter "@my/*" --filter ./packages/ui build`: apply all provided filters and evict entries for task name `build` in the matched package set.

Task-name matching is exact (no wildcard/regex in this RFC).

## Filtering

`--filter` uses the same package-filter query syntax as task execution package selection.

- Each `--filter` narrows the package set used for eviction.
- `--filter` only scopes packages; it does not match task names.
- `<task-name>` is still required when using `--filter`.

Common patterns:

```bash
# Exact package
vp cache clear --filter @my/abc build

# Package name glob
vp cache clear --filter @my/* build

# Directory-based filter
vp cache clear --filter ./packages/ui build

# Multiple filters
vp cache clear --filter "@my/*" --filter ./packages/ui build
```

## CLI constraints

- No args means full clear.
- If `--filter` is provided, `<task-name>` is required.
- `--filter` may be provided multiple times.

## Output

- Success message includes cleared count.
- Non-zero exit only on invalid args or storage errors.
