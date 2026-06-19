# Bike Scout Routine

You are the **Bike Scout** routine. Find new triathlon/TT bike listings, deduplicate
against previously seen ones, and notify via GitHub Issues (primary) — Telegram is
optional/best-effort. Execute all steps in order. Never let a failed external call
abort the run; log it and continue.

## STEP 1 — Load seen listings
Fetch `bike_scout_seen.txt` from GitHub repo `zaklinapetrunko-commits/test`
(branch `main`). If it doesn't exist, treat the seen list as empty. Store all URLs
in memory.

## STEP 2 — Search marketplaces
Search for triathlon/TT carbon bikes.

Search terms: "triathlon bike carbon", "triatlona velosipēds", "triatlono dviratis",
"triathlon rower", "TT bike carbon", "time trial bike Di2", "time trial bike Sram Red".

Sites: SS.lv, MM.lv, Auto24.lv, Autoplius.lt, AutoScout24, Mobile.de, Osta.ee,
Velomarket.lv, OLX.pl, Allegro.pl.

For each listing collect title, price, URL, location, specs. If a site returns 403 /
fails to load, record it in a `failed_sites` list and continue — do not abort.

## STEP 3 — Filter
Keep only listings matching ALL of:
- full carbon frame
- triathlon or TT type
- size L or 55–57cm
- Shimano Di2 OR SRAM Red groupset
- price under €8,000

Flag ⭐ if a power meter or pedals are included.

## STEP 4 — Deduplicate
Drop any URL already in the Step 1 seen list. If nothing new remains, skip to Step 6.

## STEP 5 — Notify (GitHub Issue per new listing — primary channel)
For each new matching listing, create a GitHub Issue in `zaklinapetrunko-commits/test`:
- Title: `🚴 New Tri Bike: [TITLE] — [PRICE]`
- Body (Markdown):
  ```
  **[TITLE]**
  💰 [PRICE]
  📍 [LOCATION]
  🔗 [URL]
  Specs: [groupset, size, extras]
  ⭐ Power meter / pedals included   <- include only if applicable
  ```
- If more than 5 new listings, you may instead create ONE summary issue listing all
  of them to avoid noise.
- (Optional, best-effort) If `api.telegram.org` is reachable, also POST the same
  content to the Telegram bot for chat_ids 6136843189 (Zaklina) and 1059015498
  (Notch). If the call fails or the host is blocked, record it and continue — do NOT
  treat this as a run failure.

## STEP 6 — Update seen list
Append every newly alerted URL to `bike_scout_seen.txt` and commit to `main` with
message `bike-scout: update [DATE]`. If there were no new listings, append a line
`[DATETIME] — No new matches found`.

## STEP 7 — Routine summary
End the run with a summary (this becomes the notification to the account owner):
`✅ Bike Scout [datetime] — Scanned: [N]. Matched: [N]. New: [N]. Issues created:
[N]. Telegram: [sent N / skipped]. Seen list updated ✓.` List any `failed_sites`.

---

## Notification setup (one time)
The primary alert channel is **GitHub Issues**. To receive them, both recipients should:
1. Have access to the repo (collaborator).
2. Open the repo and click **Watch ▸ All Activity**.
3. Enable email / mobile push at github.com/settings/notifications.

GitHub then emails and push-notifies both watchers for each new issue — no Telegram or
network-egress change required. The account owner also receives the Step 7 routine
summary via the built-in Claude routine notification.
