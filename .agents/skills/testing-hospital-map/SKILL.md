---
name: testing-hospital-map
description: Test the UShift Hospital Intelligence Platform (hospital-map) end-to-end. Use when verifying detail panel UI, outreach status buttons, Google Sheets integration, filters, or region dashboard changes.
---

# Testing the Hospital Map App

## Running the App

The app is a single HTML file with no build step or dependencies.

```bash
cd hospital-map
python3 -m http.server 8080
```

Open `http://localhost:8080/index.html` in Chrome.

## Architecture Notes

- **Single file**: All HTML, CSS, and JS live in `hospital-map/index.html`
- **Data**: `hospital-map/hospitals_all.json` contains ~8,038 hospitals
- **Persistence**: Uses `localStorage` for user data (call status, findings, Google Sheets URL)
- **Map**: Leaflet.js with CARTO dark tile layer
- **No backend**: Google Sheets integration uses client-side `fetch` with `mode: 'no-cors'`

## UI Interaction Tips

- **Opening hospital details**: Clicking map markers can be unreliable due to cluster overlaps. Use the **Hospitals tab** in the sidebar and click a hospital from the list instead — this reliably opens the detail panel.
- **Closing detail panel**: Click the × button in the top-right corner of the detail panel.
- **Sidebar tabs**: Filters | Regions | Hospitals — click the tab name at the top of the sidebar.
- **Toast notifications**: Appear at bottom-right of screen, auto-dismiss after ~3 seconds. Green = success, Red = error.

## Key Test Areas

### 1. Detail Panel Sections
- Verify expected sections are present/absent
- Check section headers and content structure
- Look for any remnants of removed features

### 2. Outreach Status Buttons
- 3 buttons: Called, Call Later, Not Responding
- Mutually exclusive — only one should be highlighted at a time
- Color coding: Called = blue, Call Later = yellow/amber, Not Responding = red
- Clicking a button should trigger a toast notification
- Status persists in localStorage and shows in hospital list

### 3. Findings Section
- Software textarea for typing notes
- "Save & Send to Google Sheets" button
- Findings history shows timestamped entries with status

### 4. Google Sheets Integration
- **Config**: Filters tab → Google Sheets Configuration (collapsible)
- **Error path**: No URL configured → red toast "No Google Sheets URL configured"
- **Success path**: URL configured → green toast "Sent to Google Sheets!"
- **Persistence**: URL saved in localStorage, survives page refresh
- **Test URL**: Use any valid URL like `https://script.google.com/macros/s/test123/exec`
- Since `mode: 'no-cors'` is used, the POST will appear to succeed even with a fake URL

### 5. Call Status Filter
- Filters tab → Call Status dropdown
- Should contain: All Statuses, None, Called, Call Later, Not Responding
- Applying filter should reduce hospital list to matching statuses

### 6. Region Dashboard
- Regions tab shows cards for each region
- Each card has stats: Total Hospitals, Target Hospitals, Called, Call Later, Not Responding, Remaining

## Testing Workflow

1. Clear localStorage before testing: `localStorage.clear()` in browser console, then refresh
2. Start with Hospitals tab to select a hospital
3. Test detail panel sections and buttons
4. Test Google Sheets error path (before configuring URL)
5. Configure Google Sheets URL, test persistence
6. Test Google Sheets success path
7. Test filters with new status values
8. Check region dashboard labels

## Devin Secrets Needed

None required. The app runs entirely client-side with no authentication.
