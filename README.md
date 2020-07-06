<p align="center">
   <h1 align="center">Update Deno Version</h1>
</p>
<p align="center">Fetch the latest version from denoland/deno and store it in a file</p>
<p align="center">
   <img src="https://img.shields.io/github/v/tag/jeroenptrs/gh-action-update-deno?label=latest" />
</p>

---

_**NOTE:** this is a recepy not a fully fledged Github Action!, copy and paste the code below into a new action!_


```yml
name: Update Deno

on:
  schedule:
    - cron: '0 */4 * * *'

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: oprypin/find-latest-tag@v1
        id: denoVersion
        with:
          repository: denoland/deno
          prefix: v

      - uses: actions/checkout@v2
        
      - name: updates Deno version
        run: |
          echo "${{ steps.denoVersion.outputs.tag }}" | cut -d'v' -f 2 > DENO_VERSION
      
      - name: Create Pull Request
        id: cpr
        uses: peter-evans/create-pull-request@v2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          commit-message: Update Deno version to ${{ steps.denoVersion.outputs.tag }}
          committer: Github <noreply@github.com>
          author: ${{ github.actor }} <${{ github.actor }}@users.noreply.github.com>
          title: Update Deno version to ${{ steps.denoVersion.outputs.tag }}
          body: |
            Deno updated to version ${{ steps.denoVersion.outputs.tag }}
          labels: automated pr
          draft: false
          branch: bump-deno-${{ steps.denoVersion.outputs.tag }}
          request-to-parent: false

      - name: Check outputs
        run: |
          echo "Deno version is ${{ steps.denoVersion.outputs.tag }}"
          echo "Pull Request Number - ${{ steps.cpr.outputs.pull-request-number }}"
```

This Github action is designed to keep track of the latest Deno version. It will
1. check `denoland/deno` every 4 hours for the latest tag with prefix `v`
   - std has `std` as prefix, so v is for Deno itself
   - this action uses the Github Actions cron functionality. You can change the check to any interval you'd like
1. checkout your repo, echo the Deno version to a file
   - By default the `v` prefix is stripped
   - Also by default, the file name is DENO_VERSION
1. try to open up a Pull Request with the updated version
   - It will skip creating a pull request when there are no changes
