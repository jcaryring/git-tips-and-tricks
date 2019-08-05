# git-tips-and-tricks

# Useful Third-Party Tools
* [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) - zsh is an incredibly powerful shell, and oh-my-zsh makes managing it very easy.  Installation is one line and you instantly have some very helpful tools for git (as well as lots of other things)
* [iterm](https://www.iterm2.com/) - Iterm has many advantages over the built in mac terminal

# Workflows

Below are suggested workflows for various development tasks.  There are many ways to tackle these workflows.  The suggestions below simply outline one approach.

## New Feature (squash at completion approach)
This approach allows the developer to have incremental commits during feature development.  This might be helpful if you want to see various stages of you feature development to rollback or re-evaluate a change.  These commits will likely be meaningless after the feature development is complete, so they should be consolidated (squashed) to keep the git history clean.

*NOTE: It is also possible to use a combination of squash/ammend approaches*

1. `git checkout develop`
2. `git pull`
3. `git checkout -b APP-XXXX` (use your story/bug number as branch name
4. Start work on your feature
5. `git add .` then `git commit -m 'APP-XXXX: Description of work'`
6. repeat steps 4-5 as work progresses.  Pushing to remote is a good idea here as well `git push` or, if pushing for the first time, `git push origin HEAD -u`
7. `git log` When work is complete, inspect commits in the local branch and decide which should be kept and which should be squashed
8. Use interactive rebase to squash commits (see interactive rebase section)
9. `git push origin HEAD -u` push to origin (or, `git push` if you've already pushed and set remote)
10. Open PR - **PRO TIP**: The first time you push to origin, the command line output will contain a link to open a PR in github

## New Feature (ammend as you go approach)
This approach allows a developer to keep feature work in one commit without the hassle of an interactive rebase when work is complete.

*NOTE: It is also possible to use a combination of squash/ammend approaches*

1. `git checkout develop`
2. `git pull`
3. `git checkout -b APP-XXXX` (use your story/bug number as branch name
4. Start work on your feature
5. `git add .` then `git commit -m 'APP-XXXX: Description of work'`
6. repeat steps 4-5 as work progresses.  Pushing to remote is a good idea here as well `git push` or, if pushing for the first time, `git push origin HEAD -u`
7. `git log` When work is complete, inspect commits in the local branch and decide which should be kept and which should be squashed
8. Use interactive rebase to squash commits (see interactive rebase section)
9. `git push origin HEAD -u` push to origin (or, `git push` if you've already pushed and set remote)
10. Open PR - **PRO TIP**: The first time you push to origin, the command line output will contain a link to open a PR in github
