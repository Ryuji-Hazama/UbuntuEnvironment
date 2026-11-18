# Git

## Install Git

### Check if Git is already installed

Before installing Git, check if Git is already installed.

```bash
git --version
```

### Install

If Git is not installed, run the following command to install Git.

```bash
sudo apt update
sudo apt install git
```

## Initialize a Local Git Repository

```bash
cd /path/to/your/local/repo
git init
```

## Create a Bare Git Repository (Hub)

### Create an empty hub

```bash
git init --bare /path/to/your/bare/project/hub.git
```

### Create from an existing Git repository

#### Create a bare clone

```bash
git clone --bare /path/to/your/local/repo /path/to/your/bare/project/hub.git
```

#### Connect the local repository to the new hub

```bash
cd /path/to/your/local/repo
git remote add origin /path/to/your/bare/project/hub.git
```

## Clone from the Hub

### Configure safe directory

```bash
git config --global --add safe.directory /path/to/your/bare/project/hub.git
```

### Clone

```bash
git clone /path/to/your/bare/project/hub.git
```

## Set User Informations

### User name

```bash
git config [--global] user.name "Your Name"
```

### E-mail

```bash
git config [--global] user.email "your@e-mail.com"
```

## Fetch from the Hub

```bash
git fetch
```

### Check the difference

```bash
git diff origin/<target-branch-name>
```

## Pull

```bash
git pull origin <target-branch-name>
```

## Add files

Use `add` command to add a file to the git.

```bash
git add <fileName>
```

To add all files in a directory, you can specify the directory name instead of the file name, and using `.` to add all files in the directories and files.

```bash
git add .
```

## Nested Repositories

You can have nested Git repositories, also known as submodules. This allows you to include one Git repository inside another.

### Add a submodule

```bash
git submodule add <repository-url> [<path-to-submodule>]
```

### Update submodules

```bash
git submodule update --remote --merge
```

### Create and add a new submodule

```bash
# Create a new repository under the parent repository directory
mkdir <submodule-directory>
cd <submodule-directory>
git init

# Set remote and push initial commit
git remote add origin <repository-url>
git add .
git commit -m "Initial commit"
git push -u origin master

# Add the submodule to the parent repository
cd ..
git submodule add <repository-url> <submodule-directory>
```

### Clone submodules

#### If the parent repository is cloned (which has empty submodule directories)

```bash
git submodule update --init --recursive
```

#### Clone the parent repository with submodules

```bash
git clone --recurse-submodules <repository-url>
```

### Remove a submodule

```bash
git submodule deinit -f <submodule-directory>
git rm -f <submodule-directory>
rm -rf .git/modules/<submodule-directory>
```

## Stash Changes

You can temporarily save your changes without committing them using the `stash` command.

```bash
git stash
```

To apply the stashed changes later, use:

```bash
git stash apply
```

To apply the stashed changes and remove them from the stash list, use:

```bash
git stash pop
```

You can also list all stashed changes using:

```bash
git stash list
```

If the stash list contains multiple entries, you can apply a specific stash using:

```bash
git stash apply stash@{n}
```
where `n` is the index of the stash in the list.

If you no longer need a stashed change and want to remove it from the stash list without applying it, you can drop it using:

```bash
git stash drop [stash@{n}]
```
where `n` is the index of the stash in the list, and if you omit it, the most recent stash will be dropped.

If you want to clear all stashed changes, you can use:

```bash
git stash clear
```

## Commit Changes

```bash
git commit -m "Commit messages"
```

You can use `-a` option to stage all changes and commit at once.

```bash
git commit -a -m "Commit messages"
```

## Push

```bash
git push origin <target-branch-name>
```

### Push to an empty repository

If the remote repository is empty (GitHub, GitLab, etc.), you need to specify the upstream branch when push for the first time.

```bash
git push -u origin <target-branch-name>

# or
# git push --set-upstream origin <target-branch-name>
```

## Reset

To reset to most recent local git commit:

```bash
git reset --hard
```

To reset to most recent origin git commit:

```bash
git reset --hard origin/<branch_name>
```

