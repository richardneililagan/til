---
title: How to remove files from the git index when you update your .gitignore
tags: [git]
---

One of the things I notoriously forget when creating a project from scratch is to plug in a proper `.gitignore` before actually doing anything I'll regret. 

Like today, when I haphazardly installed node dependencies on a starter project, and promptly committed that into git, including all the stuff in `/node_modules`. Argh.

So in case you forget your `.gitignore` (or change it eventually), here's how you remove indexed files in git that now match something in your `.gitignore` without deleting them from your file system.

> Stuff here I picked up from this [StackOverflow answer](http://stackoverflow.com/a/13541721/304588).

### Rewriting history

Running the following command should remove (newly) matching files in `.gitignore` from your git index. It's essentially a "reevaluate" kind of thing:

```
git rm --cached `git ls-files -i --exclude-from=.gitignore`
```

#### On Windows

On a Windows OS, that command barfs up because of what seems like a parse error. So instead, use this:

```
git ls-files -i --exclude-from=.gitignore | xargs git rm --cached  
```
