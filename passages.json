name: Daily myth

# Rebuilds the generated files every morning so the site stays current and
# data/today.json reflects the day's selection. Also runs whenever you edit
# the corpus or the page itself.

on:
  schedule:
    - cron: "0 9 * * *"   # 09:00 UTC daily (~4-5am US Eastern). Change to taste.
  workflow_dispatch:        # lets you run it by hand from the Actions tab
  push:
    branches: [ main ]
    paths:
      - "data/passages.json"
      - "index.html"
      - "scripts/build.mjs"

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: "20"

      - name: Generate passages.js, today.json, preview.html
        run: node scripts/build.mjs

      - name: Commit if anything changed
        run: |
          git config user.name "mythos-bot"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add data/passages.js data/today.json preview.html
          if git diff --staged --quiet; then
            echo "Nothing to update today."
          else
            git commit -m "Daily build: $(date -u +%Y-%m-%d)"
            git push
          fi
