# GitHub Actions workflows

Example workflow that tracks pushes:

```yml
name: Track push
on:
  push:
    branches:
      - main
jobs:
  track:
    runs-on: ubuntu-latest
    env:
      GETDX_SECRET_KEY: ${{ secrets.DX_SECRET_KEY }}
    steps:
      - name: Track push
        run: |
          printf -v data '{"name": "branch.pushed", "github_username": "%s", "timestamp": "%s", "metadata": {"repo": "%s", "branch": "main"}}' "$GITHUB_ACTOR" "$EPOCHSECONDS" "$GITHUB_REPOSITORY"
          curl -X POST -H "Content-Type: application/json" -H "Authorization: Bearer $GETDX_SECRET_KEY" -d "$data" https://api.getdx.com/events.track
```


Example workflow that tracks reviews:

```yml
name: Track PR approvals
on:
  pull_request_review:
    types: [submitted]
jobs:
  track:
    runs-on: ubuntu-latest
    env:
      GETDX_SECRET_KEY: ${{ secrets.GETDX_SECRET_KEY }}
      REVIEW_STATE: ${{ github.event.review.state }}
    steps:
      - name: Track PR approvals
        run: |
          printf -v data '{"name": "pull_request_review.submitted", "github_username": "%s", "timestamp": "%s", "metadata": {"repo": "%s", "review_state": "%s"}}' "$GITHUB_ACTOR" "$EPOCHSECONDS" "$GITHUB_REPOSITORY" "$REVIEW_STATE"
          curl -X POST -H "Content-Type: application/json" -H "Authorization: Bearer $GETDX_SECRET_KEY" -d "$data" https://api.getdx.com/events.track
```
