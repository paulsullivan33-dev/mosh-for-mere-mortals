# Glossary

**Bootstrap** — the start of a Mosh
session, in which SSH does the login
and starts the server, then leaves
(chapter 2).

**Client** — `mosh-client`, the program
on your machine: it draws the screen,
predicts your keystrokes, and syncs
with the server (chapter 2).

**Contact bar** — the notice at the top
of the screen when the client has heard
nothing from the server for a while,
with a counter of the silence
(chapters 4 and 12).

**Escape key** — Ctrl-^, reserved by the
client: Ctrl-^ . quits, Ctrl-^ Ctrl-Z
suspends, Ctrl-^ Ctrl-^ sends one
literal Ctrl-^ (chapter 6).

**Locale** — the system setting that
declares a text encoding and language.
Mosh requires UTF-8 at both ends and
refuses otherwise (chapter 3).

**Local echo** — the client showing your
keystrokes' predicted effect at once,
underlined until the server confirms
them (chapters 2 and 6).

**MOSH CONNECT line** — the single line
a starting server prints, naming its
UDP port and session key:
`MOSH CONNECT 60001 <key>` (chapter 2).

**NAT** — network address translation,
the rewriting a router does between a
home network and the internet; clients
live happily behind it, servers need
forwards through it (chapter 8).

**Prediction** — see *local echo*.
Controlled by `--predict=adaptive`,
`always`, `never`, or `experimental`
(chapter 6).

**Roaming** — a session's survival of
the client's change of network and IP
address: the server follows the most
recent authenticated packet (chapter 5).

**Server** — `mosh-server`, one per
session, started by your SSH login on
the far machine: it owns the shell, the
terminal, and the true screen state
(chapter 2).

**Session key** — the random per-session
credential the server generates and the
wrapper carries to the client over SSH.
Possession of it *is* the identity, from
any address; treat it as a password
(chapters 2 and 11).

**Socket** — one end of a network
conversation, held by a program; `ss`
lists them, and chapter 2's check reads
the server's from one.

**State Synchronization Protocol** —
Mosh's protocol: client and server each
hold a numbered copy of the intended
screen and sync the *state*, over UDP,
rather than streaming bytes (chapter 2).

**TCP** — the stream protocol under
SSH: reliable, ordered, and bound to
the two addresses it started with —
the properties Mosh exists to escape
(chapter 1).

**UDP** — the packet protocol under
Mosh: no built-in order, delivery, or
connection; Mosh's protocol supplies
exactly what it needs on top. Mosh
uses ports 60000–61000 by default
(chapter 8).

**Wrapper** — the `mosh` command: a
small program that runs the SSH login,
starts the server, hands port and key
to the client, and exits (chapter 2).
