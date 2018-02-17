# Git

### Terminal commands

* Undoing changes
	* `git reset [commit]`
		* Moves HEAD to that commit and leaves some commits dangling (to be deleted next time git does garbage collection)
		* ex. `git reset HEAD~2`
	* `git revert [commit(s)]`
		* make a new commit that undoes the changes made in the commits
	* `git checkout --theirs .`
		* merge and accept their changes for all conflicted files
		* [reference](https://easyengine.io/tutorials/git/git-resolve-merge-conflicts/)
		* [reference2](https://stackoverflow.com/questions/914939/simple-tool-to-accept-theirs-or-accept-mine-on-a-whole-file-using-git)

### Utilities/references

* Use sourcegraph to look thru repos on web: sourcegraph.com/github.com/OWNER/REPO  
* Git hooks: run some scripts each time git action is taken <https://www.atlassian.com/git/tutorials/git-hooks>
* Remove previously committed large files: <https://stackoverflow.com/questions/8083282/how-do-i-remove-a-big-file-wrongly-committed-in-git>
* [Push/pull to multiple remotes](https://stackoverflow.com/questions/849308/pull-push-from-multiple-remote-locations)
* [Forking on Github](https://help.github.com/articles/fork-a-repo/)
* [submodules vs subtrees](https://andrey.nering.com.br/2016/git-submodules-vs-subtrees/)
* [making commits in the past](https://leewc.com/articles/making-past-git-commits/)
* [Merge theirs](https://stackoverflow.com/questions/10697463/resolve-git-merge-conflicts-in-favor-of-their-changes-during-a-pull)
