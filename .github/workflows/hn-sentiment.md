---
# Trigger - when should this workflow run?
on:
  issue_comment:
    types: [created]
if: startsWith(github.event.comment.body, '/hn-sentiment')

# Permissions - what can this workflow access?
# Write operations (creating issues, PRs, comments, etc.) are handled
# automatically by the safe-outputs job with its own scoped permissions.
permissions:
  contents: read
  issues: read

# Tools - external API access via web-fetch
tools:
  web-fetch: null

# Network access
network:
  allowed:
    - hacker-news.firebaseio.com

# Outputs - what APIs and tools can the AI use?
safe-outputs:
  add-comment:
    max: 1
  # actions:
  # activation-comments:
  # create-issue:
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

# hn-sentiment

Create a ChatOps slash command called /hn-sentiment. When a user posts
a comment on a GitHub issue that starts with "/hn-sentiment <url>",
where <url> is a Hacker News story URL (e.g.
https://news.ycombinator.com/item?id=12345), do the following:
1) Extract the Hacker News item ID from the URL.
2) Fetch up to 50 top-level comments for that story from the Hacker
   News API.
3) Perform sentiment analysis on the comment text, classifying each
   comment as Positive, Negative, or Neutral.
4) Produce a summary that shows: the overall sentiment (with percentage
   breakdown), the top 3 most positive comments (with excerpt), and
   the top 3 most negative comments (with excerpt).
5) Reply to the original issue comment with the analysis formatted in
   Markdown.
If no URL is provided or the URL is not a valid Hacker News item,
reply with a helpful error message.

## Instructions

1. Parse the comment body and extract the first argument after
   `/hn-sentiment`.
2. Validate that the argument is a Hacker News story URL and extract the
   item ID from either:
   - `https://news.ycombinator.com/item?id=<id>`
   - `https://hacker-news.firebaseio.com/v0/item/<id>.json`
3. If the URL is missing or invalid, reply to the original comment with a
   clear error message explaining the expected format.
4. Fetch the story data and up to 50 top-level comments from the Hacker
   News API using `web-fetch`:
   - `https://hacker-news.firebaseio.com/v0/item/<id>.json`
   - For each top-level comment ID in `kids`, fetch the comment text.
5. For every retrieved comment, classify the sentiment as Positive,
   Negative, or Neutral.
6. Build a Markdown reply with:
   - overall sentiment counts and percentages
   - the top 3 most positive comments with short excerpts
   - the top 3 most negative comments with short excerpts
   - a note if no top-level comments were available
7. Post the analysis as a reply to the original issue comment using
   `add-comment`.
8. If the Hacker News item cannot be fetched or the story has no valid
   comments, reply with a helpful error message instead of the analysis.

## Notes

- Run `gh aw compile` to generate the GitHub Actions workflow
- See https://github.github.com/gh-aw/ for complete configuration options and tools documentation
