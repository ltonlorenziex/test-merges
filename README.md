# test-merges


HISTORY:

Repo created
First commit with ltonlorenziex in main branch

Repo addded in settings:
    -repo successfully cloned: inserted into db and active
    -main branch selected by default: inserted into db
    -first commit inserted into db

checkout to FEATURE branch
commit with ltonlorenziex
pushed the commit
[BUG]: new FEATURE branch not listed in settings: even if I remove the cached data
    CAUSE: the local repo does not have the new branches from origin, and the command is not listing those branches
        -git for-each-ref --sort=-committerdate --format="%(refname:short) %(committerdate:iso8601)" refs/remotes/origin/
    SOLUTION: fetch origin before running that command

branch FEATURE inserted into DB
commits from FEATURE inserted into DB: author ltonlorenziex

