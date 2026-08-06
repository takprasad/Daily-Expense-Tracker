# Rupaya — Expense & Income Tracker

A fast, attractive, mobile-first money tracker that stores everything in a Google Sheet you own. No backend server, no build step — one HTML file plus a Google Apps Script.

## Features

**Track**
- Expenses *and* income, with payment method (Cash / Card / UPI / Bank / Other — UPI is the default when adding a new one)
- Custom categories — add, edit, or remove your own, separately for expenses and income, synced to your Sheet so they follow you across devices
- Recurring transactions (e.g. rent, salary, subscriptions) on a weekly, monthly, or yearly cadence — auto-repeats and catches up automatically
- Edit, duplicate, or delete any past entry
- **Quick add** — your most frequent expenses (same category + amount + note, repeated 2+ times) show up as one-tap chips on the home screen, so logging your usual coffee or lunch doesn't need the full form
- Note autocomplete — suggests notes you've used before for whatever category is selected

**Understand**
- Weekly, monthly, and yearly views with period-over-period comparison
- Category breakdown (donut chart, with the period total in the center) for expenses or income
- Interactive trend chart (daily / weekly / monthly bars depending on the period) — tap any bar to see that day/week/month's total, how it compares to the average, and its top categories, then jump straight to those transactions in History
- Tap a category in the "By category" legend to jump straight to that category's transactions for the current period
- An insights panel: average daily spend, top category, biggest single expense, days logged, savings rate
- A one-line auto-generated insight on the home screen
- A gentle nudge if you haven't logged anything yet today
- The home screen's mini chart shows real daily spending for the current month at a glance, not a decorative placeholder — today's bar is highlighted

**Budget**
- Set an overall monthly budget and per-category monthly budgets
- Progress bars turn amber near the limit and red when you go over
- A heads-up warning appears right after you save an expense that pushes you over a budget

**Big expenses**
- A dedicated **Big** tab for major one-time purchases (a laptop, an appliance, a trip) that would otherwise distort your daily numbers
- Mark any transaction as a "big one-time expense" when adding or editing it — it's automatically kept out of the home screen, weekly/monthly/yearly stats, budget calculations, and quick-add suggestions
- The Big tab shows its own this-year and all-time totals, plus a category breakdown, so major purchases are still easy to review — just on their own terms
- History stays clean by default too; a toggle in its filter sheet lets you bring big expenses back into view there if you want everything in one list

**Find**
- Sort by newest, oldest, highest amount, or lowest amount
- Filter by type, category (multi-select), payment method, date range, and amount range
- Search notes, category names, or amounts
- Swipe a transaction left to delete it, or use multi-select mode for bulk deleting
- Any delete (single or bulk) can be undone from the confirmation toast for a few seconds
- Export any filtered view (or everything) to CSV

**Everything else**
- Works offline with a local cache, syncs back to your Sheet when connected
- **Reliable offline sync** — any expense, edit, or delete made while offline is queued and automatically retried once you're back online, instead of silently disappearing the next time the app syncs. A badge on the Settings icon shows how many changes are still waiting to sync.
- **PIN lock** — optionally require a 4–6 digit PIN to open the app, for privacy on a shared phone (Settings → Privacy). This is a simple on-device lock, not encryption.
- Dark mode
- Custom currency symbol, with proper decimal support (no more amounts silently rounded to whole numbers)
- Installable to your phone's home screen (PWA manifest + icons included)
- Backend input validation — the Apps Script rejects malformed amounts/dates with a clear error instead of silently writing bad data into your Sheet

## 1. Set up the Google Sheet backend

