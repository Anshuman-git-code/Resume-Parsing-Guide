# Detailed Role Assignments & Technical Specifications

## Member 1: Resume Upload & Text Extraction

### Technical Stack
- **AWS Services:** S3, Lambda, Textract, API Gateway
- **Programming Language:** Python 3.9+ (recommended) or Node.js
- **Libraries:** boto3 (AWS SDK), uuid

### Detailed Tasks

#### Week 1 Tasks (Days 1-3)
1. **S3 Bucket Setup**
   - Create bucket: `resume-parser-team-[yourname]`
   - Enable versioning
   - Configure CORS for web uploads
   - Set lifecycle policy (delete after 30 days)
   - Create folders: `/resumes/raw/` and `/resumes/processed/`

2. **Upload Handler Lambda**
   ```python
   # Function: resume-parser-upload-handler
   # Trigger: API Gateway POST /upload
   # Purpose: Receive resume, upload to S3, trigger Textract
   
   Key Features:
   - Validate file type (PDF only)
   - Generate unique resume_id
   - Upload to S3
   - Trigger Textract processing
   - Return upload confirmation
   ```

3. **Textract Processor Lambda**
   ```python
   # Function: resume-parser-textract-processor
   # Trigger: S3 upload event
   # Purpose: Extract text from PDF using Textract
   
   Key Features:
   - Call Textract DetectDocumentText API
   - Handle multi-page PDFs
   - Extract raw text
   - Save to S3: /resumes/processed/[resume_id].json
   - Trigger next Lambda (skill parser)
   ```

### Output Format (Critical for Handoff)
```json
{
  "resume_id": "550e8400-e29b-41d4-a716-446655440000",
  "upload_timestamp": "2025-11-12T10:30:00Z",
  "file_name": "john_doe_resume.pdf",
  "s3_location": "s3://bucket/resumes/raw/550e8400.pdf",
  "extraction_status": "completed",
  "raw_text": "John Doe\nSoftware Engineer\n...",
  "page_count": 2,
  "confidence_score": 0.95
}
```

### Testing Requirements
- Test with at least 5 different resume formats
- Test with 1-page and multi-page PDFs
- Test error handling (corrupted PDF, non-PDF file)
- Verify Textract costs (should be <$1 for 50 resumes)

### Handoff Deliverables
1. S3 bucket name and access instructions
2. Lambda function code (pushed to GitHub)
3. 5 sample output JSON files
4. API endpoint URL for upload
5. Textract output format documentation
6. Known issues and workarounds

---

## Member 2: Skill Parsing & NLP Logic

### Technical Stack
- **AWS Services:** Lambda, Comprehend (optional), S3
- **Programming Language:** Python 3.9+
- **Libraries:** boto3, re (regex), nltk or spacy (optional)

### Detailed Tasks

#### Week 1 Tasks (Days 2-4)
1. **Skill Extraction Logic**
   ```python
   # Function: resume-parser-skill-parser
   # Trigger: S3 event from Textract output
   # Purpose: Parse structured data from raw text
   
   Key Features:
   - Extract skills (technical and soft skills)
   - Identify job titles and companies
   - Calculate total experience
   - Extract education details
   - Parse contact information
   ```

2. **Skills Database**
   Create a comprehensive skills list:
   ```json
   {
     "programming_languages": ["Python", "Java", "JavaScript", "C++", ...],
     "frameworks": ["React", "Django", "Spring Boot", ...],
     "cloud": ["AWS", "Azure", "GCP", "Docker", "Kubernetes", ...],
     "databases": ["MySQL", "MongoDB", "PostgreSQL", ...],
     "soft_skills": ["Leadership", "Communication", "Problem Solving", ...]
   }
   ```

3. **Parsing Algorithms**
   - **Skill Matching:** Keyword matching with fuzzy logic
   - **Experience Calculation:** Regex to find date ranges
   - **Education Parsing:** Pattern matching for degrees
   - **Contact Info:** Regex for email, phone

4. **Amazon Comprehend Integration (Optional)**
   - Use for entity recognition
   - Extract key phrases
   - Identify job titles automatically

