# Publishing to PyPI

This document explains how to publish the `rds-migration-toolkit` package to PyPI.

## Setup (One-time)

### 1. Create PyPI Account

1. Go to https://pypi.org and create an account
2. Verify your email address
3. Enable 2FA (required for trusted publishers)

### 2. Configure Trusted Publishing (Recommended)

This is the modern, secure way to publish without storing API tokens:

1. Go to your PyPI account settings
2. Navigate to "Publishing" → "Add a new pending publisher"
3. Fill in:
   - **PyPI Project Name**: `rds-migration-toolkit`
   - **Owner**: `arthur-mandel-freshRealm`
   - **Repository name**: `openSourceTools`
   - **Workflow name**: `publish-to-pypi.yml`
   - **Environment name**: (leave blank)

### 3. Alternative: Use API Token

If you prefer traditional API tokens:

1. Go to https://pypi.org/manage/account/token/
2. Create a new API token with scope: "Entire account (all projects)"
3. Copy the token (starts with `pypi-`)
4. Go to your GitHub repository → Settings → Secrets and variables → Actions
5. Create new repository secret:
   - **Name**: `PYPI_API_TOKEN`
   - **Value**: (paste your PyPI token)

## Publishing Methods

### Method 1: GitHub Release (Recommended)

1. Ensure all changes are committed and pushed
2. Go to GitHub repository → Releases → "Draft a new release"
3. Create a new tag (e.g., `v1.0.0`)
4. Title: `Release 1.0.0`
5. Description: Document changes in this release
6. Click "Publish release"
7. The workflow will automatically build and publish to PyPI

### Method 2: Manual Workflow Dispatch

1. Go to GitHub → Actions → "Publish to PyPI"
2. Click "Run workflow"
3. Choose branch: `main`
4. (Optional) Enter custom version number
5. Click "Run workflow"

### Method 3: Local Build (Testing)

For testing the build locally before publishing:

```bash
# Install build tools
pip install build twine

# Build the package
python -m build

# Check the package
twine check dist/*

# Test upload to Test PyPI (optional)
twine upload --repository testpypi dist/*

# Upload to PyPI (when ready)
twine upload dist/*
```

## Version Management

The version is defined in `pyproject.toml`:

```toml
[project]
version = "1.0.0"
```

### Versioning Guidelines

Follow [Semantic Versioning](https://semver.org/):

- **Major** (1.0.0): Breaking changes
- **Minor** (1.1.0): New features, backwards compatible
- **Patch** (1.0.1): Bug fixes, backwards compatible

### Pre-release Versions

For pre-releases, use:
- `1.0.0a1` - Alpha
- `1.0.0b1` - Beta
- `1.0.0rc1` - Release Candidate

## Workflow Details

### publish-to-pypi.yml

Triggered by:
- GitHub releases (automatic)
- Manual workflow dispatch (with optional version override)

Steps:
1. Checkout code
2. Set up Python 3.11
3. Install build dependencies
4. Update version (if manual dispatch with version specified)
5. Build package using `python -m build`
6. Validate package with `twine check`
7. Publish to PyPI
8. Create release summary

### test.yml

Runs on:
- Every push to `main`
- Every pull request to `main`

Tests:
- Python 3.9, 3.10, 3.11, 3.12
- Type checking (mypy)
- Linting (ruff)
- Formatting (black)
- Package build validation

## Post-Publishing

After publishing, verify:

1. Package appears on PyPI: https://pypi.org/project/rds-migration-toolkit/
2. Installation works: `pip install rds-migration-toolkit`
3. CLI command works: `rds-migrate --help`
4. Update badges in README.md automatically show correct version

## Troubleshooting

### "Project name already exists"

If the name is taken, update `pyproject.toml`:
```toml
name = "rds-migration-toolkit-yourusername"
```

### "Trusted publishing not configured"

Either:
1. Configure trusted publishing on PyPI (recommended)
2. Add `PYPI_API_TOKEN` to GitHub secrets

### "File already exists"

The workflow has `skip-existing: true`, so it won't fail on re-uploads.
To upload a new version, increment the version number.

### "Invalid distribution"

Run locally:
```bash
python -m build
twine check dist/*
```

This will show validation errors.

## Security Notes

- Never commit API tokens to the repository
- Use trusted publishing when possible (no tokens needed)
- The workflow uses `id-token: write` for OIDC authentication
- All credentials are managed by GitHub Actions secrets

## Resources

- [PyPI Publishing Guide](https://packaging.python.org/en/latest/tutorials/packaging-projects/)
- [GitHub Actions for PyPI](https://github.com/marketplace/actions/pypi-publish)
- [Trusted Publishing](https://docs.pypi.org/trusted-publishers/)
- [Semantic Versioning](https://semver.org/)
