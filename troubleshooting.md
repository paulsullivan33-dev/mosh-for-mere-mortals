# Troubleshooting

The failures below are the ones this
guide's readers will actually meet,
each with the check that separates it
from its lookalikes. The lab met two of
them personally; its transcripts are
cited where they exist.

## The session never connects

*Symptom:* `mosh you@server.example.org`
pauses, then the client sits showing a
rising "without contact" counter, and
eventually reports it did not make a
successful connection.

*What it means:* the SSH login worked
(the server started) but no UDP packet
has completed the trip — the firewall
case, and the single most common Mosh
failure in the world. The lab produced
these exact messages from its own
blocked machine (transcript
`mosh-lab/transcripts/t02-client-connect-attempt.txt`):

```text
Nothing received from server on UDP port 60001. [To quit: Ctrl-^ .]
mosh did not make a successful connection to 127.0.0.1:60001.
Please verify that UDP port 60001 is not firewalled and can reach the server.
```

*Checks, in order:*

1. On the server, `ss -lunp | grep
   mosh-server` shows a listening line —
   the server exists (chapter 2's
   check). No line: the problem is
   earlier; see "The server never
   starts", below.
2. The server's firewall and any cloud
   security group allow **UDP**
   60000–61000 (chapter 8) — not TCP,
   however natural TCP felt.
3. A home server's router forwards the
   same UDP range, and you are testing
   from a genuinely outside network.

## The login itself fails

*Symptom:* an SSH-flavoured error —
permission denied, host key
verification failed, connection timed
out on port 22 — before any Mosh
behaviour appears.

*What it means:* Mosh has not started
yet; the bootstrap is SSH (chapter 7).
Fix the plain `ssh
you@server.example.org` login first —
same error, clearer documentation —
and Mosh will follow it. A special case
worth knowing: the login succeeds but
the far end answers that `mosh-server`
is not found. The server package is
missing on that machine (chapter 3
installs at *both* ends), or your
login's PATH is too small to see it;
the wrapper's `--server=/full/path/to/mosh-server`
option names it explicitly.

## The locale refusal

*Symptom:* the connection stops with a
long message beginning:

```text
mosh-server needs a UTF-8 native locale to run.
```

*What it means:* chapter 3's
requirement, enforced. The full text
(transcript
`mosh-lab/transcripts/t03-locale.txt`)
lists the locale the server found.
Generate a UTF-8 locale on the server
and make sure your login sets it;
check with `locale` over plain SSH.

## It works at home, fails everywhere else

*Symptom:* flawless on the LAN, dead
from any outside network.

*What it means:* almost always the
router: no UDP port-forward, or a
forward for TCP only, or a successful
test that was actually hairpinned from
inside (chapter 9's catch). Confirm
from a phone hotspot. The rarer cause
is an outside network that blocks
outbound UDP entirely — confirm by
failing identically toward a second,
known-good server; chapter 11's advice
applies to the network's answer.

## Typing feels wrong

*Symptom:* characters arrive in bursts,
predictions are wrong often enough to
annoy, or an editor redraws strangely.

*Checks:* you are seeing the link's
true speed plus the predictor's
guesses (chapter 6). Try
`--predict=never` for an hour: if the
strangeness vanishes, it was prediction
correcting itself on a bad link — drop
to `adaptive`, or accept `always` with
its flicker. If plain SSH to the same
machine feels identical, the link is
simply bad, and no client setting will
out-argue physics; chapter 5's small
-output habits are the remedy.

## Sessions pile up on the server

*Symptom:* `ps` shows a garden of old
`mosh-server` processes.

*What it means:* orphaned servers from
clients that escaped, died, or were
closed by force (chapter 11). They
hold a port each and the memory of a
shell; they are harmless singly and
untidy in herds. Clear your own with
chapter 11's `pkill`, carefully, from a
fresh login — and note the live ones
first, if any doubt.

## The wrong screen size, forever

*Symptom:* the far end insists the
terminal is 80×24 (or some fossil
size) whatever you do.

*What it means:* the client connected
before the terminal reported its real
size — some wrappers and scripted
starts do this — or a multiplexer
inside is holding its own size. Resize
the window once (the client re-reports
on change, chapter 6), and inside tmux
detach and reattach, which renegotiates
sizes all the way down.
