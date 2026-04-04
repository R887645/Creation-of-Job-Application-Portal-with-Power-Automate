# 💼 Job Application Portal — Power Automate + Google Drive

> A fully functional job application portal built without traditional backend infrastructure. The frontend is powered by Google Drive and the automation backend is handled entirely through Microsoft Power Automate — collecting, validating, and storing applicant data seamlessly.

---Report.MD

## 📌 Project Overview

This project replicates a real-world job application portal by combining a custom-built frontend interface with a Power Automate workflow backend. Applicants can fill out and submit their details through a web interface, and all submission data is automatically captured and stored in Google Drive — no server, no database, no code deployment required.

---

## 🗂️ Table of Contents

- [Tech Stack](#-tech-stack)
- [Part 1: Frontend — Building the Interface with Google Drive](#-part-1-frontend--building-the-interface-with-google-drive)
- [Part 2: Backend — Power Automate Flow for Data Collection & Storage](#-part-2-backend--power-automate-flow-for-data-collection--storage)
- [Project Structure](#-project-structure)
- [Screenshots](#-screenshots)
- [How to Replicate This Project](#-how-to-replicate-this-project)
- [Key Learnings](#-key-learnings)

---

## 🛠️ Tech Stack

| Layer | Tool |
|---|---|
| Frontend Interface | Google Drive (Google Forms / Google Sites) |
| Automation Engine | Microsoft Power Automate |
| Data Storage | Google Drive (Google Sheets / Drive Folder) |
| Trigger Mechanism | Form Submission / HTTP Connector |
| File Handling | Power Automate File Actions |

---

## 🖥️ Part 1: Frontend — Building the Interface with Google Drive

This section covers how the applicant-facing side of the portal was built and connected to Google Drive.

### Step 1 — Design the Application Form

- Opened **Google Forms** and created a new form titled *"Job Application Portal"*.
- Added all required applicant fields:
  - Full Name
  - Email Address
  - Phone Number
  - Position Applied For
  - Years of Experience
  - Upload CV / Resume (File upload field)
  - Cover Letter (Long answer text)
- Enabled **file upload** to allow applicants to attach their CVs directly to a linked Google Drive folder.
- Applied consistent branding — custom header, logo, and theme color to match a real job portal aesthetic.

### Step 2 — Configure Google Drive Storage for Submissions

- Opened **Google Drive** and created a dedicated folder structure:
  ```
  /Job Portal/
    ├── Applications/
    │     └── [Auto-created per applicant]
    └── CVs/
  ```
- Linked the Google Form's file upload field to the `/Job Portal/CVs/` folder so all uploaded CVs are stored automatically.
- Enabled **Google Sheets response collection**: navigated to the form's *Responses* tab → clicked the Sheets icon → linked it to a new Google Sheet named `Applications Tracker`.

### Step 3 — Build the Public-Facing Portal Page (Google Sites)

- Opened **Google Sites** and created a new site titled *"Careers Portal"*.
- Embedded the Google Form directly onto the landing page using *Insert → Forms → [Select Form]*.
- Added supporting content: job listings, company description, and application instructions.
- Published the site to generate a shareable public URL — this acts as the live frontend of the portal.

### Step 4 — Test the Frontend

- Opened the published site URL in an incognito browser window.
- Submitted a test application with a sample CV upload.
- Verified the response appeared in the linked Google Sheet and the CV was saved to the correct Drive folder.

---

## ⚙️ Part 2: Backend — Power Automate Flow for Data Collection & Storage

This section covers the automated workflow built in Microsoft Power Automate to process, enrich, and store every application submission.

### Step 1 — Create a New Automated Cloud Flow

- Logged into **Power Automate** at [make.powerautomate.com](https://make.powerautomate.com).
- Clicked **+ Create → Automated Cloud Flow**.
- Named the flow: `Job Application Portal — Data Collector`.
- Selected the trigger: **"When a new response is submitted"** (Google Forms connector).
- Connected the Google account and selected the target form: *Job Application Portal*.

### Step 2 — Get Full Response Details

- Added the action: **Google Forms → Get response details**.
  - Form ID: *Job Application Portal*
  - Response ID: (dynamic value from the trigger — `Response Id`)
- This step fetches all the individual field values (name, email, position, etc.) from the form response.

### Step 3 — Parse and Organize Applicant Data

- Added a **Compose** action to build a structured JSON object from the response fields:
  ```json
  {
    "Full Name": "<Name field>",
    "Email": "<Email field>",
    "Phone": "<Phone field>",
    "Position": "<Position field>",
    "Experience": "<Experience field>",
    "Submission Date": "<utcNow()>",
    "CV Link": "<File upload URL>"
  }
  ```
- Used Power Automate **expressions** for data formatting:
  - `utcNow()` — to timestamp each submission
  - `toUpper()` — to normalize the position field
  - `trim()` — to clean whitespace from text inputs

### Step 4 — Append Data to Google Sheets (Primary Storage)

- Added the action: **Google Sheets → Insert Row**.
  - File: *Applications Tracker* (the linked Google Sheet)
  - Sheet: `Sheet1`
  - Mapped each dynamic column to the corresponding response field (Name → Column A, Email → Column B, etc.)
- This ensures every new application is logged as a new row in the tracker spreadsheet in real time.

### Step 5 — Create a Dedicated Folder per Applicant in Google Drive

- Added the action: **Google Drive → Create Folder**.
  - Parent Folder: `/Job Portal/Applications/`
  - Folder Name: Dynamic — `<Full Name> - <Submission Date>`
- This creates a personal folder for each applicant inside the Applications directory.

### Step 6 — Copy CV File to Applicant Folder

- Added the action: **Google Drive → Copy File**.
  - File: (CV file URL from the form response)
  - Destination Folder: (Folder ID returned from Step 5)
- This moves the uploaded CV from the general CVs folder into the applicant's dedicated folder for easy retrieval.

### Step 7 — Send Confirmation Email to Applicant

- Added the action: **Office 365 Outlook → Send an Email (V2)** *(or Gmail connector)*.
  - To: `<Email field from response>`
  - Subject: `Application Received — <Position field>`
  - Body:
    ```
    Hi <Full Name>,

    Thank you for applying for the <Position> role. 
    We have received your application and our team will be in touch shortly.

    Best regards,
    The Hiring Team
    ```
- This gives applicants immediate confirmation that their submission was successful.

### Step 8 — Send Internal Notification to HR

- Added a second **Send Email** action targeted at the internal HR email address.
- Subject: `New Application: <Full Name> — <Position>`
- Body includes all applicant details + a direct link to the Google Drive folder.

### Step 9 — Save and Test the Flow

- Clicked **Save** in Power Automate.
- Submitted a new test response via the Google Sites portal.
- Monitored the **Flow Run History** to confirm all actions completed successfully (green checkmarks on every step).
- Verified:
  - ✅ New row appeared in Google Sheets
  - ✅ New applicant folder created in Google Drive
  - ✅ CV copied to the correct folder
  - ✅ Confirmation email received by applicant
  - ✅ HR notification email delivered

---

## 📁 Project Structure

```
Job-Application-Portal/
│
├── Frontend (Google Drive)
│   ├── Google Form          → Applicant intake form
│   ├── Google Sites Page    → Public-facing careers portal
│   └── Google Drive Folders → CV storage + applicant folders
│
├── Backend (Power Automate)
│   ├── Trigger              → New Google Form response
│   ├── Get Response Details → Parse applicant fields
│   ├── Compose              → Structure JSON data
│   ├── Insert Row           → Log to Google Sheets
│   ├── Create Folder        → Applicant folder in Drive
│   ├── Copy File            → Move CV to applicant folder
│   ├── Send Email (x2)      → Applicant confirmation + HR alert
│   └── Flow Run History     → Monitoring & debugging
│
└── README.md
```

---

## 📸 Screenshots

> *(Add screenshots of your Google Sites portal, the Google Form, the Power Automate flow canvas, and the Google Sheets tracker here)*

| View | Description |
|---|---|
| `portal-home.png` | Public-facing Google Sites careers page |
| `application-form.png` | Google Form with all input fields |
| `power-automate-flow.png` | Full Power Automate flow canvas |
| `sheets-tracker.png` | Google Sheets applications log |
| `drive-folders.png` | Applicant folders in Google Drive |

---

## 🚀 How to Replicate This Project

1. **Clone or fork** this repository.
2. Create a Google Form using the field structure described in Part 1.
3. Link the form responses to a new Google Sheet.
4. Set up the Google Drive folder structure as described.
5. Publish a Google Sites page and embed your form.
6. In Power Automate, create a new Automated Cloud Flow using the steps in Part 2.
7. Connect your Google and email accounts as required connectors.
8. Test end-to-end with a sample submission.

---

## 💡 Key Learnings

- **No-code automation** is powerful enough to replicate real-world application systems without writing a single line of backend code.
- **Power Automate's Google connectors** (Forms, Sheets, Drive) work seamlessly together when properly authenticated.
- **Dynamic expressions** like `utcNow()`, `trim()`, and `toUpper()` are essential for data quality in automated flows.
- **Google Sites + Google Forms** provide a surprisingly capable frontend when you don't need a custom-coded UI.
- Structuring Drive folders per applicant makes HR review significantly faster and more organized.

---

## 👤 Author

Built by **[Your Name]**
- GitHub: [@yourusername](https://github.com/yourusername)
- LinkedIn: [Your LinkedIn](https://linkedin.com)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).
