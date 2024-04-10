# Productivity Developer

These are  my thoughts on how to be a better developer improving performance in your daily and sometimes trivial activities.

## Table of Contents

- [Git](#git)
- [Bash](#bash)
- [curl](#curl)
- [find](#find)
- [sed](#sed)
- [grep](#grep)
- [regex](#regex)
- [SSH](#ssh)
- [Vim](#vim)
- [Docker](#docker)

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

### Go to the last branch

You can jump back to your previous Git branch with `git checkout -`

### Rebase

I usually commit my changes frequently but I don't like to push or merge a lot of commits. To fix this git rebase is perfect.

Example:

```bash
git rebase -i <base>

# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
```

### Push local git branch to a remote with a different name

I've worked in a company where they use jira tickets as branch names. I don't like this pattern because it is difficult to know about what is that branch without access the jira. So there's a way to use a local, intuitive branch name and keep some strange remote branch name at same time :)

```bash
git push -u origin localBranchName:remoteBranchName
```

### Pull all sub folder in a single command

Sometimes I need to work with a lot of repositories and that is a shortcut to maintain them up to date.

```bash
for d in ./*/ ; do (cd "$d" && echo "$d" && [ -e .git ] &&  g pull); done
```

* Start a loop based in your current folder;
* Run `cd` command to directory;
* Print current directory;
* Check if .git path exists inside the current directory;
* Run `git pull` in the current directory;

### Change local user info

That is the way to change your local repository user info

```bash
git config user.email <youremail>
```

```bash
git config user.name <name>
```

If you need change the author of a commit:

```bash
git commit --amend --author="Author Name <email@address.com>" --no-edit
```

### Interactively rebase starting from a specific commit

If you want to interactively rebase starting from a specific commit, excluding that commit itself, you can use the following command:

```bash
git rebase -i '<my-specific-commit>^'
```

For example, if you have a specific commit with the hash abcdefg, you can use the following command to start an interactive rebase from the commit immediately before it:
```bash
git rebase -i 'abcdefg^'
```

### Git Hooks

There are many several useful git hooks to increase productivity. [This](https://gist.github.com/eduardosilva/d84a6ccfb521300ae0cba7ebd2b95f39) is a list with some useful hooks.

### Debug ignore files

```bash
git check-ignore -v <my-file>
```

### Get current branch name

```bash
git branch | grep -Po "(^\*\s)\K(.*)"
```

I have a function my `.bashrc` to get the current branch name

```bash
function branchName {
    git branch | grep -Po "(^\*\s)\K(.*)"
}
```

This way, I can run a command like below without to worry about read or search for the current branch name:

```bash
git push -u origin $(branchName)
```

### Checkout to a specific branch with name contains a word

```bash
git branch | grep dev | xargs -I % git checkout %
```

### Fixup the first commit 

1. Start an interactive rebase session with the following command:

```bash
git rebase -i --root
``` 

2. Your default text editor will open with a list of commits. Locate the first commit, which will typically be denoted as pick or edit.
3. Change the command associated with the first commit from pick to edit. Save and close the file.
4. Amend the first commit by making the necessary changes. You can modify files, add/remove files, or even rewrite the commit message using the following commands:

```bash
git add <modified_files>
git rm <deleted_files>
git commit --amend
```

Make the desired changes, then save and close the commit message.

5. Continue the rebase using the following command:

```bash
git rebase --continue
```

6. Git will apply your changes to the first commit and continue with the remaining commits, if any.


### Cherry-pick without commit

```bash
git cherry-pick <commit-hash> --no-commit
```

### Checkout a specific file from another branch

```bash
git checkout <other-branch-name> -- path/to/your/folder
```

## Bash

### Navigation

* `CTRL+A`: beginning of the line
* `CTRL+E`: ending of the line
* `ALT+F`: next word
* `ALT+B`: previous word


### Shortcut to the latest command

You can use `CTRL+R` and type to search last command 

First:
```bash
git status
```

After using `CTRL+R` type `status`


### Go to the last directory

Use `cd -` to go back into the directory you were in before your last cd command. 


### Using pushd and popd to navigate between directories

Using pushd to go to .config directory

```bash
pushd .config/
```
Using popd to go back to the previous directory


```bash
popd
```

### Extract column from a tabular output


```bash
git status -s | awk '{print $2}'
```

Or you can create a function as recommended in the reference (it was what I did):

```bash
function col {
    awk -v col=$1 '{print $col}'
}
```

```bash
git status -s | col 2
```

[Reference](https://blog.developer.atlassian.com/ten-tips-for-wonderful-bash-productivity/)

### Search, remove, change directories and another things using wildcard

Bash has tree basic wildcards:

* Asterisk `*` - any character for zero or more times;
* Question mark `?` - fixed number of character;
* Square brackets `[]` - match with the characters of a defined range or a group of character - match with the characters of a defined range or a group of characters;


Searching file names that starts with `t`.

```bash
ls t*
```

Removing file names that start with `t` with three characters with a .txt extension.

```bash
rm t???.txt
```

Searching file names that contains `m` or `u` character

```bash
ls [m-u]*.*
```

### Get random file line

```bash
shuf -n 1 ~/my-file.md
```

### Run parallel commands

```bash
cat urls.txt | xargs -I % -P 10 curl % --output %
```

#### Get only duplicate lines

```bash
cat urls.txt | uniq -D
```

### Using Bash To Output To Screen And File At The Same Time

```bash
ls -al | tee blah.txt
```

### Using cd + and cd - to Navigate Bash History

Example of using cd +:

```bash
cd /path/to/some/folder
cd /path/to/some/other/folder
cd +
# Now you're back in /path/to/some/folder
```

Example of using cd -:

```bash
cd /path/to/some/folder
cd /path/to/some/other/folder
cd +
cd -
# Now you're back in /path/to/some/folder again
```

> Note that you can use the Tab key after typing `cd +` or `cd -` to get a list of available directories to navigate. Alternatively, you can use the `dirs -v` command to display a list of previously visited directories. This can be especially useful when you have a large number of directories in your history and need to quickly switch between them.

To enable a history file in Bash, you can add the following lines to your .bashrc file:

```bash
export HISTCONTROL=ignoreboth
export HISTSIZE=10000
export HISTFILESIZE=20000
```

## Curl

### Download HTTP body response from request made by content file

Let's create a `movies.txt` file with IMDB title ids:

```
tt0068646
tt0111161
```

Now we can get the HTML body content like below:

```bash
while read line; do curl "https://www.imdb.com/title/${line// /%}/" -o "${line}.txt"; done < movies.txt;
```

### Download a sequence of files with curl

```bash
curl -O http://google-maps-icons.googlecode.com/files/blue0[0-9].png
```

> The -O flag tells curl to write the file out as a file instead of to standard output.

### Saving files with a different file name, based on the sequence

```bash
curl -o "#1.html" http://www.example.com/page/[1-20]
```

### Saving file with range of words

```bash
curl -L -o "#1.html" 'google.com/search?q={curl,wget}'

```
#### CURL with retry

```bash
curl --retry 5 https://example.com
```

#### CURL with retry and max time

```bash
# Make curl retry up to 5 times, but no more than two minutes:
curl --retry 5 https://example.com
```

#### CURL retry with connection refused

```bash
curl --retry 5 --retry-connrefused https://example.com
```
### Remove urls in a file if not exists

```bash
while read line; do
    status_code=$(curl --retry 3 --retry-all-errors --write-out %{http_code} --silent --output /dev/null $line)

    if [[ "$status_code" -ne 200 ]] ; then
        sed "/$line/d" my-file.txt -n
    fi
done < my-file.txt
```

## Find

### Find & copy command

```bash
find . -name '*-config.yaml' -exec cp {} ../otherFolder/ \;
```

### Find & delete folder

```bash
find . -type d -name "FOLDER-NAME" | xargs --no-run-if-empty rm -rf
```

### Find & delete file

```bash
find . -type f -name "FILE-NAME" -delete
```

### Find all the files ending in the current directory and move to the specified directory

```bash
find . -name “*.tar” -exec mv {}./backup/ ;
```
A common use case is to find and delete LOG files larger than 100M in the current directory that are more than 30 days old:

```bash
find . -name "*.log" –mtime +30 –type f –size +100M --delete
```

## Sed

### Sed with multiple expressions

```bash
# remove spaces and ,
sed -e 's/^[ \t]*//;s/[ \t]*$//' -e 's/,//' my-file.txt
```

### Find the current row, and then modify the argument after the row

```bash
sed -i '/SELINUX/s/enforcing/disabled/' /etc/selinux/config
```

### See a file in a specific range of lines

```bash
sed -n '10,20p' example.txt
```

## Grep

### Grep showing lines before and or after the match

Using grep to show 3 lines before match:

```bash
grep -B 3 my-file.txt
```

Using grep to show 5 lines before match:

```bash
grep -B 5 my-file.txt
```

Using grep to show 3 lines before and 3 after match:

```bash
grep -C 3 my-file.txt
```

### Using grep to search recursively

```bash
grep -r <my-text>
```

### Lookahed & Lookbehind

Find all the email addresses that end with 'gmail.com', but not include 'gmail.com' in the result

```bash
grep -o '\w+@(?=gmail\.com)' file.txt
```

Find all the URLs that start with http://, but not include http:// in the result 

```bash
grep -o '(?<=http://)\S+' file.txt
```

## Regex

### Capture group

This is a simple and rustic example what you can do with regex capture group.

Change this:

```html
<a href="https://myerrordomain.com/">Link 1</a>
<a href="https://myerrordomain.com/">Link 2</a>
<a href="https://myerrordomain.com/">Link 3</a>
```

For:

```html
<a href="https://mydomain.com/p/1">Link 1</a>
<a href="https://mydomain.com/p/2">Link 2</a>
<a href="https://mydomain.com/p/3">Link 3</a>
```

In the VSCode Find feature enable Regular Expression (.*).

Find:

```bash
(<a href=")(https://myerrordomain.com/")(>Link )([0-9])
```

Replace for:

```bash
$1https://mydomain.com/p/$4"$3$4
```

> Yes, you can easily improve this regex, this only a example to getting started.

### Changing case with regex


Change this:

```
item 1: electronics
item 2: farm
item 3: music
```

For:

```
item 1: Electronics
item 2: Farm
item 3: Music
```

In the VSCode Find feature enable Regular Expression (.*).

Find:

```bash
(item [0-9]: )(\w)(\w*)
```

Replace for:

```bash
$1\u$2$3
```

* \u: uppercase;
* \l: lowercase;

## SSH

### Configure access to a specific server

Change this:

```bash
ssh <ip> -l <login>
```

To that:

```bash
# ~/.config/.ssh/config
Host my-server
    User <login>
    HostName <ip>

ssh my-server
```

### Copy file using ssh

```bash
# local to remote
scp <local>/<path>/<file> my-server:<remote>/<path>/<file>

# remote to local
scp my-server:<remote>/<path>/<file> <local>/<path>/<file>
```


### SSH Dynamic Forwarding

SSH dynamic forwarding allows you to create a secure, encrypted tunnel for your internet traffic through an SSH server. This can be useful if you're on an unsecured network (like a public Wi-Fi network) and want to encrypt your internet traffic to protect against eavesdropping.

#### Configuring an SSH Server 

If you want to enable dynamic forwarding on your SSH server, you'll need to make a few configuration changes. Here's how you can do it:

1. Open the SSH server configuration file on your server. This file is usually located at `/etc/ssh/sshd_config`.

2. Find the following line in the file:

```
#AllowTcpForwarding yes
```
3. Uncomment the line by removing the `#` and change the value to `yes`:
4. Add the following lines to the bottom of the file to allow dynamic forwarding:

```
AllowStreamLocalForwarding yes
```

5. Save the changes to the file and restart the SSH server:

```bash
sudo service ssh restart
```

Once you've made these changes, users will be able to use dynamic forwarding when connecting to your SSH server. However, keep in mind that this can have security implications and it's important to ensure that only authorized users have access to your SSH server.

#### Configuring an SSH Client 

Here's how you can set up dynamic forwarding with SSH:

1. Open a terminal window and run the following command, replacing `user@ssh_server` with the username and SSH server you want to connect to:

```bash
ssh -D 8080 user@ssh_server
```

This will start an SSH connection and create a dynamic SOCKS proxy on port 8080 on your local machine.

2. Configure your web browser or other applications to use the SOCKS proxy you just created. The specific steps for doing this will depend on the application you're using, but generally you'll need to go into the network settings and specify a SOCKS proxy on port 8080 with the IP address of your local machine.

3. Once you've set up the proxy, all of your internet traffic will be routed through the SSH server and encrypted. This can be especially useful if you're using an unsecured Wi-Fi network or if you're accessing sensitive information over the internet.

Keep in mind that dynamic forwarding can have performance implications, since all of your internet traffic is being routed through the SSH server. It's also important to use a secure SSH server and to keep your SSH credentials secure to prevent unauthorized access to your internet traffic.


## VIM

### Incrementing and Decrementing Numbers in Vim

Vim has several built-in commands for incrementing and decrementing numbers. Here's how you can use them:

1. Incrementing a number: Place the cursor on the number you want to increment and press `Ctrl-a`. This will increment the number by 1.

2. Decrementing a number: Place the cursor on the number you want to decrement and press `Ctrl-x`. This will decrement the number by 1.

3. Incrementing by a specific value: Place the cursor on the number you want to increment and type `N` followed by `Ctrl-a`, where `N` is the number you want to increment by. For example, to increment a number by 5, type `5Ctrl-a`.

4. Decrementing by a specific value: Place the cursor on the number you want to decrement and type `N` followed by `Ctrl-x`, where `N` is the number you want to decrement by. For example, to decrement a number by 5, type `5Ctrl-x`.

5. Select a block of numbers in Visual mode (V) and then use g CTRL+A or g CTRL+X to perform batch increments or decrements.

You can also use these commands in combination with other Vim commands, such as searching and replacing. For example, to increment all numbers in a document by 1, you could use the following command:

```bash
:%s/\d+/=submatch(0)+1/g
```

This command searches for all numbers in the document and replaces them with the number plus 1.
Keep in mind that these commands only work with integers and may not work with numbers in scientific notation or with decimal points.



### Enable check spell

```
set spell
```

### Open Terminal

`:term` will open a horizontal split with a terminal.

`:vert term` will open a vertical split with a terminal.

## Docker

### Get container output in the Docker Host

```bash
docker run -e -i --log-driver=none -a stdin -a stdout -a stderr ubuntu bash -c "env" | xargs echo "this is the ubuntu env: "
```

### Copy file from container or from host


```bash
# from container
docker cp <container-id|container-name>:path/file path/file
```

```bash
# from host
docker cp path/file <container-id|container-name>:path/file
```

### Get docker container logs

```bash
# get all logs
docker logs <container-id|container-name>

# follow logs
docker logs -f <container-id|container-name>

# tail logs
docker logs -n 100 <container-id|container-name> 
```

### bind specific volume file in docker compose

```yaml
# config
volumes:
    - type: bind
    source: ./config/cmd.sh
    target: /root/configs/docker/cmd.sh
```

