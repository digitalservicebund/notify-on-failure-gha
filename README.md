# Slack notification for CI failure

## About

This GHA sends a message to a Slack channel when a [workflow job](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions#jobs) fails.

**Note:** if you want to receive a message for every run status (successul, failed, cancelled), use the [GitHub + Slack Integration](https://github.com/integrations/slack#actions-workflow-notifications) instead of this GHA.

## How to use

- Ask the platform team to add the `SLACK_BOT_TOKEN` secret to your repo.
- Find and copy the slack channel ID: right clicking on channel -> view channel details -> about.
- Add this code as final step of every job you wish to received failure notification from:
```yaml
- name: Send failure to Slack
  uses: digitalservicebund/notify-on-failure-gha@HASH_PLACEHOLDER
  if: ${{ failure() }}
  with:
    SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}
    SLACK_CHANNEL_ID: <slack channel ID>
```
- Copy the last commit hash from our [internal GitHub actions repo](https://github.com/digitalservicebund/notify-on-failure-gha/commits/main/).
- Back to your code, search and replace every occurence of `HASH_PLACEHOLDER` with the commit hash you just copied.

### Example

```yaml
name: Pipeline

on: [push]

jobs:
  test_job:
    runs-on: ubuntu-latest
    steps:
      - name: Faulty step
        run: exit 1
      - name: Send failure to Slack
        uses: digitalservicebund/notify-on-failure-gha@latest
        if: ${{ failure() }}
        with:
          SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}
```

## Updating this action

After merging a dependabot PR or pushing changes, you need to [cut a new release](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository).
