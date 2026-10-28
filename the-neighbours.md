# The Neighbours

Mosh sits in a crowded neighbourhood
of tools that all promise some version
of "a shell that survives". This
chapter characterises each neighbour —
what it does that Mosh doesn't, and
vice versa — without pretending the lab
benchmarked them; they are described,
not run.

## Plain SSH

The incumbent, and chapter 7's partner
as much as rival. SSH carries the
stream whole: every byte in order,
scrollback as deep as your terminal's,
port forwarding, X11, agent forwarding,
file transfer's whole family. On a
stable wired link between two fixed
machines, SSH is not the worse tool —
it is the complete one. Its weaknesses
are precisely Mosh's three (chapter 1):
address changes, sleep, echo at the
speed of the link. Many people run
both, choosing by the day's network —
a habit this guide endorses rather
than apologises for.

## tmux and GNU Screen

The multiplexers are not rivals at all;
they are the other half of the standard
pairing (chapter 5). They live on the
server and keep shells, panes, windows
and scrollback alive through anything,
offering detach and reattach to any
later client. What they cannot do is
make the *connection* to them survive
or type well — over plain SSH to a tmux
session, a roaming laptop still loses
the connection (and reattaches, one
login later, in a noticeably worse
mood). Mosh handles the wire, tmux
handles the room. Neither substitutes
for the other, and chapter 9's alias is
the two shaken hands.

## autossh

A small supervisor that restarts SSH
connections when they die, optionally
with forwarding maintained. autossh
answers failure with *reconnection*:
the connection returns, but the shell
that was running in it is as dead as
the connection was. For keeping a
tunnel up unattended it is the right,
honest tool. For interactive work it
gives you a new room each time the
network blinks, where Mosh gives you
the same room and tmux gives you the
same furniture. The three compose;
they do not compete.

## Eternal Terminal

The closest relative: a remote shell
designed, like Mosh, to reconnect and
survive roaming. Eternal Terminal
(`et`) keeps a byte stream over TCP
with automatic reconnection, which
buys it two things Mosh lacks — native
scrollback and SSH-style port
forwarding — at the cost of the
stream's properties: severe loss and
delay are felt more like SSH feels
them, and its local echo is not
predictive in Mosh's underlined sense.
Choosing between them is choosing
which failure you meet more often:
the dead client (both suffer it; tmux
covers it) or the dreadful link
(Mosh's home ground).

## Browser terminals and remote desktops

Hosted web terminals (a shell in a
browser tab, served by the cloud
provider or a tool like `wetty`) solve
a different problem: a shell from a
machine you cannot install anything
on. They inherit the browser's
connection and usually its death on
sleep. Remote desktops — VNC, RDP and
friends — move the whole graphical
room rather than one terminal: the
right tool when the work *is*
graphical, and a heavy one when it is
not. Mosh's claim in this company
stays narrow and, within its borders,
strong: for text work on an untrustworthy
network, nothing else feels as much
like the network isn't there.
