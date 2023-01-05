#!/usr/bin/env bash
set -e

pr_sha () {
  local pr_number="$1"

  git ls-remote 2>/dev/null | grep "pull/${pr_number}/head" | awk '{ print $1 }'
}

pr_numbers_to_md () {
  for pr_number in "$@"; do
    echo "* #${pr_number}"
  done
}

default_branch () {
  git remote show origin | grep 'HEAD branch' | awk '{ print $NF }'
}

current_date=`date +%Y-%m-%d`
current_date_tight=`date +%Y%m%d`
branch_name="dependabot-${current_date_tight}"
default_branch=$(default_branch)

git fetch
git checkout -b "$branch_name" "origin/$default_branch"

for pr_number in "$@"; do
  head_sha=$(pr_sha $pr_number)
  git rev-list --reverse "origin/${default_branch}..${head_sha}" | git cherry-pick --stdin  --allow-empty
done

git push origin "$branch_name" --set-upstream

pr_body="
Cherry picked combination of:
$(pr_numbers_to_md $@)
"

gh pr create --title "Dependabot updates from ${current_date}" --body "$pr_body"
