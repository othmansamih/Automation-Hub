# Job Hunt Automation

An intelligent n8n workflow that automates the entire job application process - from scraping LinkedIn jobs to generating tailored resumes, cover letters, and reaching out to relevant contacts.

## 🎯 What This Workflow Does

This automation streamlines your job search by:

1. **Scraping & Filtering Jobs** - Automatically collects job postings from LinkedIn and filters them based on your criteria (experience level, job type, field relevance)
2. **Intelligent Resume Customization** - Analyzes job descriptions and selects the most relevant projects from your portfolio to include in your resume
3. **Personalized Cover Letters** - Generates tailored cover letters in English or French based on the job posting language
4. **Contact Discovery** - Finds relevant hiring managers and team leads at target companies
5. **Professional Outreach** - Creates personalized LinkedIn messages and email drafts for networking

## ✨ Key Features

- **Multi-language Support** - Automatically detects job language and generates materials in French or English
- **LaTeX Resume Generation** - Creates professional PDF resumes from customizable templates
- **Smart Contact Filtering** - Identifies AI/ML/Data Science managers and leadership roles
- **Email Discovery** - Finds professional email addresses for contacts
- **Organized Tracking** - Saves all materials and contacts to Google Sheets for easy management
- **Draft Email Creation** - Automatically creates Gmail drafts with attachments ready to send

## 🛠️ Setup Guide

**Author: [Othman SAMIH](https://agentrius.com/)**

Follow these steps to complete your setup:

### 1. Connect Google Credentials
* Allow access to your **Google Drive**, **Google Sheets**, **Google Docs**, and **Gmail**.
* This enables the workflow to:
  * Save job offers and application materials to Google Sheets
  * Create and update cover letters in Google Docs
  * Upload resumes and cover letters to Google Drive
  * Create email drafts in Gmail

### 2. Connect [OpenRouter](https://openrouter.ai/) API Key
* This enables the chat models (GPT-4.1, GPT-5-mini, GPT-4.1-mini) used for:
  * Job offer classification and language detection
  * Resume project selection and customization
  * Cover letter generation
  * LinkedIn contact position verification

### 3. Connect [Apify](https://apify.com/) API Key
* Required for the **LinkedIn Jobs Scraper** to collect job postings from LinkedIn.

### 4. Connect [BrightData](https://brightdata.com/) API Key
* Used to filter and retrieve LinkedIn profiles of relevant contacts at target companies.

### 5. Connect [Hunter](https://hunter.io/) API Key
* Enables email discovery for LinkedIn contacts at target companies.

### 6. Connect [Tavily](https://tavily.com/) API Key
* Used to search for company domain names when finding contact emails.

### 7. Install LaTeX
* Ensure `pdflatex` is installed on your n8n server/environment.
* The workflow uses LaTeX to generate PDF resumes from templates.
* Required directories: `/tmp/CV-LATEX/` and `/tmp/CV-PDF/`

### 8. Set Up Google Sheets Structure
* Create a Google Sheet with these tabs:
  * **"Linkedin Job Offers"** - All scraped job offers
  * **"Adapted Linkedin Job Offers"** - Filtered relevant jobs with resume/cover letter links
  * **"People To Reach"** - Contact information and outreach messages

### 9. Prepare Resume Templates
* Store your resume photo (`photo.png`) in Google Drive
* The workflow expects LaTeX resume templates in both French and English
* Prepare a list of your projects with descriptions for the AI to select from

---

## 🚀 How to Use

1. **Trigger the Workflow**: Send a POST request to the webhook endpoint with LinkedIn job search URLs:
```json
{
  "urls": [
    "https://www.linkedin.com/jobs/search/?keywords=AI%20Engineer&location=France"
  ]
}
```

2. **Automatic Processing**: The workflow will:
   - Scrape all job postings from the provided URLs
   - Filter jobs based on your criteria
   - Generate customized resumes and cover letters
   - Find relevant contacts at each company
   - Create personalized outreach messages
   - Generate Gmail drafts ready to send

3. **Review & Send**: Check your Google Sheets for tracking and Gmail for draft emails with all attachments included.

## 📊 Workflow Structure

The workflow is organized into 11 main groups:

1. **Job Scraping & Filtering** - Collects and validates job postings
2. **French Resume Generation** - Creates French-language resumes
3. **English Resume Generation** - Creates English-language resumes
4. **French Cover Letter Generation** - Generates French cover letters
5. **English Cover Letter Generation** - Generates English cover letters
6. **LinkedIn Contact Discovery** - Finds relevant contacts
7. **Contact Validation** - Verifies contact relevance
8. **LinkedIn Message Generation** - Creates connection messages
9. **Email Discovery & Message Generation** - Finds emails and creates drafts
10. **Contact & Materials Storage** - Organizes all data
11. **Email Draft Creation** - Prepares Gmail drafts

## 🎨 Customization

- **Job Filtering Criteria**: Modify the filtering logic in the "Filter Job Offers" node
- **Resume Projects**: Update the project list in the resume builder nodes
- **Contact Search**: Adjust the position filters in "Filter Linkedin People"
- **Message Templates**: Customize the French/English message nodes

## 📝 Requirements

- n8n instance (self-hosted or cloud)
- Active API keys for all services
- LaTeX installation on the server
- Google Workspace access
- Resume templates in LaTeX format

## 🔒 Privacy & Security

- All credentials are stored securely in n8n
- Personal data is only stored in your Google Drive/Sheets
- API keys are never exposed in the workflow
- Email drafts are created but not sent automatically

## 🤝 Contributing

Feel free to fork this workflow and customize it for your needs. Suggestions and improvements are welcome!

## 📧 Contact

**Author**: Othman SAMIH  
**Website**: [agentrius.com](https://agentrius.com/)  
**LinkedIn**: [linkedin.com/in/othmansamih](https://linkedin.com/in/othmansamih)

---

⭐ If this workflow helps you land your dream job, consider giving it a star!
