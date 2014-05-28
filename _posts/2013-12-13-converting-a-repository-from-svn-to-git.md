---
layout: post
published: false
category: Development
---

Conversion from SVN to git

Table of Contents
Overview
Setup a GitHub repository
Import SVN repo
Overview
Authors
Importing
Pruning
Normalize Line Endings
.gitignore
Push to GitHub
Decommission SVN Repository
Integrate with TeamCity
Integrate with FogBugz (mfission)


Overview

There are a few steps to this process:

Set up a GitHub repository and clone it locally
Import svn repo and dress it up
Push to GitHub
Decommission SVN Repository
Integrate with TeamCity
Integrate with FogBugz
Setup a GitHub repository




Import SVN repo

Overview

An overview of the process:
Generate your authors file
Import all commits from svn history the local repo
Reorganize tags from folders in Git to real tags
Prune out commits that contain large files that you don't want in your git repository 
Normalize line endings
Modifying the .gitignore file
Importing from SVN to git is a common process, and as a result an importer tool is built into git.  It is the git-svn utility which bridges git and svn.   You can even use it set up a local git repository as a proxy to svn.  That way you can use the git client to commit branch, etc, and all code changes are pushed to subversion.  In our case, you simply use it for a one time import.

Authors

git-svn uses a mapping file to map svn users to git users.  The git-svn import will fail when it finds a user that is not in your mapping file.  there are ways to go through your svn history and find all user names.  google it.

Importing

If you care about your tags and branches, you'll want to use svn2git.  As tags are simply folders in svn, the git-svn client imports them as such.   svn2git is a wrapper around git-svn and converts the branches and tags folders created during the import process to real tags in git.   Unfortunately, svn2git gets hairy when you have not-typical svn structures.   The preferred structure is:
/trunk
   /code
/tags
  /tag1
  /tag2
/branches
  /branch1

svn2git takes care of both steps 1 and 2 above.

Pruning

Remember everyone gets the full commit history from the beginning of time when cloning the repository, so it is important to keep the repository size down.  This also means that if a large file (like a database backup) was commited to svn last year but deleted 6 months ago, it will still be in your git history when it is imported.   

You would use the git filter-branch command to delete unwanted files from your history.  Something like:

git filter-branch -f --index-filter "git rm --cached --ignore-unmatch Admin/Dependencies -r" --prune-empty --tag-name-filter cat -- --all

This single command may take a really long time since it looks at every commit in your repo for the files you're looking to filter out.  It may take a second per commit, so that adds up quickly.

There are tools to go through yoru repo and give you the filenames of your largest files in your history.  Google it.

It is important that you do this before you make your repo publically available since all commit hashes (revision numbers) change when changing the git history.

Normalize Line Endings

git and GitHub in particular are pretty strict about line endings and also have problems when sending text files back and forth between windows and linux systems.   The result is you will make a one line change, commit it, and github will think you changed the whole file because all of the line endings have changed.

The GitHub for windows client will fix all of your line endings for you.  There are other ways to do this too.  You'd want to do this before other people start using the repo.

.gitignore

You'll want to conver your svnignore settings to the .gitingore file.  you'll want to do this before people start using the repo since it's awkward to switch between branches that have an old version of a .gitignore and a new one.  Files show up as ready for commit when they should be deleted.  It's a pain.

Push to GitHub




Decommission SVN Repository

You'll want to make sure people don't think the SVN repo is the real deal.   at least make it read only.  But people could still do an SVN update, make a bunch of changes, then go to commit only to find out they're knocking on the wrong door.

Renaming the repo would be good cuz then people can't even do an SVN update anymore.

Integrate with TeamCity

Generate deploy keys in github and put the key file on the team city server.   Reference this file in your VSC root settings.

Integrate with FogBugz (mfission)

In the repository settings in GitHub, click on the "Service Hooks" item.  Choose FogBugz and follow the instructions.  You can now put BugzId: XXX in any commit, and the commit will be referenced in the XXX mfission case.