To move back to the specific commit:

```bash
git reset --hard HEAD[~n]
```

## Branches

### List branches

```bash
git branch
```

### Create a new branch and switch

```bash
git switch -c <branch_name>

# or checkout the branch
# git checkout -b <branch_name>

git add .
git commit -m "Initializing a new branch"

# Initialize the remote repository
git push -u origin <branch_name>
```

Use `user-name/fucntion[/id]` format for personal branches for the better branch managesment.

### Switch to a branch

```bash
git switch <branch_name>
```

Or if you want to checkout the changes in current branch and switch to another, run

```bash
git checkout <branch_name>
```

### Merge changes

If you want to merge dev(development branch) to master(stable), run the following commands.

```bash
# Switch branch to "master"
git checkout master

# Fetch and pull the changes
git fetch
git pull origin master

# Merge
git merge dev

# Push the changes
git push origin master

```

### Fast forward

You can switch fast forward with `--no-ff` and `--ff` when merge branches.

```text
--no-ff | --ff
 |        |
 O        O
 |\       |
 O O      O
 | |      |
 O O      O
 |/       |
 O        O
 |        |
```

#### Set as default

```bash
git config [--global] --add merge.ff {true/false}
```

### Delete branch

#### Delete merged branch

```bash
git branch -d <branch-name>
```

#### Delete unmerged branch (force)

```bash
git branch -D <branch-name>
```

#### Delete remote branch

```bash
git push origin --delete <branch-name>
```

### Branch naming tips

#### master/main branch

- Master branch for whole project.
- Stable commits only.

#### develop branch

- Development branch.
- The branch where the developpers are working on.
- Commits would be unstable.

#### Personal branches

`user-name/function[/id]`

- Personal branches for developping and testing.
- Specify the owner and the purpose.
  - E.g.: Mike's testing branch No.2 would be `mike/test/2`

## Daily Jobs

### Before start your work

You need to `pull` from the remote repository before you start your work to ensure your workspace is up to date.

```bash
# Swithc to working branch
git checkout <target-branch>

# Pull the lates changes from the server repository
git pull origin <target-branch>
```

### Push your changes

You should `pull` before push your changes to prevent accidentally overwrite the other changes.

```bash
# Stash your changes first
git stash

# Pull before commit
git pull origin <target-branch>

# Apply stashed changes
git stash apply

# Add new files
git add <file-name>

# Commit changes
git commit -m "Commit message"

# Push to remote
git push origin <target-branch>
```

## Multiple Branches

When you are working as a team and each member has their own branch, it is important to keep your branches up to date with the latest changes from the main/master branch to avoid conflicts and ensure smooth integration.

When the main/master branch is updated, you have two options to update your personal branches:

- Rebase your personal branch onto the main/master branch.
- Merge the main/master branch into your personal branch.

Before updating your personal branch, make sure your local main/master branch is up to date with the remote repository.

```bash
# Switch to main/master branch
git checkout <main/master>

# Pull the latest changes from the remote repository
git pull origin <main/master>
```

### Rebase

If the branch you are working on has been developed only by you, and you want a clean, linear history, you can rebase your personal branch onto the main/master branch.

```bash
# Switch to your personal branch
git checkout <personal-branch>

# Rebase onto the main/master branch
git rebase <main/master>

# Push the rebased branch to the remote repository
git push origin <personal-branch> --force-with-lease
```

Your git graph will look like:

```text
  o   Rebase onto main development branch
  |
  o
  |\
  | o Your old commit
  o |
 /| |
o | | Other member's commit
| | |
```

### Merge

If you prefer not to rewrite history, or if your branch has been developed by multiple people, you can merge the main/master branch into your personal branch.

```bash
# Switch to your personal branch
git checkout <personal-branch>

# Merge the main/master branch into your personal branch
git merge <main/master> [--no-ff]

# Push the merged branch to the remote repository
git push origin <personal-branch>
```

Now, your git graph will look like:

```text
    o Merge main into your branch
   /|
  o |
  |\|
  | o Your old commit
  o |
 /| |
o | | Other member's commit
| | |
```