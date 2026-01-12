# 🎯 Upwork Automation Workflow

An intelligent automation system that scrapes Upwork job listings, uses AI to score job relevance, and automatically logs qualified leads to Airtable with optional Slack notifications.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Workflow Details](#workflow-details)
- [Enhancements Implemented](#enhancements-implemented)
- [Troubleshooting](#troubleshooting)
- [Future Improvements](#future-improvements)

## 🌟 Overview

This project automates the process of finding relevant Upwork jobs for automation specialists. It:

1. **Scrapes** Upwork every 8 hours using Apify API
2. **Filters** jobs posted within the last 6 hours
3. **Scores** each job using OpenAI GPT-4o (1-10 scale)
4. **Classifies** jobs as High, Medium, or Low priority
5. **Logs** all qualified jobs to Airtable
6. **Alerts** via Slack for high-priority opportunities

## ✨ Features

### Core Features
- ✅ **Automated Scheduling**: Runs every 8 hours automatically
- ✅ **Time-Based Filtering**: Only processes jobs posted in the last 6 hours
- ✅ **AI-Powered Scoring**: Uses GPT-4o to evaluate job relevance (1-10)
- ✅ **Priority Classification**: Categorizes jobs as High/Medium/Low
- ✅ **Structured Data Storage**: Pushes to Airtable with all job details
- ✅ **Smart Alerts**: Slack notifications for high-priority jobs

### Enhanced Features
- ✅ **Budget Tracking**: Captures and logs budget/hourly rate information
- ✅ **Improved AI Prompt**: Context-aware scoring based on automation skills
- ✅ **Environment Variables**: Secure credential management
- ✅ **Error Handling**: Robust workflow with proper error management
- ✅ **Docker Support**: Easy deployment with Docker Compose

## 📦 Prerequisites

Before you begin, ensure you have:

### Software Requirements
- **Docker Desktop** (running)
- **VS Code** (recommended)
- **Git** (for version control)

### API Keys & Accounts
You'll need accounts and API keys for:

1. **Apify** - For scraping Upwork jobs
   - Sign up: https://apify.com/
   - Get API token from: https://console.apify.com/account/integrations
   - **Note**: We use the FREE actor `nahom.network~upwork-job-finder` instead of the paid `neatrat~upwork-job-scraper` to avoid credit costs

2. **OpenAI** - For AI-powered job scoring
   - Sign up: https://platform.openai.com/
   - Get API key from: https://platform.openai.com/api-keys
   - Ensure GPT-4o access

3. **Airtable** - For storing job data
   - Sign up: https://airtable.com/
   - Get Personal Access Token or OAuth credentials
   - Create a base with a table for job storage

4. **Slack** (Optional) - For high-priority job alerts
   - Create a workspace: https://slack.com/
   - Create an incoming webhook or use Slack API

## 🚀 Installation

### Step 1: Clone or Download the Project

```bash
# Navigate to your workspace
cd c:\Users\sahil\Desktop\EDXSO

# Or clone from GitHub if uploaded
git clone <your-repo-url>
cd EDXSO
```

### Step 2: Configure Environment Variables

```bash
# Copy the example environment file
copy .env.example .env

# Edit .env with your actual credentials
notepad .env
```

Fill in your actual API keys in `.env`:

```env
APIFY_API_TOKEN=apify_api_YOUR_ACTUAL_TOKEN_HERE
OPENAI_API_KEY=sk-YOUR_ACTUAL_OPENAI_KEY_HERE
AIRTABLE_API_KEY=patYOUR_ACTUAL_AIRTABLE_TOKEN_HERE
AIRTABLE_BASE_ID=appYOURBASEID
AIRTABLE_TABLE_NAME=Upwork Leads
```

### Step 3: Start Docker

Make sure Docker Desktop is running, then:

```bash
docker-compose up -d
```

This will:
- Download the n8n Docker image
- Start the n8n container
- Expose n8n on port 5678
- Mount persistent storage

### Step 4: Access n8n

Open your browser and navigate to:
```
http://localhost:5678
```

On first access:
1. Create an admin account
2. Set up your workspace

## ⚙️ Configuration

### Import the Workflow

1. In n8n, click **"Workflows"** in the left sidebar
2. Click **"Import from File"** or use the import button
3. Select `Upwork-Automation-Enhanced.json`
4. The workflow will appear on your canvas

### Configure Credentials

You need to set up credentials for each service:

#### 1. Apify API
- Go to **Settings → Credentials**
- Click **"Add Credential"**
- Select **"Apify API"**
- Enter your Apify API token
- Save as "Apify API"

#### 2. OpenAI API
- Add new credential
- Select **"OpenAI"**
- Enter your OpenAI API key
- Model: `gpt-4o`
- Save as "OpenAI API"

#### 3. Airtable OAuth2
- Add new credential
- Select **"Airtable OAuth2 API"**
- Follow OAuth flow or use Personal Access Token
- Save as "Airtable OAuth2"

#### 4. Slack API (Optional)
- Add new credential
- Select **"Slack API"**
- Follow OAuth flow or use webhook URL
- Save as "Slack API"

### Update Workflow Nodes

1. **HTTP Request Node** (Apify): Ensure it uses `{{ $env.APIFY_API_TOKEN }}`
2. **OpenAI Chat Model**: Link to your OpenAI credentials
3. **Airtable Node**: Link to your Airtable credentials
4. **Slack Node**: Link to your Slack credentials (if using)

### Configure Airtable Base

Create a table in Airtable with these fields:

| Field Name | Type | Description |
|------------|------|-------------|
| Title | Single line text | Job title |
| Description | Long text | Job description |
| URL | URL | Link to job posting |
| Score | Number | AI score (1-10) |
| Priority | Single select | High/Medium/Low |
| Reason | Long text | AI reasoning |
| Budget | Single line text | Project budget |
| Hourly Rate | Single line text | Hourly rate |
| Job Type | Single line text | Fixed/Hourly |
| Experience Level | Single line text | Entry/Intermediate/Expert |
| Time Job Created | Single line text | When posted |

## 🎮 Usage

### Manual Execution

1. Open the workflow in n8n
2. Click **"Execute Workflow"** button
3. Watch the nodes execute in sequence
4. Check Airtable for new records

### Scheduled Execution

The workflow is configured to run automatically every 8 hours.

To activate:
1. Open the workflow
2. Toggle the **"Active"** switch in the top right
3. The workflow will now run on schedule

### Monitor Executions

- Go to **"Executions"** in the left sidebar
- View execution history, logs, and errors
- Click any execution to see detailed node-by-node results

## 🔧 Workflow Details

### Workflow Flow

```
Every 8 Hours (Trigger)
    ↓
Fetch Upwork Jobs (Apify API)
    ↓
Edit Fields (Extract data)
    ↓
Filter Jobs by Time (Last 6 hours)
    ↓
AI Score Job (OpenAI GPT-4o)
    ↓
Save to Airtable
    ↓
Check If High Priority
    ↓
Send Slack Alert (if High Priority)
```

### Node Descriptions

1. **Every 8 Hours**: Schedule trigger that runs every 8 hours
2. **Fetch Upwork Jobs (Apify)**: HTTP request to Apify API to scrape Upwork
3. **Edit Fields**: Extracts and formats job data (title, description, budget, etc.)
4. **Filter Jobs by Time**: Only passes jobs posted within last 6 hours
5. **AI Score Job**: Uses GPT-4o to score job relevance (1-10 scale)
6. **Save to Airtable**: Creates a record in Airtable with all job details
7. **Check If High Priority**: IF condition checking for "High" priority
8. **Send Slack Alert**: Sends formatted alert to Slack channel

### AI Scoring Logic

The AI evaluates jobs based on:

**High Priority (8-10/10)**:
- Directly mentions n8n, automation, AI, chatbots, Twilio, Retell
- Clear scope, completable in under 48 hours
- Reasonable budget ($100+ or $20+/hour)
- Good client indicators

**Medium Priority (5-7/10)**:
- Some automation elements, unclear scope
- Lower budget but portfolio-worthy
- New client or longer timeline
- API integration work

**Low Priority (1-4/10)**:
- Not automation-related
- WordPress/design/generic dev
- Very low budget or red flags
- Skills mismatch

## 🚀 Enhancements Implemented

### Mandatory Enhancements (2 implemented)

#### 1. ✅ Slack Alert for High-Priority Jobs
- Automatically sends detailed Slack messages when High priority jobs are found
- Includes job title, score, reasoning, budget, and direct link
- Enables quick response to best opportunities

#### 2. ✅ Budget Pre-Filtering & Tracking
- Captures budget and hourly rate information
- Logs financial details to Airtable
- AI considers budget in scoring decisions

### Additional Improvements

- **Enhanced AI Prompt**: More detailed scoring criteria for automation relevance
- **Environment Variables**: Secure credential management via .env
- **Time Filtering**: Ensures only fresh jobs (last 6 hours) are processed
- **Better Field Extraction**: Captures more job metadata
- **Production-Ready Docker Setup**: Easy deployment and scaling

## 🛠️ Troubleshooting

### Common Issues

**Problem**: Docker won't start
- **Solution**: Make sure Docker Desktop is running and updated

**Problem**: Can't access n8n at localhost:5678
- **Solution**: Check if port 5678 is available: `netstat -ano | findstr :5678`

**Problem**: Workflow fails at Apify node
- **Solution**: Verify your Apify API token in .env file
- Check Apify account has sufficient credits

**Problem**: OpenAI node returns errors
- **Solution**: Verify API key is correct
- Ensure you have GPT-4o access enabled
- Check OpenAI account has sufficient credits

**Problem**: Airtable node fails
- **Solution**: Verify base ID and table name
- Check OAuth credentials are valid
- Ensure table fields match workflow expectations

**Problem**: No jobs being processed
- **Solution**: Check Apify is finding jobs with your search query
- Verify time filter isn't too restrictive
- Test with manual execution first

### Debugging Tips

1. **Check Execution Logs**: View detailed error messages in n8n Executions tab
2. **Test Individual Nodes**: Use "Test step" to debug specific nodes
3. **Verify Credentials**: Re-authenticate if credentials expire
4. **Check API Limits**: Monitor API usage for rate limits
5. **Review Docker Logs**: `docker-compose logs -f n8n`

## 🔮 Future Improvements

Potential enhancements for production deployment:

1. **Duplicate Detection**: Prevent re-processing the same jobs
2. **Email Alerts**: Alternative to Slack for notifications
3. **Advanced Filtering**: Pre-filter by keywords, budget thresholds
4. **Custom Skills Scoring**: Weighted scoring based on specific skills
5. **Analytics Dashboard**: Track success rates and application metrics
6. **Auto-Proposal Generation**: Use AI to draft custom proposals
7. **Multi-Query Support**: Search multiple skill combinations
8. **Database Integration**: PostgreSQL or MongoDB for advanced queries
9. **Webhook Triggers**: Real-time processing instead of scheduled
10. **Error Recovery**: Automatic retry logic for failed API calls




