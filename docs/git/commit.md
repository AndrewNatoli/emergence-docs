## Overview
When you trigger a commit, the Emergence will synchronize any changes in the virtual filesystem to the repository's working directory on disk, commit all files with
the provided message, and push to the remote repository.

## Executing a commit
Log in as a developer and open <kbd>/git/commit?repo=**myrepo**</kbd> where **myrepo** is the `Git::$repositories` array key you defined your link under. You will be prompted
with a text field to enter a commit message before the commit is actually executed. The commit will be immediately pushed to the remote repository.