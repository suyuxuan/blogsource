---
title: Git -  How to modify the timestamp of a commit
date: 2018-09-17 22:59:50
tags:
---

Sometimes we need to modify the date of a commit in git to show a readable history.

In git, there are two fields to mark timestamp on each commit - `Author Date` and `Commit Date`. An answer from @eckes on stackflow explains the diff between these two fields:
[https://stackoverflow.com/questions/11856983/why-git-authordate-is-different-from-commitdate](https://stackoverflow.com/questions/11856983/why-git-authordate-is-different-from-commitdate)

<!-- more -->

To overwrite author date we can use the command :

```bash
> date -R

Mon, 17 Sep 2018 23:12:03 +0800

> git commit --amend --date="Mon, 17 Sep 2018 23:12:03 +0800"
```

Author Date of current commit would be updated to `Mon, 17 Sep 2018 23:12:03 +0800` after the command.

While if we want to modify the Commit Date, it would be a little more complicated:

```bash
> GIT_COMMITTER_DATE='2018-09-17 00:00:00' git commit --amend --no-edit --date '2018-09-17 00:00:00'
```

References:
[https://segmentfault.com/q/1010000015386964](https://segmentfault.com/q/1010000015386964)
[https://github.com/bitriddler/git-change-date](https://github.com/bitriddler/git-change-date)
