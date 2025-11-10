# Resume Parser & Skill Matcher - Team Collaboration Guide

**Project Duration:** Nov 10 - Nov 17, 2025 (7 days)  
**Team Size:** 4 members (1 Team Lead + 3 Developers)  
**Daily Standup:** 7 PM Google Meet

---

## Table of Contents
1. [Team Setup & Communication](#team-setup--communication)
2. [AWS Account Setup](#aws-account-setup)
3. [GitHub Repository Setup](#github-repository-setup)
4. [Role Distribution & Dependencies](#role-distribution--dependencies)
5. [Day-by-Day Timeline](#day-by-day-timeline)
6. [Handoff Protocols](#handoff-protocols)
7. [Issue Resolution Process](#issue-resolution-process)
8. [Testing & Integration](#testing--integration)

---

## Team Setup & Communication

### Communication Channels
- **Google Chat Group:** Daily updates, quick questions, blockers
- **Google Meet (7 PM Daily):** Standup, code reviews, integration discussions
- **GitHub Issues:** Track bugs, features, and tasks
- **Shared Google Doc:** Meeting notes, decisions, API documentation

### Daily Standup Format (15 mins max)
Each member shares:
1. What I completed today
2. What I'm working on next
3. Any blockers or help needed
4. What information/files I need from teammates

### Communication Best Practices
- **Morning Check-in (by 10 AM):** Post your daily goal in Google Chat
- **Blocker Alert:** If stuck for >30 mins, post in chat immediately
- **Code Push Notification:** Notify team when pushing important changes
- **Evening Update (by 6 PM):** Share progress before the standup
- **Weekend Check:** Saturday quick sync to ensure Sunday work is coordinated

---

## AWS Account Setup

### Option 1: Shared AWS Account (Recommended for Learning)
**Setup Steps:**
1. **Team Lead creates AWS Free Tier account**
   - Use a dedicated email (e.g., team.resumeparser@gmail.com)
   - Enable MFA for security
   - Set billing alerts at $5, $10, $20

2. **Create IAM Users for Each Team Member**
   ```
   Team Lead (Anshuman): Administrator access
   Member 2: S3, Lambda, Textract, Comprehend access
   Member 3: DynamoDB, Lambda access
   Member 4: Lambda, API Gateway, CloudFront access
   ```

3. **IAM User Creation Process:**
   - Go to IAM Console → Users → Add User
   - Enable "Programmatic access" + "AWS Management Console access"
   - Attach policies based on role
   - Download credentials CSV (share securely via Google Drive)
   - Each member sets up AWS CLI with their credentials

4. **Shared Resources Naming Convention:**
   ```
   S3 Bucket: resume-parser-team-[yourteamname]
   DynamoDB Tables: resume-parser-[table-name]
   Lambda Functions: resume-parser-[function-name]
   ```

### AWS CLI Setup (Each Member)
```bash
# Install AWS CLI
# macOS: brew install awscli
# Windows: Download from AWS website

# Configure credentials
aws configure
# Enter your IAM Access Key ID
# Enter your IAM Secret Access Key
# Default region: us-east-1
# Default output format: json
```

### Cost Management
- **Daily Cost Check:** Team lead monitors AWS billing dashboard
- **Resource Cleanup:** Delete test resources after use
- **Free Tier Limits:**
  - S3: 5GB storage, 20,000 GET requests
  - Lambda: 1M free requests/month
  - Textract: 1,000 pages/month
  - DynamoDB: 25GB storage

---

## GitHub Repository Setup

### Repository Structure
```
resume-parser-project/
├── .github/
│   └── workflows/          # CI/CD (optional)
├── backend/
│   ├── lambda-functions/
│   │   ├── upload-handler/
│   │   ├── textract-processor/
│   │   ├── skill-parser/
│   │   └── job-matcher/
│   ├── utils/
│   └── tests/
├── frontend/
│   ├── dashboard/
│   └── assets/
├── infrastructure/
│   ├── cloudformation/     # AWS resource templates
│   └── iam-policies/
├── docs/
│   ├── api-documentation.md
│   ├── architecture-diagram.png
│   └── handoff-notes/
├── sample-data/
│   ├── resumes/
│   └── job-descriptions/
├── README.md
└── requirements.txt
```

### GitHub Setup Steps

1. **Team Lead Creates Repository**
   ```bash
   # Create repo on GitHub: resume-parser-skill-matcher
   # Initialize locally
   git clone https://github.com/[username]/resume-parser-skill-matcher.git
   cd resume-parser-skill-matcher
   
   # Create initial structure
   mkdir -p backend/lambda-functions frontend/dashboard infrastructure docs sample-data
   touch README.md
   git add .
   git commit -m "Initial project structure"
   git push origin main
   ```

2. **Add Team Members as Collaborators**
   - Settings → Collaborators → Add people
   - Grant "Write" access to all members

3. **Branch Strategy**
   ```
   main          → Production-ready code (protected)
   develop       → Integration branch
   feature/[name] → Individual feature branches
   ```

4. **Git Workflow for Each Member**
   ```bash
   # Start working on a feature
   git checkout develop
   git pull origin develop
   git checkout -b feature/your-feature-name
   
   # Make changes and commit frequently
   git add .
   git commit -m "Clear description of changes"
   
   # Push your branch
   git push origin feature/your-feature-name
   
   # Create Pull Request on GitHub
   # Request review from team lead or another member
   
   # After approval, merge to develop
   ```

5. **Commit Message Convention**
   ```
   feat: Add resume upload to S3
   fix: Resolve Textract timeout issue
   docs: Update API documentation
   test: Add unit tests for skill parser
   refactor: Optimize matching algorithm
   ```

---

## Role Distribution & Dependencies

### Member 1: Resume Upload & Text Extraction (Foundation)
**Responsibilities:**
- S3 bucket setup and configuration
- Lambda function for resume upload handling
- AWS Textract integration
- Output: Raw extracted text from PDFs

**Key Deliverables:**
1. S3 bucket with proper IAM policies
2. Lambda function: `upload-handler` (handles file upload)
3. Lambda function: `textract-processor` (extracts text)
4. Sample output JSON with extracted text
5. API endpoint for resume upload

**Handoff Package:**
```json
{
  "s3_bucket_name": "resume-parser-team-xyz",
  "textract_output_format": {
    "resume_id": "uuid",
    "candidate_name": "extracted_name",
    "raw_text": "full_extracted_text",
    "extraction_timestamp": "ISO_timestamp"
  },
  "api_endpoint": "https://[api-gateway-url]/upload",
  "sample_files": "sample-data/textract-outputs/"
}
```

---

### Member 2: Skill Parsing & NLP Logic (Depends on Member 1)
**Responsibilities:**
- Parse extracted text to identify skills, experience, education
- Use Amazon Comprehend or custom NLP
- Structure data for storage
- Output: Structured candidate profile

**Key Deliverables:**
1. Lambda function: `skill-parser`
2. Skill extraction logic (keywords, job titles, years of experience)
3. Education parser
4. Structured JSON output format
5. List of recognized skills/technologies

**Handoff Package:**
```json
{
  "parsed_resume_format": {
    "resume_id": "uuid",
    "candidate_name": "John Doe",
    "email": "john@example.com",
    "phone": "+1234567890",
    "skills": ["Python", "AWS", "Machine Learning"],
    "experience": [
      {
        "title": "Software Engineer",
        "company": "ABC Corp",
        "duration_years": 2.5
      }
    ],
    "education": [
      {
        "degree": "B.Tech",
        "field": "Computer Science",
        "institution": "XYZ University"
      }
    ],
    "total_experience_years": 3.5
  },
  "skills_database": "docs/recognized-skills.json",
  "sample_outputs": "sample-data/parsed-resumes/"
}
```

---

### Member 3: Job Matching Algorithm & DynamoDB (Depends on Member 2)
**Responsibilities:**
- DynamoDB table design for job descriptions and candidates
- Implement matching algorithm (Jaccard/Cosine similarity)
- Lambda function for matching logic
- Output: Ranked job matches for candidates

**Key Deliverables:**
1. DynamoDB tables: `candidates`, `job_descriptions`, `matches`
2. Lambda function: `job-matcher`
3. Matching algorithm implementation
4. API endpoint to get matches for a candidate
5. Sample job descriptions in DynamoDB

**Handoff Package:**
```json
{
  "dynamodb_tables": {
    "candidates": "resume-parser-candidates",
    "jobs": "resume-parser-jobs",
    "matches": "resume-parser-matches"
  },
  "match_output_format": {
    "candidate_id": "uuid",
    "matches": [
      {
        "job_id": "job_uuid",
        "job_title": "Senior Python Developer",
        "match_score": 0.85,
        "matched_skills": ["Python", "AWS", "Docker"],
        "missing_skills": ["Kubernetes"]
      }
    ]
  },
  "api_endpoints": {
    "get_matches": "GET /matches/{candidate_id}",
    "add_job": "POST /jobs"
  },
  "sample_data": "sample-data/job-descriptions.json"
}
```

---

### Member 4: Dashboard & API Integration (Depends on Member 3)
**Responsibilities:**
- Build recruiter dashboard (simple HTML/CSS/JS or React)
- Integrate all backend APIs
- Display parsed resumes and matches
- Download candidate reports feature

**Key Deliverables:**
1. Recruiter dashboard UI
2. API Gateway configuration
3. Frontend-backend integration
4. PDF report generation
5. Deployment (S3 + CloudFront or local)

**Handoff Package:**
```
Dashboard Features:
- Upload resume interface
- View all candidates list
- View candidate details (parsed info)
- View job matches with scores
- Download candidate report (PDF/JSON)
- Add new job descriptions

API Integration Documentation:
- All endpoint URLs
- Request/response formats
- Error handling
- Sample API calls (Postman collection)
```

---

### Team Lead (Anshuman): Coordination & Integration
**Responsibilities:**
- AWS account setup and IAM management
- Architecture decisions and code reviews
- Integration testing
- Documentation compilation
- Final presentation preparation
- SQS setup for scaling (if time permits)

---

## Day-by-Day Timeline

### Day 1 (Nov 10 - Sunday) - Setup & Planning
**All Members:**
- [ ] Join GitHub repository
- [ ] Set up AWS CLI with IAM credentials
- [ ] Clone repository and create your feature branch
- [ ] Review architecture and your role

**Team Lead:**
- [ ] Create AWS account and IAM users
- [ ] Set up GitHub repository
- [ ] Create shared Google Doc for documentation
- [ ] Prepare architecture diagram

**Evening Standup (7 PM):**
- Confirm everyone has access
- Review timeline and dependencies
- Clarify any questions

---

### Day 2 (Nov 11 - Monday) - Foundation Development
**Member 1:**
- [ ] Create S3 bucket with versioning enabled
- [ ] Write upload-handler Lambda (Python/Node.js)
- [ ] Test file upload to S3
- [ ] Integrate Textract API
- [ ] Test with 2-3 sample PDFs
- [ ] Document S3 bucket name and output format

**Member 2:**
- [ ] Research Amazon Comprehend API
- [ ] Design skill extraction logic
- [ ] Create list of common skills/technologies
- [ ] Set up development environment

**Member 3:**
- [ ] Design DynamoDB table schemas
- [ ] Create sample job descriptions (5-10)
- [ ] Research similarity algorithms

**Member 4:**
- [ ] Design dashboard wireframes
- [ ] Set up frontend project structure
- [ ] Research API Gateway setup

**Evening Standup:**
- Member 1 shares S3 bucket name
- Discuss any AWS access issues
- Review progress and blockers

---

### Day 3 (Nov 12 - Tuesday) - Core Development
**Member 1:**
- [ ] Complete Textract integration
- [ ] Create sample output files (3-5 examples)
- [ ] Push code to GitHub
- [ ] **HANDOFF:** Share S3 bucket name, output format, sample files with Member 2

**Member 2:**
- [ ] Receive handoff from Member 1
- [ ] Implement skill parser Lambda
- [ ] Test with Member 1's sample outputs
- [ ] Parse skills, experience, education
- [ ] Create structured JSON output

**Member 3:**
- [ ] Create DynamoDB tables
- [ ] Insert sample job descriptions
- [ ] Start implementing matching algorithm

**Member 4:**
- [ ] Build basic dashboard layout
- [ ] Create upload form UI
- [ ] Set up API Gateway (basic config)

**Evening Standup:**
- Member 1 demos Textract output
- Member 2 shares parsing progress
- Address integration questions

---

### Day 4 (Nov 13 - Wednesday) - Integration Phase 1
**Member 1:**
- [ ] Help Member 2 with any Textract issues
- [ ] Add error handling and logging
- [ ] Write API documentation

**Member 2:**
- [ ] Complete skill parser
- [ ] Test with 5+ different resumes
- [ ] Generate sample parsed outputs
- [ ] Push code to GitHub
- [ ] **HANDOFF:** Share parsed resume format, sample outputs with Member 3

**Member 3:**
- [ ] Receive handoff from Member 2
- [ ] Complete matching algorithm
- [ ] Test matching with sample data
- [ ] Store results in DynamoDB

**Member 4:**
- [ ] Integrate upload API with dashboard
- [ ] Test file upload flow
- [ ] Build candidate list view

**Evening Standup:**
- Demo end-to-end: Upload → Extract → Parse
- Member 3 shares matching algorithm approach
- Plan integration for next day

---

### Day 5 (Nov 14 - Thursday) - Integration Phase 2
**Member 1 & 2:**
- [ ] Integration testing: Upload → Extract → Parse pipeline
- [ ] Fix any bugs
- [ ] Optimize Lambda performance

**Member 3:**
- [ ] Complete job matcher Lambda
- [ ] Create API endpoints for matches
- [ ] Test with various scenarios
- [ ] Push code to GitHub
- [ ] **HANDOFF:** Share API endpoints, match format with Member 4

**Member 4:**
- [ ] Receive handoff from Member 3
- [ ] Integrate matching API
- [ ] Build match results display
- [ ] Implement candidate report download

**Team Lead:**
- [ ] Set up SQS for resume processing queue
- [ ] Integration testing across all components
- [ ] Start compiling documentation

**Evening Standup:**
- Demo full pipeline: Upload → Parse → Match
- Identify remaining bugs
- Plan final features

---

### Day 6 (Nov 15 - Friday) - Polish & Testing
**All Members:**
- [ ] Bug fixes from integration testing
- [ ] Add error handling
- [ ] Improve logging
- [ ] Code cleanup and comments

**Member 4:**
- [ ] Polish dashboard UI
- [ ] Add loading states and error messages
- [ ] Test all user flows
- [ ] Deploy dashboard

**Team Lead:**
- [ ] End-to-end testing with fresh data
- [ ] Security review (IAM policies, S3 permissions)
- [ ] Performance testing
- [ ] Start presentation slides

**Evening Standup:**
- Full system demo
- Create punch list of remaining tasks
- Assign final fixes

---

### Day 7 (Nov 16 - Saturday) - Documentation & Presentation
**All Members:**
- [ ] Write individual component documentation
- [ ] Record demo videos of your component
- [ ] Prepare to explain your code

**Team Lead:**
- [ ] Compile final documentation
- [ ] Create architecture diagram
- [ ] Write limitations and enhancements section
- [ ] Prepare presentation
- [ ] Create demo script

**Deliverables to Complete:**
1. README with setup instructions
2. Architecture diagram
3. API documentation
4. Sample parsed outputs
5. Matching logic explanation
6. Limitations and enhancement ideas
7. Presentation slides

**Evening Standup:**
- Presentation dry run
- Final review of all deliverables
- Assign presentation sections

---

### Day 8 (Nov 17 - Sunday) - Submission Day
**Morning (Before 12 PM):**
- [ ] Final testing
- [ ] Fix any critical bugs
- [ ] Update README
- [ ] Tag release on GitHub (v1.0)

**Afternoon:**
- [ ] Presentation practice
- [ ] Prepare for Q&A
- [ ] Submit project

---

## Handoff Protocols

### When to Handoff
- Your component is 80% complete and testable
- You have sample input/output files
- Documentation is written
- Code is pushed to GitHub

### Handoff Checklist
1. **Code Push**
   ```bash
   git add .
   git commit -m "feat: Complete [component name]"
   git push origin feature/your-branch
   ```

2. **Create Handoff Document** (in `docs/handoff-notes/`)
   ```markdown
   # Handoff: [Your Component] → [Next Member]
   Date: [Date]
   From: [Your Name]
   To: [Next Member Name]
   
   ## What's Complete
   - Feature 1
   - Feature 2
   
   ## How to Use
   - Step-by-step instructions
   - Code examples
   
   ## Sample Files
   - Location of sample inputs
   - Location of sample outputs
   
   ## Known Issues
   - Issue 1 (workaround: ...)
   
   ## Configuration Needed
   - Environment variables
   - AWS resource names
   
   ## Contact
   - Available for questions: [Your availability]
   ```

3. **Notify in Google Chat**
   ```
   @NextMember Handoff ready! 🚀
   - Code pushed to: feature/your-branch
   - Handoff doc: docs/handoff-notes/your-component.md
   - Sample files: sample-data/your-folder/
   - Available for questions until 10 PM
   ```

4. **Quick Sync Call** (15 mins)
   - Walk through your code
   - Show sample outputs
   - Answer questions
   - Share any gotchas

---

## Issue Resolution Process

### Types of Issues

#### 1. Technical Blockers
**Examples:** AWS API errors, Lambda timeouts, parsing failures

**Resolution Process:**
1. **Try for 30 mins** - Google, AWS docs, Stack Overflow
2. **Post in Google Chat** with:
   ```
   🚨 BLOCKER: [Brief description]
   Component: [Your component]
   Error: [Error message/screenshot]
   What I tried: [List attempts]
   Need help from: [Specific person or anyone]
   ```
3. **Team Lead triages** - Assigns helper or schedules quick call
4. **Pair programming session** - Screen share and debug together
5. **Document solution** - Add to docs/troubleshooting.md

#### 2. Integration Issues
**Examples:** Data format mismatch, API not working

**Resolution Process:**
1. **Check handoff documentation** - Verify you're using correct format
2. **Test with sample files** - Use the provided examples first
3. **Contact previous member** - They know their code best
4. **Joint debugging session** - Both members on call
5. **Update handoff docs** - Clarify any confusion

#### 3. AWS Access/Permission Issues
**Examples:** S3 access denied, Lambda can't invoke Textract

**Resolution Process:**
1. **Check IAM policies** - Verify you have necessary permissions
2. **Contact Team Lead** - They manage IAM
3. **Team Lead updates policies** - Grants required access
4. **Test again** - Confirm issue resolved
5. **Document permissions** - Add to setup guide

#### 4. Merge Conflicts
**Resolution Process:**
```bash
# Update your branch with latest develop
git checkout develop
git pull origin develop
git checkout feature/your-branch
git merge develop

# If conflicts occur
# 1. Open conflicted files
# 2. Resolve conflicts (keep both changes if possible)
# 3. Test that code still works
git add .
git commit -m "fix: Resolve merge conflicts"
git push origin feature/your-branch
```

### Escalation Path
```
Level 1: Self-debug (30 mins)
   ↓
Level 2: Google Chat help (1 hour)
   ↓
Level 3: Team Lead involvement (immediate)
   ↓
Level 4: Team call to solve together (schedule ASAP)
```

### Common Issues & Solutions

#### Issue: Textract taking too long
**Solution:** 
- Use smaller test PDFs initially
- Implement async processing with SQS
- Increase Lambda timeout to 5 minutes

#### Issue: Skill parsing not accurate
**Solution:**
- Use Amazon Comprehend for entity recognition
- Maintain a skills dictionary
- Implement fuzzy matching for skill names

#### Issue: DynamoDB queries slow
**Solution:**
- Add indexes on frequently queried fields
- Use batch operations
- Implement caching for job descriptions

#### Issue: Dashboard not connecting to API
**Solution:**
- Check CORS settings on API Gateway
- Verify API endpoint URLs
- Check browser console for errors
- Test API with Postman first

---

## Testing & Integration

### Unit Testing (Each Member)
Test your component in isolation:

```python
# Example: Test skill parser
def test_skill_extraction():
    sample_text = "Experienced in Python, AWS, and Docker"
    result = extract_skills(sample_text)
    assert "Python" in result["skills"]
    assert "AWS" in result["skills"]
    assert len(result["skills"]) >= 3
```

### Integration Testing (Team Lead + All)

**Test Scenarios:**
1. **Happy Path:** Upload resume → Extract → Parse → Match → Display
2. **Invalid PDF:** Upload non-PDF file
3. **Resume with no skills:** Handle gracefully
4. **No matching jobs:** Show appropriate message
5. **Large resume:** Test with 10+ page PDF

**Testing Schedule:**
- Day 4 Evening: Test Upload → Parse
- Day 5 Evening: Test full pipeline
- Day 6: Comprehensive testing

### Testing Checklist
- [ ] Upload 5 different resume formats
- [ ] Test with resumes in different layouts
- [ ] Verify all skills are extracted
- [ ] Check matching scores are reasonable
- [ ] Test dashboard on different browsers
- [ ] Verify downloads work
- [ ] Test error scenarios
- [ ] Check AWS costs (should be under $5)

---

## Best Practices

### Code Quality
- **Comment your code:** Explain complex logic
- **Use meaningful variable names:** `candidate_skills` not `cs`
- **Error handling:** Try-catch blocks with helpful messages
- **Logging:** Use CloudWatch logs for debugging
- **Keep functions small:** One function = one purpose

### AWS Best Practices
- **Tag all resources:** `Project: ResumeParser, Owner: [YourName]`
- **Use environment variables:** Don't hardcode bucket names
- **Least privilege IAM:** Only grant necessary permissions
- **Enable CloudWatch logs:** For all Lambdas
- **Set Lambda timeouts:** Appropriate for each function

### Git Best Practices
- **Commit often:** Small, logical commits
- **Pull before push:** Avoid conflicts
- **Write clear commit messages:** Others need to understand
- **Don't commit secrets:** Use environment variables
- **Review before merging:** At least one team member reviews

### Communication Best Practices
- **Be responsive:** Check chat every 2 hours
- **Ask questions early:** Don't wait until stuck
- **Share progress:** Even small wins
- **Be specific:** "Textract Lambda done" vs "Made progress"
- **Help others:** You'll need help too

---

## Emergency Protocols

### If Someone Gets Sick/Unavailable
1. **Immediate notification** in Google Chat
2. **Team Lead reassigns** critical tasks
3. **Pair up:** Two members handle that component
4. **Simplify scope:** Cut non-essential features

### If Running Behind Schedule
1. **Daily standup assessment:** Are we on track?
2. **Prioritize core features:**
   - Must have: Upload, Extract, Parse, Basic Match, Simple Dashboard
   - Nice to have: Advanced matching, SQS, Beautiful UI, Reports
3. **Parallel work:** Multiple members on critical path
4. **Extended hours:** Add morning sync if needed

### If AWS Costs Spike
1. **Immediate alert** to Team Lead
2. **Check billing dashboard:** Identify culprit service
3. **Stop expensive resources:** Delete or pause
4. **Use smaller test data:** Fewer/smaller PDFs
5. **Optimize:** Reduce Lambda memory, use smaller instances

---

## Final Deliverables Checklist

### Code Repository
- [ ] All code pushed to GitHub
- [ ] README with setup instructions
- [ ] Requirements.txt / package.json
- [ ] Sample data included
- [ ] No secrets in code

### Documentation
- [ ] Architecture diagram
- [ ] API documentation
- [ ] Setup guide for running locally
- [ ] AWS resources list
- [ ] Limitations and enhancements document

### Working System
- [ ] Resume upload works
- [ ] Text extraction works
- [ ] Skill parsing works
- [ ] Job matching works
- [ ] Dashboard displays results
- [ ] Can download reports

### Presentation
- [ ] Slides prepared (15-20 mins)
- [ ] Demo video recorded (backup)
- [ ] Each member knows their part
- [ ] Q&A preparation
- [ ] Limitations discussed honestly

---

## Success Metrics

### Technical Success
- ✅ System processes at least 10 different resumes
- ✅ Extracts skills with >70% accuracy
- ✅ Matching algorithm produces reasonable results
- ✅ Dashboard is functional and user-friendly
- ✅ No critical bugs in demo

### Team Success
- ✅ All members contributed meaningfully
- ✅ Code is integrated and working together
- ✅ Documentation is complete
- ✅ Delivered on time
- ✅ Learned AWS services hands-on

### Learning Outcomes
- ✅ Understand serverless architecture
- ✅ Experience with AWS services (S3, Lambda, Textract, DynamoDB)
- ✅ Team collaboration on cloud project
- ✅ Git workflow in team setting
- ✅ API design and integration

---

## Motivational Notes

**Remember:**
- This is a learning project - mistakes are okay!
- Ask for help early and often
- Celebrate small wins together
- Focus on working code over perfect code
- You have 7 days - pace yourselves
- Communication is more important than code

**Daily Mantra:**
"Progress over perfection. Together we build."

---

## Quick Reference

### Important Links
- GitHub Repo: [Add URL after creation]
- AWS Console: https://console.aws.amazon.com
- Google Chat: [Add link]
- Shared Docs: [Add link]
- Meeting Link: [Add Google Meet link]

### Emergency Contacts
- Team Lead (Anshuman): [Phone/Email]
- Member 2: [Phone/Email]
- Member 3: [Phone/Email]
- Member 4: [Phone/Email]

### Key AWS Resources
- S3 Bucket: `resume-parser-team-[name]`
- Region: `us-east-1`
- DynamoDB Tables: `resume-parser-*`
- Lambda Functions: `resume-parser-*`

---

**Good luck, team! You've got this! 🚀**
