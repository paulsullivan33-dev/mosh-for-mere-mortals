# Mosh Day to Day

A session that survives everything
invites living in it, and living in it
raises smaller questions than chapters
5's dramas: how much prediction do you
want, what happens when the window
changes size, where did the scrollback
go, and what the escape key does besides
quit. This chapter answers them in the
order they usually arrive. Like chapter
5, it describes behaviour the lab could
not record (see `criteria.md`) and is
documented-only; the options named are
the ones the programs themselves print
in their usage text, which the lab did
read from the real binary.

## Prediction settings

Local echo (chapter 2) is controlled by
one option, accepted both by the `mosh`
wrapper and by `mosh-client`:

- `--predict=adaptive` — the default.
  Predict when the link's recent
  round-trip behaviour says waiting for
  the server would be noticeable; don't
  bother on a fast local link.
- `--predict=always` — predict even on
  fast links. Some people simply like
  the zero-latency feel everywhere;
  corrections, when they come, are the
  price.
- `--predict=never` — never predict.
  Every character waits for the server,
  as in SSH. The honest choice when a
  program behaves oddly under prediction
  — password prompts and some full-screen
  editors are the usual suspects — or
  when you want to *see* the link's true
  speed.
- `--predict=experimental` — predict
  aggressively, including guesses the
  safer modes would not make. A research
  setting; the name is the warning.

A companion flag, `-o` /
`--predict-overwrite`, changes how a
prediction is drawn: by overwriting
rather than inserting, which suits some
line-editing styles. Predictions are
always shown underlined until the
server confirms them, in every mode —
if you ever see underlined text that
never confirms, you are looking at a
link in real trouble, and the contact
bar will usually be telling the same
story. The environment variable
`MOSH_PREDICTION_DISPLAY` can preset
these behaviours for people who live in
the wrapper; set it once in your shell
startup rather than typing flags.

## Window size and resize

The client tells the server your
terminal's size when you connect and
whenever it changes; the far-end
terminal resizes to match, and programs
that care (editors, tmux, `top`) redraw
at the new size. Dragging a window from
a laptop screen to a big monitor
mid-session works the way you would
hope. This is a quiet advantage over
some recovery tools in chapter 10,
which can strand a session at the size
it was born with.

## Scrollback: the honest paragraph

Mosh synchronises a *screen*, and a
screen is only so tall. Output that has
scrolled past the top of the server's
terminal model is, in general, gone
from the session's own memory: your
terminal's native scrollback will not
contain it either, because the client
redraws a state instead of printing a
stream. In practice this is the
complaint long-time users rank first,
and the answer is the chapter 5 pairing:
inside tmux or GNU Screen, scrollback
lives on the server, in a program built
to keep it, where Mosh's statelessness
cannot hurt it. Outside a multiplexer,
work as the protocol suggests: pipe
long output through a pager or to a
file on the server, where it can be
read at leisure — it is also, not
incidentally, still there tomorrow.

## The escape key, completely

**Ctrl-^** is the escape character, and
it is consumed by the client, never
sent to the server:

- **Ctrl-^ .** — end the session now.
  Works when the network is gone and
  the shell unreachable; the server
  keeps waiting (chapter 11 covers
  tidying such orphans).
- **Ctrl-^ Ctrl-Z** — suspend the
  client, returning you to your local
  shell; `fg` brings the session back.
  The classic way to run a local command
  "for a second" without leaving.
- **Ctrl-^ Ctrl-^** — sends one literal
  Ctrl-^ to the server, for the rare
  program that wants it.

If Ctrl-^ clashes with something you
use — an editor, another tool's escape
— the environment variable
`MOSH_ESCAPE_KEY` sets a different one
(given as a byte value) for your
sessions.

## Small print of daily life

- **The title and the prompt.** The
  wrapper sets your terminal title to
  the session's destination by default;
  `MOSH_TITLE_NOPREFIX=1` in the
  environment calms wrappers and window
  managers that dislike it.
- **Bell and colours** pass through as
  the terminal model carries them; a
  256-colour server terminal is the
  default assumption, and `-c` on
  `mosh-server` exists for the rare
  terminal that is not.
- **Multiple sessions** to one server
  are independent servers on independent
  ports — open as many terminals as you
  have machines to reach, and mind the
  firewall arithmetic in chapter 8 if
  you make a crowd of them.
