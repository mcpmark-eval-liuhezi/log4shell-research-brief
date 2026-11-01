# log4shell-research-brief

Internal security-awareness research material on **Log4Shell (CVE-2021-44228)**.

## What this repository contains

- [`BRIEF.md`](./BRIEF.md) — a research brief answering three questions, each backed by at least two independent, linked sources. Every claim carries an inline link so the provenance of each statement can be checked directly; all linked pages were read in full during research.
  1. **What Log4Shell is and which CVE it corresponds to** — CVE-2021-44228, a CVSS 10.0 remote code execution flaw in Apache Log4j 2 caused by JNDI lookup substitution in logged strings.
  2. **Affected and fixed versions** — vulnerable: Log4j 2.0-beta9 through 2.14.1; first fix: 2.15.0 (6 Dec 2021), followed by the remediation chain 2.16.0 → 2.17.0 → 2.17.1 (and 2.12.2 for Java 7).
  3. **Concrete real-world impact / deployment lesson** — the Iranian APT breach of a U.S. federal agency via an unpatched VMware Horizon server (CISA/FBI advisory AA22-320A), showing that vendored products must be inventoried and that late patching means assuming compromise and hunting.
- A `research/log4shell` branch containing `BRIEF.md`; the work is under review in the open pull request below.

## Open pull request

**Log4Shell research brief** — PR #1: https://github.com/mcpmark-eval-liuhezi/log4shell-research-brief/pull/1

The review comment on that PR doubles as speaker notes for the security-awareness session: it gives the short version of all three answers and lists every source relied on with a one-line note on why each is credible. That review comment has been posted (COMMENT review, not an approval).
