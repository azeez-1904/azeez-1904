name: Generate snake

on:
  schedule:
    # 03:12 UTC daily — off the hour, so it doesn't queue behind the
    # thundering herd of workflows everyone else schedules at :00
    - cron: "12 3 * * *"
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  generate:
    runs-on: ubuntu-latest
    permissions:
      contents: write

    steps:
      - name: Generate the snake SVGs
        uses: Platane/snk@v3
        id: snake
        with:
          github_user_name: ${{ github.repository_owner }}
          # Palettes match the personal site: near-black ground, violet ramp
          # from #241539 through #a855f7 to #c9a2ff. The light variant keeps
          # the same hue so the two read as one design, not two.
          outputs: |
            dist/snake-dark.svg?color_snake=#c9a2ff&color_dots=#0c0a10,#241539,#4d2676,#a855f7,#c9a2ff
            dist/snake-light.svg?color_snake=#7c3aed&color_dots=#f4f1f7,#e0d4f5,#b794f6,#7c3aed,#4c1d95

      - name: Push to the output branch
        uses: crazy-max/ghaction-github-pages@v4
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
