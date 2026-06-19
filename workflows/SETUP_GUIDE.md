# Corona Digital Shelf Intelligence — n8n Setup Guide

## Files in this folder

| File | Purpose |
|---|---|
| `corona_shelf_intelligence_main.json` | Main daily scraping workflow (Talabat + Breadfast MVP) |
| `corona_shelf_intelligence_report.json` | Daily AI report sub-workflow (07:30 Cairo) |

---

## Step 1 — Create the Google Sheet

1. Go to Google Drive → New → Google Sheets → name it **`Price_Intelligence`**
2. Create three tabs:
   - `Tracked_Keywords` — columns: `Keyword | Our_SKU | Brand | Category | Is_Ours | Platform`
   - `Price_Log` — columns: `Date | Platform | Raw_Product_Name | Matched_SKU | Price_EGP | Original_Price_EGP | Promo_Type | Discount_Percent | In_Stock | Stock_Hint | First_Seen_OOS | Match_Confidence | Category | Is_Ours`
   - `Price_Index` — columns: `Date | SKU | Platform | Category | Is_Ours | Price_EGP | In_Stock`
3. Copy the Sheet ID from the URL: `https://docs.google.com/spreadsheets/d/**SHEET_ID**/edit`
4. Add a `Platform` column to `Tracked_Keywords` — fill each row with `Talabat`, `Breadfast`, `Carrefour`, or `Amazon`

---

## Step 2 — Configure n8n Credentials

In your n8n instance at `khaledmah3123.digitalage-eg.site`:

### Google Sheets OAuth2
- Settings → Credentials → New → Google Sheets OAuth2 API
- Name it exactly: **`Google Sheets account`**
- Follow OAuth consent screen setup

### Groq API
- Settings → Credentials → New → Groq API
- Name it exactly: **`Groq account`**
- Use your existing credential key (ID: `JQCBvOC9oeYkiJ7I`)

### Telegram Bot
- Settings → Credentials → New → Telegram API
- Name it exactly: **`Telegram Bot account`**
- Use your existing bot token

---

## Step 3 — Import Workflows

1. n8n UI → Workflows → Import from File
2. Import `corona_shelf_intelligence_main.json` first
3. Import `corona_shelf_intelligence_report.json`

---

## Step 4 — Fill in Placeholders

Search for `PASTE_YOUR_GOOGLE_SHEET_ID_HERE` in both workflows and replace with your actual Sheet ID.

Replace chat IDs:
- `TRADE_MARKETING_CHAT_ID` → your Trade Marketing Telegram chat/group ID
- `SALES_DIRECTOR_CHAT_ID` → Sales Director's Telegram chat ID

---

## Step 5 — Phase 1: Capture Real API Endpoints (Week 1)

The Talabat and Breadfast HTTP nodes use **placeholder URLs**. Replace them after DevTools capture:

### Talabat Mart
1. Open Chrome → `talabat.com/egypt/grocery` → F12 → Network tab → XHR filter
2. Search for a product (e.g., "corona chocolate")
3. Find the search request → copy URL, headers, and any auth tokens
4. Paste into the **HTTP - Talabat API** node

### Breadfast
1. Same process on `breadfast.com`
2. Paste into the **HTTP - Breadfast API** node

---

## Step 6 — Populate Tracked_Keywords Sheet

Fill in your SKU list per the spec's competitor mapping:

| Keyword | Our_SKU | Brand | Category | Is_Ours | Platform |
|---|---|---|---|---|---|
| corona bimbo | FGBSBCM02 | Corona | Bimbo | TRUE | Talabat |
| hohos king chocolate | COMP-HOHOS | Edita | Bimbo | FALSE | Talabat |
| cadbury dairy milk | COMP-CDM | Cadbury | Chocolate Bars | FALSE | Breadfast |
| ... | | | | | |

---

## Step 7 — Activate

1. Open main workflow → toggle Active
2. Open report workflow → toggle Active
3. Run manually once to test (use the "Test workflow" button)
4. Check Google Sheets for appended rows
5. Verify Telegram messages arrive

---

## Schedule Summary

| Workflow | Time (Cairo) | Days |
|---|---|---|
| Main scrape | 06:00 | Mon–Thu, Sat–Sun |
| Daily AI report | 07:30 | Mon–Thu, Sat–Sun |

---

## Phase Roadmap Reminder

| Phase | Action |
|---|---|
| ✅ Phase 0 | This setup + Tracked_Keywords population |
| 🔄 Phase 1 | DevTools capture of real Talabat + Breadfast endpoints |
| 🔄 Phase 2 | Activate and run MVP (Talabat + Breadfast) |
| ⏳ Phase 3 | 7-day pilot + accuracy validation (target >90%) |
| ⏳ Phase 4 | Add Carrefour (Playwright node) + Amazon (ScraperAPI) |
| ⏳ Phase 5 | Power BI dashboard on top of Price_Log sheet |
