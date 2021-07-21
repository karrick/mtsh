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

NOTE: This program invokes the `ssh` executable found in the `PATH` of
the local machine, and `ssh` will use the configuration options for
each remote connection that it creates in accordance with the
documentation found in `man ssh_config`. If additional `ssh`
configuration parameters are required for hosts, one typically puts
them in their personal `ssh` configuration file found in
`~/.ssh/config`.

The below example creates two new terminals and in each connects to
one of each of the hosts specified on the command line.

```Bash
mtsh foo.example.com bar.example.com
```

### Running a single command on each host

The below example creates two new terminals and connects to each host
specified in the command line, but rather than invoking the user's
default login shell on each of the remote hosts, invokes the `htop`
command:

```Bash
mtsh -c htop foo.example.com bar.example.com
```

You may use single or double quotes to quote the command line option's
argument when the command string has special characters.

```Bash
mtsh -c 'tail -F /var/log/messages' foo.example.com bar.example.com
```

As one might expect, when double quotes are used, the invoking shell
on the local system will interpolate the string before sending the
command to the remote hosts. Whereas when single quotes are used, the
invoking shell on the remote connection will interpolate the string.

```Bash
mtsh -c 'echo $SHELL' foo.example.com
mtsh -c "echo $SHELL" foo.example.com
```

### Use -g command line argument to group newly created terminals

```Bash
mtsh -g messsages -c 'tail -F /var/log/messages' foo.example.com bar.example.com
```

NOTE: When running `tmux` or `screen` inside either Apple Terminal or
iTerm, this program applies rules specific to `tmux` or `screen` and
ignores Apple Terminal and iTerm.

NOTE: On `tmux`, when not grouping, this program creates a new tmux
window for each host. When grouping, this program creates a new tmux
window named using the specified group name, and inside that tmux
window creates a new pane for each host.

NOTE: On `screen`, when not grouping, this program creates a new
screen window for each host named after the host. When grouping, each
screen window will use the group name as the window name prefix before
the hostname.

NOTE: On Apple Terminal and iTerm, when not grouping, a new tab in the
current window will be created for each host. When grouping, this
program creates a new window, then creates a new tab in that window
for each host.
