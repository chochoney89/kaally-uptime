# kaally-uptime

Uptime monitor for kaally.com.au, running on GitHub Actions (public repo = unlimited free
Actions minutes; there is nothing sensitive here — tokens live in Actions **secrets**).

Every ~5 minutes (GitHub best-effort, usually 5–15 min) it checks:

1. `https://kaally.com.au/` — HTTP 200 + the word "Kaally" in the body (customer experience).
2. `https://kaally.com.au/wp-login.php` — HTTP 200 + a login form. This is the load-bearing
   check: the homepage is served from LiteSpeed's full-page cache and can look fine while
   PHP/DB are dead; wp-login is uncached.

Three attempts 20 s apart before declaring DOWN (no flapping). Alerts go to Telegram **on
transitions only** — one 🔴 DOWN message, one 🟢 RECOVERED message — never every 5 minutes.
Last known status lives in `status/current`.

## Setup (one-time)

Add two repository secrets (Settings → Secrets and variables → Actions), or via CLI:

```sh
gh secret set TG_BOT_TOKEN -R chochoney89/kaally-uptime   # paste the bot token when prompted
gh secret set TG_CHAT_ID  -R chochoney89/kaally-uptime    # the chat id the 7am digest uses
```

Until the secrets exist, the workflow still runs and records status, it just can't alert.

## Test

Actions → uptime → **Run workflow** (manual dispatch). To see a DOWN alert end-to-end,
temporarily edit the URL in `uptime.yml` to something bogus, run it, then revert.

## Caveats (accepted)

Single vantage point, best-effort timing, no status page, no SMS. If those ever matter,
UptimeRobot free is the better tool — this exists because it's $0, no new accounts, and
alerts land in the Telegram channel Ada already reads.
