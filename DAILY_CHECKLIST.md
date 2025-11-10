# Daily Progress Checklist

Track your team's progress day by day. Check off items as you complete them.

---

## Day 1: November 10, 2025 (Sunday) - Setup Day

### Team Lead Tasks
- [ ] Create AWS Free Tier account
- [ ] Enable MFA on root account
- [ ] Set up billing alerts ($5, $10, $20)
- [ ] Create 4 IAM users with appropriate permissions
- [ ] Download and securely share credentials
- [ ] Create GitHub repository
- [ ] Add team members as collaborators
- [ ] Create initial project structure
- [ ] Push initial commit to main and develop branches
- [ ] Create shared Google Doc for documentation
- [ ] Set up Google Meet recurring link
- [ ] Create architecture diagram (draft)

### All Team Members
- [ ] Accept GitHub invitation
- [ ] Receive AWS credentials
- [ ] Install AWS CLI
- [ ] Configure AWS CLI with your credentials
- [ ] Test AWS access: `aws sts get-caller-identity`
- [ ] Clone GitHub repository
- [ ] Checkout develop branch
- [ ] Read PROJECT_GUIDE.md
- [ ] Read your section in ROLE_ASSIGNMENTS.md
- [ ] Review CODE_TEMPLATES.md for your component
- [ ] Install required tools (Python, Node.js, etc.)
- [ ] Join Google Chat group
- [ ] Attend 7 PM standup meeting

### Evening Standup (7 PM)
- [ ] Confirm all members have AWS access
- [ ] Confirm all members have GitHub access
- [ ] Review project timeline
- [ ] Clarify roles and dependencies
- [ ] Answer questions
- [ ] Set goals for Day 2

---

## Day 2: November 11, 2025 (Monday) - Foundation

### Member 1: Upload & Extraction
- [ ] Create S3 bucket: `resume-parser-bucket-main`
- [ ] Configure S3 bucket (versioning, CORS)
- [ ] Create upload-handler Lambda function
- [ ] Test file upload to S3
- [ ] Integrate AWS Textract API
- [ ] Test Textract with 2-3 sample PDFs
- [ ] Create sample output JSON files
- [ ] Push code to feature/upload-handler branch
- [ ] Document S3 bucket name and output format

### Member 2: Skill Parser Setup
- [ ] Research Amazon Comprehend API
- [ ] Design skill extraction logic approach
- [ ] Create comprehensive skills database (JSON)
- [ ] Set up Lambda development environment
- [ ] Review Member 1's expected output format
- [ ] Create feature/skill-parser branch

### Member 3: Database Design
- [ ] Design DynamoDB table schemas (candidates, jobs, matches)
- [ ] Create sample job descriptions (5-10 jobs)
- [ ] Research Jaccard vs Cosine similarity
- [ ] Draft matching algorithm pseudocode
- [ ] Create feature/job-matcher branch

### Member 4: Dashboard Planning
- [ ] Design dashboard wireframes (sketch/Figma)
- [ ] Set up frontend project structure
- [ ] Research API Gateway setup
- [ ] Plan dashboard pages and features
- [ ] Create feature/dashboard branch

### Team Lead
- [ ] Monitor AWS costs (should be ~$0-1)
- [ ] Review Member 1's progress
- [ ] Help resolve any blockers
- [ ] Update shared documentation

### Evening Standup (7 PM)
- [ ] Member 1 shares S3 bucket name
- [ ] Discuss any AWS access issues
- [ ] Review progress vs timeline
- [ ] Address blockers

---

## Day 3: November 12, 2025 (Tuesday) - Core Development

### Member 1: Complete Extraction
- [ ] Complete Textract integration
- [ ] Add error handling for failed extractions
- [ ] Create 5 sample output files with different resume formats
- [ ] Test with various PDF formats
- [ ] Add CloudWatch logging
- [ ] Push final code to GitHub
- [ ] Create handoff document in docs/handoff-notes/
- [ ] **HANDOFF to Member 2:** Share bucket name, output format, samples

### Member 2: Build Skill Parser
- [ ] Receive handoff from Member 1
- [ ] Implement skill parser Lambda function
- [ ] Test with Member 1's sample outputs
- [ ] Parse skills using keyword matching
- [ ] Extract experience and education
- [ ] Create structured JSON output
- [ ] Test with 5+ different resumes
- [ ] Push code to GitHub

### Member 3: Database & Algorithm
- [ ] Create DynamoDB tables (run create_tables.py)
- [ ] Insert sample job descriptions into DynamoDB
- [ ] Implement matching algorithm (Jaccard similarity)
- [ ] Test algorithm with mock data
- [ ] Document matching logic

### Member 4: Dashboard UI
- [ ] Build basic dashboard HTML structure
- [ ] Create CSS styling
- [ ] Build upload form UI
- [ ] Set up API Gateway (basic configuration)
- [ ] Test static dashboard locally

