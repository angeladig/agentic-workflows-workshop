---
# Trigger - when should this workflow run?
on:
  schedule:
    - cron: daily on weekdays  # Every weekday at 9 AM UTC
  workflow_dispatch:  # Also allow manual trigger

# Permissions - what can this workflow access?
# Write operations (creating issues, PRs, comments, etc.) are handled
# automatically by the safe-outputs job with its own scoped permissions.
permissions:
  contents: read
  issues: read
  pull-requests: read

# Tools - GitHub API access via toolsets (context, repos, issues, pull_requests)
tools:
  github:
    toolsets: default

# Network access
network:
  allowed:
    - hacker-news.firebaseio.com

# Outputs - what APIs and tools can the AI use?
safe-outputs:
  create-issue:          # Creates issues (default max: 1)
    max: 1               # Optional: specify maximum number
  # actions:
  # activation-comments:
  # add-comment:
  # add-labels:
  # add-reviewer:
  # allowed-github-references:
  # assign-milestone:
  # assign-to-agent:
  # assign-to-user:
  # autofix-code-scanning-alert:
  # call-workflow:
  # close-discussion:
  # close-issue:
  # close-pull-request:
  # concurrency-group:
  # create-agent-session:
  # create-agent-task:
  # create-code-scanning-alert:
  # create-discussion:
  # create-project:
  # create-project-status-update:
  # create-pull-request:
  # create-pull-request-review-comment:
  # dispatch-workflow:
  # dispatch_repository:
  # environment:
  # failure-issue-repo:
  # footer:
  # group-reports:
  # hide-comment:
  # id-token:
  # link-sub-issue:
  # mark-pull-request-as-ready-for-review:
  # max-bot-mentions:
  # max-patch-files:
  # mentions:
  # merge-pull-request:
  # missing-data:
  # missing-tool:
  # noop:
  # push-to-pull-request-branch:
  # remove-labels:
  # reply-to-pull-request-review-comment:
  # report-failure-as-issue:
  # report-incomplete:
  # resolve-pull-request-review-thread:
  # scripts:
  # set-issue-type:
  # steps:
  # submit-pull-request-review:
  # threat-detection:
  # unassign-from-user:
  # update-discussion:
  # update-issue:
  # update-project:
  # update-pull-request:
  # update-release:
  # upload-artifact:
  # upload-asset:

---

# hn-daily-digest

Create a daily digest workflow for professional developers, referencing
relevant top Hacker News stories on technology that can be used today
by large companies. Every weekday, fetch the top 30 stories from the
Hacker News API (https://hacker-news.firebaseio.com/v0/topstories.json),
filter to stories with a score above 100 that are about software
engineering, cloud infrastructure, AI/ML, developer tooling, or
distributed systems. For each qualifying story include: the title, the
URL, the score, the number of comments, and a one-sentence summary of
why it is relevant to enterprise developers. Create a GitHub issue
titled "HN Digest – <date>" with the results formatted as a Markdown
table.

## Instructions

1. **Fetch Hacker News Stories**: Make a request to the Hacker News API (https://hacker-news.firebaseio.com/v0/topstories.json) to get the top 30 story IDs.

2. **Retrieve Story Details**: For each story ID, fetch the story details from https://hacker-news.firebaseio.com/v0/item/{id}.json to get:
   - Title
   - URL
   - Score (points)
   - Number of comments (descendants)
   - Time posted

3. **Filter Stories**: Keep only stories with:
   - Score > 100
   - Content related to: software engineering, cloud infrastructure, AI/ML, developer tooling, or distributed systems

4. **Create Summaries**: For each qualifying story, write a one-sentence summary explaining why it's relevant to enterprise developers.

5. **Format Results**: Create a Markdown table with columns: Title | URL | Score | Comments | Enterprise Relevance

6. **Create Issue**: Generate a GitHub issue with:
   - Title: "HN Digest – {date}" (use YYYY-MM-DD format)
   - Body: Include the Markdown table with filtered stories
   - No labels or assignees needed

7. **Handle Edge Cases**: If fewer than 5 qualifying stories are found, still create the issue with the available stories.

## Notes

- Run `gh aw compile` to generate the GitHub Actions workflow
- See https://github.github.com/gh-aw/ for complete configuration options and tools documentation
