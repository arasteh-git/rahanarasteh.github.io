# Setting up the sofa booking page

Two pieces: the page (`visit/index.html`, goes in your repo) and the backend
(`_backend/Code.gs`, lives in Google, **not** in the repo).

Total time: about fifteen minutes.

---

## 1. Make the spreadsheet

Create a new Google Sheet. Call it whatever. Copy its ID out of the URL —
it's the long string between `/d/` and `/edit`.

You don't need to add any headers; the script creates them on first use.

## 2. Set up the script

1. Go to **script.google.com** → **New project**.
2. Delete the placeholder code, paste in everything from `_backend/Code.gs`.
3. Fill in the CONFIG block at the top:
   - `SHEET_ID` — the ID you just copied
   - `NOTIFY_EMAIL` — already set to hello@rahanarasteh.com
   - `CALENDAR_ID` — leave as `'primary'` for your main calendar
   - `PASSCODE` — leave empty for open booking, or set a word to gate it
4. Save.

## 3. Deploy it

**Deploy** → **New deployment** → gear icon → **Web app**.

- Execute as: **Me**
- Who has access: **Anyone**

Click Deploy. Google will ask you to authorize it — you'll see an "unverified
app" warning, which is normal for your own scripts. Click Advanced → Go to
project.

Copy the **Web app URL**. It ends in `/exec`.

## 4. Connect the page

Open `visit/index.html`, find this line near the bottom:

```js
const ENDPOINT = 'PASTE_YOUR_APPS_SCRIPT_URL_HERE';
```

Replace it with your `/exec` URL. If you set a passcode, also flip
`REQUIRE_PASSCODE` to `true`.

## 5. Upload

Drop the `visit` folder into your repo alongside `index.html` and `photos/`.
It goes live at **rahanarasteh.com/visit**.

Don't upload `_backend/` — it's just your copy of the script.

---

## How it behaves

- Availability is read live from the sheet, so **everyone sees the same blocked
  dates**. That was the whole problem with the old version.
- A booking writes a row, creates an all-day event on your calendar, and emails
  you. The guest gets a link to add it to their own calendar.
- Ranges are half-open: if someone checks out on the 14th, the 14th is bookable
  by the next person.
- Two people submitting at the same moment can't double-book — the script takes
  a lock and the second one gets told the dates just went.

## Cancelling

Open the sheet, change that row's **Status** cell to `cancelled`. The dates free
up on the site immediately. Delete the calendar event separately.

## Changing the script later

Every time you edit `Code.gs` you have to **Deploy → Manage deployments → edit →
New version**. Saving alone doesn't update the live URL. This trips everyone up
at least once.

---

## Worth knowing

**The page is unlisted, not private.** Nothing links to it, and it has a
`noindex` tag so Google should skip it, but anyone with the URL can open it. The
passcode option is there if you want an actual gate.

**Your endpoint URL is public.** It's in the page source. Someone could POST to
it directly. The honeypot stops bots, and the script caps stay length and how
far ahead you can book, but a determined person could spam rows into your sheet.
For a couch this is fine. If it ever becomes a problem, turn on the passcode.

**Guest emails land in your spreadsheet.** That's personal data about your
friends sitting in a Google Sheet. Keep the sheet private — it is by default,
just don't share the link around.

**Free tier limits.** Apps Script allows a generous number of emails and script
runs per day on a consumer account — far more than a sofa will ever need. No
inactivity pause, unlike Supabase.
