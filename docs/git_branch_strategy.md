# Git Branch Strategy

## Main Branches

### `main` Branch
- Production-ready code
- Protected branch
- Requires pull request and review for merging
- No direct commits allowed
- Tagged with version numbers for releases

### `develop` Branch
- Main development branch
- Direct commits and merges allowed
- Force pushes allowed for administrators
- Base branch for feature development
- Contains latest delivered development changes
- Merged back to `main` through release branches

## Supporting Branches

### Feature Branches (`feature/*`)
- Branch from: `develop`
- Merge back to: `develop`
- Naming convention: `feature/brief-description`
  - Use kebab-case for the description
  - Issue ID is optional: `feature/[issue-id-]brief-description`
- Examples: 
  - `feature/user-authentication`
  - `feature/123-user-authentication`

### Release Branches (`release/*`)
- Branch from: `develop`
- Merge back to: `main` and `develop`
- Naming convention: `release/vX.Y.Z`
- Example: `release/v1.0.0`
- Used for release preparation
- Only bug fixes and release-related tasks

### Hotfix Branches (`hotfix/*`)
- Branch from: `main`
- Merge back to: `main` and `develop`
- Naming convention: `hotfix/vX.Y.Z`
- Example: `hotfix/v1.0.1`
- Used for urgent production fixes

## Branch Protection Rules

### Main Branch (`main`)
- Require pull request reviews
- Require status checks to pass
- Require linear history
- No direct pushes
- No force pushes

### Develop Branch (`develop`)
- Main development branch
- Direct commits and merges allowed
- Force pushes allowed for administrators

## Workflow

1. Feature Development
   ```
   # Create feature branch
   git checkout develop
   git pull origin develop
   git checkout -b feature/xxx-feature-name
   
   # Work on feature
   git add .
   git commit -m "feat: add new feature"
   
   # Push feature
   git push origin feature/xxx-feature-name
   
   # Create PR to develop
   ```

2. Release Process
   ```
   # Create release branch
   git checkout develop
   git checkout -b release/vX.Y.Z
   
   # Make release preparations
   git add .
   git commit -m "chore: prepare vX.Y.Z release"
   
   # Merge to main and develop
   ```

3. Hotfix Process
   ```
   # Create hotfix branch
   git checkout main
   git checkout -b hotfix/vX.Y.Z
   
   # Fix the issue
   git add .
   git commit -m "fix: critical issue"
   
   # Merge to main and develop
   ```

## Commit Message Convention

Format: `<type>(<scope>): <subject>`

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc)
- `refactor`: Code refactoring
- `test`: Adding tests
- `chore`: Maintenance tasks

Example:
```
feat(auth): implement user authentication
fix(api): resolve user data fetch issue
docs(readme): update installation instructions
```

## Implementation Timeline

1. Initial Phase (Current)
   - Use `main` branch for initial project setup
   - Set up basic repository structure
   - Add initial documentation

2. Development Phase Start
   - Create `develop` branch
   - Set up branch protection rules
   - Begin feature branch workflow

3. First Release
   - Implement release branch process
   - Set up CI/CD pipelines
   - Configure automated testing

4. Maintenance Phase
   - Implement hotfix process
   - Regular maintenance and updates
   - Version tagging
