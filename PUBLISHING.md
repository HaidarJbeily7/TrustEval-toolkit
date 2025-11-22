# Publishing Guide for TrustEval

This guide explains how to publish the TrustEval package to PyPI using GitHub Actions.

## Overview

The project is configured with three GitHub Actions workflows:

1. **Test Build** (`test-build.yml`) - Runs on every push and PR to ensure the package builds correctly
2. **Publish to TestPyPI** (`publish-to-test-pypi.yml`) - Publishes to TestPyPI for testing
3. **Publish to PyPI** (`publish-to-pypi.yml`) - Publishes to PyPI on releases

## Prerequisites

### 1. Configure PyPI Trusted Publishing

PyPI now supports "trusted publishing" via GitHub Actions, which is more secure than using API tokens.

#### For PyPI:
1. Go to https://pypi.org/manage/account/publishing/
2. Add a new pending publisher with:
   - **PyPI Project Name**: `trusteval`
   - **Owner**: Your GitHub username or organization
   - **Repository name**: `TrustEval-toolkit`
   - **Workflow name**: `publish-to-pypi.yml`
   - **Environment name**: (leave empty)

#### For TestPyPI (optional, for testing):
1. Go to https://test.pypi.org/manage/account/publishing/
2. Follow the same steps as above but use `publish-to-test-pypi.yml` as the workflow name

### 2. Update Package Version

Before publishing, update the version in `pyproject.toml`:

```toml
[project]
version = "0.1.0"  # Update this version
```

Follow [Semantic Versioning](https://semver.org/):
- **Major version** (1.0.0): Breaking changes
- **Minor version** (0.1.0): New features, backward compatible
- **Patch version** (0.0.1): Bug fixes

## Publishing Process

### Method 1: Publishing via GitHub Release (Recommended)

This is the standard way to publish to PyPI:

1. **Commit and push your changes:**
   ```bash
   git add .
   git commit -m "Prepare version X.Y.Z"
   git push origin main
   ```

2. **Create a new release on GitHub:**
   - Go to your repository on GitHub
   - Click "Releases" → "Create a new release"
   - Create a new tag (e.g., `v0.1.0`)
   - Title: `Release v0.1.0`
   - Description: Add release notes
   - Click "Publish release"

3. **Automatic deployment:**
   - GitHub Actions will automatically trigger the `publish-to-pypi.yml` workflow
   - The package will be built and published to PyPI
   - Check the Actions tab to monitor progress

### Method 2: Manual Trigger

You can also manually trigger the workflow:

1. Go to the "Actions" tab in your GitHub repository
2. Select the "Publish to PyPI" workflow
3. Click "Run workflow"
4. Select the branch and click "Run workflow"

### Method 3: Testing with TestPyPI

Before publishing to PyPI, you can test with TestPyPI:

1. The workflow `publish-to-test-pypi.yml` runs automatically when you push changes to `main` that affect `pyproject.toml` or the `trusteval/` directory
2. Alternatively, manually trigger it from the Actions tab
3. Once published to TestPyPI, you can test install:
   ```bash
   pip install --index-url https://test.pypi.org/simple/ --extra-index-url https://pypi.org/simple/ trusteval
   ```

## Local Testing Before Publishing

Before creating a release, test the build locally:

```bash
# Install build tools
pip install build twine

# Build the package
python -m build

# Check the build
twine check dist/*

# Test install locally
pip install dist/trusteval-0.1.0-py3-none-any.whl

# Test import
python -c "import trusteval; print('Success!')"
```

## Verification

After publishing, verify your package:

1. **Check PyPI:** Visit https://pypi.org/project/trusteval/
2. **Test installation:**
   ```bash
   # Create a fresh virtual environment
   python -m venv test_env
   source test_env/bin/activate  # On Windows: test_env\Scripts\activate
   
   # Install from PyPI
   pip install trusteval
   
   # Test import
   python -c "import trusteval; print('Successfully installed from PyPI!')"
   ```

## Troubleshooting

### Build Fails

- Check that all imports work correctly
- Ensure `pyproject.toml` is valid
- Verify that all required files are included in `MANIFEST.in`

### Publishing Fails

- Ensure you've set up Trusted Publishing on PyPI
- Check that the version number hasn't been used before
- Review the GitHub Actions logs for specific errors

### Package Installation Issues

- Ensure all dependencies are listed in `pyproject.toml`
- Check that data files (JSON, YAML, etc.) are included via `package-data` in `pyproject.toml`

## Version Management Tips

1. Keep a CHANGELOG.md to track changes between versions
2. Use git tags for releases: `git tag v0.1.0 && git push --tags`
3. Consider using pre-release versions for testing: `0.1.0rc1`, `0.1.0b1`

## Rolling Back

If you need to remove a version from PyPI:

1. Go to https://pypi.org/project/trusteval/
2. Navigate to the specific version
3. Click "Options" → "Delete release"

**Note:** You cannot re-upload the same version number after deletion. You must increment the version.

## Additional Resources

- [Python Packaging User Guide](https://packaging.python.org/)
- [PyPI Help](https://pypi.org/help/)
- [Trusted Publishing Guide](https://docs.pypi.org/trusted-publishers/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

