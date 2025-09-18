# Aran Media Payroll – GitHub Pages + Google Apps Script

A lightweight payroll app:
- **Frontend:** Pure HTML+JS hosted on **GitHub Pages**
- **Backend:** **Google Apps Script Web App** that reads/writes a Google Sheet
- **Datastore:** Your Google Sheet (Employees, Attendance, Loans, Bonuses, Holidays)

---

## 1) Create Google Sheet (tabs)

Create a Google Sheet with the following tabs and headers (use the CSVs in `/templates` if you like):

### Employees
`EmpCode,Name,DOJ,MonthlySalary,Active`

### Attendance
`Date,EmpCode,Status,Notes`
- `Status`: `P` for Present, `A` for Absent

### Loans
`EmpCode,LoanID,IssuedOn,Amount,MonthlyRecovery`

### Bonuses
`EmpCode,Month,Amount,Note`
- `Month`: format `YYYY-MM` (e.g., `2025-09`)

### Holidays (optional)
`Date,Name`

> **Rules**: Monthly salary; no ESI/PF/OT; no paid leave; attendance-based deductions; loan recovery; ad-hoc bonuses. Saturdays count as working days; Sundays are off.

---

## 2) Apps Script (Web App)

1. In the Google Sheet, go to **Extensions → Apps Script**.
2. Create a new script file and paste contents of `apps_script.gs` from this repo.
3. Replace `SHEET_ID` with your Google Sheet ID and set `API_KEY` to a long random string.
4. **Deploy → New deployment → Web app**
   - **Execute as:** Me
   - **Who has access:** Anyone with link
5. Copy the **Web App URL**.

---

## 3) Frontend (GitHub Pages)

1. Put `index.html` at the root of your GitHub repo.
2. Edit the two placeholders in `index.html`:
   - `APPS_SCRIPT_WEBAPP_URL` → paste your web app URL
   - `API_KEY` → same key as in Apps Script
3. Push to GitHub, then open **Settings → Pages → Deploy from branch** (root `/`).
4. Visit your site at the Pages URL and test.

---

## 4) Usage

- **Load Employees**: reads `Employees` (Active!=FALSE).
- **Record Attendance**: appends to `Attendance`.
- **Preview Payroll**: calculates for a given `YYYY-MM` by:
  - Working days = weekdays excluding Sundays + excluding dates in `Holidays`
  - `absentDays = workingDays - presentDays`
  - `perDay = MonthlySalary / workingDays`
  - `deductions = attendanceDeduction + loanRecovery`
  - `netPay = gross - deductions + bonus`

---

## 5) Security Notes

- This is a simple internal tool. For better security:
  - Restrict access to the Apps Script (e.g., GSuite domain), or
  - Add a server intermediary, or
  - Use OAuth with Google Identity (advanced).
- The included **API key** is a basic shared secret; keep it private.

---

## 6) Troubleshooting

- **CORS**: The frontend uses `Content-Type: text/plain` to avoid preflight.
- **Unauthorized**: Check that your `API_KEY` matches in **both** places.
- **No data**: Confirm sheet tab names & headers exactly match the README.
- **Wrong working days**: Verify `Holidays` and note that **Sundays are excluded, Saturdays included**.

---

## 7) License
MIT (do what you want, but no warranty).