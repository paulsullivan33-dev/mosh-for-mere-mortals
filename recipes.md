# Recipes

Four situations Mosh users actually
live in, each as a small recipe: the
shape of the answer, the command, and
the catch. Everything here assumes
chapters 3 and 8 are done — Mosh
installed at both ends, the UDP range
open at the server.

## The laptop that never sits still

*Situation:* you administer a server
from a laptop that sleeps, roams, and
changes networks several times a day,
and you are tired of logging in again
every time.

*Recipe:* make the pairing from chapter
5 the default, with an alias in your
shell startup file on the laptop:

```bash
alias work='mosh you@server.example.org -- tmux attach || tmux new'
```

Read it as: connect with Mosh; inside,
attach to the existing tmux session, or
start one if there is none. From then
on, `work` is your whole morning
ceremony. Lid closures, network changes
and client crashes all reduce to typing
`work` again and being back in the same
room, with the same panes, mid-thought.

*Catch:* the *first* connect each day
still does a full SSH login (chapter
7), so key and passphrase prompts appear
then, not later.

## The terrible link

*Situation:* a site connection with
satellite delay, a congested hotspot,
or a rural link that drops a visible
fraction of its packets. SSH is
unusable; you still need a shell.

*Recipe:* connect as usual, then lean
on prediction harder than the default:

```bash
mosh --predict=always you@site.example.org
```

and inside the session, prefer tools
that print little: pagers over `cat`,
`--quiet` flags over progress spam,
builds and backups started under tmux
so their output never has to cross the
link at all. Judge the link by the
underline: if most of your typing
confirms within a second, you are in
better shape than the raw delay
suggests.

*Catch:* prediction cannot help output.
A command that floods the screen will
still take its time; you will see its
final state first and its middle never,
which is usually the right trade.

## The server behind the home router

*Situation:* a small server at home, a
laptop anywhere else, and a consumer
router between them.

*Recipe:* chapter 8 in full: a fixed
inside address for the server, a router
port-forward of UDP 60000–61000 to it,
and a hostname that finds your home
from outside (a dynamic DNS name is the
usual answer). Test from a phone
hotspot — a genuinely outside network —
rather than from the sofa, because many
routers cannot hairpin their own
forward and a sofa test can pass while
the world fails.

*Catch:* do the chapter 1 arithmetic
before opening anything. A Mosh port
open to the world is a door only your
session keys can unlock; an *SSH* port
open to the world is the same as it
ever was, and it is the SSH settings —
keys only, no password login — that
carry the real weight here.

## The phone as a lifeline

*Situation:* something is down, you are
away from every desk, and the only
terminal in reach is a phone.

*Recipe:* a Mosh-capable terminal app on
the phone, your SSH key in it (set up
on a calm day, not this one), and the
same command as everywhere else. Mosh
was designed for exactly this network:
a session started on café Wi-Fi
continues on mobile data as you walk
out, and the small screen costs nothing
because the server matches its state to
whatever size the client reports.

*Catch:* the phone's client dying is
chapter 11's dead-client case — so on
the phone above all, land in tmux
(recipe one) and let the server keep
your place.
