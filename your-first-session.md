# Your First Session

Time to connect. This chapter takes one
session from the command to the prompt,
naming everything you see on the way —
including the server started by hand,
once, so the ceremony in chapter 2 stops
being a diagram. The full session itself
is the one part of this guide the lab
could not record (its build machine
refuses all UDP; `criteria.md` tells
that story, and transcript t02 keeps the
evidence), so the inside of the session
is described from the project's
documentation, while everything around
it — the server, its port, its refusal
to be misconfigured — is the lab's own
transcripts.

## The ordinary way

With Mosh installed at both ends and
SSH working between them:

```bash
mosh you@server.example.org
```

(Throughout this guide, `you` is your
username and `server.example.org` a
machine you have an account on — the
chapter 1 rule in placeholder form.)

Behind that one command, the ceremony
from chapter 2 runs: SSH logs you in,
`mosh-server` starts and prints its
`MOSH CONNECT` line, the wrapper hands
the port and key to `mosh-client`, SSH
leaves. What you see is a brief pause —
the SSH login taking its usual time —
and then the remote shell's prompt, in
your terminal, indistinguishable from
an SSH session's except for what happens
later.

To run one command instead of an
interactive shell, put it after the
host, exactly as with SSH:

```bash
mosh you@server.example.org -- tmux attach
```

Readers who live in tmux will type that
so often that chapter 9 turns it into a
habit and an alias.

## The server, once by hand

You will never normally do this — the
wrapper exists precisely so you don't —
but doing it once makes the machinery
real. Log in to the server with plain
SSH in one terminal, and run:

```bash
mosh-server new
```

Check: the lab did exactly this, and
transcript `mosh-lab/transcripts/t01-server-start.txt`
keeps what came back:

```text
MOSH CONNECT 60001 nIND/kuyuYavvM6V92+oHQ

mosh-server (mosh 1.4.0) [build mosh 1.4.0]
...
[mosh-server detached, pid = 61082]
```

Three facts to take from it. The server
chose port **60001**, from the default
range 60000–61000. It printed a random
session key — the credential for this
session only. And it **detached**: the
command returned, the server went on
living in the background with a shell
attached to a fresh terminal, waiting
for a client that, in the lab, could
only almost arrive (transcript t02 is
the client's side of that story, and
chapter 12 makes troubleshooting gold
of it). A client with the key connects
directly:

```bash
MOSH_KEY=<the key> mosh-client server.example.org 60001
```

which is all the wrapper automates. A
hand-started server with no client
simply waits; stop it like any program
(`kill` its pid) when the demonstration
is over, and do not leave printed keys
lying around in scrollback or notes —
chapter 11 explains the habit.

## Inside the session

Documented-only in this guide, and
brief, because it mostly feels like
nothing: you have a shell. Two things
are visibly different from SSH.

- **The contact bar.** When the client
  has heard nothing from the server for
  a while — a dead network, a sleeping
  server — a bar appears at the top of
  the screen saying so, with a counter
  of seconds since the last reply. The
  lab's client showed this bar in
  transcript t02, counting up without
  contact; in real use it vanishes the
  moment the link returns, and your
  session is simply *there*, current.
  (The same bar is how a colleague's
  "Mosh feels haunted" report usually
  resolves: their network drops, the bar
  tells the truth, SSH would have lied
  until the TCP timeout.)
- **The escape key.** Mosh reserves
  **Ctrl-^** as its escape character,
  shown in its own messages as
  `[To quit: Ctrl-^ .]`. **Ctrl-^ .**
  ends the session from the client side,
  wherever the network has gone;
  **Ctrl-^ Ctrl-Z** suspends the client
  to your local shell, leaving the
  server waiting. To type a literal
  Ctrl-^, type it twice.

## Ending a session

The ordinary way is to end the shell:
`exit`, or log out as you would from
SSH. The server notices its shell has
gone, and both ends tidy up. The escape
sequence above is the other way — it
leaves the client immediately, useful
when the network is gone and the shell
is unreachable; the server end will wait
for a client that is never coming back,
and chapter 11 covers clearing out such
orphans. What you cannot do is close
the client and later reattach to the
same session from a new client: that
door does not exist, and chapter 5
shows the tmux pairing that covers it.
