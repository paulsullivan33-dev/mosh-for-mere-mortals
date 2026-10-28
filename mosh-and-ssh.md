# Mosh and SSH

The most common misunderstanding about
Mosh is that it replaces SSH. It does
not: it *employs* SSH, briefly, and then
goes its own way — and everything
practical about keys, jump hosts, agents
and file transfer follows from that
division. This chapter draws the line
precisely, because most Mosh
disappointments are expectations
inherited from SSH that Mosh never
agreed to meet.

## Who does what

- **SSH authenticates.** Your keys,
  your `~/.ssh/config`, your passwords
  and passphrases, the server's host
  key and your decision to trust it —
  all of that is SSH, happening exactly
  as it would for a plain login. If you
  cannot `ssh you@server.example.org`,
  you cannot `mosh` to it either, and
  the error you get will be SSH's.
- **SSH starts the server.** The login
  runs one command — `mosh-server new` —
  and receives the `MOSH CONNECT` line
  (chapter 2; transcript t01 is that
  server started by hand).
- **SSH leaves.** The login closes.
  Nothing of SSH remains in the path:
  not its encryption, not its connection,
  not its agent socket.
- **Mosh carries the session.**
  Encrypted with the per-session key,
  over UDP, until the shell exits or the
  client escapes (chapter 4).

## Consequences, one by one

**Your SSH configuration still works.**
Host aliases, `User`, `Port`,
`IdentityFile`, `ProxyJump` — the
wrapper passes your host argument to
SSH, so everything in `~/.ssh/config`
applies to the login. To pass extra
arguments to that SSH invocation
explicitly, the wrapper accepts
`--ssh='ssh -p 2222'` and friends.

**Jump hosts work — for the login.**
`ProxyJump` and friends get the SSH
login to the far end, where the server
starts. But the UDP session that
follows is *direct* between your client
and that far server. A jump host that
was necessary because the target is
unreachable from your network is still
necessary afterwards, and Mosh cannot
use it: the target's UDP port must be
reachable from wherever you roam
(chapter 8). This is the sharpest edge
in the chapter: estates built entirely
behind bastions are estates Mosh mostly
cannot serve, and chapter 11 says so
again in the limits ledger.

**The agent does not come with you.**
SSH agent forwarding does not survive
the handover, because there is no SSH
connection left to forward through.
Inside a Mosh session, `ssh` commands
to a third machine cannot borrow the
agent from your laptop; they will ask
for keys or passphrases available *on
the server*. Plan accordingly: keys
that live on the server, an agent
started on the server, or hop with
plain SSH for that leg.

**No port forwarding, no X11.** Mosh
forwards a terminal and nothing else.
`ssh -L`, `-R`, `-D`, and `-X` have no
counterparts; a workflow that needs a
tunnel needs SSH (or a VPN) alongside,
which is a perfectly respectable
arrangement: many people keep one SSH
connection for tunnels and files, and
live in Mosh for the shell.

**Files go by SSH, as ever.** `scp`,
`sftp` and `rsync` are untouched by
Mosh and work exactly as before, in
parallel with any number of Mosh
sessions. Mosh neither helps nor
hinders them; it simply has no opinion
about files.

## The security shape, in one paragraph

Your exposure is the sum of two familiar
parts. The login is SSH's: as strong as
your keys and your host-key discipline.
The session is Mosh's: encrypted and
authenticated with a fresh random key
per session, printed once over the SSH
channel, never reused. The new thing to
guard is that key during the session's
life (chapter 11), and the new thing to
keep patched is one more small program
on the server — `mosh-server`, started
only by authenticated logins, holding
no privilege beyond your own account's.
Chapter 11 collects the whole ledger.
