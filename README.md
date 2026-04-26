# Calendly Availability Extractor

This project extracts scheduling availability from a public Calendly event page.

The core implementation is scraper-first: it navigates Calendly's calendar UI in a headless browser, reads day-level availability and slot lists, and returns structured JSON.

## Scraping Architecture

- Runtime trigger: a POST request containing `calendlyUrl` and `weeks`.
- Local entrypoint: Node.js + Express backend.
- Production execution path: AWS Lambda invokes browserless.io (Chromium Function API) to run scraping code remotely.
- Data source: Calendly's rendered DOM (calendar header, day buttons, and spot list elements).

## Scraping Logic and Approach

1. Compute date boundaries.
   - Start at next Monday.
   - End at `start + (7 * weeks - 1)`.
2. Open the Calendly event page in a headless Chromium session.
3. Wait for calendar readiness using stable `data-testid` selectors.
4. Iterate the calendar grid month-by-month.
5. For each day button:
   - Confirm the cell belongs to the visible month.
   - Skip days before the computed start.
   - Detect disabled/enabled state.
6. For enabled days:
   - Click the day.
   - Read slot entries from the rendered spot list.
7. Build a normalized response object grouped by year and month.
8. Stop once the configured end date has been processed.

## Data Extraction Strategy

- Selector-driven parsing over the live page DOM.
- Availability represented as:
  - day with `timeSlots` when open
  - day with only `monthDay` when unavailable
- Month navigation continues until the requested range is fully captured.

## Response Shape

```json
{
  "2026": {
    "May": [
      { "monthDay": "12", "timeSlots": ["9:00am", "9:30am"] },
      { "monthDay": "13" }
    ],
    "June": [{ "monthDay": "1", "timeSlots": ["1:00pm"] }]
  }
}
```

## Key Technologies

- TypeScript across frontend and backend.
- Node.js + Express for local API orchestration.
- AWS Lambda for serverless production execution.
- browserless.io for headless Chromium function execution.
- Playwright/Puppeteer-compatible scraping approach via DOM automation.

## Frontend

The frontend is a React + Vite + Tailwind interface that submits scrape requests and displays returned availability in a calendar view with day-level slot details.

## Local Development

```bash
cd backend
npm install
npm run dev
```

```bash
cd frontend
npm install
npm run dev
```
