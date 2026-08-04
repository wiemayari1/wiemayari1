name: Update GitHub stats

on:
  schedule:
    - cron: "0 3 * * *"
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: write

    steps:
      - uses: actions/checkout@v4

      # === STREAK STATS ===
      - name: Generate streak stats
        uses: DenverCoder1/github-readme-streak-stats@main
        with:
          options: user=wiemayari1&theme=tokyonight&hide_border=true&disable_animations=true
          path: profile/streak.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      # === GENERAL STATS ===
      - name: Generate GitHub stats
        uses: lowlighter/metrics@latest
        with:
          filename: profile/stats.svg
          token: ${{ secrets.GITHUB_TOKEN }}
          user: wiemayari1
          template: classic
          base: header, activity, community, repositories, metadata
          config_timezone: Africa/Tunis
          config_twemoji: yes
          config_display: large
          plugin_lines: yes
          plugin_languages: yes
          plugin_languages_ignored: html, css
          plugin_languages_details: bytes-size, percentage
          plugin_languages_limit: 8
          plugin_isocalendar: yes
          plugin_isocalendar_duration: full-year
        continue-on-error: true

      # === COMMIT ===
      - name: Commit all stats
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add profile/
          git commit -m "Update GitHub stats" || exit 0
          git push
