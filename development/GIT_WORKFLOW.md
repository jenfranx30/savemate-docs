# Git Workflow

## Branch Strategy

```
main              # Production-ready code
  ├── develop     # Integration branch
  │   ├── feature/auth-system
  │   ├── feature/deal-management
  │   └── bugfix/login-error
```

## Commit Messages

Format: `type(scope): description`

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Formatting
- `refactor`: Code restructuring
- `test`: Adding tests

Examples:
```
feat(auth): add JWT refresh token support
fix(deals): resolve image upload error
docs(api): update authentication endpoints
```

## Workflow

```bash
# 1. Create feature branch
git checkout -b feature/new-feature

# 2. Make changes and commit
git add .
git commit -m "feat: add new feature"

# 3. Push to remote
git push origin feature/new-feature

# 4. Create pull request
# 5. After review, merge to develop
```
