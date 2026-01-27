# Contributing to Ally Demo (Python)

This is a **reference integration** for the Ally accessibility toolchain.

Its purpose is to demonstrate the canonical pattern for emitting `cli.error.v0.1` messages and integrating with `a11y-assist`, `a11y-lint`, and `a11y-ci`.

---

## What this repo is

- A minimal, boring example
- A copy-paste template for tool authors
- A test fixture for the Ally ecosystem

## What this repo is NOT

- A framework
- A library for import
- A place for creative features

---

## Contribution guidelines

### ✅ Welcome
- Bug fixes
- Documentation improvements
- Additional error scenarios (following the existing pattern)
- CI improvements

### ❌ Not appropriate here
- Abstraction layers
- Plugin systems
- Dependencies beyond `click`
- Anything that makes this harder to copy-paste

---

## Adding a new error scenario

1. Add a new command in `demo_cli/cli.py`
2. Use the `CliErrorV01` dataclass
3. Include a SAFE `Re-run:` command in the Fix field
4. Add a test in `tests/test_errors.py`
5. Update README if needed

Pattern to follow:

```python
@main.command()
@click.option("--json-out", type=click.Path(dir_okay=False), help="Write JSON to file")
def your_error(json_out: str | None) -> None:
    """Brief description."""
    err = CliErrorV01(
        level="ERROR",
        code="DEM0XX",  # Next available code
        id="DEMO.CATEGORY.DETAIL",
        title="Short title",
        what=["What happened."],
        why=["Why it happened."],
        fix=[
            "First step.",
            "Re-run: demo-cli your-error --dry-run",
        ],
    )
    emit_and_exit(err, json_out)
```

---

## Error ID conventions

IDs should be stable and follow the namespace pattern:

```
DEMO.DOMAIN.CONDITION
```

Examples:
- `DEMO.NETWORK.TIMEOUT`
- `DEMO.CONFIG.MISSING`
- `DEMO.AUTH.INVALID_TOKEN`

---

## SAFE rerun commands

Always include a SAFE rerun command in Fix:
- Use `--dry-run` flag
- Use read-only flags when available
- Never suggest destructive defaults

Example:
```
Fix:
- Re-run: demo-cli network-timeout --timeout 60 --dry-run
```

This dramatically improves downstream assist quality.

---

## Code of conduct

Be respectful. Keep it simple. This is infrastructure, not art.

Thank you for helping make developer tools more accessible.
