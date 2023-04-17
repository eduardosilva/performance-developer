# Productivity Developer

These are  my thoughts on how to be a better developer improving performance in your daily and sometimes trivial activities.

## Table of Contents

- [Git](#git)

## Git

### Configure prompt with git information

Put git information in your prompt (i.e.: branch and status) is a great way to get these without execute commands. There are many examples about how to do that, so that is a link with one of them [Git Prompt](https://github.com/magicmonty/bash-git-prompt)

### Auto-correct

Enable auto-correct and suggested commands will run after 2 seconds.

before:

```bash
git statsh pop

git: 'statsh' is not a git command . See 'git --help'.

the most similar command is
    stash
```

```bash
git config --global help.autocorrect 20
```

after:

```bash
git statsh pop

WARNING: You called a Git command named 'statsh', witch does not exist.
Continuing in 2.0 seconds, assuming that you meant 'stash'.
```

### Changing push strategies

Using `current` option to push branches with same name.

before:

```bash
git push
fatal: The current branch mko has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin mko
```

configure strategy:

```bash
git config --global push.default current
```

after:

```bash
git push
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To [origin branch]
 * [new branch]      mko -> mko
```

### Enable Git's autosquash feature by default

```bash
git config --global rebase.autosquash true
```

### .gitattributes Line Endings

Git can actually be configured to automatically handle line endings using a setting called autocrlf. This automatically changes the line endings in files depending on the operating system. However, you shouldn't rely on people having correctly configured Git installations. If someone with an incorrect configuration checked in a file, it would not be easily visible in a pull request and you'd end up with a repository with inconsistent line endings.

The solution to this is to add a .gitattributes file at the root of your repository and set the line endings to be automatically normalized like so:

```text
# Set default behavior to automatically normalize line endings.
* text=auto
```

### RERERE

[git-rerere](https://git-scm.com/docs/git-rerere) is a command that allows you to save resolved conflict to replicate the same solution in the future.

```bash
git config --global rerere.enabled 1
```

#### Forgot recorded wrong rerere resolution

If you have enabled rerere feature and record a wrong merge conflict, you can erase/forgot this merge resolution with following command:

```bash
git rerere forget <pathtofile>
```

### Show all aliases

```bash
git config --get-regexp alias
```

### Folder root

To get the root folder based in the .git folder configuration

```bash
git rev-parse --show-toplevel
```

To move to the root folder

```bash
cd $(git rev-parse --show-toplevel)
```

That's why I have a function in the `.bashrc` called `root`

```bash
function root {
    cd $(git rev-parse --show-toplevel)
}
```

