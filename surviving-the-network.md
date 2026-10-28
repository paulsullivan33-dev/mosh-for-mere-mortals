# Surviving Sleep, Roaming and Bad Networks

This is the chapter Mosh exists for.
Chapter 1 named the three failures of a
TCP stream; here is each one as it
actually happens to a session, and what
you see — and don't have to do — when it
does. Nothing in this chapter could be
run in the guide's lab, whose sandbox
blocks the very first packet (see
`criteria.md`); it is taught from the
project's documentation, and the one
piece of it the lab *could* record —
the client's behaviour while it has no
contact at all — is transcript t02.

## Sleep: the laptop closes

You close the lid mid-session. The
client stops sending; the server, which
was never holding a connection in the
TCP sense, simply holds its state. Hours
pass. You open the lid — possibly on a
different continent — and the client
sends its next sync. The server answers
with the current screen. Commands you
left running have finished; their output
is on the screen (the last screenful of
it — see the scrollback honesty in
chapter 6); long-running jobs that were
writing to the terminal have been
writing to the server's model of it all
along, undisturbed.

There is nothing to configure and
nothing to reconnect. The failure mode
to know is the contact bar (chapter 4):
if the server itself has gone away —
rebooted, say — the bar counts up
forever, truthfully, and the session is
gone with the machine, as it would be
under any tool.

## Roaming: the address changes

You move from desk Ethernet to Wi-Fi,
from Wi-Fi to a phone hotspot, from one
café to another. Each move gives your
machine a new IP address. A TCP
connection would die at the first move.
The Mosh client just sends its next
sync packet from wherever it now is;
the server, seeing an authenticated
packet (chapter 2), replies to the new
address. The interruption you experience
is the length of the move itself — the
seconds with no network at all — plus a
beat. Nothing restarts, nothing logs in
again, no key is re-typed.

One edge to know: if two different
clients somehow held the same session
key, the session would follow whichever
sent most recently — another reason the
key is a password (chapter 11) and why
you do not share `MOSH CONNECT` lines.

## Bad networks: loss and delay

On a link that drops packets or delivers
them seconds late, two mechanisms from
chapter 2 share the work. The sync
protocol means loss is not damage: the
next successful packet describes the
current screen, whole. Delay means you
see fewer updates per second, not a
growing backlog. And prediction keeps
typing usable: your keystrokes appear
at once, underlined, and are confirmed
or quietly corrected as syncs land.

Practical experience, from the project's
documentation and a decade of field
reports: at delays where SSH echo is
painful (around a third of a second),
Mosh typing feels local. At delays of
several seconds it feels like typing
into a slow-motion mirror — still
usable, honestly strange. Under loss
that reduces SSH to a stutter, Mosh
degrades to a slideshow that remains
*correct*. What Mosh cannot do is create
bandwidth: a command that prints ten
thousand lines will still take its time
arriving; you will simply see the final
screen rather than every line scrolling
past (set `less` or a pager on the far
end if you wanted the middle).

## The pairing that covers the last gap

Mosh has one honest hole: if the
**client program** dies — the terminal
window is closed by force, the laptop's
battery gives out mid-write, the phone
app is swiped away — the session is
over, because the client's half of the
state is gone and a new client cannot
adopt the server (chapter 11). The
standard answer is to run **tmux** (or
GNU Screen) inside the session on the
server:

```bash
mosh you@server.example.org -- tmux attach
```

Then the division of labour is clean.
Mosh keeps the network's weather off
your back; tmux keeps your shells,
panes and scrollback alive on the
server through anything, including a
dead client. If the client dies, you
connect again with Mosh and `tmux
attach` returns you to the exact room
you left. Most long-term Mosh users end
up with this pairing without ever
deciding to; chapter 9 makes it a
one-word habit.
