# Legal-automation-suite
Excel-driven automation for law-firm workflows: Clio notes and calendar, Gmail drafts, document uploads, and Airtable. Python + FastAPI

A local web app that turns an **Excel sheet of immigration cases** into finished work: Clio notes and calendar events, Gmail drafts with attachments, uploaded documents, SMS messages and Airtable deadlines — in one click, with a live log.

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?logo=fastapi&logoColor=white)
![Selenium](https://img.shields.io/badge/Selenium-43B02A?logo=selenium&logoColor=white)
![Gmail API](https://img.shields.io/badge/Gmail%20API-EA4335?logo=gmail&logoColor=white)
![Airtable](https://img.shields.io/badge/Airtable-18BFFF?logo=airtable&logoColor=white)
![JavaScript](https://img.shields.io/badge/Vanilla%20JS-F7DF1E?logo=javascript&logoColor=black)

---

## 🎬 Demo video

<!-- 📹 PLACEHOLDER — DEMO VIDEO
     File: docs/assets/test-clio-gmail.mp4
     GitHub does not play a repo .mp4 inline. Two options:
       A) Drag the .mp4 into a GitHub issue/release comment, copy the generated URL and paste it on its own line here (it becomes an inline player).
       B) Use a clickable thumbnail (below): add a screenshot as docs/assets/video-thumbnail.png and keep the link. -->

[![Watch the demo](docs/assets/video-thumbnail.png)](docs/assets/test-clio-gmail.mp4)

**What the video shows:**
1. Creating a **Clio note** from a saved preset
2. Creating **Clio calendar events** from a saved preset
3. **Uploading documents** to the matter
4. Creating **Gmail drafts** with a PDF and screenshots attached

---

## ✨ What it does

Law-firm staff used to copy data from a spreadsheet into several systems by hand, case by case. This tool reads the spreadsheet once and does it for every row:

| Module | What it automates |
|---|---|
| 📝 **Clio notes** | Creates a note on the right matter, with subject and body built from Excel columns |
| 📅 **Clio calendar** | Creates hearing/deadline events plus reminders, on one or two Clio accounts |
| ✉️ **Gmail drafts** | Builds drafts (To / CC / subject / body / signature) with a PDF and N images attached |
| 📎 **Upload documents** | Uploads each PDF to the matching Clio matter |
| 🔍 **Data collection** | Reads client data from Clio back into the Excel; finds a matter URL from an A-number |
| 💬 **SMS** | Sends templated text messages through a web gateway (Selenium) |
| 🗂 **Airtable** | Pushes deadlines to Airtable |
| 📸 **Clio screenshots** | Captures the matter calendar page with a real browser (Selenium) |

---

## 🖼 How it works

The idea: **configure a card once, save it as a preset, and let conditional rules pick the right preset for each row.**

### 1. Presets for SMS and Clio notes

<!-- 🖼 IMAGE: docs/assets/preset-sms-webscrapper.png
     ⚠️ UPDATE IMAGE TO HIDE SENSITIVE DATA (names/template text) before publishing -->
![SMS and Clio note presets](docs/assets/preset-sms-webscrapper.png)
*Control panel: message template and Clio note template with `{column}` variables taken from the Excel headers.*

<!-- 🖼 IMAGE: docs/assets/preset-notes-clio.png -->
![Clio note preset](docs/assets/preset-notes-clio.png)
*Saving and updating a note preset.*

### 2. Clio notes — before and after

<!-- 🖼 IMAGES: docs/assets/clio-test-notas-antes.png / docs/assets/clio-test-notas-despues.png -->
| Before | After |
|---|---|
| ![Notes before](docs/assets/clio-test-notas-antes.png) | ![Notes after](docs/assets/clio-test-notas-despues.png) |

*The matter's Notes tab before the run, and the new note created by the tool.*

### 3. Calendar and Gmail presets with conditional rules

<!-- 🖼 IMAGE: docs/assets/preset-calendar-clio-gamil.png
     ⚠️ UPDATE IMAGE TO HIDE SENSITIVE DATA (email addresses in To/CC and template text) before publishing -->
![Calendar and Gmail presets](docs/assets/preset-calendar-clio-gamil.png)
*Calendar card (date/time columns, title, location, reminders, secondary entries) and Gmail card. The **conditional filters** at the bottom choose the preset by cell value, e.g. “if `meta.doc_type` contains `DEADLINE` → use the `deadlines` preset”.*

### 4. Clio calendar — before and after

<!-- 🖼 IMAGES: docs/assets/clio-test-calendario-antes.png / docs/assets/clio-test-calendarClio-despues.png -->
| Before | After |
|---|---|
| ![Calendar before](docs/assets/clio-test-calendario-antes.png) | ![Calendar after](docs/assets/clio-test-calendarClio-despues.png) |

### 5. The test case used in the demo

<!-- 🖼 IMAGE: docs/assets/clio-test-contact.png -->
![Test contact in Clio](docs/assets/clio-test-contact.png)
*All demos run against a **test contact and test matters**, never real client data.*

### 6. Upload documents

<!-- 🖼 IMAGE (to add): docs/assets/upload-documents.png — screenshot of the “Upload files” card and the result in Clio Documents -->
![Upload documents](docs/assets/upload-documents.png)

### 7. Gmail drafts

<!-- 🖼 IMAGE (to add): docs/assets/gmail-draft-result.png — screenshot of a created draft with the PDF and images attached
     ⚠️ Hide sensitive data -->
![Gmail draft with attachments](docs/assets/gmail-draft-result.png)

### 8. Excel input

<!-- 🖼 IMAGE (to add): docs/assets/excel-sample.png — a sample sheet with fake data
     ⚠️ Use fake data only -->
![Excel sample](docs/assets/excel-sample.png)

---

## 🧠 Engineering highlights

- **Presets + conditional rules.** Each card saves its own preset. Rules like *“column X contains Y → preset Z”* pick the preset per row. In data collection, *all* matching rules run in order (a cascade); other cards use the first match.
- **Two Clio accounts with automatic fallback.** If the matter is not found on account 1, the tool retries on account 2.
- **Reliable date and time parsing.** Accepts real Excel dates and text in several formats, 12-hour AM/PM and 24-hour times, parsed manually so it also works on a Spanish-locale Windows.
- **OAuth done properly.** Clio tokens rotate on every refresh; the app saves the new pair and shows a live connection status for Clio and Gmail.
- **Fuzzy search, strict validation.** Clio’s contact search is fuzzy, so results are validated by digits (ignoring spaces/dashes), including abbreviated forms like `246 920 149 / 150 / 153`.
- **Safe long-running jobs.** Jobs run in background threads with a live log over Server-Sent Events, and a **cooperative Stop button** that halts between rows (and between sends) without corrupting the Excel.
- **Random / loop placeholders.** Pick a different value per row (random or round-robin) for templates.
- **Gmail drafts with attachments.** One PDF plus any number of images, each from its own folder; attachment settings are global, so they don’t need to be repeated per preset.
- **Real-browser screenshots.** Selenium uses a dedicated, persistent browser profile (log in once, stays logged in), with full-page capture through the DevTools protocol.
- **No build step.** The frontend is plain HTML + vanilla JS served by FastAPI.

---

## 🏗 Architecture

```
Excel (.xlsx)  ──►  FastAPI (routes)  ──►  background job thread  ──►  Clio API (1 or 2 accounts)
   ▲                     │                        │                ──►  Gmail API (drafts)
   │                     ▼                        │                ──►  Airtable
 live log  ◄──  Server-Sent Events  ◄──  shared state  ◄─────────  Selenium (SMS, screenshots)
```

```
├── app.py                         # FastAPI + uvicorn entry point (port 8000)
├── api/routes.py                  # HTTP endpoints, start jobs in daemon threads
├── core/job.py                    # All job logic (notes, calendar, Gmail, upload, data collection)
├── core/state.py                  # State shared by routes and jobs (Excel, log queue, stop flag)
├── models/schemas.py              # Pydantic request models
├── clio_*.py                      # Clio API clients (notes, calendar, matters, documents, OAuth)
├── gmail_draft.py                 # Gmail API: drafts with PDF + images
├── airtable.py / airtable_client.py
├── Selenium_take_screenshots.py   # Browser screenshots of Clio pages
├── msg.py / login.py              # SMS gateway automation (Selenium)
└── static/ (index.html, app.js)   # UI, no build step
```

**Stack:** Python 3.10+, FastAPI, Uvicorn, Pydantic, Selenium, openpyxl, Google API client, pyairtable, PyMuPDF, vanilla JavaScript.

---

## 🚀 Getting started

```bash
pip install -r requirements.txt
python app.py
```

The browser opens at `http://127.0.0.1:8000`.

1. Open **Credentials** and enter your Clio app **Client ID / Secret** (a second account is optional), then click **Auth Clio** to authorize.
2. For Gmail, place your Google OAuth `credentials.json` in the project folder and authorize once.
3. Load your Excel, build a card, save it as a preset, and press **Run**.

Requirements: Python 3.10+, Google Chrome (for Selenium), a Clio developer application, and a Google Cloud OAuth client for Gmail.

---

## 🔐 Security note

Credentials and tokens (`.env`, `credentials.json`, `gmail_token.pkl`) live **only on the local machine** and must never be committed. The demo uses a test contact and fake data.

---

## 👤 Author

**Dante Zuriel** · [LinkedIn](www.linkedin.com/in/dante-zuriel-núñez-solis-7955b838a)
