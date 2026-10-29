# log4shell-research-brief

Source material for our internal security-awareness session on **Log4Shell (CVE-2021-44228)**.

This repository contains a research brief ([`BRIEF.md`](./BRIEF.md)) that answers three questions, each backed by at least two independent, linked sources ([the full source set lives in the brief's source register](./BRIEF.md#source-register)):

1. What Log4Shell is and which CVE identifier it corresponds to (CVE-2021-44228).
2. Which Apache Log4j versions were vulnerable (2.0-beta9 → 2.14.1) and which release fixed it (2.15.0, with follow-ups through 2.17.1).
3. A real-world incident and deployment lesson — the Iranian APT breach of a U.S. federal agency via an unpatched VMware Horizon server.

**➡️ The brief is under review in open pull request #1: https://github.com/mcpmark-eval-liuhezi/log4shell-research-brief/pull/1** — please read it there; the review comment on that PR doubles as the speaker notes for the session. Every claim in the brief includes an inline link to the page it came from so the provenance of each statement can be checked directly.

**Latest update (2026-10-22):** PR #1 is open and current; a COMMENT review on PR #1 containing the short version of all three answers plus a source-by-source credibility note has been posted by the repository owner; every source cited in the brief was read in full during research (not just search snippets).
