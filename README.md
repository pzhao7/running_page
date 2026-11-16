# Meet Cri-Kee

My personal running homepage. Running data syncs from Strava and renders as a static site.

Live site: https://mulanandcrikee.com

## How it works

```
Strava API → scripts/data.db (SQLite) + src/static/activities.json
           → SVG posters in assets/
           → Gatsby build → Vercel
```

## Sync new runs

```bash
# 1. Sync from Strava (needs a Strava API app: client id, client secret, refresh token)
python3 scripts/strava_sync.py <client_id> <client_secret> <refresh_token>

# 2. Regenerate the SVG posters (run from the repo root)
python3 scripts/gen_svg.py --from-db --title "Meet Cri-Kee" --type github --athlete "pzhao7" --special-distance 10 --special-distance2 20 --special-color yellow --special-color2 red --output assets/github.svg --use-localtime --min-distance 0.5
python3 scripts/gen_svg.py --from-db --title "Over 10km Runs" --type grid --athlete "pzhao7" --output assets/grid.svg --special-color yellow --special-color2 red --special-distance 20 --special-distance2 40 --use-localtime --min-distance 10
python3 scripts/gen_svg.py --from-db --type circular --use-localtime

# 3. Build and deploy (Vercel deploys master automatically)
yarn build
git add -A && git commit -m "Sync Strava data" && git push
```

## Automatic sync (disabled)

`.github/workflows/run_data_sync.yml` can sync daily via GitHub Actions. It is currently off (`RUN_TYPE: pass`). To enable it, set `RUN_TYPE: strava` and add the Strava secrets (`STRAVA_CLIENT_ID`, `STRAVA_CLIENT_SECRET`, `STRAVA_CLIENT_REFRESH_TOKEN`) to the repo.

## Stack

- Gatsby 4 + React 17, Mapbox for the run map
- Node 22 is required to build (`engines` in `package.json` pins `22.x`; Gatsby 4 fails on Node 23+)
- Python 3 for the sync scripts (`pip install -r requirements.txt`)

## License

MIT — see [LICENSE](LICENSE).
