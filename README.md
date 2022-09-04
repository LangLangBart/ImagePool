
### 🔴 Problem
- GitHub does not provide a way to upload an image through its API. To work around this limitation, this repository hosts all the images I use in Issues and PullRequests.
  - More details on this limitation: [repo:cli/cli #1895](https://github.com/cli/cli/issues/1895#issuecomment-718899617)
### 👍 Solution request to GitHub
- There is already a [rate-limit](https://docs.github.com/en/rest/rate-limit) for using the GitHub API, a good solution would be to extend this for uploading images to limit abuse.
  - For example, 1 MB per file size and 30 MB per month (free offer).
