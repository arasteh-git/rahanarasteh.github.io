# /visit — Rahan's Sofa

A booking page for my couch. Live at
**[rahanarasteh.com/visit](https://rahanarasteh.com/visit)**, deliberately not
linked from anywhere on the main site.

Pick dates on the calendar, leave a name and email, and it's booked. The dates
block for everyone else, the stay lands on my calendar, and I get an email.

---

## The problem this solves

The first version stored bookings in `localStorage`. That meant every visitor
saw only their own reservations — two friends could book the same week and
neither would know. It also "notified" me by opening a `mailto:` link and
trusting the guest to press send.

GitHub Pages serves static files and nothing else. No server, no database. So
the state lives outside the repo, in a Google Apps Script web app that owns a
Sheet and my calendar.

```
browser  ──GET?action=availability──▶  Apps Script  ──▶  Google Sheet
         ◀───── blocked dates ──────                       │
         ──POST booking───────────▶                        ├──▶ Calendar event
         ◀───── ok / conflict ─────                        └──▶ email to me
```

The page ships with no credentials. The only thing in the source is the script's
public `/exec` URL.

## Setup

Full walkthrough in [`SETUP-visit.md`](../SETUP-visit.md). Short version:

1. Make a Google Sheet, copy its ID.
2. Paste `_backend/Code.gs` into a new project at script.google.com, fill in the
   CONFIG block at the top.
3. Deploy as a Web App — execute as **me**, access **anyone**.
4. Put the resulting `/exec` URL into the `ENDPOINT` constant in `index.html`.

After *any* edit to the script you have to Deploy → Manage deployments → **New
version**. Saving alone doesn't update the live URL. This catches everyone once.

## How dates work

Ranges are **half-open**: check out on the 23rd and the 23rd is free for the
next guest. A stay from the 25th to the 28th occupies the 25th, 26th and 27th.
Back-to-back bookings don't collide.

Everything is a `YYYY-MM-DD` string in local time. Never `toISOString()` — that
converts to UTC and moves the day.

## Cancelling

Open the Sheet, set that row's **Status** to `cancelled`. The dates free up on
the site immediately. Delete the calendar event separately.

## Guardrails

- A script lock, so two simultaneous submissions can't double-book — the second
  one is told the dates just went.
- A hidden honeypot field that bots fill in and humans don't.
- Caps on stay length and how far ahead anyone can book.
- An optional passcode, off by default. Set `PASSCODE` in the script and flip
  `REQUIRE_PASSCODE` in the page.

## Honest limitations

**Unlisted is not private.** Nothing links here and the page is `noindex`, but
free GitHub Pages requires a public repo and the URL is guessable. Assume a
stranger could open it.

**The endpoint is public too.** It's in the page source, so someone could POST
to it directly. The honeypot and the caps make that unrewarding rather than
impossible. The passcode is the actual gate if it ever matters.

**Guest emails end up in a spreadsheet.** That's other people's contact details
sitting in my Drive. The Sheet stays private, and the availability endpoint
returns dates only — never names or addresses.

## Things that broke

**Off-by-one on every blocked date.** Sheets stores a typed date as a real date
value at midnight in the *spreadsheet's* timezone. The script was formatting it
in the *script project's* timezone, and the two didn't match, so every booking
shifted back a day. Fixed by reading in the spreadsheet's zone and forcing the
date columns to plain text so no conversion happens at all.

Append `?action=debug` to the web app URL to see both timezones and the raw
versus parsed value of every row.
