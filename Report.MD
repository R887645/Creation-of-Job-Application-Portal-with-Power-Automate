
---
# Job Application Portal — Power Automate + Google Drive

A low-code job application portal built entirely with Microsoft Power Automate. Guided by [@theoyibooke](https://www.youtube.com/@theoyibooke) on YouTube, I replicated a fully functional hiring portal, no server, no database, no code required. The frontend is a Power Automate instant flow form, which collects and stores all applicant data across Google Drive, Google Sheets, and OneDrive.

---

## 🧩 Features

- Applicants submit their details through a Power Automate instant flow form
- All submissions are automatically logged to a live Google Sheets tracker
- A dedicated Google Drive folder is created per applicant with their CV saved inside
- Backup copies of every submission are stored on OneDrive
- Applicants receive an automatic confirmation email with their reference ID
- HR receives an instant notification with a direct link to the applicant's Drive folder
- Failed submissions are caught by an error branch and logged automatically

---

## ⚙️ Architecture

- **Frontend:** Power Automate Instant Flow (Manual Trigger as form interface)
- **Storage:** Google Drive (folder per applicant) + OneDrive (backup)
- **Database:** Google Sheets (live applications tracker)
- **Automation:** Power Automate (end-to-end flow handling all logic)


## 🖥️ Part 1: Frontend & Storage Setup

I started by setting up the storage layer in Google Drive, creating a folder structure to organise all incoming applications:

```
/Job Application Portal/
    ├── Submissions/    ← one folder auto-created per applicant
    └── CVs/            ← raw CV uploads
```

I then created a Google Sheet named **Applications Tracker** to serve as the live database, with columns mapped directly to every field in the form:

`Application ID` · `First Name` · `Last Name` · `Email` · `Phone` · `Position Applied` · `LinkedIn` · · `Cover Letter` · `CV ` · `Date of Birth` ·

For the frontend, I built an **Instant Cloud Flow** in Power Automate using the **"Manually trigger a flow"** trigger with all the form fields added as custom inputs. This generates a shareable link that acts as the live portal, no UI framework needed. I then connected the Google Drive connector and authenticated it to the `/Job Application Portal/` folder so the flow could read and write to the right directories.

![Google Sheets](https://github.com/R887645/Creation-of-Job-Application-Portal-with-Power-Automate/blob/main/MyHire%20sheet.png?raw=true)

---

## ⚙️ Part 2 — Backend Automation Flow

I built the full backend as a single Power Automate flow. I started by creating an **Initialize Variable** action to auto-generate a unique Submission ID for every entry using the expression `concat('APP-', formatDateTime(utcNow(), 'yyyyMMdd-HHmmss'))`. I then added a **Compose** action to structure and clean all form fields — using `trim()` to remove whitespace, `toUpper()` to normalise the position field, and `utcNow()` to timestamp each submission.

From there, the flow writes the structured data to Google Sheets via **Insert Row**, creating a new logged entry for every applicant with a default status of `Pending`. It then creates a named folder for the applicant inside Google Drive under `/Submissions/`, uploads their CV directly into that folder, and saves a backup copy of both the CV and a plain-text data summary to OneDrive.

Once storage is handled, the flow sends two emails — a confirmation to the applicant with their reference ID and position, and an internal notification to the HR team with all applicant details and a direct link to their Google Drive folder. Finally, I added a **Parallel Branch** configured to run only on failure, which logs any error as a JSON file in OneDrive and sends an alert to the admin so no submission is ever silently lost.

![Google Sheets](https://github.com/R887645/Creation-of-Job-Application-Portal-with-Power-Automate/blob/main/powr%20automate%20flow.png?raw=true)

![My Image](https://github.com/R887645/Creation-of-Job-Application-Portal-with-Power-Automate/blob/main/Real%20Job%20App.png?raw=true)

![My Image](https://github.com/R887645/Creation-of-Job-Application-Portal-with-Power-Automate/blob/main/Response%20after%20submission.png?raw=true)


## The Job Application Portal Link

This project is LIVE [My Project](https://413bc38af3b4e87580d09a649b333f.0d.environment.api.powerplatform.com/powerautomate/automations/direct/workflows/59d6fec6366c4bbaa13dd5b60bf0066a/triggers/manual/paths/invoke?api-version=1&sp=/triggers/manual/run&sv=1.0&sig=_43czEbm8ZFCPqtmFI9S-gVOkwbf0AqSwf2PeNuQWPs).

**[View a SnapShot Video of the project Here](https://drive.google.com/file/d/1GbUsrwvxDifRlnDifpEId16taGyRVNkd/view?usp=sharing)**

---

---

## 📋 Form Fields

| Field | Type |
|---|---|
| First Name | Text |
| Last Name | Text |
| Email Address | Text |
| Phone Number | Text |
| Position Applied For | Text |
| LinkedIn Profile | Text |
| Portfolio / Website | Text |
| Years of Experience | Text |
| Cover Letter | Text (long) |
| CV Upload | File |

---

## 📸 Screenshots

| View | File |
|---|---|
| Power Automate flow canvas | `pa-flow.png` |
| Trigger input fields | `pa-trigger.png` |
| Google Sheets tracker | `sheets.png` |
| Google Drive folders | `drive.png` |
| OneDrive backup | `onedrive.png` |

---

## 💡 Built With

- Microsoft Power Automate
- Google Drive
- Google Sheets
- Microsoft OneDrive

---

## 🙏 Credits

Guided by [@theoyibooke](https://www.youtube.com/@theoyibooke) on YouTube, this project was built following his tutorial as a hands-on learning exercise in Power Automate and no-code automation.

---

*Crafted with by Rofiah Techie Hub*

---

## 💡 Key Learnings

- **Power Automate's manual trigger** functions as a complete no-code form frontend, eliminating the need for any external form builder or web UI.
- **Google Drive + Google Sheets** serve as a capable file system and live database when paired with Power Automate's connectors.
- **OneDrive as a secondary backup layer** adds critical resilience — essential for any production-grade automation workflow.
- Using **Compose actions** to structure and normalize data before writing to storage makes flows easier to debug and outputs more consistent.
- **Parallel error branches** ensure no submission is silently dropped if a connector step fails mid-flow.
- Auto-generating a **Submission ID** per entry makes tracking, filtering, and HR follow-up significantly more efficient than relying on names or timestamps alone.

---

##  Author

Built by **AbdulAzeez Rofiah**
- GitHub: [@yourusername](https://rofiahportfolio.mystrikingly.com/
- LinkedIn: [View Your LinkedIn](https://www.linkedin.com/in/rofiah-abdul-azeez/)

---

## The Job Application Portal Link

This project is LIVE [My Project](https://413bc38af3b4e87580d09a649b333f.0d.environment.api.powerplatform.com/powerautomate/automations/direct/workflows/59d6fec6366c4bbaa13dd5b60bf0066a/triggers/manual/paths/invoke?api-version=1&sp=/triggers/manual/run&sv=1.0&sig=_43czEbm8ZFCPqtmFI9S-gVOkwbf0AqSwf2PeNuQWPs).

**[View a SnapShot Video of the project Here](https://drive.google.com/file/d/1GbUsrwvxDifRlnDifpEId16taGyRVNkd/view?usp=sharing)**
