---
layout: post
title: "Removing Bias"
authors: ["nick-dyer"]
tags: ["Craftsmanship"]
---

Here at the 8th Light London office, a dedicated group of individuals meets
regularly to discuss diversity. Our intent is to broaden the company’s
understanding of the problems our industry is facing. One of the outcomes of
our conversations has been the objective to remove any bias from our
recruitment process.

Our application process has a few stages, one of which,
the main technical part, is a coding challenge. Having been set the challenge,
applicants send us their code, which is reviewed and feedback is given to the
applicant. Historically, this entire process has has been carried out by one
individual, taking the candidate through the initial submission, feedback
session and subsequent resubmission.


![Before Process](/images/Before.png "Before Process"){: .center-image}


There are benefits to this approach, the biggest of which is the ability to
develop a relationship with the applicant. This not only gives us some insight
on them, but can also help put the candidate at ease. The downside to this
approach is that it introduces unconscious bias. In order to combat this we are
taking a number of steps to make our process fairer:


![After Process](/images/After.png "After Process"){: .center-image}


1. Candidates are given the code challenge by Crafter 1, without any
   interaction with Crafters 2 or 3.
2. Once received, their code submission is then anonymised.
3. Crafter 2 then reviews the code by following a code review rubric and
   records their feedback.
4. The feedback is delivered to the candidate by Crafter 3 during a code
   review.
5. The candidate then re-submits their code taking into account the feedback.
6. The anonymised re-submission is assessed by the original reviewer for
   continuity.


You might wonder why Crafter 1 doesn’t deliver the feedback. This is just
a logistical consideration since Crafter 1 carries out this role permanently
for every applicant, so in order to balance that workload doesn’t regularly get
involved in the reviews themselves. Importantly, the reviewer abstraction we
now have means that Crafter 2 can analyse the code free of bias. Crafter
3 can deliver Crafter 2’s feedback with more objectivity since the comments
are not their own.


### Anonymisation Script


This script has been adapted from this
[gist](https://gist.github.com/pozorvlak/8784840).


### Run Instructions


```
$ cd <code_submissions_directory>

$ ./anonymise.sh applicant-152 git@github.com:<name>/<repo>.git
```

The script is available here: [Git Gist](https://gist.github.com/nickbdyer/7203b4f6854e29c28f0d569a7f415b04)


A lot of this script will be relatively straight forward. I’ll just pick out
a couple of lines to discuss.


```shell
for branch in $BRANCHES; do
  git filter-branch --env-filter '
    export GIT_AUTHOR_NAME="Anonymous"
    export GIT_AUTHOR_EMAIL="anon@example.com"
    export GIT_COMMITTER_NAME="Anonymous Committer"
    export GIT_COMMITTER_EMAIL="anon@commit.com"' $NEW_BRANCH
done
```


This is the key part of anonymisation. We use `filter-branch` to rewrite the
history in each branch. The `--env-filter` flag because we are only editing the
environment in which the commit was made (GIT_AUTHOR and GIT_COMMITTER).


```
git for-each-ref --format="%(refname)" refs/original/ | xargs -n 1 git update-ref -d
```


We then remove any reference to the previous commits which are automatically
backed up into `refs/original/`.


```
git remote add -f --no-tags $IDENTIFIER $TEMP
```


With the git references cleaned, we add them as a remote to our source
directory and fetch automatically. Now when we checkout to each branch in the
next step, all the information is already there.  After a few tidy up functions
what we are left with is a collection of branches on our GitHub repository, all
tagged with a specific identifier. These branches represent the work done by
the applicant in its entirety. Code reviewers can thus be directed to
a specific branch on the repository and review the anonymised code.


Credit is due to Daniel Irvine for his work on the adaptation of the script.
