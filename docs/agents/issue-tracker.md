# Issues

GitHub Issues in `Myrpisset/bastion-android` is the repo's issue tracker.

## Working with issues

- Read the whole issue before working on it: text, labels, comments and linked pull requests.
- An issue should describe one coherent problem, with acceptance criteria and blockers in the text.
- Decisions that should last go into the documentation, not only into a comment.

## From a cloud session

`gh issue` does not work in cloud sessions (GraphQL is disabled). Use REST. Set the issue number first:

```bash
n=123
```

Read an issue:

```bash
gh api "repos/Myrpisset/bastion-android/issues/$n"
```

Read its comments:

```bash
gh api "repos/Myrpisset/bastion-android/issues/$n/comments"
```

List open issues:

```bash
gh api "repos/Myrpisset/bastion-android/issues?state=open"
```

Create an issue:

```bash
gh api repos/Myrpisset/bastion-android/issues -f title="Short title" -f body="Description"
```

Comment:

```bash
gh api "repos/Myrpisset/bastion-android/issues/$n/comments" -f body="Comment text"
```

Close:

```bash
gh api -X PATCH "repos/Myrpisset/bastion-android/issues/$n" -f state=closed
```