### Team Lead
- [ ] Review Member 1's code and handoff
- [ ] Verify S3 and Textract working
- [ ] Check AWS costs
- [ ] Help Member 2 with integration

### Evening Standup (7 PM)
- [ ] Member 1 demos Textract output
- [ ] Member 2 shares parsing progress
- [ ] Confirm handoff successful
- [ ] Address integration questions

---

## Day 4: November 13, 2025 (Wednesday) - Integration Phase 1

### Member 1: Support & Polish
- [ ] Help Member 2 with any Textract issues
- [ ] Add comprehensive error handling
- [ ] Write API documentation
- [ ] Optimize Lambda performance
- [ ] Create API Gateway endpoint for upload

### Member 2: Complete Parser
- [ ] Complete skill parser implementation
- [ ] Test with 10+ different resumes
- [ ] Generate sample parsed outputs
- [ ] Validate output format consistency
- [ ] Push final code to GitHub
- [ ] Create handoff document
- [ ] **HANDOFF to Member 3:** Share parsed format, samples, DynamoDB requirements

### Member 3: Build Matcher
- [ ] Receive handoff from Member 2
- [ ] Complete job matcher Lambda function
- [ ] Implement matching algorithm
- [ ] Test matching with sample data
- [ ] Store results in DynamoDB matches table
- [ ] Verify match scores are reasonable

### Member 4: API Integration Start
- [ ] Integrate upload API with dashboard
- [ ] Test file upload flow end-to-end
- [ ] Build candidates list view
- [ ] Add loading states and error handling
- [ ] Test with real uploaded resumes

### Team Lead
- [ ] Integration testing: Upload → Extract → Parse
- [ ] Review Member 2's code
- [ ] Help debug integration issues
- [ ] Update architecture diagram

### Evening Standup (7 PM)
- [ ] Demo: Upload → Extract → Parse pipeline
- [ ] Member 3 shares matching algorithm approach
- [ ] Plan integration for Day 5
- [ ] Identify any blockers

---

## Day 5: November 14, 2025 (Thursday) - Integration Phase 2

### Member 1 & 2: Pipeline Testing
- [ ] Integration testing: Upload → Extract → Parse
- [ ] Fix any bugs discovered
- [ ] Optimize Lambda performance (memory, timeout)
- [ ] Add comprehensive logging
- [ ] Document API endpoints

### Member 3: Complete Matching
- [ ] Complete job matcher Lambda
- [ ] Create API endpoints (GET /matches/{id})
- [ ] Test with various candidate-job combinations
- [ ] Verify match scores make sense
- [ ] Push final code to GitHub
- [ ] Create handoff document
- [ ] **HANDOFF to Member 4:** Share API endpoints, match format

### Member 4: Full Integration
- [ ] Receive handoff from Member 3
- [ ] Integrate matching API with dashboard
- [ ] Build match results display page
- [ ] Show match scores with visual indicators
- [ ] Implement candidate report download
- [ ] Add job description form
- [ ] Test all user flows

### Team Lead
- [ ] Set up SQS for resume processing queue (optional)
- [ ] End-to-end integration testing
- [ ] Security review (IAM policies, S3 permissions)
- [ ] Start compiling documentation
- [ ] Begin presentation outline

### Evening Standup (7 PM)
- [ ] Demo full pipeline: Upload → Parse → Match → Display
- [ ] Identify remaining bugs
- [ ] Create punch list of final tasks
- [ ] Plan Day 6 activities

---

## Day 6: November 15, 2025 (Friday) - Polish & Testing

### All Members: Bug Fixes
- [ ] Fix bugs from integration testing
- [ ] Add comprehensive error handling
- [ ] Improve CloudWatch logging
- [ ] Code cleanup and add comments
- [ ] Remove any hardcoded values
- [ ] Test edge cases

### Member 1
- [ ] Test with corrupted PDFs
- [ ] Test with very large PDFs (10+ pages)
- [ ] Verify S3 permissions are correct
- [ ] Document upload API

### Member 2
- [ ] Test with resumes missing information
- [ ] Improve skill extraction accuracy
- [ ] Test with different resume formats
- [ ] Document parsing logic

### Member 3
- [ ] Test matching with edge cases (no skills, all skills)
- [ ] Verify DynamoDB queries are optimized
- [ ] Test with 20+ job descriptions
- [ ] Document matching algorithm

### Member 4: UI Polish
- [ ] Polish dashboard UI/UX
- [ ] Add loading states for all API calls
- [ ] Add error messages for failures
- [ ] Test on different browsers (Chrome, Firefox, Safari)
- [ ] Test on mobile devices
- [ ] Deploy dashboard to S3 + CloudFront
- [ ] Document dashboard features

### Team Lead
- [ ] Comprehensive end-to-end testing
- [ ] Security review checklist
- [ ] Performance testing
- [ ] Start presentation slides
- [ ] Compile all documentation
- [ ] Create demo script

