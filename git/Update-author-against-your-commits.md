To update the author for the last commit

- reset the author in the config

```bash
git config --global user.email "munotpratik2009@gmail.com"
git config user.email
git config --list
```

- reset author for last commit

```
git commit --amend --reset-author --no-edit
```

- Validate the change in the email address

```bash
git log --format='%ae'
```

- To perform for last N commits, use the below

```bash
git rebase --onto HEAD~N --exec "git commit --amend --reset-author --no-edit" HEAD~N
```

Force push your changes without overwriting anyone else's commits

```bash
git push --force-with-lease
```

> Note that this will also change the author timestamp.
