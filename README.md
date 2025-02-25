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

COMMIT 1 with ltonlorenziex

COMMIT 2:
//changed the author and created a new commit
GIT_COMMITTER_NAME="ltonlorenzi"
GIT_COMMITTER_EMAIL="luciano.tonlorenzi@gmail.com" git commit --author="ltonlorenzi <luciano.tonlorenzi@gmail.com>" -m "commit from ltonlorenzi"

git push --set-upstream origin FEATURE2

refetch branches: list correctly the new branch (not selected)
branch not inserted into db, commit not inserted into db

refetch repos: 
    -commits inserted into db: first commit with main branch (wrong), second commit fine
[BUG]: git log: only get the branch name for the HEAD commits (the last commit in each branch)
    - listCommitsForRepo: its using git log, if its not the HEAD commit, it assing the commit to main branch

[TESTED] created a "third commit", this one has got the branch (tip commit/last)
        -refetch repos: this commit is inserted
                        -the other commits assigned to main branch
[SOLUTION]:
    instead of assignin main branch, get the branch for the commit without refs: 
    getEarliestBranch: git for-each-ref --sort=committerdate --contains bcb55ecde66a8912f4e2eb65faa1dd622a063153 --format="%(refname:short)" refs/remotes/
    this function returns the first branch that has got this commit

TEST: it works perfect. assigning the branches correctly, but what about the time?

TIME TO EXECUTE listRepos WITH THE CHANGE: 
    - 17188.07 ms
    - 17537.47
    - 17301.98

TIME WITHOUT THE CHANGE
    - 9160.12
    - 9894.25
    - 10181.99

WITH PARALELL EXECUTION OF PROMISES
    - 10688.69
    - 11238.83
    - 10889.72


NOW CONTINUING WITH TEST 2:

- branch main: 3 commits
- branch FEATURE2: 3 commits
both branches inserted into db, 6 commits inserted with original authors and correct branch

diselect FEATURE2 BRANCH: SUCCESS 3 commits deleted and branch FEATURE2 deleted
select FEATURE2 BRANCH: SUCCESS added again

NOW I WILL DO A --no-ff merge

1. created a PR from FEATURE2 -> Main
2. Merge --no-ff: Merge pull request #1 from ltonlorenziex/FEATURE2

the commit has not being inserted automatically into db
merge commit created on github with: 4a4e33da234d933ea0be394e77d970943a724c19
refetch branches: does not insert the commit into db
refetch repos: insert merge commit into db [FIX]: should not insert merged commits into DB

[BUG]: now the commits from main branch are reassigned to FEATURE2, because FEATURE2 now also have those commits
fixed with git name-rev

Now I will go on deleting the branch on github

nothing happens in the app and the db
refetch branch: branch deleted from db and not listed - OK
correctly main branch inserted into the commits
