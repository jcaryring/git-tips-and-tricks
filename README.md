# git-tips-and-tricks

# Useful Third-Party Tools
* [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) - zsh is an incredibly powerful shell, and oh-my-zsh makes managing it very easy.  Installation is one line and you instantly have some very helpful tools for git (as well as lots of other things)
* [iterm](https://www.iterm2.com/) - Iterm has many advantages over the built in mac terminal

# [Workflows](#workflows)

Below are suggested workflows for various development tasks.  There are many ways to tackle these workflows.  The suggestions below simply outline one approach.

## [New Feature (squash at completion approach)](#new-feature-squash-completions)
This approach allows the developer to have incremental commits during feature development.  This might be helpful if you want to see various stages of you feature development to rollback or re-evaluate a change.  These commits will likely be meaningless after the feature development is complete, so they should be consolidated (squashed) to keep the git history clean.

*NOTE: It is also possible to use a combination of squash/ammend approaches*

1. `git checkout develop`
2. `git pull`
3. `git checkout -b APP-XXXX` (use your story/bug number as branch name)
4. Start work on your feature
5. `git add .` then `git commit -m 'APP-XXXX: Description of work'`
6. repeat steps 4-5 as work progresses.  Pushing to remote is a good idea here as well `git push` or, if pushing for the first time, `git push origin HEAD -u`
7. `git log` When work is complete, inspect commits in the local branch and decide which should be kept and which should be squashed
8. Use interactive rebase to squash commits (see interactive rebase section)
9. `git push origin HEAD -u` push to origin (or, `git push` if you've already pushed and set remote)
10. Open PR - **PRO TIP**: The first time you push to origin, the command line output will contain a link to open a PR in github

## [New Feature (ammend as you go approach)](#new-feature-amend-as-you-go)
This approach allows a developer to keep feature work in one commit without the hassle of an interactive rebase when work is complete.  *This approach might be difficult if you have multiple developers working in the same feature branch.*

*NOTE: It is also possible to use a combination of squash/ammend approaches*

1. `git checkout develop`
2. `git pull`
3. `git checkout -b APP-XXXX` (use your story/bug number as branch name)
4. Start work on your feature
5. `git add .` then `git commit -m 'APP-XXXX: Description of work'`
6. As work progresses, ammend your commit with the new work. `git add .` followed by `git commit --amend --no-edit`
7. `git push origin HEAD -u` push to origin regularly.  If you've ammended a commit, a force push will be necessary `git push origin HEAD -f`
8. Open PR - **PRO TIP**: The first time you push to origin, the command line output will contain a link to open a PR in github

### Important Note
This approach is very conventient, but there is a downside.  If multiple team members are working in the feature branch, it will be necessary to reset local branches from origin after force pushes occur. 
**Here's how that would work:**
1. Person A force pushes for to APP-XXXX
2. Person B has local work in APP-XXXX but is not up to date with changes from Person A
3. Person B fetches `git fetch`
4. Person B rebases their local APP-XXXX on the remote APP-XXXX branch `git rebase origin/APP-XXXX`
5. Person B fixes and conflicts and is ready to continue work

# Rewriting History
## [Rebase Feature Branch](#rebase-feature-branch)
As you work in a feature branch, other work may be commited to the base branch which can cause conflicts or cause your branch to be stale (out of date with the base).  To bring your branch up to date with the base branch, perform a rebase.
1. `git fetch` - this downloads all remote changes but does not do a merge/etc like `git pull`
2. `git checkout APP-XXX` - checkout feature branch
3. `git rebase origin/develop` - perform the rebase
4. At this point, you might have merge conflicts.  Git will tell you this.  
** If you have conflicts, fix them with `git mergetool`
** `git rebase --continue` Once you've addressed merge conflicts, continue the rebase.  You may have to fix more conflicts along the way
** **NOTE** `git status` will tell you if the rebase is still in progress.  If you are using zsh, you will see the branch name becomes a hash during the rebase.  This will change back to the branch name when the rebase is complete
5. `git push origin HEAD -f` Force push to remote (or if you haven't pushed yet, `git push origin HEAD -u`)

## [Alternative to Rebase (for difficult merge issues)](#rebase-alternative)
Sometimes, if you are very out of date with teh base branch and lots of work has happened, fixing conflicts during a rebase can be very time consuming.  Rebasing works by rolling back to a shared commit between branches and sequentially applying commits in order.  Because of this, you may end up resolving unnecessary conflicts.  To work around this, you can use an alternative, cherry-pick based approach.  This works best on branches with very few or just one commit of new work.
1. `git log` - copy the commit hash(es) for relevant branch work to a note app
2. `git fetch` - ensure you've downloaded all up-to-date repo data
3. `git reset --hard origin/BASE-BRANCH-NAME` - replace `BASE-BRANCH-NAME` with your base branch name.  This is likely `develop`
4. `git cherry-pick XXXXXXXXX` replace `XXXXXXXXX` with the hash noted above
5. Resolve any conflicts
6. Repeat steps 4-5 for additional commits
7. Done!

## [Squash Commits - Interactive Rebase](#interactive-rebase-squash-commits)
When working on a feature, you might end up with multiple commits which should be consolidated into one.  To do this, use the interactive rebase feature.
1. `git log` In the log, identify which commit you will keep and which will be squashed
2. Count the number of commits to squash plus the commit you will squash into
3. `git rebase -i HEAD~X` where `X` is the number from step 2
4. vi will now open.  For all commits you intend to squash, change the work `pick` to `f`
5. Save and exit vi
6. Your commits are now squashed! Since you rewrote history, you will need to force push the branch if you've already pushed to origin

## [Amending a commit](#amending-commits)
* `git commit --amend` - amend a commit and update the commit message
* `git commit --amend --no-edit` - amend a commit (perhaps add files/changes) but don't change the commit message
* `git commit --amend --reset-author` - amend a commut and update the message and author

## [Add/change a previous commit](#add-change-previous-commit)
1. `git log` - count the position of you commit in history.  e.g., if it's the 3rd commit, note that number
2. `git rebase -i HEAD~X` where X is the number from the previous step
3. Find commit in the list and change the word `pick` to `edit` or `e`
4. Save/Quit vi
5. At this point the history is rolled back to the selected commit.  Make any changes you'd like to the commit
6. `git rebase --continue` when satisfied, continue the rebase
7. You may need to fix conflicts at this point
8. continue the rebase after any conflict resolution
9. Done!

## [Reword a previous commit](#reword-previous-commit)
1. `git log` - count the position of you commit in history.  e.g., if it's the 3rd commit, note that number
2. `git rebase -i HEAD~X` where X is the number from the previous step
3. Find commit in the list and change the word `pick` to `reword` or `r`
4. Save/Quit vi
5. vi will now be reopened with the commit message of the selected commit.  Edit it and save the changes
6. `git rebase --continue` when satisfied, continue the rebase
7. Done!

# Reading History

## [Compare Branches](#compare-branches)
Show commits that are new to a branch.  Useful to see what new commits are in a release compared with the last release

`git log --cherry-pick --oneline --no-merges --left-only release-v3.7.0...release-v3.8.0`

## [List Branches Containing Commit](#list-branches-containing-commit)
`git branch --contains <hash>`
