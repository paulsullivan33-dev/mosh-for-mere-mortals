# Mosh for Mere Mortals

You are working in a shell on a remote
machine over **SSH**, and the connection
is the fragile part. Close the laptop lid,
and the session is gone. Move from Wi-Fi
to wired — or to a train, a café, a
different network entirely — and the
session is gone. Hit a patch of bad
reception, and every keystroke takes a
second to appear, until typing feels like
dictating to someone underwater.
**Mosh** — the **mobile shell** — is the
SSH replacement built for exactly those
moments. The session survives sleep. It
survives your machine arriving on a
completely different network with a
different address. And on a slow or lossy
link it shows your keystrokes instantly,
predicting what the screen should say
instead of waiting for the server to agree.

This guide explains how that works — the
protocol that synchronises *screen states*
instead of streaming bytes, the brief SSH
login that starts it, the single UDP port
it lives on — and how to install it,
connect with it, pair it with SSH and
tmux, get it through firewalls, and
recognise its limits, which are real:
Mosh does not forward ports, does not
forward your SSH agent, and cannot give
you back a session whose client program
has died.

The rule that governs everything here is
stated in chapter 1 and repeated in
chapter 11: **your machines, your
accounts, or explicit permission to use
them.** The lab obeyed it strictly. Every
connection in this guide is between
programs the lab itself ran, on one
machine, over addresses that never leave
that machine.

The lab behind this guide ran the real
thing: **Mosh 1.4.0**, the official Ubuntu
package, unpacked on a small Linux
machine. Against it the lab read the
version banner, started `mosh-server` by
hand — the same server the `mosh` command
starts for you over SSH — and watched it
print its `MOSH CONNECT` line and settle
in listening on its UDP port. The lab
also ran the server under a non-UTF-8
locale and collected its refusal, because
that refusal is the first thing many
readers will meet.

What the lab could not do, it did not
fake. The build machine's sandbox refuses
every UDP packet, even to itself — and
UDP is the one thing Mosh is made of. So
the lab's attempt at a full session is
kept in the transcripts exactly as it
ended: the real client, in a real
terminal, counting seconds without
contact and giving up, while the server
waited patiently on its port. That
failure is useful evidence — it is
precisely what a firewalled UDP port
looks like from inside Mosh — but it
means the chapters about life inside a
session (typing, roaming, local echo) are
taught from the project's documentation
and the two programs' observable
behaviour, and are labelled documented-
only where it matters. `criteria.md`
keeps the full ledger of what was run
and what was only read.

## What this guide covers

- [**What Mosh is**](what-mosh-is.md) —
  the problem it solves, the session that
  survives, and the rule that comes
  before everything else
- [**How Mosh works**](how-mosh-works.md) —
  the moving parts: the SSH bootstrap,
  the session key, the state-sync
  protocol, roaming, and local echo
- [**Installing Mosh**](installing-mosh.md) —
  a package on each end, the version
  check, and the UTF-8 locale Mosh
  insists on
- [**Your first session**](your-first-session.md) —
  connecting, what the screen tells you,
  the escape key, and how a session ends
- [**Surviving sleep, roaming and bad
  networks**](surviving-the-network.md) —
  the three failures that kill SSH
  sessions, and what Mosh does about
  each of them
- [**Mosh day to day**](mosh-day-to-day.md) —
  prediction settings, window sizes,
  scrollback honesty, and living in a
  session all day
- [**Mosh and SSH**](mosh-and-ssh.md) —
  how the two split the work: SSH logs
  you in and leaves, and what that means
  for keys, jump hosts, and everything
  SSH forwards that Mosh doesn't
- [**Firewalls and NAT**](firewalls-and-nat.md) —
  one UDP port range to open, choosing a
  fixed port, and servers behind home
  routers
- [**Recipes**](recipes.md) — the laptop
  on the move, the flaky link, the
  all-day tmux session, the server you
  administer from a phone
- [**The neighbours**](the-neighbours.md) —
  plain SSH, tmux and GNU Screen,
  autossh, Eternal Terminal, and remote
  desktops: what each does that Mosh
  doesn't, and vice versa
- [**Limits**](limits.md) — no
  forwarding, no reattaching, one screen
  of scrollback, a session key that is
  a password, and the rest, stated
  plainly
- [**Troubleshooting**](troubleshooting.md) —
  the session that never connects, the
  locale refusal, the wrong server, and
  the traps this guide's lab fell into
  so you don't have to
- [**Glossary**](glossary.md) — the
  terms, collected in one place

## What it doesn't cover

Connecting to machines you are not
authorised to use — that is not a harder
chapter of this book, and chapter 11
explains the line. Defeating a network's
firewall policy from inside it, hiding a
session from the people who run the
network, and tunnelling other traffic
through Mosh (it refuses the job; see
chapter 11) are likewise out. Running
Mosh as managed infrastructure for a
whole organisation — fleet policy,
jump-host estates, audit — is pointed
at, not taught; this guide stays with
one person and their own machines,
where the tool does its honest work.

## How to use this guide

Read chapter 1 for the problem and the
rules, and chapter 2 before installing
anything — knowing which part holds
what turns later confusion into a named
problem. Chapter 4 takes your first
session in the same sitting as the
install, ideally to a machine in the
same room, so you can watch both ends.
Chapter 5 is best read with a laptop
lid in reach: close it mid-session and
see. Chapter 7 is the one that prevents
the classic disappointment — expecting
Mosh to forward something it never
promised to — and chapter 8 prevents
the other classic, a session that works
at home and never connects from
anywhere else. The lab's transcripts
live in `mosh-lab/transcripts/`; its
distilled commands are
`examples/commands.txt` and its notes
are `examples/lab-notes.txt`.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [SSH for Mere Mortals](https://paulsullivan33-dev.github.io/ssh-for-mere-mortals/)
- [tmux for Mere Mortals](https://paulsullivan33-dev.github.io/tmux-for-mere-mortals/)
- [Home Networking for Mere Mortals](https://paulsullivan33-dev.github.io/home-networking-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/mosh-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/mosh-for-mere-mortals/blob/main/LICENSE-CODE).
