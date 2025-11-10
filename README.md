#!/usr/bin/env bash
# Enforce "SC-1234: short summary" on the first line

set -euo pipefail

HOOK_FILE="${1:?missing commit message file}"
SOURCE="${2:-}"     # message|template|merge|squash|commit|...
SHA1="${3:-}"

# Skip for merge/squash commits
if [[ "$SOURCE" == "merge" || "$SOURCE" == "squash" ]]; then
  exit 0
fi

# Read first line only
read -r COMMIT_MSG < "$HOOK_FILE"

# Require ticket like SC-1234: ...
PATTERN='^SC-[0-9]+: '

if [[ ! "$COMMIT_MSG" =~ $PATTERN ]]; then
  {
    echo
    echo "    ERROR! Bad commit message."
    echo "    First line: '$COMMIT_MSG'"
    echo "    Missing JIRA ticket. Expected format: 'SC-1234: my commit'."
    echo
  } >&2
  exit 1
fi

exit 0
