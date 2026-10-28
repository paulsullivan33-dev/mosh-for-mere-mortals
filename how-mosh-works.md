# How Mosh Works

Four ideas do all of Mosh's work: borrow
SSH to get started, throw the stream
away and synchronise *state* instead,
treat the client's address as a rumour
rather than an identity, and let the
client guess what you typed. This
chapter takes each in turn, so that
everything later — sleep, roaming, echo,
firewalls — is a consequence of a named
idea, not a mystery.

## The bootstrap: SSH, briefly

When you run:

```bash
mosh you@server.example.org
```

the `mosh` wrapper first does an
ordinary SSH login, with your ordinary
keys and configuration. On the far end
it starts `mosh-server`, which chooses a
UDP port, generates a random **session
key**, starts your shell attached to a
fresh terminal, and prints one line:

```text
MOSH CONNECT 60001 nIND/kuyuYavvM6V92+oHQ
```

(the lab's own copy of this line is
transcript `mosh-lab/transcripts/t01-server-start.txt`,
from a server started by hand — the
wrapper does exactly this, over SSH).

The wrapper reads the port and the key,
starts `mosh-client` on your machine,
and hands it both — the key through an
environment variable, never typed, never
sent over the network in the clear. SSH
logs out. From here on, SSH is not
involved: client and server talk
directly, over UDP, encrypted with the
session key. Anyone who learns that key
while the server lives can join the
session, which is why this guide treats
a `MOSH CONNECT` line like a password
(chapter 11) and why the lab's printed
key belonged to a server stopped
immediately afterwards.

Check: the lab's server, started by
hand, settled into listening on its
port, exactly as a wrapper-started one
does:

```text
UNCONN 0  0  0.0.0.0:60001  0.0.0.0:*  users:(("mosh-server",pid=61082,fd=4))
```

(`ss` output, from transcript t01. `ss`
shows sockets; a **socket** is one end
of a network conversation, held by a
program.)

## State, not a stream

An SSH session is a pipe of bytes. Every
keystroke travels one way, every screen
update the other, and if a byte is lost
or delayed, everything behind it waits.
Mosh's protocol — the **State
Synchronization Protocol** — asks a
different question. The server knows what
your terminal screen *should* look like:
24 rows of characters, a cursor, some
colours. The client knows what it is
currently showing. Each side keeps its
own copy of that state, numbered like
pages in a ledger. Periodically they
compare notes, and the server sends not
"everything that happened since you last
looked" but "here is what the screen
looks like *now*" — the difference
between the state you have and the state
that is true.

Three consequences matter:

- **Loss heals itself.** A dropped
  packet is not a hole in a stream; the
  next sync simply describes the current
  screen again. On a link dropping a
  fifth of its packets, an SSH session
  stutters and stalls while a Mosh
  session merely updates less often.
- **Delay stops compounding.** If the
  link stalls for ten seconds, SSH owes
  you ten seconds of backlog. Mosh owes
  you one current screen. Intermediate
  frames are skipped without regret.
- **Sleep is free.** While your laptop
  is closed, nothing needs to happen at
  all. The server keeps its state; when
  the client wakes and syncs, the screen
  becomes true again. There is no
  connection to time out, because in the
  TCP sense there is no connection.

Each UDP packet is encrypted and carries
a sequence number and a timestamp, so
the two ends can tell current news from
replayed or stale packets and ignore
anything that does not authenticate.

## Roaming: the address is a rumour

The server does not pin the session to
the address the client started from. It
remembers the address of the most recent
*authenticated* packet — one encrypted
with the session key — and sends its
replies there. Your laptop wakes on a
new network, sends its next sync from a
new address, and the server quietly
moves the conversation to where you now
are. No handshake, no reconnect, nothing
for you to do. (The same design is why
the key matters so much: possession of
the key *is* the identity, from any
address on Earth.)

## Local echo: prediction with an apology built in

While the sync protocol keeps the screen
true, a second mechanism keeps typing
feeling instant. The client watches what
you type and **predicts** the effect:
most keystrokes simply add their
character to the screen at the cursor,
and most backspaces remove one. The
client shows those predictions at once,
**underlined**, and keeps a note of what
it guessed. When the server's next sync
arrives, confirmed predictions lose their
underline; wrong ones are quietly
replaced by the truth. On a good link
you never notice any of this. On a bad
one, you type at full speed against a
screen of underlined guesses, and watch
them confirm in waves as the link
permits.

Prediction is adjustable — chapter 6
covers `--predict=always`, `never`, and
the default `adaptive`, which predicts
only when the link's recent behaviour
says the wait would be noticeable.

## What each part holds

- **`mosh` (the wrapper)** — speaks SSH,
  starts the server, carries the port
  and key to the client, exits.
- **`mosh-server`** — one per session.
  Owns the shell, the terminal, and the
  true screen state. Listens on one UDP
  port. Waits — patiently and
  indefinitely — for its client.
- **`mosh-client`** — owns your local
  terminal: draws the screen, predicts
  your keystrokes, and syncs with the
  server whenever the network allows.
- **The session key** — generated per
  session by the server, carried to the
  client through the SSH login, and the
  only credential the UDP conversation
  ever checks.

Keep that division in mind. Nearly every
surprise later in this guide — the dead
agent (chapter 7), the firewall rule
(chapter 8), the unrecoverable client
(chapter 11) — is one of these four
parts behaving exactly as described
here.
