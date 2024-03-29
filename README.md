# Slack notification for CI failure

## About

This GHA sends a message to a Slack channel when a [workflow job](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions#jobs) fails.

**Note:** if you want to receive a message for every run status (successul, failed, cancelled), use the [GitHub + Slack Integration](https://github.com/integrations/slack#actions-workflow-notifications) instead of this GHA.

## How to use

- Add the `SLACK_WEBHOOK_URL` secret to your repo. This URL is an [`incoming webhook`](https://api.slack.com/legacy/custom-integrations/messaging/webhooks) that tells slack which channel should receive this notification. If you don't have one, ask [IT support on slack](https://digitalservicebund.slack.com/archives/C02SB73D90F).
- Add this code as final step of every job you wish to received failure notification from:

```yaml
- name: Send failure to Slack
  uses: digitalservicebund/notify-on-failure-gha@HASH_PLACEHOLDER
  if: ${{ failure() }}
  with:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
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
        uses: digitalservicebund/notify-on-failure-gha@d050613c380ee805de056e6ba37eb6676a98ce4f # v1.1.0
        if: ${{ failure() }}
        with:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

## Updating this action

After merging a dependabot PR or pushing changes, you need to [cut a new release](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository).