1. Create a new [Google Sheet](https://sheets.new) — this will be your database.
2. In the Sheet, go to **Extensions → Apps Script**.
3. Delete the placeholder code and paste in the contents of [`apps-script/Code.gs`](apps-script/Code.gs).
4. Click **Deploy → New deployment**.
5. Click the gear icon next to "Select type" and choose **Web app**.
6. Set:
   - **Execute as:** Me
   - **Who has access:** Anyone
7. Click **Deploy**, then **Authorize access** and approve the permissions (it's your own script, acting on your own Sheet).
8. Copy the **Web app URL** shown after deployment — it looks like `https://script.google.com/macros/s/AKfycb.../exec`.

The script automatically creates an "Expenses" sheet and a "Budgets" sheet with the right columns the first time each is used.

> If you redeploy after editing the script, use **Manage deployments → Edit → New version** to keep the same URL.

## 2. Host the app on GitHub

1. Create a new GitHub repository (public or private).
2. Upload the whole `expense-tracker` folder contents to the root of the repo — `index.html`, `manifest.json`, and the `icons/` folder.
3. Go to **Settings → Pages**.
4. Under "Build and deployment", set **Source: Deploy from a branch**, branch: `main`, folder: `/ (root)`.
5. Save. GitHub will give you a URL like `https://yourusername.github.io/your-repo/`.

## 3. Connect the app to your Sheet

1. Open your GitHub Pages URL on your phone or computer.
2. Paste the Web App URL from step 1 into the onboarding screen (or later in **Settings → Connection**).
3. Tap **Connect & sync**. Every expense or income entry you add becomes a row in your Sheet.

You can also use the app **offline without a Sheet** — tap "Skip for now" on the onboarding screen; your data then stays in that browser's local storage only.

### Installing it like an app

On your phone, open the site in Chrome or Safari and use "Add to Home Screen" (Safari) or the install prompt / menu option (Chrome/Android). It'll open full-screen with its own icon, no browser bar.

Long-pressing the installed icon on Android also shows an "Add expense" shortcut that jumps straight to the add-transaction form.

## Turning it into a real Android app (.apk / Play Store)

Rupaya is already a installable PWA (see above) — that alone gives most people everything an "app" needs: a home screen icon, full-screen launch, and offline caching. If you specifically want an actual `.apk`/`.aab` file (to sideload, distribute, or publish to the Play Store), the easiest route needs no coding:

1. Deploy `index.html` to GitHub Pages first (see step 2 above) so you have a public HTTPS URL.
2. Go to **[pwabuilder.com](https://www.pwabuilder.com)** and paste your GitHub Pages URL.
3. It reads `manifest.json` automatically (already configured with icons, name, theme color, and the shortcut). Click **Package for stores → Android**.
4. Download the generated `.aab` (for Play Store) or `.apk` (for direct install/sideloading). PWABuilder signs it for you, or lets you supply your own signing key if you plan to publish and update it later.
5. Sideload the `.apk` directly on a phone (enable "install unknown apps" for your browser/file manager), or upload the `.aab` to the Google Play Console if you want a real Play Store listing.

If you'd rather build it yourself with full control, Google's [Bubblewrap CLI](https://github.com/GoogleChromeLabs/bubblewrap) does the same thing from a terminal (`npm i -g @bubblewrap/cli`, then `bubblewrap init --manifest=https://yoursite.github.io/manifest.json`) — but that requires the Android SDK and a JDK installed locally, so PWABuilder is the faster path for most people.

## Project structure

```
expense-tracker/
├── index.html              # the entire app (HTML + CSS + JS, self-contained)
├── manifest.json            # PWA manifest — enables "install to home screen"
├── icons/
│   ├── icon-192.png
│   └── icon-512.png
├── apps-script/
│   └── Code.gs               # Google Apps Script backend — paste into your Sheet's script editor
└── README.md
```

## How it works

- The app talks to your Sheet through a small JSON API defined in `Code.gs`: `doGet` lists transactions and budgets, `doPost` handles `add`, `update`, `delete`, `saveBudget`, and `deleteBudget`.
- Requests are sent as plain-text bodies to avoid CORS preflight requests, which Apps Script Web Apps can't handle — this is the standard way to talk to Apps Script from a static site.
- All stats, filtering, sorting, and budget math run client-side from the transaction list already in memory, so there's no extra cost or round-trip for viewing data differently.
- Recurring transactions work by "catching up": each time the app loads, it checks every recurring transaction against its frequency (weekly, monthly, or yearly) and fills in anything missed since its last occurrence, both locally and in your Sheet.

## Customizing

- **Categories:** managed entirely in **Settings → Categories** inside the app (no code editing needed) — add, edit, or delete them, and they sync to a "Categories" tab in your Sheet so they carry over to any device you use Rupaya on. Deleting a category that's still in use will ask first and offer to move those transactions to "Other" rather than losing track of them.
- **Currency symbol:** **Settings → Appearance → Currency symbol**.
- **Colors/fonts:** design tokens are CSS variables at the top of the `<style>` block in `index.html` (`:root { ... }` for light mode, `body.dark { ... }` for dark mode).

## Upgrading from an earlier version

If you already have an "Expenses" sheet from an earlier version of this script, **don't just swap in the new `Code.gs` and keep using it as-is** — the column layout has changed over time (the original v1 layout was just `ID, Date, Category, Amount, Note, Timestamp`; v2 added Type/Method/Recurring columns; v3 added a Frequency column; the current version adds a Big column marking major one-time expenses). Reading old data with a newer script without migrating would misalign fields.

Instead, run the built-in migration — it's a single function that detects whichever layout you're currently on (v1, v2, or current) and upgrades it in one step, no matter how far behind you are:

1. Paste the new `Code.gs` into your Sheet's Apps Script editor (replacing the old code), and save.
2. In the toolbar, pick **migrateFromV1** from the function dropdown next to the Run button, then click **Run**.
3. Approve the permission prompt if asked. Your "Expenses" sheet now has the current column layout, with every old row preserved — sensible defaults get filled in for anything that didn't exist yet in your old layout (Type defaults to "expense", Method to "cash", Frequency to "monthly" for rows that were already marked recurring).
4. Redeploy the web app (**Deploy → Manage deployments → Edit → New version**) so it's running the updated code.
5. It's safe to run `migrateFromV1` more than once, any time in the future — it detects an already-current sheet and does nothing.

A "Budgets" sheet and a "Categories" sheet are both created automatically the first time they're needed — no migration needed for either. If you'd already customized your categories in the app before this update, the app pushes those up to the new "Categories" sheet automatically the first time it syncs, rather than overwriting them with fresh defaults.
