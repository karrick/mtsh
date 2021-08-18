# mtsh

multiterm shell

## Description

Multiterm Shell launches
[ssh](https://en.wikipedia.org/wiki/Secure_Shell_Protocol) connections
to multiple remote hosts. It is compatible with [Apple
Terminal](https://en.wikipedia.org/wiki/Terminal_(macOS)),
[iTerm](https://iterm2.com/),
[screen](https://en.wikipedia.org/wiki/GNU_Screen), and
[tmux](https://en.wikipedia.org/wiki/Tmux).

## Usage

The below example creates a new terminal and in it connects to a
single host `foo.example.com`.

```Bash
mtsh foo.example.com
```

NOTE: This program invokes the `screen`, `ssh`, and `tmux` executable
programs found in the `PATH` of the originating terminal, and each
program will use their respective configuration files.

Of particular note, `ssh` will use the configuration options for each
remote connection that it creates in accordance with the documentation
found in `man 5 ssh_config`. If additional `ssh` configuration
parameters are required for hosts, one typically puts them in their
personal `ssh` configuration file found in `~/.ssh/config`.

The below example creates two new terminals and in each newly created
terminal connects to one of the hosts specified on the command line.

```Bash
mtsh foo.example.com bar.example.com
```

### Running a single command on each host

One may use the `-c COMMAND` command line option to specify a
particular command to execute on the remote hosts, in lieu of the
user's default login program.

```Bash
mtsh -c htop foo.example.com bar.example.com
```

One may use either single or double quotes to quote the command line
option's argument when the command string has special characters.

```Bash
mtsh -c 'tail -F /var/log/messages' foo.example.com bar.example.com
```

As one might expect, when double quotes are used, the invoking shell
on the originating host will interpolate the string before sending the
command to the remote hosts. However, when single quotes are used, the
invoking shell on the remote connection will interpolate the
string. If the default login shell for the local host and remote host
were different, the below two commands would yield different output.

```Bash
mtsh -c 'echo $SHELL' foo.example.com
mtsh -c "echo $SHELL" foo.example.com
```

### Grouping newly created terminals

One may use the `-g` command line option to group multiple
terminals.

```Bash
mtsh -g -c 'tail -F /var/log/messages' foo.example.com bar.example.com
```

Grouping terminal behavior is dependent on what terminal program is
being used on the originating host. When running `screen` or `tmux`
inside either Apple Terminal or iTerm, this program applies rules
specific to `screen` or `tmux` and ignores the fact that that terminal
program is running inside Apple Terminal or iTerm.

*NOTE:* On `screen`, when not grouping, this program creates a new
screen window for each host named after the host. When grouping, each
screen window will use the group name as the window name prefix before
the hostname.

*NOTE:* On `tmux`, when not grouping, this program creates a new tmux
window for each host. When grouping, this program creates a new tmux
window named using the specified group name, and inside that tmux
window creates a new pane for each host.

*NOTE:* On Apple Terminal or iTerm, and not grouping, a new tab in
the current window will be created for each host. When grouping, this
program creates a new window, then creates a new tab in that window
for each host.

### Trimming off a common host prefix and suffix strings from titles

#### Common Hostname Prefix is used for the window group name

The -p PREFIX argument allows specifying what PREFIX string to remove
from each of the host names. When omitted, the common prefix string of
all provided hosts will be used.

The -s SUFFIX argument allows specifying what SUFFIX string to remove
from each of the host names. When omitted, the common suffix string of
all provided hosts will be used.

In the below example, the longest common suffix is `.example.com`, and
would be stripped from each of the host names.

```Bash
mtsh -s .com foo.example.com bar.example.com
```

