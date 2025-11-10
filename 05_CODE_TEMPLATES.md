# Code Templates & Starter Code

Quick-start templates for each team member to accelerate development.

## Table of Contents
1. [Member 1: Upload & Textract](#member-1-upload--textract)
2. [Member 2: Skill Parser](#member-2-skill-parser)
3. [Member 3: Job Matcher](#member-3-job-matcher)
4. [Member 4: Dashboard](#member-4-dashboard)
5. [Common Utilities](#common-utilities)

---

## Member 1: Upload & Textract

### Lambda: Upload Handler (Python)

**File:** `backend/lambda-functions/upload-handler/handler.py`

```python
import json
import boto3
import uuid
import base64
from datetime import datetime

s3_client = boto3.client('s3')
textract_client = boto3.client('textract')

BUCKET_NAME = 'resume-parser-bucket-main'

def lambda_handler(event, context):
    """
    Handle resume upload from API Gateway
    """
    try:
        # Parse request
        body = json.loads(event['body'])
        file_content = base64.b64decode(body['file'])
        file_name = body['fileName']
        
        # Generate unique ID
        resume_id = str(uuid.uuid4())
        s3_key = f"resumes/raw/{resume_id}.pdf"
        
        # Upload to S3
        s3_client.put_object(
            Bucket=BUCKET_NAME,
            Key=s3_key,
            Body=file_content,
            ContentType='application/pdf'
        )
        
        # Trigger Textract
        response = textract_client.detect_document_text(
            Document={'S3Object': {'Bucket': BUCKET_NAME, 'Name': s3_key}}
        )
        
        # Extract text
        extracted_text = extract_text_from_response(response)
        
        # Save extracted text
        output_key = f"resumes/processed/{resume_id}.json"
        output_data = {
            'resume_id': resume_id,
            'upload_timestamp': datetime.utcnow().isoformat(),
            'file_name': file_name,
            's3_location': f"s3://{BUCKET_NAME}/{s3_key}",
            'raw_text': extracted_text,
            'extraction_status': 'completed'
        }
        
        s3_client.put_object(
            Bucket=BUCKET_NAME,
            Key=output_key,
            Body=json.dumps(output_data),
            ContentType='application/json'
        )
        
        return {
            'statusCode': 200,
            'headers': {'Access-Control-Allow-Origin': '*'},
            'body': json.dumps({
                'message': 'Resume uploaded successfully',
                'resume_id': resume_id
            })
        }
        
    except Exception as e:
        print(f"Error: {str(e)}")
        return {
            'statusCode': 500,
            'headers': {'Access-Control-Allow-Origin': '*'},
            'body': json.dumps({'error': str(e)})
        }

def extract_text_from_response(response):
    """Extract text from Textract response"""
    text = ""
    for item in response['Blocks']:
        if item['BlockType'] == 'LINE':
            text += item['Text'] + "\n"
    return text
```

### requirements.txt
```
boto3==1.28.0
```

---

## Member 2: Skill Parser

### Lambda: Skill Parser (Python)

**File:** `backend/lambda-functions/skill-parser/handler.py`

```python
import json
import boto3
import re
from datetime import datetime

s3_client = boto3.client('s3')
dynamodb = boto3.resource('dynamodb')

BUCKET_NAME = 'resume-parser-bucket-main'
CANDIDATES_TABLE = 'resume-parser-candidates'

# Skills database
SKILLS_DATABASE = {
    'programming': ['Python', 'Java', 'JavaScript', 'C++', 'Go', 'Ruby', 'PHP'],
    'frameworks': ['React', 'Angular', 'Vue', 'Django', 'Flask', 'Spring'],
    'cloud': ['AWS', 'Azure', 'GCP', 'Docker', 'Kubernetes'],
    'databases': ['MySQL', 'PostgreSQL', 'MongoDB', 'Redis', 'DynamoDB'],
    'tools': ['Git', 'Jenkins', 'Terraform', 'Ansible']
}

def lambda_handler(event, context):
    """Parse resume text and extract structured data"""
    try:
        # Get resume from S3
        resume_id = event['resume_id']
        key = f"resumes/processed/{resume_id}.json"
        
        response = s3_client.get_object(Bucket=BUCKET_NAME, Key=key)
        resume_data = json.loads(response['Body'].read())
        raw_text = resume_data['raw_text']
        
        # Parse resume
        parsed_data = {
            'resume_id': resume_id,
            'parsing_timestamp': datetime.utcnow().isoformat(),
            'candidate': extract_contact_info(raw_text),
            'skills': extract_skills(raw_text),
            'experience': extract_experience(raw_text),
            'education': extract_education(raw_text),
            'total_experience_years': calculate_total_experience(raw_text)
        }
        
        # Store in DynamoDB
        table = dynamodb.Table(CANDIDATES_TABLE)
        table.put_item(Item=parsed_data)
        
        return {
            'statusCode': 200,
            'body': json.dumps(parsed_data)
        }
        
    except Exception as e:
        print(f"Error: {str(e)}")
        return {'statusCode': 500, 'body': json.dumps({'error': str(e)})}

def extract_contact_info(text):
    """Extract name, email, phone"""
    email_pattern = r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b'
    phone_pattern = r'\+?[\d\s\-\(\)]{10,}'
    
    email = re.search(email_pattern, text)
    phone = re.search(phone_pattern, text)
    
    # Name is usually first line
    lines = text.split('\n')
    name = lines[0].strip() if lines else "Unknown"
    
    return {
        'name': name,
        'email': email.group(0) if email else None,
        'phone': phone.group(0) if phone else None
    }

def extract_skills(text):
    """Extract technical skills"""
    found_skills = []
    text_lower = text.lower()
    
    for category, skills in SKILLS_DATABASE.items():
        for skill in skills:
            if skill.lower() in text_lower:
                found_skills.append(skill)
    
    return list(set(found_skills))  # Remove duplicates

def extract_experience(text):
    """Extract work experience"""
    # Simple pattern matching for job titles
    experience = []
    # This is simplified - enhance based on resume format
    return experience

def extract_education(text):
    """Extract education details"""
    education = []
    degrees = ['B.Tech', 'M.Tech', 'Bachelor', 'Master', 'PhD', 'MBA']
    
    for degree in degrees:
        if degree.lower() in text.lower():
            education.append({'degree': degree})
    
    return education

def calculate_total_experience(text):
    """Calculate total years of experience"""
    # Look for patterns like "5 years", "3+ years"
    pattern = r'(\d+)\+?\s*years?'
    matches = re.findall(pattern, text.lower())
    
    if matches:
        return max([int(m) for m in matches])
    return 0
```

---

## Member 3: Job Matcher

### Lambda: Job Matcher (Python)

**File:** `backend/lambda-functions/job-matcher/handler.py`

```python
import json
import boto3
from decimal import Decimal

dynamodb = boto3.resource('dynamodb')

CANDIDATES_TABLE = 'resume-parser-candidates'
JOBS_TABLE = 'resume-parser-jobs'
MATCHES_TABLE = 'resume-parser-matches'

def lambda_handler(event, context):
    """Match candidate with jobs"""
    try:
        candidate_id = event['candidate_id']
        
        # Get candidate
        candidates_table = dynamodb.Table(CANDIDATES_TABLE)
        candidate = candidates_table.get_item(Key={'resume_id': candidate_id})['Item']
        
        # Get all jobs
        jobs_table = dynamodb.Table(JOBS_TABLE)
        jobs = jobs_table.scan()['Items']
        
        # Calculate matches
        matches = []
        for job in jobs:
            match_score = calculate_match_score(candidate, job)
            if match_score > 0.3:  # Threshold
                matches.append({
                    'job_id': job['job_id'],
                    'job_title': job['job_title'],
                    'company': job['company'],
                    'match_score': match_score,
                    'matched_skills': get_matched_skills(candidate, job),
                    'missing_skills': get_missing_skills(candidate, job)
                })
        
        # Sort by score
        matches.sort(key=lambda x: x['match_score'], reverse=True)
        
        # Store matches
        matches_table = dynamodb.Table(MATCHES_TABLE)
        matches_table.put_item(Item={
            'candidate_id': candidate_id,
            'matches': matches
        })
        
        return {
            'statusCode': 200,
            'body': json.dumps({'matches': matches}, default=decimal_default)
        }
        
    except Exception as e:
        print(f"Error: {str(e)}")
        return {'statusCode': 500, 'body': json.dumps({'error': str(e)})}

def calculate_match_score(candidate, job):
    """Calculate Jaccard similarity"""
    candidate_skills = set([s.lower() for s in candidate.get('skills', [])])
    job_skills = set([s.lower() for s in job.get('required_skills', [])])
    
    if not job_skills:
        return 0
    
    intersection = candidate_skills.intersection(job_skills)
    union = candidate_skills.union(job_skills)
    
    skill_score = len(intersection) / len(union) if union else 0
    
    # Experience match
    exp_match = 1.0 if candidate.get('total_experience_years', 0) >= job.get('experience_required', 0) else 0.5
    
    # Weighted score
    final_score = (0.7 * skill_score) + (0.3 * exp_match)
    
    return round(final_score, 2)

def get_matched_skills(candidate, job):
    """Get skills that match"""
    candidate_skills = set([s.lower() for s in candidate.get('skills', [])])
    job_skills = set([s.lower() for s in job.get('required_skills', [])])
    return list(candidate_skills.intersection(job_skills))

def get_missing_skills(candidate, job):
    """Get skills candidate is missing"""
    candidate_skills = set([s.lower() for s in candidate.get('skills', [])])
    job_skills = set([s.lower() for s in job.get('required_skills', [])])
    return list(job_skills - candidate_skills)

def decimal_default(obj):
    """JSON serializer for Decimal"""
    if isinstance(obj, Decimal):
        return float(obj)
    raise TypeError
```

### DynamoDB Table Creation Script

**File:** `infrastructure/create_tables.py`

```python
import boto3

dynamodb = boto3.client('dynamodb')

def create_candidates_table():
    dynamodb.create_table(
        TableName='resume-parser-candidates',
        KeySchema=[{'AttributeName': 'resume_id', 'KeyType': 'HASH'}],
        AttributeDefinitions=[{'AttributeName': 'resume_id', 'AttributeType': 'S'}],
        BillingMode='PAY_PER_REQUEST'
    )

def create_jobs_table():
    dynamodb.create_table(
        TableName='resume-parser-jobs',
        KeySchema=[{'AttributeName': 'job_id', 'KeyType': 'HASH'}],
        AttributeDefinitions=[{'AttributeName': 'job_id', 'AttributeType': 'S'}],
        BillingMode='PAY_PER_REQUEST'
    )

def create_matches_table():
    dynamodb.create_table(
        TableName='resume-parser-matches',
        KeySchema=[{'AttributeName': 'candidate_id', 'KeyType': 'HASH'}],
        AttributeDefinitions=[{'AttributeName': 'candidate_id', 'AttributeType': 'S'}],
        BillingMode='PAY_PER_REQUEST'
    )

if __name__ == '__main__':
    create_candidates_table()
    create_jobs_table()
    create_matches_table()
    print("Tables created successfully!")
```

---

## Member 4: Dashboard

### Dashboard HTML

**File:** `frontend/dashboard/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Resume Parser - Recruiter Dashboard</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>Resume Parser & Skill Matcher</h1>
            <nav>
                <button onclick="showSection('upload')">Upload Resume</button>
                <button onclick="showSection('candidates')">Candidates</button>
                <button onclick="showSection('jobs')">Jobs</button>
            </nav>
        </header>

        <!-- Upload Section -->
        <section id="upload-section" class="section active">
            <h2>Upload Resume</h2>
            <div class="upload-area" id="upload-area">
                <input type="file" id="file-input" accept=".pdf" />
                <p>Drag & drop PDF or click to browse</p>
            </div>
            <button onclick="uploadResume()">Upload</button>
            <div id="upload-status"></div>
        </section>

        <!-- Candidates Section -->
        <section id="candidates-section" class="section">
            <h2>All Candidates</h2>
            <input type="text" id="search" placeholder="Search by name or skills..." />
            <table id="candidates-table">
                <thead>
                    <tr>
                        <th>Name</th>
                        <th>Email</th>
                        <th>Skills</th>
                        <th>Experience</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody id="candidates-tbody"></tbody>
            </table>
        </section>

        <!-- Jobs Section -->
        <section id="jobs-section" class="section">
            <h2>Job Descriptions</h2>
            <button onclick="showAddJobForm()">Add New Job</button>
            <div id="jobs-list"></div>
        </section>
    </div>

    <script src="app.js"></script>
</body>
</html>
```

### Dashboard JavaScript

**File:** `frontend/dashboard/app.js`

```javascript
const API_BASE_URL = 'https://your-api-id.execute-api.us-east-1.amazonaws.com/prod';

// Show/hide sections
function showSection(section) {
    document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
    document.getElementById(`${section}-section`).classList.add('active');
    
    if (section === 'candidates') loadCandidates();
    if (section === 'jobs') loadJobs();
}

// Upload resume
async function uploadResume() {
    const fileInput = document.getElementById('file-input');
    const file = fileInput.files[0];
    
    if (!file) {
        alert('Please select a PDF file');
        return;
    }
    
    const reader = new FileReader();
    reader.onload = async function(e) {
        const base64 = e.target.result.split(',')[1];
        
        try {
            const response = await fetch(`${API_BASE_URL}/upload`, {
                method: 'POST',
                headers: {'Content-Type': 'application/json'},
                body: JSON.stringify({
                    file: base64,
                    fileName: file.name
                })
            });
            
            const data = await response.json();
            document.getElementById('upload-status').innerHTML = 
                `<p class="success">Resume uploaded! ID: ${data.resume_id}</p>`;
        } catch (error) {
            document.getElementById('upload-status').innerHTML = 
                `<p class="error">Upload failed: ${error.message}</p>`;
        }
    };
    
    reader.readAsDataURL(file);
}

// Load candidates
async function loadCandidates() {
    try {
        const response = await fetch(`${API_BASE_URL}/candidates`);
        const candidates = await response.json();
        
        const tbody = document.getElementById('candidates-tbody');
        tbody.innerHTML = '';
        
        candidates.forEach(candidate => {
            const row = `
                <tr>
                    <td>${candidate.candidate.name}</td>
                    <td>${candidate.candidate.email || 'N/A'}</td>
                    <td>${candidate.skills.join(', ')}</td>
                    <td>${candidate.total_experience_years} years</td>
                    <td>
                        <button onclick="viewCandidate('${candidate.resume_id}')">View</button>
                        <button onclick="viewMatches('${candidate.resume_id}')">Matches</button>
                    </td>
                </tr>
            `;
            tbody.innerHTML += row;
        });
    } catch (error) {
        console.error('Error loading candidates:', error);
    }
}

// View candidate details
async function viewCandidate(candidateId) {
    try {
        const response = await fetch(`${API_BASE_URL}/candidates/${candidateId}`);
        const candidate = await response.json();
        
        // Display in modal or new section
        alert(JSON.stringify(candidate, null, 2));
    } catch (error) {
        console.error('Error:', error);
    }
}

// View job matches
async function viewMatches(candidateId) {
    try {
        const response = await fetch(`${API_BASE_URL}/matches/${candidateId}`);
        const data = await response.json();
        
        // Display matches
        console.log(data.matches);
    } catch (error) {
        console.error('Error:', error);
    }
}

// Load jobs
async function loadJobs() {
    try {
        const response = await fetch(`${API_BASE_URL}/jobs`);
        const jobs = await response.json();
        
        const jobsList = document.getElementById('jobs-list');
        jobsList.innerHTML = '';
        
        jobs.forEach(job => {
            const jobCard = `
                <div class="job-card">
                    <h3>${job.job_title}</h3>
                    <p><strong>Company:</strong> ${job.company}</p>
                    <p><strong>Skills:</strong> ${job.required_skills.join(', ')}</p>
                    <p><strong>Experience:</strong> ${job.experience_required} years</p>
                </div>
            `;
            jobsList.innerHTML += jobCard;
        });
    } catch (error) {
        console.error('Error loading jobs:', error);
    }
}
```

### Dashboard CSS

**File:** `frontend/dashboard/styles.css`

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: #f5f5f5;
    color: #333;
}

.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
}

header {
    background: #2c3e50;
    color: white;
    padding: 20px;
    border-radius: 8px;
    margin-bottom: 20px;
}

header h1 {
    margin-bottom: 15px;
}

nav button {
    background: #3498db;
    color: white;
    border: none;
    padding: 10px 20px;
    margin-right: 10px;
    border-radius: 5px;
    cursor: pointer;
}

nav button:hover {
    background: #2980b9;
}

.section {
    display: none;
    background: white;
    padding: 30px;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.section.active {
    display: block;
}

.upload-area {
    border: 2px dashed #3498db;
    padding: 40px;
    text-align: center;
    border-radius: 8px;
    margin: 20px 0;
    cursor: pointer;
}

.upload-area:hover {
    background: #ecf0f1;
}

button {
    background: #27ae60;
    color: white;
    border: none;
    padding: 12px 24px;
    border-radius: 5px;
    cursor: pointer;
    font-size: 16px;
}

button:hover {
    background: #229954;
}

table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
}

th, td {
    padding: 12px;
    text-align: left;
    border-bottom: 1px solid #ddd;
}

th {
    background: #34495e;
    color: white;
}

tr:hover {
    background: #f5f5f5;
}

.success {
    color: #27ae60;
    padding: 10px;
    background: #d5f4e6;
    border-radius: 5px;
    margin-top: 10px;
}

.error {
    color: #e74c3c;
    padding: 10px;
    background: #fadbd8;
    border-radius: 5px;
    margin-top: 10px;
}

.job-card {
    background: #ecf0f1;
    padding: 20px;
    margin: 10px 0;
    border-radius: 8px;
    border-left: 4px solid #3498db;
}
```

---

## Common Utilities

### Config File

**File:** `backend/utils/config.py`

```python
import os

# AWS Configuration
AWS_REGION = os.environ.get('AWS_REGION', 'us-east-1')
S3_BUCKET = os.environ.get('S3_BUCKET', 'resume-parser-bucket-main')

# DynamoDB Tables
CANDIDATES_TABLE = os.environ.get('CANDIDATES_TABLE', 'resume-parser-candidates')
JOBS_TABLE = os.environ.get('JOBS_TABLE', 'resume-parser-jobs')
MATCHES_TABLE = os.environ.get('MATCHES_TABLE', 'resume-parser-matches')

# Matching Configuration
MATCH_THRESHOLD = 0.3
TOP_MATCHES_COUNT = 10
```

### S3 Helper

**File:** `backend/utils/s3_helper.py`

```python
import boto3
import json

s3_client = boto3.client('s3')

def upload_file(bucket, key, content, content_type='application/json'):
    """Upload file to S3"""
    s3_client.put_object(
        Bucket=bucket,
        Key=key,
        Body=content,
        ContentType=content_type
    )

def download_file(bucket, key):
    """Download file from S3"""
    response = s3_client.get_object(Bucket=bucket, Key=key)
    return response['Body'].read()

def download_json(bucket, key):
    """Download and parse JSON from S3"""
    content = download_file(bucket, key)
    return json.loads(content)
```

---

## Testing Scripts

### Test Upload

**File:** `tests/test_upload.py`

```python
import requests
import base64

API_URL = 'https://your-api-id.execute-api.us-east-1.amazonaws.com/prod'

def test_upload():
    with open('sample-data/resumes/test_resume.pdf', 'rb') as f:
        file_content = base64.b64encode(f.read()).decode('utf-8')
    
    response = requests.post(
        f'{API_URL}/upload',
        json={
            'file': file_content,
            'fileName': 'test_resume.pdf'
        }
    )
    
    print(f"Status: {response.status_code}")
    print(f"Response: {response.json()}")

if __name__ == '__main__':
    test_upload()
```

---

## Deployment Scripts

### Deploy Lambda

**File:** `scripts/deploy_lambda.sh`

```bash
#!/bin/bash

FUNCTION_NAME=$1
HANDLER_FILE=$2

if [ -z "$FUNCTION_NAME" ] || [ -z "$HANDLER_FILE" ]; then
    echo "Usage: ./deploy_lambda.sh <function-name> <handler-file>"
    exit 1
fi

# Create deployment package
cd backend/lambda-functions/$FUNCTION_NAME
zip -r function.zip .

# Update Lambda function
aws lambda update-function-code \
    --function-name resume-parser-$FUNCTION_NAME \
    --zip-file fileb://function.zip

echo "Deployed $FUNCTION_NAME successfully!"
```

---

These templates provide a solid starting point. Customize based on your specific needs!
