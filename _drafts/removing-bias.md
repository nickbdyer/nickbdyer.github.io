---
layout: post
title: "Removing Bias"
authors: ["nick-dyer"]
tags: ["Craftsmanship"]
---

I have recently taken over the application process at 8th Light's London
Office. During the meeting where I offered to take on that role a colleague,
Daniel, suggested that we take a look into removing bias from our applications
process. Daniel is a driving force behind 8th Light's diversity programme in
London.

As part of the #london-diversity Slack channel, we have recently been reading
"The Loudest Duck" by Laura Liswood.

Anonymisation Script

```
#!/bin/sh

# $1 - new identifier to use
# $2 - repo to clone

IDENTIFIER=$1
SOURCE_REPO=$2
DEST_DIR=`pwd`

# Step 1 - Make temporary directory
SOURCE_GIT_DIR=`mktemp -d`
echo $SOURCE_GIT_DIR

cd $SOURCE_GIT_DIR

# bare because we don't need a working copy
git clone --bare $SOURCE_REPO $SOURCE_GIT_DIR

BRANCHES=`git branch --list | awk '{print $2}'`

# Step 2 - rename branches
for branch in $BRANCHES; do
  NEW_BRANCH=$IDENTIFIER-$branch
  git branch -m $branch $NEW_BRANCH
done

# Step 3 - change commit info
for branch in $BRANCHES; do
  # -f so multiple operations can be performed at once
  git filter-branch -f --env-filter '
    export GIT_AUTHOR_NAME="Anonymous"
    export GIT_AUTHOR_EMAIL="anon@example.com"
    export GIT_COMMITTER_NAME="Captain Commit"
    export GIT_COMMITTER_EMAIL="anon@commit.com"' $NEW_BRANCH
done

# Step 4 - Remove originals
git for-each-ref --format="%(refname)" refs/original/ | xargs -n 1 git update-ref -d

# Step 5 - Create remote in the original repo
# TODO: use --no-tags because this script won't have done any work on tags?
cd $DEST_DIR
git remote add -f --no-tags $IDENTIFIER $SOURCE_GIT_DIR

# Step 6 - checkout branch
for branch in $BRANCHES; do
  git checkout $IDENTIFIER-$branch
done

# Step 7 - Delete temporary directory
rm -rf $TEMP

# Step 8 - Delete remote branch
git remote rm $IDENTIFIER

# Step 9 - Push to origin
git push

# Step 10 - Delete local branch
git checkout master
git branch -D $IDENTIFIER-$branch

```

Most of this script should be relatively straight forward. I'll just pick out
a couple of lines that are more likely to be unfamiliar.

The purpose of this line is to accumlate all the branches and rename them. Most
applicants unsurpsingly use "master" as their submission branch. However, if
the applicant has been diligent with pull requests, then residual branches can
be interesting to see, and are preserved through this command.