### Output Format (Critical for Handoff)
```json
{
  "resume_id": "550e8400-e29b-41d4-a716-446655440000",
  "parsing_timestamp": "2025-11-12T10:35:00Z",
  "candidate": {
    "name": "John Doe",
    "email": "john.doe@email.com",
    "phone": "+1-234-567-8900",
    "location": "San Francisco, CA"
  },
  "skills": {
    "technical": ["Python", "AWS", "Docker", "React", "PostgreSQL"],
    "soft": ["Leadership", "Team Collaboration"],
    "certifications": ["AWS Solutions Architect"]
  },
  "experience": [
    {
      "title": "Senior Software Engineer",
      "company": "Tech Corp",
      "start_date": "2020-01",
      "end_date": "2023-05",
      "duration_months": 40,
      "description": "Led team of 5 engineers..."
    }
  ],
  "education": [
    {
      "degree": "Bachelor of Technology",
      "field": "Computer Science",
      "institution": "XYZ University",
      "graduation_year": 2019
    }
  ],
  "total_experience_years": 5.5,
  "summary": "Experienced software engineer with 5+ years..."
}
```

### Testing Requirements
- Test with resumes from different industries
- Verify skill extraction accuracy (aim for >70%)
- Test with resumes having different formats
- Handle missing information gracefully

### Handoff Deliverables
1. Lambda function code (pushed to GitHub)
2. Skills database JSON file
3. 10 sample parsed resume outputs
4. Parsing accuracy report
5. Documentation of parsing logic
6. Known limitations

---

## Member 3: Job Matching Algorithm & DynamoDB

### Technical Stack
- **AWS Services:** DynamoDB, Lambda
- **Programming Language:** Python 3.9+
- **Libraries:** boto3, scikit-learn (for cosine similarity)

### Detailed Tasks

#### Week 1 Tasks (Days 2-5)
1. **DynamoDB Table Design**

   **Table 1: Candidates**
   ```
   Table Name: resume-parser-candidates
   Primary Key: candidate_id (String)
   Attributes: All parsed resume data
   GSI: email-index (for lookup by email)
   ```

   **Table 2: Job Descriptions**
   ```
   Table Name: resume-parser-jobs
   Primary Key: job_id (String)
   Attributes:
   - job_title
   - company
   - required_skills []
   - preferred_skills []
   - experience_required (years)
   - description
   - location
   - posted_date
   ```

   **Table 3: Matches**
   ```
   Table Name: resume-parser-matches
   Primary Key: candidate_id (String)
   Sort Key: job_id (String)
   Attributes:
   - match_score (Number)
   - matched_skills []
   - missing_skills []
   - match_timestamp
   ```

2. **Matching Algorithm Implementation**

   **Option 1: Jaccard Similarity**
   ```python
   def jaccard_similarity(candidate_skills, job_skills):
       set_candidate = set(candidate_skills)
       set_job = set(job_skills)
       intersection = set_candidate.intersection(set_job)
       union = set_candidate.union(set_job)
       return len(intersection) / len(union)
   ```

   **Option 2: Cosine Similarity (Recommended)**
   ```python
   # Use TF-IDF vectorization
   # Compare candidate skills + experience vs job requirements
   # Returns score between 0-1
   ```

   **Weighted Scoring:**
   ```python
   final_score = (
       0.5 * skill_match_score +
       0.3 * experience_match_score +
       0.2 * education_match_score
   )
   ```

3. **Job Matcher Lambda**
   ```python
   # Function: resume-parser-job-matcher
   # Trigger: New candidate added to DynamoDB
   # Purpose: Match candidate with all jobs
   
   Key Features:
   - Fetch all active jobs from DynamoDB
   - Calculate match score for each job
   - Store top 10 matches
   - Sort by match score (descending)
   ```

4. **Sample Job Descriptions**
   Create 10-15 diverse job descriptions:
   - Software Engineer (Python/AWS)
   - Frontend Developer (React)
   - Data Scientist (ML/Python)
   - DevOps Engineer (Kubernetes/AWS)
   - Full Stack Developer
   - etc.

### Output Format (Critical for Handoff)
```json
{
  "candidate_id": "550e8400-e29b-41d4-a716-446655440000",
  "candidate_name": "John Doe",
  "match_timestamp": "2025-11-12T10:40:00Z",
  "total_jobs_matched": 10,
  "matches": [
    {
      "job_id": "job-001",
      "job_title": "Senior Python Developer",
      "company": "Tech Innovations Inc",
      "match_score": 0.87,
      "match_percentage": 87,
      "matched_skills": ["Python", "AWS", "Docker", "PostgreSQL"],
      "missing_skills": ["Kubernetes", "GraphQL"],
      "experience_match": true,
      "education_match": true,
      "recommendation": "Excellent match"
    },
    {
      "job_id": "job-002",
      "job_title": "Full Stack Developer",
      "company": "StartUp XYZ",
      "match_score": 0.72,
      "match_percentage": 72,
      "matched_skills": ["React", "Python", "PostgreSQL"],
      "missing_skills": ["Node.js", "MongoDB"],
      "experience_match": true,
      "education_match": true,
      "recommendation": "Good match"
    }
  ]
}
```

