# Security Policy

Yeti 2.0 is an opinionated key management tutorial, not an application. This repository
contains documentation and shell commands for building an air-gapped
Bitcoin Core multisig vault. It does not ship software to install.

Security for users of this guide depends on:

- the correctness of the instructions in this repository
- the security of a small set of upstream projects this guide relies on
  (primarily Bitcoin Core and Ubuntu)
- the operator following the guide, including verification steps

See [THREAT_MODEL.md](THREAT_MODEL.md) for the canonical write up.

## In scope for this repository

Report privately if following the published guide as written could
reasonably cause:

- key or seed material to be written, copied, or transmitted unsafely
- verification steps (ISO, Bitcoin Core, GPG, SHA256, PSBT) to accept
  untrusted software or a tampered transaction
- funds to become unspendable or easier to steal than the guide claims
- a dangerous command, omitted isolation step, or stale download URL /
  version that undermines the air-gap or backup design

Examples: a command that copies a wallet.dat onto the online machine;
a verify step that still passes on a substituted binary; a backup
procedure that omits a file required for recovery.

## Out of scope

Do **not** use private vulnerability reports for:

- Design disagreements (3-of-7 vs other M-of-N, DVD vs paper,
  Bitcoin Core vs hardware wallets, no seed phrases, etc.). Read the
  [guide](README.md) and [FAQ](FAQ.md) first. If something is still
  wrong or unexplained, open a **public issue**.
- Preference for a different OS, wallet, or backup medium.
- Operational mistakes by an individual operator who skipped a step.
- Vulnerabilities in upstream software this guide uses. Report those
  to the upstream project:

  | Project | Report |
  | --- | --- |
  | Bitcoin Core | https://github.com/bitcoin/bitcoin/security |
  | Ubuntu | https://ubuntu.com/security/disclosures |
  | GNOME | https://security.gnome.org/ |

If an upstream issue changes whether a step in this guide is safe,
open a public issue here *after* it is disclosed or patched upstream,
or a private report here if the guide currently tells people to do
something that is already known-unsafe.

## How to report

Use GitHub's private vulnerability reporting on this repository:

**Security → Report a vulnerability**

Include:

- the file and section (or commit) that is wrong
- what a reader would do
- what the unsafe outcome is
- a suggested correction if you have one

Do not post raw keys, descriptors tied to live funds, or recovery
material in the report.

## What to expect

This is a documentation project maintained in limited spare time.

- You should get an acknowledgement when the report is seen.
- In-scope issues will be fixed in the guide, or explained if the
  risk is already an explicit design tradeoff.
- Out-of-scope design debate will be closed with a pointer to the FAQ
  or converted to a public issue.

There are no versioned software releases. The `main` branch is the
supported text of the guide.

## Operator responsibility

This guide cannot protect funds if the air-gap is broken, backups are
not tested, discs are not distributed, or verification steps are
skipped. Before storing meaningful value, complete the test
transactions described in the README.
