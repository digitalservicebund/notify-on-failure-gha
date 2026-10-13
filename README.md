# Slack notification for CI failure

## About

This GHA sends a message to a Slack channel when a [workflow job](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions#jobs) fails.

**Note:** if you want to receive a message for every run status (successful, failed, cancelled), use the [GitHub + Slack Integration](https://github.com/integrations/slack#actions-workflow-notifications) instead of this GHA.

## How to use

- Make sure your repository has access to the `SLACK_BOT_TOKEN` secret. DigitalService engineers can find instructions on how to request access in our internal documentation.
- Find and copy the Slack channel ID: right-click on the channel -> View channel details -> About.
- Make sure the job has checked out the git repository (e.g. with `actions/checkout`), as the action reads the latest commit.
- Add this code as the final step of every job you wish to receive failure notifications from:
```yaml
- name: Send failure to Slack
  uses: digitalservicebund/notify-on-failure-gha@HASH_PLACEHOLDER #vX.Y.Z
  if: ${{ failure() }}
  with:
    SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}
    SLACK_CHANNEL_ID: <slack channel ID>
```
- Open the [latest release](https://github.com/digitalservicebund/notify-on-failure-gha/releases/latest) and copy the full commit hash of its tag.
- Back in your code, replace every occurrence of `HASH_PLACEHOLDER` with the commit hash you just copied, and `vX.Y.Z` with the release version.

### Inputs

| Name               | Required | Description                                                                 |
| ------------------ | -------- | --------------------------------------------------------------------------- |
| `SLACK_BOT_TOKEN`  | yes      | Slack bot token, passed through from secrets.                               |
| `SLACK_CHANNEL_ID` | yes      | ID of the Slack channel to send messages to.                                |
| `GITHUB_JOB_NAME`  | no       | Job name to show in the message, if the action is not running within the job itself. Defaults to the current job. |

### Example

```yaml
name: Pipeline

on: [push]

jobs:
  test_job:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
      - name: Faulty step
        run: exit 1
      - name: Send failure to Slack
        uses: digitalservicebund/notify-on-failure-gha@b8fdda76a38fa8e94cf623ebdd03e4570bc4ceb0 # v1.9.0
        if: ${{ failure() }}
        with:
          SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}
          SLACK_CHANNEL_ID: C1290845823
```