### API Endpoints to Create
```
POST /jobs - Add new job description
GET /jobs - List all jobs
GET /matches/{candidate_id} - Get matches for candidate
GET /candidates - List all candidates
GET /candidates/{candidate_id} - Get candidate details
```

### Testing Requirements
- Test matching with various skill combinations
- Verify scores are reasonable (0-1 range)
- Test with candidates having no matches
- Test with candidates matching all jobs
- Performance test with 50+ jobs

### Handoff Deliverables
1. DynamoDB table names and schemas
2. Lambda function code (pushed to GitHub)
3. Sample job descriptions (in DynamoDB)
4. Matching algorithm documentation
5. API endpoint documentation
6. Sample match outputs (10+ examples)
7. Postman collection for API testing

---

## Member 4: Dashboard & API Integration

### Technical Stack
- **Frontend:** HTML5, CSS3, JavaScript (or React if comfortable)
- **AWS Services:** S3 (hosting), CloudFront (optional), API Gateway
- **Libraries:** Axios/Fetch for API calls, jsPDF for reports

### Detailed Tasks

#### Week 1 Tasks (Days 3-6)
1. **Dashboard Pages**

   **Page 1: Upload Resume**
   - File upload form (drag & drop)
   - Upload progress indicator
   - Success/error messages
   - View uploaded resume details

   **Page 2: Candidates List**
   - Table showing all candidates
   - Columns: Name, Email, Skills, Experience, Upload Date
   - Search/filter functionality
   - Click to view details

   **Page 3: Candidate Details**
   - Full parsed resume information
   - Skills visualization (tags/badges)
   - Experience timeline
   - Education details
   - Job matches section

   **Page 4: Job Matches**
   - List of matched jobs with scores
   - Visual match percentage (progress bar)
   - Matched vs missing skills
   - Apply/contact buttons (mock)

   **Page 5: Add Job Description**
   - Form to add new job
   - Fields: Title, Company, Skills, Experience, Description
   - Submit to backend API

2. **API Gateway Configuration**
   ```
   Base URL: https://[api-id].execute-api.us-east-1.amazonaws.com/prod
   
   Endpoints:
   POST /upload - Upload resume
   GET /candidates - List candidates
   GET /candidates/{id} - Get candidate details
   GET /matches/{candidate_id} - Get job matches
   POST /jobs - Add job description
   GET /jobs - List jobs
   ```

   Configure CORS:
   ```json
   {
     "Access-Control-Allow-Origin": "*",
     "Access-Control-Allow-Methods": "GET,POST,OPTIONS",
     "Access-Control-Allow-Headers": "Content-Type"
   }
   ```

3. **Report Generation**
   ```javascript
   // Generate PDF report for candidate
   function generateCandidateReport(candidateData) {
     // Include:
     // - Candidate information
     // - Skills summary
     // - Experience details
     // - Top 5 job matches
     // - Match scores and recommendations
   }
   ```

4. **UI/UX Design**
   - Clean, professional design
   - Responsive (works on mobile)
   - Loading states for API calls
   - Error handling with user-friendly messages
   - Color scheme: Professional blues/grays

### Dashboard Features Checklist
- [ ] Upload resume (PDF only)
- [ ] View upload status
- [ ] List all candidates
- [ ] Search candidates by name/skills
- [ ] View candidate full details
- [ ] View job matches with scores
- [ ] Download candidate report (PDF)
- [ ] Add new job description
- [ ] View all jobs
- [ ] Responsive design
- [ ] Error handling
- [ ] Loading indicators

### Testing Requirements
- Test on Chrome, Firefox, Safari
- Test on mobile devices
- Test all API integrations
- Test error scenarios (API down, invalid data)
- Test file upload with large PDFs
- Test report download

### Handoff Deliverables
1. Complete dashboard code (pushed to GitHub)
2. Deployed dashboard URL (S3 + CloudFront)
3. API integration documentation
4. User guide (how to use dashboard)
5. Screenshots of all pages
6. Known issues and browser compatibility

---

## Team Lead (Anshuman): Integration & Coordination

### Responsibilities

#### Technical Leadership
1. **AWS Account Management**
   - Create and manage IAM users
   - Monitor costs daily
   - Set up billing alerts
   - Manage resource permissions

2. **Architecture Decisions**
   - Review and approve technical approaches
   - Resolve integration conflicts
   - Make trade-off decisions (time vs features)

