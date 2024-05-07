---
title: "The Missing Semester of Your CS Education"
description: "Learning notes from The Missing Semester of Your CS Education course by MIT"
publishDate: "11 April 2024"
tags: ["courses", "cli", "learning-notes"]
draft: true
---

# The Missing Semester of Your CS Education

## Lecture 1: Course Overview + The Shell

The shell is a program that takes commands from the keyboard and gives them to the operating system to perform. In the past, it was the only way to interact with a computer.

They are built to be composable: small programs that do one thing well. As such, they can be combined and automated to perform complex tasks.

Shell is also a programming language. It has variables, loops, conditionals, functions, etc.

Platforms: Windows (Command Prompt, PowerShell), macOS (Terminal), Linux (Terminal).

Terminals are programs that run shells. They are often used interchangeably, but they are different.

Most popular shell on Linux is `bash` (Bourne Again SHell). `zsh` (Z SHell) is also popular.

### Using the shell

![Shell terminal cheatsheet](./cmd-cheatsheet.png)

Machines usually come shipped with terminal-centric programs and they are stored in the file system. Shell uses the `PATH` environment variable to find these programs.

The shell uses the `PATH` variable to find programs. It is a colon-separated list of directories that the shell searches through when you run a command.

### Navigating in the shell

`ls -l` command shows the permissions of files and directories. The first column shows the permissions of the file. The permissions are divided into three groups: user, group, and others.

- The `user` group is the owner of the file.
- The `group` group is the group that the file belongs to.
- The `others` group is everyone else.

The third and fourth columns show the user and group that own the file.

### Connecting programs

Every program has three standard streams: `stdin`, `stdout`, and `stderr`.

- `stdin` is the standard input stream. Default is the keyboard.
- `stdout` is the standard output stream. Default is the terminal.
- `stderr` is the standard error stream. Default is the terminal.

Shell allows you to redirect these streams:

- `>` redirects `stdout` to a file
- `<` redirects `stdin` from a file
- `>>` appends `stdout` to a file
- `|` pipes `stdout` of one program to `stdin` of another

### A versatile and powerful tool

The root user is a special user that has the ability to do anything. It is the superuser. It is also known as `root`. Usually, you need to use `sudo` to run commands as the root user. `#` is the prompt for the root user (as opposed to `$` for regular users).

Writing to the `sysfs` filesystem mounted under `/sys` requires root privileges. `sysfs` exposes kernel parameters as files. It can be used to configure the kernel at runtime.

### Opening files

`xdg-open` opens a file with the default program. It is a cross-platform command.

# Lecture 2: Shell Tools and Scripting

Shell scripts are the next step in complexity after one-liners. They are a sequence of commands that are executed by the shell. Most shells have their own scripting language with variables, control flow, and its own syntax. We will be using `bash`.

### Variables

```bash
foo=bar
echo "$foo"
# prints bar
echo '$foo'
# prints $foo
```

### Control flow

`bash` has support for `if`, `case`, `while`, `for`, and `functions`.

```bash
mcd () {
    mkdir -p "$1"
    cd "$1"
}
```

Here `$1` is the first argument passed to the function. `bash` uses special variables to refer to arguments, error codes, and other relevant variables. Full list is [here](https://tldp.org/LDP/abs/html/special-chars.html).

- `$0` is the name of the script
- `$1` to `$9` are the first 9 arguments to the script
- `$#` - number of arguments
- `$@` - all arguments
- `$?` - exit status/return code of the last command (0 is success)
- `$$` - process ID of the current script
- `!!` - entire last command, including arguments. Common pattern is `sudo !!` to run the last command as root.
- `$_` - last argument of the previous command. Quickly get this with `Alt + .` or `Esc + .`

Commands return output to `stdout`, errors to `stderr`, and a return code.

Exit codes can be used to conditionally execute further commands using `&&` and `||`, both are short-circuiting.

Command substitution is a way to capture the output of a command into a variable.

```bash
files=$(ls)
for file in $files; do
    echo $file
done
```

Process substitution is a way to pass the output of a command to another command. This is done by executing the command, placing the output in a temporary file and substituting the file name in the command.

```bash
diff <(ls foo) <(ls bar)
```

### Shell globbing

Globbing is the process of expanding wildcards into a list of file names. It is done by the shell, not the program.

- `*` matches any string, including the empty string
- `?` matches any single character
- `{}` matches any of the comma-separated strings inside the braces

```bash
convert image.{png,jpg}
# Will expand to
convert image.png image.jpg

cp /path/to/project/{foo,bar,baz}.sh /newpath
# Will expand to
cp /path/to/project/foo.sh /path/to/project/bar.sh /path/to/project/baz.sh /newpath

# Globbing techniques can also be combined
mv *{.py,.sh} folder
# Will move all *.py and *.sh files


mkdir foo bar
# This creates files foo/a, foo/b, ... foo/h, bar/a, bar/b, ... bar/h
touch {foo,bar}/{a..h}
touch foo/x bar/y
# Show differences between files in foo and bar
diff <(ls foo) <(ls bar)
# Outputs
# < x
# ---
# > y
```

### General scripting tips

Scripts are not necessarily written in `bash`. They can be written in any language. `bash` is just a common choice because it is available on most systems. 

The shebang `#!` is used to specify the interpreter for the script. It is followed by the path to the interpreter.

```bash
#!/bin/bash
```

```python
#!/usr/bin/env python
```

It is good practice to write shebang lines using the `env` command. This allows the script to be run by any interpreter in the user's `PATH`. This makes the script more portable.

### Shell tools

#### Finding how to use commands

- `-h`/`--help` - most commands have a help flag that prints a brief description of the command and its flags
- `man` - manual pages
- `tldr` - simplified and community-driven

#### Finding files

- `find` - search for files in a directory hierarchy. Can also perform actions on the files found.

```bash
# Find all directories named src
find . -name src -type d
# Find all python files that have a folder named test in their path
find . -path '*/test/*.py' -type f
# Find all files modified in the last day
find . -mtime -1
# Find all zip files with size in range 500k to 10M
find . -size +500k -size -10M -name '*.tar.gz'

# Delete all files with .tmp extension
find . -name '*.tmp' -exec rm {} \;
# Find all PNG files and convert them to JPG
find . -name '*.png' -exec convert {} {}.jpg \;
```

- `fd` - a simpler alternative to `find`. It is faster and has a more user-friendly interface.
- `locate` - search for files in a database. It is faster than `find` but may not be up-to-date. It uses a database that is updated daily using `updatedb` via `cron`.

#### Finding code

- `grep` - search for patterns in files. It is a powerful tool that can search for regular expressions.
- `rg` - a faster alternative to `grep`. It is written in Rust and is faster than `grep` because it is parallelized.

#### Finding shell commands

Typing the up arrow key will cycle through the history of commands. `Ctrl + R` will search through the history.

- `history` - shows the history of commands
- `fzf` - a general-purpose fuzzy finder that can be used to search through the history of commands

# Lecture 3: Editors (Vim)