### Evening Standup (7 PM)
- [ ] Full system demo
- [ ] Create final punch list
- [ ] Assign remaining tasks
- [ ] Plan Day 7 documentation work

---

## Day 7: November 16, 2025 (Saturday) - Documentation & Presentation

### All Members: Documentation
- [ ] Write component documentation
- [ ] Add code comments
- [ ] Create README for your component
- [ ] Record demo video of your component (2-3 mins)
- [ ] Prepare to explain your code
- [ ] Write your section of final report

### Member 1
- [ ] Document S3 setup process
- [ ] Document Textract integration
- [ ] List known limitations
- [ ] Suggest enhancements

### Member 2
- [ ] Document skill parsing logic
- [ ] List recognized skills
- [ ] Document parsing accuracy
- [ ] Suggest improvements

### Member 3
- [ ] Document matching algorithm
- [ ] Explain similarity calculation
- [ ] Document DynamoDB schema
- [ ] Suggest algorithm improvements

### Member 4
- [ ] Document dashboard features
- [ ] Create user guide
- [ ] Document API integration
- [ ] List browser compatibility

### Team Lead: Final Deliverables
- [ ] Compile final documentation
- [ ] Create architecture diagram (final version)
- [ ] Write limitations section
- [ ] Write enhancement ideas section
- [ ] Create presentation slides (15-20 slides)
- [ ] Prepare demo script
- [ ] Create backup demo video
- [ ] Review all deliverables

### Deliverables Checklist
- [ ] README with setup instructions
- [ ] Architecture diagram
- [ ] API documentation
- [ ] Sample parsed outputs (10+ examples)
- [ ] Matching logic explanation
- [ ] Limitations document
- [ ] Enhancement ideas document
- [ ] Presentation slides
- [ ] Demo video (backup)

### Evening Standup (7 PM)
- [ ] Presentation dry run (full team)
- [ ] Review all deliverables
- [ ] Assign presentation sections
- [ ] Practice Q&A
- [ ] Final review

---

## Day 8: November 17, 2025 (Sunday) - Submission Day

### Morning Tasks (Before 12 PM)
- [ ] Final end-to-end testing
- [ ] Fix any critical bugs
- [ ] Update README with final instructions
- [ ] Verify all code is pushed to GitHub
- [ ] Tag release: `git tag v1.0`
- [ ] Push tags: `git push origin v1.0`
- [ ] Verify dashboard is deployed and accessible
- [ ] Test demo script one more time

### Pre-Submission Checklist
- [ ] All code in GitHub repository
- [ ] README is complete and clear
- [ ] Sample data is included
- [ ] No secrets in code
- [ ] Architecture diagram is clear
- [ ] Documentation is complete
- [ ] Presentation is ready
- [ ] Demo works flawlessly

### Afternoon Tasks
- [ ] Final presentation practice (full team)
- [ ] Prepare for Q&A session
- [ ] Review limitations honestly
- [ ] Review enhancement ideas
- [ ] Submit project (follow submission instructions)

### Presentation Checklist
- [ ] Introduction (Team Lead - 2 mins)
- [ ] Architecture overview (Team Lead - 3 mins)
- [ ] Upload & Extraction demo (Member 1 - 3 mins)
- [ ] Skill Parsing demo (Member 2 - 3 mins)
- [ ] Job Matching demo (Member 3 - 3 mins)
- [ ] Dashboard demo (Member 4 - 3 mins)
- [ ] Limitations & Enhancements (Team Lead - 2 mins)
- [ ] Q&A preparation (All - 5 mins)

### Final Celebration
- [ ] Submit project successfully
- [ ] Celebrate as a team! 🎉
- [ ] Thank each other
- [ ] Reflect on learnings
- [ ] Plan AWS resource cleanup (after grading)

---

## Post-Submission (After Nov 17)

### Cleanup Tasks (After Grading)
- [ ] Delete S3 buckets
- [ ] Delete Lambda functions
- [ ] Delete DynamoDB tables
- [ ] Delete API Gateway
- [ ] Delete CloudWatch logs
- [ ] Verify AWS bill is $0
- [ ] Keep GitHub repo for portfolio

### Learning Reflection
- [ ] What went well?
- [ ] What could be improved?
- [ ] What did you learn about AWS?
- [ ] What did you learn about team collaboration?
- [ ] What would you do differently next time?

---

## Notes Section

Use this space for daily notes, decisions, or important information:

### Day 1 Notes:


### Day 2 Notes:


### Day 3 Notes:


### Day 4 Notes:


### Day 5 Notes:


### Day 6 Notes:


### Day 7 Notes:


### Day 8 Notes:


---

**Remember:** Check off items as you complete them. This helps track progress and ensures nothing is missed!

**Good luck, team! 🚀**
