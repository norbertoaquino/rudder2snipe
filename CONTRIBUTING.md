# Contributing to rudder2snipe

Thank you for your interest in contributing to rudder2snipe! This document outlines the process for contributing to this project.

## 🔄 Development Workflow

### 1. Fork the Repository
- Click the "Fork" button on GitHub
- Clone your fork locally:
```bash
git clone https://github.com/YOUR-USERNAME/rudder2snipe.git
cd rudder2snipe
```

### 2. Create a Feature Branch
```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/your-bug-fix
```

### 3. Make Your Changes
- Follow the existing code style and conventions
- Add tests for new functionality
- Update documentation as needed
- Ensure your code follows Python best practices

### 4. Test Your Changes
```bash
# Run the tool with dry-run to test
python3 rudder2snipe --dryrun --verbose

# Test with your specific configuration
python3 rudder2snipe --connection_test
```

### 5. Commit Your Changes
```bash
git add .
git commit -m "Add descriptive commit message

- Describe what you changed
- Explain why you changed it
- Reference any issues fixed"
```

### 6. Push and Create Pull Request
```bash
git push origin feature/your-feature-name
```

Then create a Pull Request through GitHub's web interface.

## 📋 Pull Request Requirements

### Before Submitting
- [ ] Code follows existing style and conventions
- [ ] Changes are tested and working
- [ ] Documentation is updated if needed
- [ ] Commit messages are clear and descriptive

### PR Description Should Include
- Clear description of changes made
- Reason for the changes
- Testing performed
- Screenshots (if UI changes)
- Reference to related issues

## 🛡️ Branch Protection

This repository has branch protection enabled on the `main` branch:

- **Direct pushes are not allowed**
- **All changes must go through Pull Requests**
- **PRs require at least 1 approval**
- **PRs must pass all status checks**
- **Administrators are also subject to these restrictions**

## 🔧 Development Setup

### Prerequisites
- Python 3.7 or higher
- pip (Python package installer)

### Local Setup
```bash
# Create virtual environment
python3 -m venv ~/.virtualenv/rudder2snipe
source ~/.virtualenv/rudder2snipe/bin/activate

# Install dependencies
pip install -r requirements.txt

# Copy example configuration
cp settings-rudder.conf.example settings.conf
# Edit settings.conf with your configuration
```

## 📖 Code Style

- Follow PEP 8 guidelines
- Use meaningful variable names
- Add comments for complex logic
- Keep functions focused and small
- Use type hints where appropriate

## 🐛 Reporting Issues

When reporting issues, please include:
- Clear description of the problem
- Steps to reproduce
- Expected vs actual behavior
- Environment details (OS, Python version, etc.)
- Relevant logs or error messages

## 💡 Feature Requests

For new features:
- Check existing issues first
- Provide clear use case and rationale
- Consider backward compatibility
- Be open to discussion and feedback

## 📄 License

By contributing, you agree that your contributions will be licensed under the MIT License.

## 🤝 Code of Conduct

Please be respectful and professional in all interactions. We aim to create a welcoming environment for all contributors.
