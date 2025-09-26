# GitHub Configuration

This directory contains GitHub-specific configuration files for the rudder2snipe project.

## 📁 Directory Structure

```
.github/
├── ISSUE_TEMPLATE/
│   ├── bug_report.md       # Template for bug reports
│   └── feature_request.md  # Template for feature requests
├── workflows/
│   └── ci.yml             # CI/CD pipeline configuration
├── pull_request_template.md # Template for pull requests
└── README.md              # This file
```

## 🛡️ Branch Protection

This repository has the following branch protection rules configured on the `main` branch:

### Required Settings
- **Require pull request reviews before merging**: ✅
  - Required approving reviews: 1
  - Dismiss stale reviews when new commits are pushed: ✅
  
- **Require status checks to pass before merging**: ✅
  - Require branches to be up to date before merging: ✅
  - Required status checks:
    - `test` (CI pipeline)
    - `security` (Security scans)
    - `validate-config` (Configuration validation)
    
- **Require conversation resolution before merging**: ✅
- **Restrict pushes that create files larger than 100 MB**: ✅
- **Do not allow bypassing the above settings**: ✅
- **Include administrators**: ✅

### What This Means
- No direct pushes to the `main` branch
- All changes must go through Pull Requests
- PRs must be reviewed and approved
- All CI checks must pass
- Even administrators must follow these rules

## 🔄 Workflow Overview

### For Contributors
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Push to your fork
5. Create a Pull Request
6. Wait for review and approval
7. Once approved and checks pass, changes will be merged

### For Maintainers
1. Review pull requests
2. Ensure CI checks pass
3. Approve changes
4. Merge using "Squash and merge" for clean history

## 🚀 CI/CD Pipeline

The CI pipeline runs on:
- Every push to `main`
- Every pull request to `main`

### Checks Performed
- **Multi-version Python testing** (3.7-3.11)
- **Code linting** with flake8 and pylint
- **Security scanning** with bandit and safety
- **Syntax validation**
- **Configuration validation**
- **Basic functionality tests**

## 📝 Templates

### Issue Templates
- **Bug Report**: Structured template for reporting bugs
- **Feature Request**: Template for suggesting new features

### Pull Request Template
- Comprehensive checklist for contributors
- Required information for reviewers
- Testing validation requirements

## 🔧 Maintaining This Configuration

To update branch protection rules:
1. Go to Repository Settings
2. Navigate to "Branches" in the sidebar
3. Edit the rule for the `main` branch
4. Update as needed

To update CI workflow:
1. Edit `.github/workflows/ci.yml`
2. Test changes in a PR first
3. Merge after validation
