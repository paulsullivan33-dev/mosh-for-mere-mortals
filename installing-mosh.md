# Installing Mosh

Mosh is installed twice: once on the
machine you type at (for `mosh-client`)
and once on every machine you want to
reach (for `mosh-server`). The two halves
ship in a single package named `mosh` on
nearly every system, so in practice you
install the same package at both ends,
plus one requirement that surprises
people: a **UTF-8 locale** — a system
setting that says text is encoded as
UTF-8 — in effect on the server for your
account, and in your terminal at the
client end. This chapter does the
installs and the two checks that prove
them: the version banner and, on the
server, the locale refusal you want to
meet here rather than later.

## The package installs

On Debian or Ubuntu, at both ends:

```bash
sudo apt install mosh
```

On Fedora, `sudo dnf install mosh`; on
Arch, `sudo pacman -S mosh`; on Alpine,
`sudo apk add mosh`. On macOS with
Homebrew, `brew install mosh` gives you
the client (and a server, if you ever
want to reach the Mac). On Windows there
is no native client: run Mosh inside
**WSL** (the Windows Subsystem for
Linux), where the Ubuntu instructions
above apply unchanged. Phones and
tablets have their own Mosh-capable
terminal apps in their app stores; the
server cannot tell the difference.

Two honest notes about the server end.
First, the server machine also needs its
usual SSH server running and reachable —
Mosh starts *through* SSH (chapter 2) —
and your account there needs a working
shell. Second, nothing about the install
opens any ports: `mosh-server` is only
ever started per session, by your own
login. The firewall question is real but
separate; it is chapter 8.

Check: the lab's version printout
(transcript `mosh-lab/transcripts/t00-version.txt`),
from the official Ubuntu package, Mosh
**1.4.0**:

```text
mosh 1.4.0 [build mosh 1.4.0]
```

Run `mosh --version` at both ends after
installing. The versions do not need to
match exactly — the protocol is old and
stable — but a very old server (1.2.x
still turns up on long-lived machines)
is worth updating while you are there.

## The locale requirement

Mosh synchronises a screen of
*characters*, and it refuses to guess
encodings: both ends must be speaking
UTF-8. If the server side is not, you
will not get a vague warning — you will
get a refusal, and it is worth seeing
once in a safe place. The lab produced
it deliberately:

```text
$ LC_ALL=POSIX mosh-server new
mosh-server needs a UTF-8 native locale to run.

Unfortunately, the local environment (LC_ALL=POSIX) specifies
the character set "US-ASCII",
```

(the full message, with its listing of
every locale variable it inspected, is
transcript `mosh-lab/transcripts/t03-locale.txt`).

The fix on the server is to give your
account a UTF-8 locale — on Debian and
Ubuntu, typically by installing the
`locales` package, generating the one
you want (for example `en_US.UTF-8`) with
`sudo locale-gen en_US.UTF-8`, and making
sure your login session sets `LANG` or
`LC_ALL` to it. Check any machine with:

```bash
locale
```

Every line should end in `UTF-8` (the
exact spelling varies: `en_US.UTF-8`,
`C.UTF-8`). On the client, the same
applies to the terminal you launch Mosh
from; modern desktop terminals are
UTF-8 by default, and the refusal above
is what "not by default" looks like.

## What you have now

- `mosh`, the wrapper, on your machine.
- `mosh-client` beside it.
- `mosh-server` on each machine you
  want to reach, started fresh for
  every session by your own SSH login —
  nothing new listening on the server
  while you are away.
- A UTF-8 locale at both ends, without
  which nothing else in this guide will
  happen.

Chapter 4 makes the first connection.
