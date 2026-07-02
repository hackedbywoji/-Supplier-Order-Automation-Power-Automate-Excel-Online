# 📧 Supplier Order Automation — Power Automate + Excel Online

Automated flow that captures supplier order emails and logs them into a shared Excel file in OneDrive — zero manual data entry.

## 🔍 The Problem

In many companies, supplier orders arrive by email and someone has to manually copy the order details (supplier, product, quantity, date) into a shared Excel tracking sheet. This process is:

- **Slow** — each order takes 2–3 minutes of copy-pasting
- **Error-prone** — typos, wrong rows, missed emails
- **Untraceable** — no timestamp of when the order was actually received

## 🔧 The Manual Process (Before)

1. Employee checks the inbox several times a day
2. Opens each order email
3. Manually copies supplier, product, quantity and date into Excel
4. Saves the file, hoping nobody else had it open at the same time

## ⚡ The Solution

A Power Automate cloud flow that runs automatically on every incoming order email:

1. **Trigger — "When a new email arrives (V3)"** with a server-side **Subject Filter** (`Pedido`). Non-matching emails never even start the flow, saving execution quota.
2. **Html to text** — converts the HTML email body into plain text so it can be parsed reliably.
3. **Data extraction with expressions** — four `Compose` actions extract each field using `split()` / `trim()` expressions:

   ```
   trim(split(split(body('Html_to_text'), 'Proveedor:')[1], decodeUriComponent('%0A'))[0])
   ```

4. **Add a row into a table (Excel Online Business)** — appends the extracted data as a new row in a named Excel Table (`TablaPedidos`) stored in OneDrive, adding a formatted reception timestamp:

   ```
   formatDateTime(triggerOutputs()?['body/receivedDateTime'], 'dd/MM/yyyy HH:mm')
   ```

### Flow overview

<img width="636" height="768" alt="image" src="https://github.com/user-attachments/assets/869e8cea-a82a-4503-aba9-65f737496981" />

### Trigger with subject filter

<img width="623" height="472" alt="image" src="https://github.com/user-attachments/assets/11887ce6-46ba-459a-998e-a80839fa3569" />

### Data extraction expression
<img width="1189" height="848" alt="image" src="https://github.com/user-attachments/assets/dc623914-815c-40b7-ac21-6c7de73658a7" />

### Successful run

<img width="1913" height="904" alt="Captura de pantalla 2026-07-02 113446" src="https://github.com/user-attachments/assets/3ea28bc4-ebe3-4cc2-a477-663ab8978fa8" />

### Result in Excel

<img width="1916" height="459" alt="Captura de pantalla 2026-07-02 113555" src="https://github.com/user-attachments/assets/a131b9c9-4216-433a-8d55-392f47d57209" />

## 🛠️ Tech Stack

| Component | Purpose |
|---|---|
| **Power Automate (cloud flow)** | Orchestration and automation engine |
| **Office 365 Outlook connector** | Email trigger with server-side subject filtering |
| **Content Conversion (Html to text)** | Reliable plain-text parsing of email bodies |
| **Data Operation (Compose) + expressions** | Field extraction with `split()`, `trim()`, `formatDateTime()` |
| **Excel Online (Business) connector** | Writes rows into a named Excel Table via Microsoft Graph |

> ✅ 100% **standard connectors** — no premium license required.

## 📊 The Result

- Order registration time: **~3 minutes → 0 seconds** (fully automatic)
- **Zero transcription errors** — data is extracted programmatically
- Every order gets an automatic **reception timestamp** for traceability
- The Excel file is always up to date for the whole team in OneDrive

## 🚀 Possible Next Iterations

- **AI Builder** entity extraction for free-format emails (no fixed template needed)
- Teams/Telegram notification when a new order is logged
- Validation step: flag rows with missing fields for human review

---

*Built by [Jowi](https://github.com/hackedbywoji) — IT technician focused on automation (Python, Power Automate, Azure).*
