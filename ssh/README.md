# SSH in a nutshell

SSH is not just a single program. The SSH ecosystem consists of a family
of related programs.

## Use cases

The primary use case for SSH is executing commands on remote machines.
SSH has other cases as well.

This nutshell focuses only on the primary use case.

## Server and client machines

A **server** is a machine that is running `sshd` (the SSH server daemon
program). `sshd` accepts incoming connections on a TCP socket. The
`sshd` socket is conventionally bound to port `22` on the server.

A **client** is a machine that connects to a **server** via SSH. A user
logged in on a **client** runs `ssh` (the SSH client program) to execute
commands on a **server**.

## Execute a single command on a server

Alice is logged in on `client_machine`, and she wants to execute `mkdir
-p /home/alice/bin` on `server_machine`. On `client_machine`, Alice
runs:

```bash
ssh alice@server_machine "mkdir -p /home/alice/bin"
```

The following steps happen in order:

- `ssh` establishes a connection to `server_machine`.
- `ssh` sends the command `mkdir -p /home/alice/bin` over the connection
  to `server_machine`.
- `server_machine` receives the command.
- `server_machine` executes the command, creating the new directory on
  `server_machine`.
- The command terminates, and `ssh` terminates the connection to
  `server_machine`.

## Execute commands interactively on a server

Typically a user wants to run commands in an interactive shell (e.g.
`bash`) on a **server**. 

For example, Alice is logged in on `client_machine`, and she wants to do
some work on `server_machine`. On `client_machine`, Alice runs:

```bash
ssh alice@server_machine
```

The following steps happen in order:

- `ssh` establishes a connection to `server_machine`.
- `server_machine` begins an interactive shell session.
- Alice executes commands on `server_machine` in her interactive shell
  session.
- Alice terminates the interactive shell session on `server_machine`.
- The interactive session terminates, and `ssh` terminates the
  connection to `server_machine`.

During this exchange, Alice's terminal may look like:

```bash
alice@client_machine ~ $ ssh alice@server_machine
alice@server_machine's password:
alice@server_machine ~ $ cd bin
alice@server_machine ~/bin $ ls
gcc python
alice@server_machine ~/bin $ exit
logout
Connection to server_machine closed.
alice@client_machine ~ $
```

Let's break this down line by line.

- Line 1: Alice runs `ssh alice@server_machine` on `client_machine`.
- Line 2: Alice enters her password to log into `server_machine`.
- Line 3: Alice runs `cd bin` on `server_machine`. This changes Alice's
  working directory on `server_machine`.
- Line 4: Alice runs `ls` on `server_machine`.
- Line 5: Ouput from `ls` run on `server_machine`.
- Line 6: Alice runs `exit` on `server_machine`. This terminates the
  interactive session.
- Line 7: Output from `exit` run on `server_machine`.
- Line 8: Output from `ssh` run on `client_machine`.
- Line 9: Alice's terminal prompt on `client_machine`.

# UNORGANIZED

```bash
ssh-agent bash
netstat -A unix -eep -l
ps f -eo ppid,pid,user,args
env | grep SSH
```

