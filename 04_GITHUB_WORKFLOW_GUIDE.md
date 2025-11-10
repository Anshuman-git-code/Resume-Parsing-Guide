# GitHub Collaboration Guide for Team

## Table of Contents
1. [Initial Setup](#initial-setup)
2. [Branch Strategy](#branch-strategy)
3. [Daily Git Workflow](#daily-git-workflow)
4. [Pull Request Process](#pull-request-process)
5. [Handling Merge Conflicts](#handling-merge-conflicts)
6. [Best Practices](#best-practices)

---

## Initial Setup

### Team Lead: Create Repository

```bash
# 1. Create repo on GitHub.com
# - Go to github.com
# - Click "New Repository"
# - Name: resume-parser-skill-matcher
# - Description: Resume Parser & Skill Matcher for Recruiters
# - Public or Private: Private (recommended)
# - Initialize with README: Yes
# - Add .gitignore: Python
# - License: MIT (optional)

# 2. Clone to your local machine
git clone https://github.com/[your-username]/resume-parser-skill-matcher.git
cd resume-parser-skill-matcher

# 3. Create initial project structure
mkdir -p backend/lambda-functions/{upload-handler,textract-processor,skill-parser,job-matcher}
mkdir -p backend/utils backend/tests
mkdir -p frontend/dashboard frontend/assets
mkdir -p infrastructure/{cloudformation,iam-policies}
mkdir -p docs/handoff-notes
mkdir -p sample-data/{resumes,job-descriptions,outputs}

# 4. Create initial files
touch backend/lambda-functions/upload-handler/handler.py
touch backend/lambda-functions/textract-processor/handler.py
touch backend/lambda-functions/skill-parser/handler.py
touch backend/lambda-functions/job-matcher/handler.py
touch backend/utils/config.py
touch frontend/dashboard/index.html
touch README.md
touch requirements.txt
touch .gitignore

# 5. Create .gitignore
cat > .gitignore << 'EOF'
# AWS Credentials
.env
credentials.csv
*.pem
.aws/
config.json

# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
venv/
env/
ENV/

# IDEs
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
*.log

# Test outputs
test-outputs/
EOF

# 6. Create README
cat > README.md << 'EOF'
# Resume Parser & Skill Matcher

AI-powered resume parsing and job matching system for recruiters.

## Team Members
- Anshuman (Team Lead)
- Member 2
- Member 3
- Member 4

## Project Timeline
Start: November 10, 2025
Deadline: November 17, 2025

## Architecture
[Architecture diagram to be added]

## Setup Instructions
[To be added]

## Documentation
See `/docs` folder for detailed documentation.
EOF

# 7. Initial commit
git add .
git commit -m "Initial project structure"
git push origin main

# 8. Create develop branch
git checkout -b develop
git push origin develop

# 9. Protect main branch (on GitHub)
# - Go to Settings → Branches
# - Add rule for 'main'
# - Require pull request reviews before merging
# - Require status checks to pass
```

### Team Members: Clone Repository

```bash
# 1. Clone the repository
git clone https://github.com/[team-lead-username]/resume-parser-skill-matcher.git
cd resume-parser-skill-matcher

# 2. Verify you have both branches
git branch -a
# Should see:
# * main
#   remotes/origin/main
#   remotes/origin/develop

# 3. Checkout develop branch
git checkout develop

# 4. Configure your Git identity (if not done)
git config user.name "Your Name"
git config user.email "your.email@example.com"

# 5. Verify setup
git status
git log --oneline
```

---

## Branch Strategy

### Branch Types

```
main (protected)
  ↓
develop (integration branch)
  ↓
feature/[feature-name] (individual work)
```

**main:**
- Production-ready code only
- Protected (requires PR approval)
- Only Team Lead merges to main

**develop:**
- Integration branch
- All features merge here first
- Should always be in working state

**feature/[name]:**
- Individual feature branches
- Created from develop
- Merged back to develop via PR

### Branch Naming Convention

```
feature/upload-handler          (Member 1)
feature/textract-integration    (Member 1)
feature/skill-parser            (Member 2)
feature/nlp-logic               (Member 2)
feature/job-matcher             (Member 3)
feature/dynamodb-setup          (Member 3)
feature/dashboard-ui            (Member 4)
feature/api-integration         (Member 4)
```

---

## Daily Git Workflow

### Starting Your Day

```bash
# 1. Make sure you're on develop
git checkout develop

# 2. Get latest changes from team
git pull origin develop

# 3. Create your feature branch (or switch to existing)
git checkout -b feature/your-feature-name
# Or if branch exists:
git checkout feature/your-feature-name

# 4. Merge latest develop into your branch
git merge develop

# 5. Start coding!
```

### During Development

```bash
# 1. Check what files you've changed
git status

# 2. See specific changes
git diff

# 3. Add files to staging
git add filename.py
# Or add all changes:
git add .

# 4. Commit with clear message
git commit -m "feat: Add resume upload to S3"

# 5. Push to your branch
git push origin feature/your-feature-name

# Commit frequently (every 30-60 mins of work)
# Small commits are easier to review and debug
```

### Commit Message Convention

```
Format: <type>: <description>

Types:
- feat: New feature
- fix: Bug fix
- docs: Documentation changes
- test: Adding tests
- refactor: Code refactoring
- style: Formatting changes
- chore: Maintenance tasks

Examples:
✅ feat: Add S3 upload handler Lambda
✅ fix: Resolve Textract timeout issue
✅ docs: Update API documentation
✅ test: Add unit tests for skill parser
✅ refactor: Optimize matching algorithm
✅ style: Format code with black
✅ chore: Update requirements.txt

❌ "updated files"
❌ "changes"
❌ "work in progress"
```

### End of Day

```bash
# 1. Commit any remaining work
git add .
git commit -m "feat: Work in progress on [feature]"

# 2. Push to remote (backup)
git push origin feature/your-feature-name

# 3. Post update in Google Chat
# "Pushed today's work to feature/your-feature-name"
```

---

## Pull Request Process

### When to Create a PR

Create a PR when:
- Your feature is complete and tested
- Code is ready for review
- You want feedback on approach
- Ready to integrate with team's work

### Creating a Pull Request

**Step 1: Prepare Your Branch**
```bash
# 1. Make sure all changes are committed
git status  # Should be clean

# 2. Update with latest develop
git checkout develop
git pull origin develop
git checkout feature/your-feature-name
git merge develop

# 3. Resolve any conflicts (see section below)

# 4. Test that everything still works

# 5. Push final version
git push origin feature/your-feature-name
```

**Step 2: Create PR on GitHub**
1. Go to repository on GitHub
2. Click "Pull Requests" tab
3. Click "New Pull Request"
4. Base: `develop` ← Compare: `feature/your-feature-name`
5. Click "Create Pull Request"

**Step 3: Fill PR Template**
```markdown
## Description
Brief description of what this PR does

## Changes Made
- Added S3 upload handler
- Integrated Textract API
- Created sample output format

## Testing Done
- Tested with 5 different PDF formats
- Verified Textract extraction accuracy
- Tested error handling

## Files for Review
- `backend/lambda-functions/upload-handler/handler.py`
- `backend/utils/s3_helper.py`

## Handoff Information
- S3 Bucket: resume-parser-bucket-main
- Output format: See `sample-data/outputs/textract-sample.json`
- Next member: @member2-name

## Screenshots (if applicable)
[Add screenshots of dashboard, logs, etc.]

## Checklist
- [ ] Code is tested
- [ ] Documentation updated
- [ ] No secrets in code
- [ ] Sample outputs provided
- [ ] Ready for integration
```

**Step 4: Request Review**
- Assign reviewers: Team Lead + 1 other member
- Add labels: `feature`, `ready-for-review`
- Link related issues (if any)

**Step 5: Notify Team**
```
Post in Google Chat:
🔍 PR Ready for Review!
Feature: [Your feature name]
Link: [PR URL]
Reviewers: @TeamLead @Member2
Please review by: [Date/Time]
```

### Reviewing a Pull Request

**As a Reviewer:**

1. **Read the description** - Understand what changed
2. **Check the files** - Review code changes
3. **Look for:**
   - Code quality and readability
   - Potential bugs
   - Security issues (hardcoded credentials?)
   - Missing error handling
   - Unclear variable names
   - Missing documentation

4. **Leave comments:**
   ```
   Good comment:
   "Consider adding error handling here for when S3 upload fails"
   
   Bad comment:
   "This is wrong"
   ```

5. **Approve or Request Changes:**
   - ✅ Approve: If code looks good
   - 💬 Comment: If you have questions
   - ❌ Request Changes: If issues must be fixed

**Review Checklist:**
- [ ] Code follows project conventions
- [ ] No hardcoded credentials or secrets
- [ ] Error handling is present
- [ ] Code is commented where needed
- [ ] Tests are included (if applicable)
- [ ] Documentation is updated
- [ ] No unnecessary files committed

### Merging a Pull Request

**Team Lead Responsibility:**

```bash
# Option 1: Merge via GitHub UI
# - Click "Merge Pull Request"
# - Choose "Squash and Merge" (cleaner history)
# - Delete branch after merge

# Option 2: Merge via command line
git checkout develop
git pull origin develop
git merge --no-ff feature/your-feature-name
git push origin develop

# Delete merged branch
git branch -d feature/your-feature-name
git push origin --delete feature/your-feature-name
```

---

## Handling Merge Conflicts

### What is a Merge Conflict?

Occurs when:
- Two people edit the same file
- Git can't automatically merge changes
- You must manually resolve

### Resolving Conflicts

```bash
# 1. Update your branch with develop
git checkout feature/your-feature-name
git merge develop

# If conflicts occur, you'll see:
# CONFLICT (content): Merge conflict in filename.py
# Automatic merge failed; fix conflicts and then commit the result.

# 2. Check which files have conflicts
git status
# Files with conflicts will be marked as "both modified"

# 3. Open conflicted file
# You'll see conflict markers:

<<<<<<< HEAD (your changes)
your code here
=======
their code here
>>>>>>> develop (their changes)

# 4. Resolve conflict by editing file
# Choose one version or combine both
# Remove conflict markers (<<<, ===, >>>)

# Example resolution:
# Before:
<<<<<<< HEAD
bucket_name = "my-bucket"
=======
bucket_name = "resume-parser-bucket"
>>>>>>> develop

# After (keep the correct version):
bucket_name = "resume-parser-bucket"

# 5. Mark as resolved
git add filename.py

# 6. Complete the merge
git commit -m "fix: Resolve merge conflict in filename.py"

# 7. Push
git push origin feature/your-feature-name
```

### Preventing Conflicts

1. **Pull frequently** - Get latest changes often
2. **Communicate** - Tell team what files you're editing
3. **Small commits** - Easier to merge
4. **Coordinate** - Don't edit same files simultaneously

### Getting Help with Conflicts

If stuck:
```
Post in Google Chat:
🆘 Need help with merge conflict
File: filename.py
Branch: feature/my-feature
Can someone help me resolve this?
```

---

## Best Practices

### Do's ✅

1. **Commit Often**
   ```bash
   # Every 30-60 minutes of work
   git add .
   git commit -m "feat: Add skill extraction logic"
   ```

2. **Write Clear Commit Messages**
   ```bash
   ✅ "feat: Add Textract integration for PDF parsing"
   ❌ "updates"
   ```

3. **Pull Before Push**
   ```bash
   git pull origin develop
   git push origin feature/your-branch
   ```

4. **Keep Branches Updated**
   ```bash
   # Daily: merge develop into your branch
   git checkout feature/your-branch
   git merge develop
   ```

5. **Test Before Pushing**
   ```bash
   # Run your code
   # Make sure it works
   # Then push
   ```

6. **Use .gitignore**
   ```bash
   # Never commit:
   # - Credentials
   # - API keys
   # - Large files
   # - IDE settings
   ```

### Don'ts ❌

1. **Don't Commit Secrets**
   ```python
   ❌ AWS_KEY = "AKIAIOSFODNN7EXAMPLE"
   ✅ AWS_KEY = os.environ.get('AWS_ACCESS_KEY_ID')
   ```

2. **Don't Push Directly to Main**
   ```bash
   ❌ git push origin main
   ✅ Create PR from feature branch
   ```

3. **Don't Commit Large Files**
   ```bash
   # Files > 50MB
   # Use Git LFS or store in S3
   ```

4. **Don't Force Push to Shared Branches**
   ```bash
   ❌ git push --force origin develop
   ✅ git push origin develop
   ```

5. **Don't Leave Branches Unmerged**
   ```bash
   # Merge or delete old branches
   git branch -d old-feature-branch
   ```

---

## Common Git Commands Reference

### Basic Commands
```bash
# Check status
git status

# See changes
git diff

# Add files
git add filename.py
git add .  # Add all

# Commit
git commit -m "message"

# Push
git push origin branch-name

# Pull
git pull origin branch-name
```

### Branch Commands
```bash
# List branches
git branch
git branch -a  # Include remote

# Create branch
git checkout -b feature/new-feature

# Switch branch
git checkout branch-name

# Delete branch
git branch -d branch-name

# Delete remote branch
git push origin --delete branch-name
```

### Viewing History
```bash
# View commits
git log
git log --oneline
git log --graph --oneline --all

# View specific file history
git log filename.py

# View changes in commit
git show commit-hash
```

### Undoing Changes
```bash
# Discard changes in file
git checkout -- filename.py

# Unstage file
git reset HEAD filename.py

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Revert a commit (creates new commit)
git revert commit-hash
```

### Stashing (Temporary Save)
```bash
# Save work temporarily
git stash

# List stashes
git stash list

# Apply stashed changes
git stash apply

# Apply and remove stash
git stash pop
```

---

## Troubleshooting

### Issue: "Permission Denied"

**Cause:** Not added as collaborator

**Solution:**
1. Team Lead adds you as collaborator
2. Accept invitation email
3. Try again

### Issue: "Merge Conflict"

**Solution:** See [Handling Merge Conflicts](#handling-merge-conflicts)

### Issue: "Pushed Wrong Code"

**Solution:**
```bash
# If not pushed yet
git reset --hard HEAD~1

# If already pushed
git revert HEAD
git push origin branch-name
```

### Issue: "Accidentally Committed Secret"

**Solution:**
```bash
# 1. Remove from file
# 2. Commit the fix
git add filename.py
git commit -m "fix: Remove hardcoded credentials"

# 3. Remove from history (if needed)
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch filename.py" \
  --prune-empty --tag-name-filter cat -- --all

# 4. Force push (only if necessary)
git push --force origin branch-name

# 5. Rotate the exposed credentials immediately!
```

### Issue: "Can't Push - Behind Remote"

**Solution:**
```bash
# Pull first, then push
git pull origin branch-name
# Resolve any conflicts
git push origin branch-name
```

---

## GitHub Project Board (Optional)

### Setting Up Project Board

1. **Go to:** Repository → Projects → New Project
2. **Template:** Basic Kanban
3. **Columns:**
   - To Do
   - In Progress
   - In Review
   - Done

4. **Create Issues for Tasks:**
   ```
   Title: Implement S3 Upload Handler
   Description: Create Lambda function to handle resume uploads
   Assignee: Member 1
   Labels: feature, member-1
   ```

5. **Move Cards as You Work:**
   - Start work → Move to "In Progress"
   - Create PR → Move to "In Review"
   - PR merged → Move to "Done"

---

## Team Collaboration Tips

### Daily Workflow

**Morning (10 AM):**
```bash
git checkout develop
git pull origin develop
git checkout feature/your-branch
git merge develop
# Start coding
```

**Before Standup (6 PM):**
```bash
git add .
git commit -m "feat: Today's progress"
git push origin feature/your-branch
# Post update in chat
```

**After Standup (8 PM):**
```bash
# Review any PRs assigned to you
# Help teammates with issues
# Plan tomorrow's work
```

### Communication

**When Pushing Code:**
```
📤 Pushed to feature/upload-handler
- Added S3 upload logic
- Tested with 3 PDFs
- Ready for integration
```

**When Creating PR:**
```
🔍 PR Ready: Upload Handler
Link: [URL]
Please review by tomorrow morning
@TeamLead @Member2
```

**When Merging:**
```
✅ Merged: Upload Handler
Next: @Member2 can now start skill parser
Handoff doc: docs/handoff-notes/upload-handler.md
```

---

## Quick Reference Card

```bash
# Daily Start
git checkout develop && git pull origin develop
git checkout feature/my-branch && git merge develop

# During Work
git add . && git commit -m "feat: description"
git push origin feature/my-branch

# Create PR
# 1. Push final version
# 2. Go to GitHub
# 3. Create PR: develop ← feature/my-branch
# 4. Request review

# After PR Merged
git checkout develop && git pull origin develop
git branch -d feature/my-branch
```

---

## Resources

**Git Documentation:**
- https://git-scm.com/doc

**GitHub Guides:**
- https://guides.github.com/

**Interactive Git Tutorial:**
- https://learngitbranching.js.org/

**Git Cheat Sheet:**
- https://education.github.com/git-cheat-sheet-education.pdf

---

**Remember:**
- Commit often, push daily
- Pull before you push
- Write clear commit messages
- Review others' code
- Ask for help when stuck

**Happy coding! 🚀**
