# What Mosh Is

You log in to a remote machine with SSH
and start working. Then real life
happens to the connection. You close the
laptop and reopen it on another network.
The train goes through a tunnel. The
Wi-Fi fades for twenty seconds. Each
time, SSH's connection — a single **TCP**
stream, introduced below — breaks, the
shell on the far end dies with it, and
whatever was running in it dies too.

**Mosh is a remote terminal built to
survive all of that.** Its name is short
for **mobile shell**. You connect with it
much as you connect with SSH, you get the
same shell prompt, and you type the same
commands. The difference is what the
connection is made of. Mosh does not keep
a fragile stream alive; it keeps a
*session* alive, and lets the network
underneath it change, vanish, and come
back in its own time. Close the laptop,
fly somewhere, open it on a different
network with a different address: the
session is still there, showing what the
server's screen looks like now.

On a bad link there is a second gift.
Mosh's client **predicts** what your
keystrokes will do and shows the result
immediately, marking its guesses until
the server confirms them. Typing over a
link with a second of delay feels, most
of the time, like typing locally.

## The three failures of an SSH session

Mosh exists because a plain SSH
connection has three weaknesses that no
SSH setting fixes, because they are
properties of TCP itself:

- **It dies when your address changes.**
  A TCP connection is identified by the
  two addresses and two ports at its
  ends. Your laptop arrives on a new
  network with a new address, and it is,
  as far as the server is concerned, a
  stranger shouting about a conversation
  it never had.
- **It dies when either end sleeps.**
  Sleep long enough and the connection's
  state is forgotten, by the server, by
  a NAT box in between, or by both. The
  shell you left running is gone.
- **It echoes at the speed of the link.**
  SSH sends each keystroke away and
  waits for the server to send the
  character back before you see it. On a
  slow or lossy link, typing lags,
  stutters, and sometimes dumps a burst
  of stale characters all at once.

Chapter 5 takes each of these in turn.
Chapter 2 explains the mechanism that
answers them: synchronising the *state*
of the screen, over **UDP**, instead of
streaming bytes over TCP.

## The rule, stated before everything else

Mosh logs you in to machines and gives
you a shell on them, resuming later from
new addresses — which makes it a tool to
be careful with in two directions: whose
machines you connect to, and who else
might connect to yours. So this guide
has one rule, and it is not fine print:

> **Connect only to machines that are
> yours — under accounts that are yours,
> or where the owner has given you
> explicit permission.**

Administering your own server from a
train: yes. A shell on your employer's
machine, under the account they gave
you, for the work they gave you: yes.
A co-worker's workstation "just to
check something", a session left running
on a machine you have sold, a friend's
server they mentioned once in passing:
no. The lab in this guide went further
than most readers will need to: every
program ran on one machine, talking to
itself, and the one session key that
appears in the transcripts belonged to a
throwaway server that was stopped as
soon as its picture was taken.

If a future idea you have for this tool
does not fit inside the rule, the idea
is the problem. Chapter 11 returns to
this, including why the session key in
chapter 4's transcript is treated there
as a password.

## A familiar shape

Mosh is two programs and a short
ceremony (chapter 2). The **`mosh`**
command you run is a wrapper: it logs in
with SSH, starts **`mosh-server`** on the
far end, receives a port number and a
session key, and hands both to
**`mosh-client`** on your machine. SSH
then leaves, and client and server talk
directly over UDP for the rest of the
session. You need an account and SSH
access on the far machine, the server
program installed there, and one range
of UDP ports open to it — chapter 3
covers the installing, chapter 8 the
ports.

A first taste, after installing
(chapter 3):

```bash
mosh you@server.example.org
```

That is the whole ceremony from the
user's side: one command, and a shell
prompt on the far machine — a prompt
that will still be there after the
laptop sleeps, which no amount of SSH
configuration can promise.

Check: the version behind that command,
from the lab's transcript
`mosh-lab/transcripts/t00-version.txt`:

```text
mosh 1.4.0 [build mosh 1.4.0]
```

## Who it is for

The administrator whose servers live
somewhere else and whose laptop lives
everywhere. The developer who works from
trains, cafés and conference Wi-Fi. The
on-call engineer woken at 3 a.m. who
opens a session on a phone hotspot and
needs it to still be there after the
laptop moves to the desk. The common
thread is one person, their own
machines, and a connection that cannot
be trusted to sit still.

## What it is not

Mosh is not a file transfer tool: it
carries a terminal, nothing else — use
`scp` or `sftp` over SSH for files
(chapter 7). It is not a VPN and not a
tunnel: it forwards no ports and no
other traffic. It is not a session
manager: if the client program itself
dies, that session is gone, which is why
chapter 5 pairs Mosh with tmux rather
than pretending otherwise. It is not a
way around a network's rules: it needs
its UDP port open, and where the port is
closed on purpose, chapter 11 says the
closed port is the answer, not a puzzle.
And it is not more secure magic than
SSH: the login is exactly as strong as
your SSH login, because it *is* your SSH
login — what Mosh adds is its own
encrypted session afterwards, and
chapter 11 is honest about the key that
session depends on.
