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

branch FEATURE inserted into DB and listed in settings (selected by default)
commits from FEATURE inserted into DB: author ltonlorenziex

created new commit in FEATURE with --author= ltonlorenzi
appears on gh as: ltonlorenzi authored and ltonlorenziex committed 2 minutes ago
nothing changed in the app and the db: refetching branches and refetching repos and nothing changed
listRepos executed but not inserting new commits into db
[BUG]: listRepos function param fetchCommits: false (In my opinion it should be false, and refetched like every 30 minutes or so)
    -changed the parm to true and it fetches correctly.
third commit inserted into db correctly: author = new author

in main
git merge FEATURE (fast-forward)
git push

no merge commit created
delete branch FEATURE on github
refetch branches and also repos: FEATURE branch persists (its in the db as active)
[BUG]: when user removes a branch on github, its not deleted from the db
    -fixed, when listBranches is executed we check that the activeBranches exists on github
    -otherwise we delete the branches
[BUG]: FEATURE branch persist
    CAUSE: listBranches command to fetch origin not updating local branches (still have references to deleted branch)
    SOLUTION: --prune

branch correctly removed from the DB
COMMITS into DB: persisted successfully, branch field updated to MAIN, author from commit 1 and 2 correctly settled


TEST 2
create new branch FEATURE2