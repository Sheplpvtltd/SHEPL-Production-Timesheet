# SHEPL Timesheet Dashboard — Dashboard Management System

A web-based, permission-controlled timesheet dashboard for **SH Engitech Pvt. Ltd.**, rebuilt from the `v1_1_Timesheet.xlsm` Excel workbook.

**Features:** blank-start data · bulk Excel upload with strict validation · downloadable bulk-upload format templates per page · auto-backup every 30 seconds · full Excel backup download · department-wise page access · column-wise access control · Dropdown master page · activity log with old → new values and Excel export · real-time sync · SHEPL logo colour theme.

---

## Folder structure

```
dashboard-management-system/
├── index.html          # The complete dashboard (single-file app)
├── firestore.rules     # Firebase security rules (trial + production stages)
├── .env.example        # Firebase variable names (no real secrets)
├── .gitignore          # Keeps secrets and junk out of GitHub
├── docs/
│   └── (screenshots, notes)
└── README.md           # This file
```

---

## How the dashboard runs

`index.html` works in two modes, automatically:

| Mode | When | Real-time behaviour |
|---|---|---|
| **Local mode** | Firebase config not filled in | Changes sync live across browser tabs on the same computer (plus 5-second polling). Data is saved in the browser and auto-backed-up every 30s. |
| **Firebase mode** 🔥 | You paste your Firebase config into `index.html` (see Part B, Step 8) | Every change by any user appears **instantly** for all other logged-in users, on any computer, via Cloud Firestore live listeners. |

The sidebar footer always shows which mode you are in.

---

# Part A — GitHub Setup (step by step)

**Step 1.** Create a GitHub account at https://github.com (or log in to your existing one).

**Step 2.** Click **New repository**. Repository name: `dashboard-management-system`.

**Step 3.** Set the repository to **Private** (recommended) unless public access is required. Do **not** tick "Add a README" (we already have one). Click **Create repository**.

**Step 4.** On your computer, open this project folder in VS Code, or open a terminal / Command Prompt inside the folder.

**Step 5.** Run these commands one by one:

```bash
git init
git add .
git commit -m "Initial dashboard setup"
git branch -M main
git remote add origin https://github.com/<your-username>/dashboard-management-system.git
git push -u origin main
```

(Replace `<your-username>` with your GitHub username. Git will ask you to log in the first time.)

**Step 6.** Refresh the GitHub page — confirm `index.html`, `firestore.rules`, `.env.example`, `.gitignore`, and `README.md` are all visible.

**Step 7.** The included `.gitignore` already blocks `node_modules`, `.env`, `.env.local`, `dist`, `build`, `.firebase`, and `.DS_Store` from ever being uploaded.

**Step 8.** ⚠️ **Never commit real Firebase keys or passwords.** `.env.example` shows the variable *names* only — real values stay on your computer. If you paste your Firebase config directly into `index.html` (the simple approach for this single-file app), keep the repository **Private**.

**Version history:** every future change you make, commit with a clear message (`git add . && git commit -m "what changed" && git push`). GitHub keeps the full history so any previous version can be restored.

---

# Part B — Firebase Setup (step by step)

**Step 1.** Go to https://console.firebase.google.com and sign in with a Google account.

**Step 2.** Click **Add project** → name it `Dashboard Management System` → continue (Google Analytics optional) → **Create project**.

**Step 3.** On the project overview page, click the **Web icon `</>`** to register a Web App. Nickname: `shepl-dashboard`. (No hosting needed yet.) Click **Register app**.

**Step 4.** Firebase shows a configuration block like:

```js
const firebaseConfig = {
  apiKey: "AIza....",
  authDomain: "dashboard-management-system.firebaseapp.com",
  projectId: "dashboard-management-system",
  storageBucket: "dashboard-management-system.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abcdef"
};
```

Copy these six values.

**Step 5.** In the Firebase console left menu: **Build → Firestore Database → Create database**. Choose a location near you (e.g. `asia-south1` Mumbai). Start in **test mode** for the first trial (you will lock it down in Step 7).

**Step 6.** Open `index.html` in a text editor. Near the top of the `<script>` section, find:

```js
const FIREBASE_CONFIG = {
  apiKey: "",
  authDomain: "",
  projectId: "",
  ...
};
```

Paste your six values in. Save the file. **That's the whole connection** — the dashboard detects the config and switches to Firebase mode automatically (sidebar shows 🔥 Firebase — live sync).

**Step 7.** Apply security rules: Firebase console → **Firestore Database → Rules** tab → replace the contents with the **Stage 1** rules from `firestore.rules` in this repo → **Publish**. (Stage 2 production rules, also in that file, apply when the full Firebase Authentication build is done — see "What comes next" below.)

**Step 8.** Test real-time updates:
1. Open `index.html` in two different browsers (e.g. Chrome and Edge), or on two different computers.
2. Log in as Admin in one, as a department user in the other.
3. Change anything in one window — add an employee, submit a timesheet, change a permission.
4. Confirm the other window updates **within 1–2 seconds without refreshing**.

**Step 9.** Test backup: leave the dashboard open for a minute, then check Firestore console → `dashboard` collection → the backup snapshot document updates every 30 seconds. In the dashboard, Admin → **Backup & Export → Download Excel Backup Now** produces the full Excel file anytime.

---

## Bulk upload workflow (every data page)

```
Download Format  →  Fill Data in Excel  →  Upload Excel  →  Validate  →  Fix errors if any  →  Imported ✅
```

- Every page with bulk upload has a **📄 Download Bulk Upload Format** button producing an Excel template with the exact column names in the exact order, mandatory columns marked `*`, and a **Guide sheet** listing allowed dropdown values.
- Validation checks: required columns present, required cells filled, dates real, numbers numeric, dropdown values matching the Dropdown master page, employees/projects referenced in timesheets actually existing, and duplicates (within the file and against existing data).
- Nothing invalid is imported silently — a result table shows **Row · Column · Error · Required correction** for every rejected row.

---

## What comes next (full production build)

This single-file app is the working system with live Firebase data sync. The complete production build from the approved development plan adds: **Firebase Authentication** (real per-user passwords / email login replacing the account-picker), **Cloud Functions** for server-side report aggregation on large data, **per-collection Firestore structure** (the Stage 2 rules in `firestore.rules`), and **CI/CD deployment** via GitHub Actions → Firebase Hosting. The data model and permissions in this app were designed to migrate into that build without loss.

---

*SH Engitech Pvt. Ltd. — Turnkey Solutions for Corrosive Applications*
