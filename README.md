# Calendly Availability Extractor

Extracts availability from a public Calendly event page.

It accepts a Calendly URL, scrapes available dates and time slots for a selected number of weeks, and displays the results in a calendar-style UI.

## What It Does

- Accepts a public Calendly event URL.
- Pulls available and unavailable days for a selected week range.
- Displays results in a simple calendar UI with selectable days and visible time slots.

## How It Works

1. A user submits a Calendly URL from the web app.
2. The app requests availability data from the API layer.
3. In production, the request is handled by AWS Lambda, which executes the browserless.io call used to run the scraping logic in a headless browser environment.
4. Extracted availability is returned and rendered as an interactive calendar.

## Key Technologies and Approaches

- React + TypeScript for the frontend experience.
- Vite + Tailwind CSS for fast UI development and styling.
- Node.js + Express for local API development.
- AWS Lambda for serverless production execution.
- Browserless.io (Chromium Function API) for headless browser automation.
- DOM-based and dynamic extraction approach that reads Calendly calendar state and slot lists.

## Repository Structure

- frontend: user interface and API client.
- backend: local Express server and scraper modules.

## Example Response

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

## Local Development

Install and run both apps:

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

## Notes

- Development mode typically uses the local Express API.
- Production mode is configured to call AWS Lambda.
- The Lambda path is where the browserless.io execution is used.
