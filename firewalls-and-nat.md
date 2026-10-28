# Firewalls and NAT

Every "Mosh works at home and nowhere
else" story is this chapter's story.
SSH needs TCP port 22, which the world
has open by habit. Mosh needs **UDP**,
ports **60000 to 61000**, reachable at
the server — and the world does not
have those open by habit, because almost
nothing else uses them. This chapter
opens them deliberately, narrows them
when narrowing is wise, and deals with
the home router standing in the way.

## What exactly must be reachable

One UDP port per live session, chosen
by the server from 60000–61000 unless
you say otherwise. The port must accept
packets from wherever your client roams
— in practice, from the whole internet,
if you roam the whole internet. The
packets that arrive are small, encrypted,
and useless to anyone without the
session key; an open Mosh port shows an
attacker a locked door with no handle,
which is not the same as no door, and
chapter 11 keeps the honest ledger. But
a firewall that drops the port does not
degrade the session — it prevents it,
silently, which is why transcript t02's
symptom (a patient client, a rising
"without contact" counter) is the one
to learn.

## Opening the range

On the server itself, with **ufw** (the
firewall front-end common on Ubuntu),
the Mosh package even ships its own
profile — the lab verified this from
the package's file list, where
`/etc/ufw/applications.d/mosh` reads
`ports=60000:61000/udp`:

```bash
sudo ufw allow mosh
```

Without ufw, the rule in whatever tool
you use is the same shape: allow
incoming UDP, destination ports
60000:61000. In a cloud console, this is
a security-group rule for UDP — the
classic mistake is adding TCP 22, seeing
SSH work, and never learning that UDP
was a separate question until the first
trip. On a home server, the router
needs a matching port-forward of the
same UDP range to the server's inside
address; forwarding only TCP 22 gives
you a login that starts a server nobody
can reach — the wrapper succeeds, the
client counts seconds, transcript t02
again.

Check: after any change, watch the
server's socket while a session is up
(chapter 2's check):

```bash
ss -lunp | grep mosh-server
```

One listening line per live session,
each on its own port in the range.

## Narrowing: one fixed port

A thousand open ports offends some
people's tidiness, and some firewalls
charge by the rule. You can pin a
session to a single port: the wrapper's
`-p` option tells the server which port
(or small range) to bind:

```bash
mosh -p 60001 you@server.example.org
```

or, from the server side by hand,
`mosh-server new -p 60001` — the same
option transcript t01's server would
take. Then the firewall rule is one
port. Two cautions travel with the
convenience: one port means one session
at a time on that rule, and a fixed,
predictable port is marginally easier
for a stranger to find and throw
packets at (which the encryption shrugs
off, but the log noise is real). Most
home users take the range; most
rented-server users take the range too,
inside a security group that already
limits sources where it can.

## NAT, and the client side

The client needs nothing: its packets
leave from an ephemeral port, the NAT
in front of it remembers the
conversation for a while, and replies
find their way back. Two wrinkles are
worth knowing. First, some NATs forget
UDP conversations quickly; Mosh's
regular sync traffic keeps them warm
while you work, and a session left
silent for a very long time may need
one keystroke to re-teach the path —
the session itself is unharmed. Second,
roaming *is* a NAT change by another
name: the server does not care which
NAT you are behind this hour (chapter
2), so a client behind the strictest
hotel network works fine, provided the
network lets UDP out at all — a small
number of locked-down networks block
outbound UDP except DNS, and on those,
Mosh cannot connect and chapter 11's
advice is to accept the network's
answer (or use your own hotspot) rather
than to tunnel around a policy that is
not yours to repeal.
