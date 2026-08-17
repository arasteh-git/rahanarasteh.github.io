# rahanarasteh.com

My personal site. One page, no framework, no build step — open `index.html`,
change the words, push, done.

Live at **[rahanarasteh.com](https://rahanarasteh.com)**, hosted on GitHub Pages.

---

## What's in here

```
index.html      the whole site — markup, styles and scripts in one file
photos/         01–23.jpg, referenced by number from index.html
og.jpg          1200×630 image for link previews
CNAME           the custom domain
visit/          a separate page, not linked from the main site
```

There's no `package.json` and nothing to compile. That's deliberate: a personal
site should still work in five years without a dependency tree to resurrect.

## The page

Six sections, all sharing one design language borrowed from a photographic
contact sheet — a sprocket rail down the left edge, numbered frames, sections
labelled like exposures.

- **Then and now** — two portraits
- **What I'm doing now** — work, the Penn program, background
- **What I watch** — soccer and college sports, including a wall of every
  jersey I own, each one drawn in CSS rather than photographed
- **Cameras** and **Recent frames** — four rolls of photos, film first
- **Where I've been** — every city, grouped by which home base I travelled out
  from, coloured as transit lines
- **Systems, ranked** — my top twelve metro systems, because of course

The jersey wall is the part worth looking at if you're reading the source. Each
shirt is a single `div` with a `clip-path` silhouette and layered CSS gradients
matching the real kit — stripes, collars, sleeve trim, the blurred blue-red
fade on the 2012/13 Barcelona shirt. No images.

## Editing

All the text lives between `>` and `<`. Anything inside angle brackets is
structure — leave it alone.

Entities you'll see in the copy: `&rsquo;` is an apostrophe, `&mdash;` an em
dash, `&middot;` a middot, `&amp;` an ampersand, `&times;` a multiplication sign.

To find a line, search for the words you can see on the page.

## Photos

Numbered `01.jpg` through `23.jpg`. To swap one, replace the file and keep the
name — nothing else needs touching. Captions live next to each `<img>`.

**Keep them at or under 1100px on the long edge.** This is not about file size.
Mobile Safari caps how much *decoded* image memory a page may hold, and decoded
cost scales with dimensions, not kilobytes. At 1800px this page silently blanked
half its photos on iPhones. At 1100px it doesn't.

## The /visit page

A booking page for my couch, deliberately not linked from anywhere. It reads
availability from a Google Sheet and writes bookings back through a Google Apps
Script web app, which also drops the dates on my calendar and emails me.

Static hosting can't keep state, so all the persistence lives in that script.
The page is `noindex`, but a public repo is a public repo — nothing sensitive
goes in it.

## Deploying

Push to `main`. GitHub Pages picks it up in under a minute.

The `CNAME` file has to stay in the repo root or the custom domain drops on the
next deploy.

## Things that broke, so I don't repeat them

**Scroll reveals with a percentage threshold.** Sections start hidden and fade
in via `IntersectionObserver`. The threshold was `0.12` — twelve percent of a
section had to be visible at once. On a phone, where everything stacks into one
column, some sections are taller than the viewport, so that could never be
satisfied and they stayed invisible forever. It's `0` now, plus a timeout that
reveals everything regardless, plus a `.js` class so nothing hides at all if the
script fails.

**`toISOString()` on a local date.** It converts to UTC first, which shifts the
day for anyone west of Greenwich. Every date here is handled as a
`YYYY-MM-DD` string in local time instead.

**Timezones between Apps Script and Sheets.** A date read out of a spreadsheet
must be formatted in the *spreadsheet's* zone. Using the script's zone moved
every booking back a day.

---

Built with a lot of back-and-forth about the exact shade of Arsenal's away
collar. Questions to hello@rahanarasteh.com.
