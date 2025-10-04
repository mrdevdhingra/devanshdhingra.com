# Google Sheets Bug Tracker Setup

This guide will help you set up a Google Sheets backend for the bug reporting feature on your website.

## Step 1: Create a New Google Sheet

1. Go to [Google Sheets](https://sheets.google.com/)
2. Create a new spreadsheet
3. Name it "Bug Tracker" (or any name you prefer)
4. In the first row, add these column headers:
   - **A1**: Date
   - **B1**: Bug
   - **C1**: Status

## Step 2: Add the Apps Script

1. In your Google Sheet, go to **Extensions > Apps Script**
2. Delete any existing code in the editor
3. Copy and paste the code below
4. Save the project (name it "Bug Tracker API" or similar)

```javascript
function doGet(e) {
  try {
    const action = e.parameter.action;
    
    if (action === 'getBugs') {
      return getBugs();
    }
    
    return ContentService.createTextOutput(JSON.stringify({
      status: 'error',
      message: 'Invalid action'
    })).setMimeType(ContentService.MimeType.JSON);
    
  } catch (error) {
    return ContentService.createTextOutput(JSON.stringify({
      status: 'error',
      message: error.toString()
    })).setMimeType(ContentService.MimeType.JSON);
  }
}

function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    const action = data.action;
    
    if (action === 'addBug') {
      return addBug(data.bug);
    }
    
    return ContentService.createTextOutput(JSON.stringify({
      status: 'error',
      message: 'Invalid action'
    })).setMimeType(ContentService.MimeType.JSON);
    
  } catch (error) {
    return ContentService.createTextOutput(JSON.stringify({
      status: 'error',
      message: error.toString()
    })).setMimeType(ContentService.MimeType.JSON);
  }
}

function getBugs() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const data = sheet.getDataRange().getValues();
  
  // Skip header row
  const bugs = [];
  for (let i = 1; i < data.length; i++) {
    bugs.push({
      date: data[i][0],
      bug: data[i][1],
      status: data[i][2] || 'Unsolved'
    });
  }
  
  // Return in reverse order (newest first)
  bugs.reverse();
  
  return ContentService.createTextOutput(JSON.stringify({
    status: 'success',
    bugs: bugs
  })).setMimeType(ContentService.MimeType.JSON);
}

function addBug(bugDescription) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const date = new Date().toLocaleString();
  
  // Add new row with bug data
  sheet.appendRow([date, bugDescription, 'Unsolved']);
  
  return ContentService.createTextOutput(JSON.stringify({
    status: 'success',
    message: 'Bug added successfully'
  })).setMimeType(ContentService.MimeType.JSON);
}
```

## Step 3: Deploy the Apps Script

1. In the Apps Script editor, click the **Deploy** button (top right)
2. Select **New deployment**
3. Click the gear icon next to "Select type" and choose **Web app**
4. Configure the deployment:
   - **Description**: Bug Tracker API
   - **Execute as**: Me (your email)
   - **Who has access**: Anyone
5. Click **Deploy**
6. Review the permissions and click **Authorize access**
7. Choose your Google account and click **Allow**
8. Copy the **Web app URL** that appears (it will look like: `https://script.google.com/macros/s/...../exec`)

## Step 4: Update Your Website

1. Open your `index.html` file
2. Find this line (around line 285):
   ```javascript
   const SCRIPT_URL = 'YOUR_GOOGLE_APPS_SCRIPT_URL_HERE';
   ```
3. Replace `'YOUR_GOOGLE_APPS_SCRIPT_URL_HERE'` with your actual Web app URL from Step 3
4. Save the file

## Step 5: Test It Out

1. Open your website in a browser
2. Try submitting a bug report
3. Check your Google Sheet - you should see the bug appear
4. The status will be "Unsolved" by default
5. To mark a bug as solved, simply edit the Status column in the Google Sheet to "Solved"

## Managing Bugs

- **View bugs**: All bugs are displayed on your website automatically
- **Mark as solved**: Edit the Status column in Google Sheets (change "Unsolved" to "Solved")
- **Delete bugs**: Delete the row in Google Sheets
- **The website will always show the latest data from your sheet**

## Troubleshooting

### Bugs not showing up?
- Make sure you deployed the Apps Script as a web app
- Check that "Who has access" is set to "Anyone"
- Verify the URL in your index.html is correct

### Can't submit bugs?
- Open the browser console (F12) to check for errors
- Verify the Google Apps Script URL is correct
- Make sure the sheet has the correct column headers (Date, Bug, Status)

### Need to update the script?
1. Make changes in the Apps Script editor
2. Click **Deploy > Manage deployments**
3. Click the edit icon (pencil) next to your deployment
4. Under "Version", select **New version**
5. Click **Deploy**

## Security Note

This setup allows anyone to submit bugs (which is typically what you want for a public bug tracker). The bugs are stored in your Google Sheet, which only you can access and edit. Users can only see bugs through your website interface.

