## Overview
An Emergence site can be linked with one or more Git repositories by defining a link in `php-config/Git.config.php` and
excecuting operations via various developer-accessible REST endpoints within `site-root/git/`. Behind the scenes, a
separate working directory is created for each link within the site's `/emergence/sites/*/site-data` scratch directory on the server's real filesystem. Files
from the site's virtual filesystem are incrementally synchronized to/from these working directories on-demand and Git's native
command-line tools are used to operate on them.

## Setup guide
### Step 1: Create remote repository
First, a remote git repository and/or branch to link with must be initialized and accessible via SSH, be it hosted on GitHub or a private
server. The branch may contain some initial files like a README, and they will be ignored as long as they are not within any of the paths specified
in the link configuration. If you will be pushing to this repository from other clients, it is recommended to give the Emergence site its own branch to push/pull
from. There is not yet any web interface available for resolving merge conflicts so if cooperation with other clients is required it is best for now to deal
with merging externally and limit all pull/push operations 

### Step 2: Create configuration
Create or open `php-config/Git.config.php` and input an entry like this:

```language-php
<?php

Git::$repositories['myrepo'] = [
    'remote' => 'git@github.com:MyOrganization/myrepo.git'
    ,'originBranch' => 'master'
    ,'workingBranch' => 'master'
    ,'localOnly' => true
	,'trees' => [
		'html-templates'
    	,'php-classes'
    	,'php-config'
		,'site-root'
	]
];
```

You should, at minimum, replace `myrepo` in the `Git::$repositories` array key with a directory name for this link and set the `remote`
property to the SSH clone URL of your repository. Available settings include:

- `remote` -- The SSH clone URL of the remote repository

- `originBranch` -- The name of an *existing* branch in the remote repository that can be cloned as a starting point (it may be an empty branch but must exist)

- `workingBranch` -- The branch name to use for the local working directory. This may match `originBranch`, another existing branch, or may be an entirely new branch. When
pushing to the remote repository, this is where changes will be written.

- `localOnly` -- Set to `true` to only include files local to the current site when pushing to the remote repository, and exclude inherited files. Defaults to `false`.

- `trees` -- An array of paths that will be pulled/pushed with the linked repository. Each item in the array my be either a string value with no key, a string value with a string key,
or an array value with a string key:
  - `'html-templates'` -- A string value with no key will serve as both the source and destination paths. This example will link the `html-templates` directory in the root of the virtual filesystem
    to an `html-templates` directory in the root of the repository.

  - `'site-root/js/myapp' => 'src'` -- A string key with a string value will be used as a source and destination path, respectively. This example will link the `site-root/js/myapp` directory
    in the root of the virtual filesystem to a `src` directory in the root of the repository.

  - `'site-root/js' => ['path' => 'src', 'localOnly' => false]` -- An array value can override the `localOnly` option on a per-directory basis. The `path` key is optional and will default to
    the source path if not provided in the array. This example will link the `site-root/js` directory in the root of the virtual filesystem to the `src directory in the root of the repository,
    and include both local and inherited files when exporting.


### Step 3: Generate deploy key
In your local terminal or on your server, use ssh-keygen to create a unique key for this link, using the address of your site as the comment:
```language-bash
user@hostname ~ $ ssh-keygen -t rsa -C "http://mysite.example.com/"
```

When prompted to "Enter a file in which to save the key", **do not** accept the default choice to overwrite your `.ssh/id_rsa` file. Instead enter something like `/tmp/mysite`
(the exact name/path doesn't matter as long as you're not overwriting anything valuable.)

When prompted to "Enter passphrase (empty for no passphrase)", press enter without typing anything to set no passphrase.

After the command finished two new files will have been written. If you entered `/tmp/mysite` when prompted to enter a file the two files would be `/tmp/mysite` and `/tmp/mysite.pub`

### Step 4: Install deploy key to remote repository
If using a private Git server for the remote repository, follow your distribution's documentation for installing the SSH key.

If using GitHub, open the "Deploy keys" section of your repository's "Settings" page (https://github.com/*MyOrganization*/*myrepo*/settings/keys) and click **Add deploy key**. For **Title**,
you can enter anything, but the http URL of your site that you used for the key's comment is a good choice. In the **Key** text area, paste the contents of the `.pub` file generated
in the previous step (it will consist of one line starting with `ssh-rsa `) and click **Add key**.

## Step 4: Initialize working repository
Finally, log in to your site as a developer and visit <kbd>/git/init?repo=**myrepo**</kbd> where **myrepo** is the `Git::$repositories` array key you defined your link under. You
will find a text area pre-filled with `-----BEGIN RSA PRIVATE KEY-----` and `-----END RSA PRIVATE KEY-----` markers. You will find these same markers at the beginning and end of the
non-`.pub` file generated in the previous step. Paste the contents of that file into the text area so that it still starts and ends with one copy of those markers and click **Create repo**.

### Step 5: Initial commit
See [Commit changes](commit).

## Next steps
- [Commit changes](commit)
- [Push commits](push)
- [Pull changes](pull)
- [Sync changes](sync)
- [Using git shell for advanced operations](shell)