3. **Code Reviews**
   - Review all pull requests
   - Ensure code quality and consistency
   - Verify security best practices
   - Check for hardcoded secrets

4. **Integration Testing**
   - Test end-to-end flow
   - Identify integration issues
   - Coordinate fixes across members

5. **SQS Implementation (Optional)**
   ```
   If time permits, add SQS queue:
   Upload → SQS → Textract → SQS → Parser → SQS → Matcher
   
   Benefits:
   - Handle high volume uploads
   - Retry failed processing
   - Decouple components
   ```

#### Project Management
1. **Daily Standups**
   - Facilitate 7 PM meetings
   - Track progress against timeline
   - Identify and resolve blockers
   - Adjust timeline if needed

2. **Documentation**
   - Compile all member documentation
   - Create architecture diagram
   - Write final report
   - Prepare presentation

3. **Risk Management**
   - Monitor for delays
   - Have backup plans
   - Manage scope creep
   - Ensure quality standards

### Weekly Checklist

**Day 1:**
- [ ] AWS account setup complete
- [ ] IAM users created
- [ ] GitHub repo created
- [ ] All members have access
- [ ] Architecture diagram created

**Day 2-3:**
- [ ] Review Member 1's code
- [ ] Verify S3 and Textract working
- [ ] Check AWS costs

**Day 4:**
- [ ] Review Member 2's code
- [ ] Test Upload → Parse pipeline
- [ ] Resolve integration issues

**Day 5:**
- [ ] Review Member 3's code
- [ ] Test full pipeline
- [ ] Start SQS implementation (if time)

**Day 6:**
- [ ] Review Member 4's dashboard
- [ ] End-to-end testing
- [ ] Security review
- [ ] Start presentation

**Day 7:**
- [ ] Compile documentation
- [ ] Finalize presentation
- [ ] Practice demo

### Deliverables
1. Architecture diagram
2. Final project report
3. Presentation slides
4. Demo video
5. Limitations and enhancements document

---

## Success Criteria for Each Role

### Member 1 Success
- ✅ Can upload PDF to S3
- ✅ Textract extracts text accurately
- ✅ Output format is consistent
- ✅ Handles errors gracefully
- ✅ Documented for next member

### Member 2 Success
- ✅ Extracts skills with >70% accuracy
- ✅ Parses experience and education
- ✅ Output format is structured
- ✅ Works with Member 1's output
- ✅ Documented for next member

### Member 3 Success
- ✅ DynamoDB tables created
- ✅ Matching algorithm produces reasonable scores
- ✅ API endpoints work correctly
- ✅ Sample jobs in database
- ✅ Documented for dashboard integration

### Member 4 Success
- ✅ Dashboard is functional and user-friendly
- ✅ All APIs integrated correctly
- ✅ Can upload, view, and match resumes
- ✅ Report download works
- ✅ Deployed and accessible

### Team Lead Success
- ✅ All components integrated
- ✅ End-to-end flow works
- ✅ Documentation complete
- ✅ Presentation ready
- ✅ Project delivered on time

---

## Communication Templates

### Daily Update Template (Post by 6 PM)
```
📅 [Your Name] - Day [X] Update

✅ Completed:
- Task 1
- Task 2

🚧 In Progress:
- Task 3 (50% done)

🎯 Tomorrow:
- Task 4
- Task 5

❓ Questions/Blockers:
- None / [Describe blocker]

📊 Status: On Track / Slightly Behind / Need Help
```

### Handoff Notification Template
```
🚀 HANDOFF READY: [Component Name]

From: [Your Name]
To: @[Next Member]

✅ What's Complete:
- Feature 1
- Feature 2

📁 Files Location:
- Code: feature/[branch-name]
- Samples: sample-data/[folder]
- Docs: docs/handoff-notes/[file]

🔑 Key Info:
- [Important detail 1]
- [Important detail 2]

📞 Available for questions: [Time range]
```

### Blocker Alert Template
```
🚨 BLOCKER ALERT

Component: [Your component]
Issue: [Brief description]
Error: [Error message or screenshot]

What I tried:
1. Attempt 1
2. Attempt 2

Need help from: @[Person] or anyone available

Urgency: High / Medium / Low
```

---

## Final Notes

### Remember
- **Communication is key** - Over-communicate rather than under-communicate
- **Ask early** - Don't wait until you're completely stuck
- **Help each other** - You're a team, not competitors
- **Document everything** - Future you will thank present you
- **Test frequently** - Don't wait until the end
- **Commit often** - Small commits are easier to debug

### You've Got This! 🚀
This is a challenging but achievable project. Stay organized, communicate well, and support each other. Good luck!
