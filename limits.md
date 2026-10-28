# Limits

What Mosh cannot do, and what it must
not be asked to do — the chapter the
rest of the guide has been promising.

## It forwards nothing

Mosh carries one terminal. There is no
port forwarding (no `-L`, `-R`, `-D`),
no X11 forwarding, and no SSH agent
forwarding — chapter 7 explains why:
after the bootstrap there is no SSH
connection left to forward through.
Workflows built on tunnels — reaching
a database through the server, showing
a remote window — still need SSH or a
VPN running alongside. This is not a
missing feature awaiting a version;
forwarding other traffic is a different
tool's job, and Mosh's small, single
purpose is part of why it is small
enough to trust and to finish.

## A dead client is a dead session

The client holds half the session's
state, and there is no reattach: force
-quit the terminal, let the battery die
mid-write, swipe the phone app away,
and the session is over. The server
waits for a client that no longer
exists — indefinitely, in fact; orphan
servers accumulate until someone kills
them, so make a small habit of it:

```bash
pkill -u "$USER" mosh-server
```

(run on the server, in a fresh login,
when you know no live session of yours
is using one). The real answer remains
chapter 5's pairing: tmux or GNU Screen
inside the session keeps your work
alive on the server, so a dead client
costs you a reconnect, not the room.

## One screen of memory

The protocol synchronises the current
screen, so scrollback inside Mosh is
limited to what the screen holds
(chapter 6). Ten thousand lines of
build output are a final screenful and
a memory. Pagers, log files, and
multiplexers are the remedies; they are
also, not coincidentally, how careful
people worked before scrollback was
deep. Treat any tool that promises to
fix this *within* the session with
suspicion — the limit is the protocol's
shape, not a setting.

## The session key is a password

Chapter 2's ceremony ends with a random
key that *is* the session's identity,
accepted from any address on Earth —
that is what makes roaming possible.
Consequences, stated flatly:

- Anyone who learns a live session's
  key and port can connect to it and
  take the shell. Treat `MOSH CONNECT`
  lines, `MOSH_KEY` values, and server
  scrollback the way you treat private
  keys: do not paste them into tickets,
  chats, or screenshots. (This guide's
  one printed key belonged to a
  throwaway server on a sealed machine,
  stopped immediately; it is the
  exception that demonstrates the rule.)
- A locked, sleeping, *connected*
  laptop is an open door into every
  live session on it, from anywhere.
  Lock the screen; that habit is now
  part of your server's security.
- Sessions have no second factor and
  no per-user audit after the login.
  The SSH login is logged; the session's
  life is not. For machines where that
  matters, chapter 10's neighbours and
  your organisation's policy — not this
  guide — set the terms.

## It needs its port, and some networks say no

Mosh speaks UDP on 60000–61000 and
nothing else. Networks that block
outbound UDP (chapter 8), carriers and
hotspots with hostile NAT, and servers
behind firewalls nobody will adjust
are all places Mosh simply cannot go.
The honest response is to use SSH
there — it is not as if the alternative
were bad — and not to tunnel Mosh
through somebody else's network
against its policy: a firewall rule is
somebody's chapter 1 rule, applied to
you.

## The legal and ethical edge

Chapter 1's rule — *your machines, your
accounts, or explicit permission* — is
not merely this guide being prim.
Unauthorised access to computer systems
is a crime in most jurisdictions, and a
tool whose sessions survive sleep and
roam across networks makes the "I was
still logged in from last week" version
of trespass easier to commit by
accident: a session left running on a
machine you have returned, sold, or
stopped being employed to use is a live
shell with your name on it. End
sessions when the permission ends;
check for orphans on machines you are
leaving (the `pkill` above, run while
the account is still yours, is the tidy
version). Everything inside the line —
and it encloses years of comfortable
work — needs no apology and no
concealment.

## Performance and fidelity

A sync protocol sends the screen, not
the keystrokes' echoes, so under real
load the display is *eventually*
perfect rather than continuously
faithful: fast-scrolling output is
sampled, not shown frame by frame
(chapter 5). Prediction, for all its
underlined honesty, is a guesser: in
password prompts it is deliberately
blind (nothing predicts there — check
by typing one wrong character and
watching nothing happen, which is the
correct behaviour), and in some
full-screen programs its corrections
can flicker. `--predict=never`
(chapter 6) is always available when
the guessing itself is the problem.
