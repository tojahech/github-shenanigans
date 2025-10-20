Github merge trickiness

```
main
  -- a
    -- b
  -- c
```

main
main < - a
mb

no changes, fast forward only, not stacked:

```
main: 39252e2
a: cb3ba2b
b: e9c263f

// Then rebase and merge a into main
// Base didn't change automatically?
// Base only changes automatically if GH is set up to delete head branches on merge
main: b193f3c
b: e9c263f
// GH UI only shows a single commit in the PR for b
// Base automatically changed and ready to merge
// But I stuffed up and didn't stack the commit, oops
```

no changes, fast forward only,:

```
main: 39252e2
a: cb3ba2b
b: 525d6718

// Then rebase and merge a into main
// Base didn't change automatically?
// Base only changes automatically if GH is set up to delete head branches on merge
main: 291341e
b: 525d6718
// GH UI shows 2 commits, the new one and the one that's just been merged.
// "No conflicts with base branch"
// Clicking Rebase and Merge does resolve the duplicate
// But the diff shows both commits

// How does rebasing go?

// Regular rebase detects that the commit has previously been included and skips it.
// Only one commit in branch b now!
// So too does --fork-point
git rebase origin/main
git rebase origin/main --fork-point
```

Same setup, but with changes in the middle PR of the stack.

```
// Initial setup
main: 39252e2
a: cb3ba2b
b: 525d6718

// Change / amend middle commit
main: 39252e2
a: 673f50319
b: 525d6718

// First PR good in UI
// Second PR shows duplicate commit

// How does rebasing work without any GH merged?

// This gets a merge conflict, hasn't recognised that it's the same commit amended
git rebase origin/gh-rbm-1
// But with fork-point works fine
git rebase --fork-point origin/gh-rbm-1

// Now what If I first rebase the first change from within GH?
main: 05ddb49
b: 525d671

// Still two commits showing in UI, with conflicts

// How does rebasing locally look to the modified main?
git rebase origin/main // Doesn't detect same commit
git rebase --fork-point origin/main // Also doesn't detect that it's the same commit!

// Specify the exact different commits?
git rebase --onto main gh-rbm-1 gh-rbm-2 // No
git rebase --onto main gh-rbm-1 gh-rbm-2 --fork-point // Yes
```

Could have a bot / service to do fast-forward merges instead (diy merge queue). Then git can rebase easily enough. Pretty screwy though.

This is promising!
https://github.com/orgs/community/discussions/59677#discussioncomment-10782359
Nope

Base shows PR pointing to gh-rbm-1 / 673f50319

After merge of PR 1, the base ref and sha of PR 2 is updated

Look at `--update-refs` for handle the restack better? Only rebase from last to main and then `--update-refs`?
