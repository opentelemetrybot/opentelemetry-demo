# GitHub Workflow Permissions Verification

This tool verifies that all GitHub workflow files have proper root-level permissions defined according to OpenSSF Scorecard recommendations.

## Purpose

This script helps maintain compliance with the [Token-Permissions security check](https://github.com/ossf/scorecard/blob/main/docs/checks.md#token-permissions) from OpenSSF Scorecard.

## Requirements

- Python 3.6+
- PyYAML library: `pip install PyYAML`

## Usage

```bash
# Verify all workflows in the current repository
python internal/tools/verify_workflow_permissions.py

# Verify workflows in a specific directory
python internal/tools/verify_workflow_permissions.py /path/to/repo

# Quiet mode (only show summary)
python internal/tools/verify_workflow_permissions.py --quiet
```

## What it checks

The script verifies that:

1. **Root-level permissions exist**: Every workflow file has a `permissions:` block at the root level
2. **Valid permission formats**: Accepts:
   - `permissions: read-all` (recommended for some use cases)
   - `permissions: { contents: read }` (recommended default)
   - `permissions: write-all` (valid but warns about over-permissive)
3. **Invalid configurations**: Detects missing, empty, or malformed permissions blocks

## Current Status

✅ **All 13 workflow files in this repository are compliant** with OpenSSF Scorecard Token-Permissions recommendations.

## Integration with CI/CD

You can integrate this script into your CI/CD pipeline to ensure ongoing compliance:

```yaml
# Example GitHub Actions step
- name: Verify Workflow Permissions
  run: |
    pip install PyYAML
    python internal/tools/verify_workflow_permissions.py
```

## Output

The script provides:
- ✅ **Pass**: Workflow has proper root-level permissions
- ⚠️ **Warning**: Workflow has valid but potentially over-permissive permissions
- ❌ **Error**: Workflow missing or has invalid root-level permissions
- 📝 **Info**: Lists jobs that have their own permission blocks

## Best Practices

1. **Root-level permissions should be minimal**: Use `contents: read` or `read-all`
2. **Job-level permissions for specific needs**: Add write permissions only to jobs that need them
3. **Regular verification**: Run this script periodically to ensure ongoing compliance
4. **Review warnings**: Address any warnings to maintain optimal security posture