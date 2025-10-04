# devanshdhingra.com

Devansh's personal website showcasing various AI and utility projects.

## Features

- **Project Links**: Quick access to AI Image Generation, AI CV Creator, and Shopify Size Utility
- **Bug Reporting**: Integrated bug tracking system powered by Google Sheets
  - Users can submit bug reports
  - View all reported bugs with their status (Solved/Unsolved)
  - Backend managed through Google Sheets for easy bug tracking

## Setup

To set up the bug reporting feature:

1. Follow the instructions in `SETUP_GOOGLE_SHEETS.md`
2. Create a Google Sheet with the proper structure
3. Deploy the Apps Script as a web app
4. Update the `SCRIPT_URL` in `index.html` with your deployment URL

## Tech Stack

- HTML5 (Retro style, no CSS)
- Vanilla JavaScript
- Google Sheets API (via Apps Script)

## Local Development

Simply open `index.html` in a web browser. No build process required.

Note: The bug reporting feature requires a deployed Google Apps Script to function.
