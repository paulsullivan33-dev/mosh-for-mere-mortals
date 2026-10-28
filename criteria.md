# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's traffic is the lab talking to itself.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 11 names what the tool
   cannot do: no port forwarding, no X11 forwarding, no SSH
   agent forwarding, no reattaching to a session whose client
   has died, scrollback limited to one screen, and a session
   key that is a password in everything but name.
8. **The ethics rule is in the text, early.** Chapter 1 and the
   README state the rule plainly — *your machines, your
   accounts, or explicit permission to use them* — before any
   procedure depends on it, and later chapters point back to
   it where an open UDP port or a printed session key would
   make crossing the line easy.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a 2-CPU, 7.7 GB Ubuntu 24.04 sandbox.
Mosh **1.4.0** (the Ubuntu package, 1.4.0-1ubuntu3) was fetched
with `apt-get download` and unpacked into a scratch folder with
`dpkg -x`, because the build machine's package system was busy
elsewhere; every library Mosh needs was already present on the
machine, plus one Perl module the package recommends
(libio-socket-ip-perl), fetched the same way. Nothing was
installed system-wide, and the guide's transcripts in
`mosh-lab/transcripts/` are the evidence.

Run for real, with transcripts as evidence:

- `mosh --version` and the package candidate, read from the
  official Ubuntu archive build of Mosh 1.4.0 (t00).
- The server half of the handshake, run by hand exactly as
  the `mosh` wrapper runs it over SSH: `mosh-server new`
  printed its `MOSH CONNECT 60001 <key>` line, detached, and
  was shown listening on UDP port 60001 by `ss` (t01).
- The UTF-8 requirement, enforced in front of the lab:
  `mosh-server` under a POSIX locale refuses to run and says
  why, at length (t03).

Could not be run — and the guide does not pretend otherwise:

- **A completed client session.** This build machine's
  sandbox refuses every UDP send, even to its own loopback
  address, even inside a fresh network namespace (proven with
  a five-line Python check, kept in t02). The lab ran the real
  `mosh-client` in a terminal against the real server from
  t01; the client's attempts, its rising "without contact"
  counter, and its final "did not make a successful
  connection" message are transcript t02. Everything after a
  successful connect — typing in the session, the sleep and
  roaming behaviour of chapter 5, local echo in chapter 6 —
  is therefore taught from the project's documentation and
  the observable behaviour of the two programs, and labelled
  documented-only where it matters. The irony is noted once,
  here: the tool whose entire purpose is surviving bad
  networks was verified on a machine whose network policy is
  worse than any it will meet in the wild, and transcript t02
  is at least a genuine recording of what blocked UDP looks
  like from inside Mosh — which is what chapter 12 teaches
  readers to recognise on real firewalls.
- The SSH login that normally starts the server: no SSH
  server runs on the build machine, so the wrapper's
  bootstrap is described from the project's documentation
  and from the server behaviour the lab ran by hand in t01,
  which is the same server the wrapper starts.

Fixtures are this guide's own and deliberately generic: one
machine talking to itself on loopback, a throwaway session
key printed by a server that was stopped as soon as its
transcript was taken (treat any real `MOSH CONNECT` line as a
password — chapter 11 explains why), and invented host and
user names (`server.example.org`, `you`) in the examples. No
example is modelled on any real person, network, service or
organisation.
