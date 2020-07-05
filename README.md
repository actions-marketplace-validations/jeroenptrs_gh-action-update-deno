# Update Deno Version
Fetch the latest version from denoland/deno and store it in a file

---

This Github action is designed to keep track of the latest Deno version. It will
1. check `denoland/deno` every 4 hours for the latest tag with prefix `v`
  - std has `std` as prefix, so v is for Deno itself
  - this action uses the Github Actions cron functionality. You can change the check to any interval you'd like
1. checkout your repo, echo the Deno version to a file
  - By default the `v` prefix is stripped
  - Also by default, the file name is DENO_VERSION
1. try to open up a Pull Request with the updated version
  - It will skip creating a pull request when there are no changes
