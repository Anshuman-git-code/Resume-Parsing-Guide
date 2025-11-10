# Quick Reference Card

One-page reference for daily use. Print or keep open while working!

---

## 📞 Team Contacts

| Role | Name | Contact | Availability |
|------|------|---------|--------------|
| Team Lead | Anshuman | [Add] | [Add] |
| Member 2 | Shivam | [Add] | [Add] |
| Member 3 | Siddhant | [Add] | [Add] |
| Member 4 | Pavan | [Add] | [Add] |

---

## 🔗 Important Links

| Resource | URL |
|----------|-----|
| GitHub Repo | [Add after creation] |
| AWS Console | https://console.aws.amazon.com |
| Google Chat | https://mail.google.com/chat/u/0/#chat/space/AAQAGqGHbk0 |
| Google Meet | https://meet.google.com/csu-izou-wyj |
| Shared Docs | [Add] |

---

## 🗓️ Daily Schedule

**10 AM:** Morning check-in (Google Chat)  
**Throughout Day:** Code, commit, communicate  
**6 PM:** Push code + post update  
**7 PM:** Daily standup (Google Meet - 15 mins)  
**8 PM:** Continue work or help teammates

---

## 💻 Daily Git Commands

```bash
# Morning routine
git checkout develop
git pull origin develop
git checkout feature/your-branch
git merge develop

# During work (every 30-60 mins)
git add .
git commit -m "feat: description"
git push origin feature/your-branch

# Evening routine
git add .
git commit -m "feat: end of day progress"
git push origin feature/your-branch
```

---

## ☁️ AWS Resources

| Resource | Name |
|----------|------|
| Region | us-east-1 |
| S3 Bucket | resume-parser-bucket-main |
| Candidates Table | resume-parser-candidates |
| Jobs Table | resume-parser-jobs |
| Matches Table | resume-parser-matches |
| Lambda Prefix | resume-parser-* |

---

## 🔑 Quick AWS Commands

```bash
# Check who you are
aws sts get-caller-identity

# List S3 buckets
aws s3 ls

# Upload to S3
aws s3 cp file.pdf s3://bucket-name/

# List Lambda functions
aws lambda list-functions

# View logs
aws logs tail /aws/lambda/function-name --follow
```

---

## 📋 Commit Message Format

```
feat: Add new feature
fix: Fix bug
docs: Update documentation
test: Add tests
refactor: Refactor code
style: Format code
chore: Maintenance
```

---

## 🚨 When You're Stuck (>30 mins)

**Post in Google Chat:**
```
🚨 BLOCKER: [Brief description]
Component: [Your component]
Error: [Error message]
What I tried: [List]
Need help from: @[Person]
```

---

## 📊 Progress Tracking

### Week Overview
- **Day 1 (Nov 10):** Setup ✓
- **Day 2 (Nov 11):** Foundation
- **Day 3 (Nov 12):** Core Dev
- **Day 4 (Nov 13):** Integration 1
- **Day 5 (Nov 14):** Integration 2
- **Day 6 (Nov 15):** Polish
- **Day 7 (Nov 16):** Documentation
- **Day 8 (Nov 17):** Submission

### My Tasks Today
- [ ] Task 1
- [ ] Task 2
- [ ] Task 3

---

## 🎯 Role Dependencies

```
Member 1 (Upload/Textract)
    ↓ (provides extracted text)
Member 2 (Skill Parser)
    ↓ (provides parsed data)
Member 3 (Job Matcher)
    ↓ (provides match results)
Member 4 (Dashboard)
```

---

## 📝 Handoff Checklist

When completing your component:
- [ ] Code pushed to GitHub
- [ ] Sample outputs created (5+ examples)
- [ ] Handoff document written
- [ ] Next member notified in chat
- [ ] Quick sync call scheduled
- [ ] Available for questions

---

## 🐛 Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| Access Denied | Contact team lead for IAM permissions |
| Merge Conflict | See GITHUB_WORKFLOW_GUIDE.md |
| API Not Working | Check CORS, test with Postman |
| Lambda Timeout | Increase timeout to 5 mins |
| Textract Slow | Use smaller test PDFs |

---

## 💰 Cost Monitoring

**Expected Total:** $2.50 - $5.00  
**Alerts Set At:** $5, $10, $20  
**Team Lead Checks:** Daily

**If costs spike:** Alert team lead immediately!

---

## ✅ Daily Checklist

**Morning:**
- [ ] Pull latest code
- [ ] Post daily goal in chat
- [ ] Review teammate updates

**During Day:**
- [ ] Commit every 30-60 mins
- [ ] Ask questions if stuck >30 mins
- [ ] Help teammates when asked

**Evening:**
- [ ] Push all code
- [ ] Post progress update
- [ ] Attend standup (7 PM)

---

## 📚 Documentation Files

| File | Purpose |
|------|---------|
| README.md | Project overview & quick start |
| PROJECT_GUIDE.md | Complete collaboration guide |
| ROLE_ASSIGNMENTS.md | Detailed role specifications |
| AWS_SETUP_GUIDE.md | AWS account setup |
| GITHUB_WORKFLOW_GUIDE.md | Git collaboration |
| CODE_TEMPLATES.md | Starter code |
| DAILY_CHECKLIST.md | Day-by-day tasks |
| QUICK_REFERENCE.md | This file! |

---

## 🎤 Standup Format (3 mins each)

1. **What I completed today**
2. **What I'm working on next**
3. **Any blockers or help needed**
4. **What I need from teammates**

---

## 🔐 Security Reminders

**NEVER commit:**
- ❌ AWS credentials
- ❌ API keys
- ❌ Passwords
- ❌ .env files

**ALWAYS use:**
- ✅ Environment variables
- ✅ IAM roles for Lambda
- ✅ .gitignore

---

## 📦 File Structure

```
backend/
  lambda-functions/
    upload-handler/
    textract-processor/
    skill-parser/
    job-matcher/
frontend/
  dashboard/
docs/
  handoff-notes/
sample-data/
  resumes/
  outputs/
```

---

## 🎯 Success Metrics

- ✅ Process 10+ resumes
- ✅ >70% skill extraction accuracy
- ✅ Reasonable match scores
- ✅ Functional dashboard
- ✅ No critical bugs
- ✅ Complete documentation
- ✅ On-time delivery (Nov 17)

---

## 💡 Tips for Success

1. **Communicate early and often**
2. **Ask for help after 30 mins stuck**
3. **Commit small and frequently**
4. **Test before pushing**
5. **Document as you go**
6. **Help your teammates**
7. **Stay positive and supportive**

---

## 🎉 Motivation

**Team Mantra:**  
"Progress over perfection. Together we build."

**Remember:**
- This is a learning project
- Mistakes are okay
- You have a great team
- You have 7 days
- You've got this! 🚀

---

## 📞 Emergency Escalation

```
Level 1: Try to solve (30 mins)
    ↓
Level 2: Ask in Google Chat (1 hour)
    ↓
Level 3: Tag team lead (immediate)
    ↓
Level 4: Emergency team call (ASAP)
```

---

## 🏆 Final Deliverables

1. ✅ Working system (upload → parse → match → display)
2. ✅ GitHub repository with all code
3. ✅ Sample outputs (10+ resumes)
4. ✅ Documentation (setup, API, architecture)
5. ✅ Dashboard (deployed and accessible)
6. ✅ Presentation (15-20 mins)
7. ✅ Report (limitations & enhancements)

---

**Print this page and keep it handy! 📄**

**Last Updated:** November 10, 2025
