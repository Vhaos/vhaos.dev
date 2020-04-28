+++
title = "Handy Bash Aliases & Functions"
date = "2020-07-03"
author = "Kareem" 
authorTwitter = "kareemdagg" #do not include @
cover = "" #cover image to show for this post
tags = ["bash", "programming", "git"] #what tags (in the tags page) to list this post under
keywords = ["bash", "programming", "shell", "git", "zsh"] #keywords to set for SEO
description = "The more you work in the terminal, the faster you start to build a repitive workflow. While aomw tasks actions are fairly simple and take no time at all, others might require multiple steps; this does tend to rather monotonous after a while. Lucky for us many of these can be abstracted or automated completely! 🔨" #text that shows in the post list view (if showFullContent) is false. Also set as description for SEO
showFullContent = false #whether to show all post content in list view (overrides description field if true)
weight = 0 #priority to set post in list and search views. 0 is defualt priority, 1 pins post, lower weight = higher priority. 
+++

The more you work in the terminal, the faster you start to build a repitive workflow. While aomw tasks actions are fairly simple and take no time at all, others might require multiple steps; this does tend to rather monotonous after a while. Lucky for us many of these can be abstracted or automated completely 🔨!

So, without further ado, here's a collection of simple aliases and functions that I have slowly begun to use over time. If you would like to use any of these yourself simply append these to the end of your `~/.bashrc` file (or `~/.kshrc` for ksh users, or `~/.zshrc` for zsh users).

---

- [Make a directory and change to it](#make-a-directory-and-change-to-it)
- [Concise oneline git log graph](#concise-oneline-git-log-graph)
- [Set git remote as branch upstream and push branch](#set-git-remote-as-branch-upstream-and-push-branch)
- [Reload .zshrc configuration](#reload-zshrc-configuration)
- [Clone a git repo and cd into it in one command](#clone-a-git-repo-and-cd-into-it-in-one-command)
- [Use wget to recursively download a website for offline/mirroring use](#use-wget-to-recursively-download-a-website-for-offlinemirroring-use)
  - [robots.txt](#robotstxt)


### Make a directory and change to it
Does exactly what it says; saves some typing on a commonly repeated task.
```bash
mkcd () {
  mkdir -p "$1" && cd "$1"
}
```
Note: a [number of defects](https://unix.stackexchange.com/a/9124) of the above function were pointed out thanks to _@Gilles_. A more foolproof, albeit messier version can be found below: 
```bash
mkcd () {
  case "$1" in
    */..|*/../) cd -- "$1";; 
    /*/../*) (cd "${1%/../*}/.." && mkdir -p "./${1##*/../}") && cd -- "$1";;
    /*) mkdir -p "$1" && cd "$1";;
    */../*) (cd "./${1%/../*}/.." && mkdir -p "./${1##*/../}") && cd "./$1";;
    ../*) (cd .. && mkdir -p "${1#.}") && cd "$1";;
    *) mkdir -p "./$1" && cd "./$1";;
  esac
}
```

Also, if you are a `zsh` user and use _Oh-My-Zsh_, you can simply use the built in [command](https://github.com/ohmyzsh/ohmyzsh/wiki/Cheatsheet) `take`, which does essentially the same as the function above.

---
### Concise oneline git log graph
Conserve the amount of screen space taken up with git log. This shows the timeline/graph of commits on the current branch. Commits take up one line and SHAs are abbreviated. 
```bash
alias glgo = "git log --graph --oneline"
```

---
### Set git remote as branch upstream and push branch

In the past, a frequent part of my usual workflow involved creating a new feature branch, committing some changes and then pushing those changes upstream.

But being the forgetful sort, I often forget I haven't created an upstream branch before initiating a push and consequently get greeted with the infamous git message:

```error
fatal: The current branch feat/my-branch has no upstream branch.
To push the current branch and set the remote as upstream, use
    git push --set-upstream origin feat/my-branch
```

No big deal right? after all it's simply a few seconds lost just copying the command and running it again. Regardless, I still found it rather irritating and that same irritation spawned this tiny shell function below!

In short, this first checks if an argument has been passed in as a remote. If not, defaults the remote to origin, gets the current branch and pushes to the remote branch of the same name

```bash
function gupush {
  if [[  $1 -eq 0  ]]; then
    echo "No remote branch specified, defaulting to origin..."
    remote_name="origin"
  else
    remote_name=$1
  fi
  branch_name=$(git rev-parse --symbolic-full-name --abbrev-ref HEAD)
  git push --set-upstream $remote_name $branch_name
}
```
---
### Reload .zshrc configuration
A simple alias for `source`ing your `.zshrc`. Particularly useful after making changes to it and wish to apply them to the current shell straight away.
```bash
alias reload="source ~/.zshrc"
```
---
### Clone a git repo and cd into it in one command
Changing into the directory of a git repository you've just cloned is a frequent occurence. This helps save some keyboard clicks.
```bash
gclone() {
  git clone "$1" && cd "$(basename "$1" .git)"
}
```
---

### Use wget to recursively download a website for offline/mirroring use
If you ever need to download an entire Web site, perhaps for off-line viewing, wget can do the job.
```bash
alias downloadsite = wget -r -nH --no-parent -R "index.html*"
```
**Usage**
```
downloadsite http://example.com/
```

#### robots.txt

The alias above may fail if the site you are downloading forbids you in it's [robots.txt](https://support.archive-it.org/hc/en-us/articles/208001096-Avoid-robots-txt-exclusions) file.

Wget allows you to [ignore robots exclusions](http://www.gnu.org/software/wget/manual/html_node/Robot-Exclusion.html) and thereby crawl material otherwise blocked by a robots.txt file. However crawling a site that a robots file is explicitly forbidding you from doing is considered, at the very least highly impolite and at worst might even get you into legal trouble.

If you understand and still wish to go ahead regardless, then adding `-e robots=off` to the alias above should do the trick. 

Consider an adding some throttling when ignoring robot exclusions. The alias both limits the download rate and waits for 10 seconds between requests 
```bash
alias mustdownloadsite = wget -r -nH --no-parent -e robots=off  -R "index.html*" --wait=10 --limit-rate=20K 
```