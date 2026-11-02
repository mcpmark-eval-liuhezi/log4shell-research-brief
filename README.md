# log4shell-research-brief

Security-awareness research brief on **Log4Shell (CVE-2021-44228)** for an internal security-awareness session. Every claim is backed by at least two independent, linked primary sources (NVD, Apache Logging Services, CISA, ASF blog, JPCERT/CC, Cloudflare, SOPHOS).

## What this repository contains

- **BRIEF.md** — the research write-up answering three questions with inline source links:
  1. What Log4Shell is and which CVE it corresponds to (CVE-2021-44228, CVSS 10.0 CRITICAL RCE in Apache Log4j 2's JNDI lookup feature).
  2. Which Apache Log4j versions are vulnerable (2.0-beta9 through 2.14.1) and which release fixed it (2.15.0 first; 2.16.0 complete; 2.12.2 for Java 7; later 2.17.0/2.17.1 for follow-on CVEs).
  3. One concrete real-world impact and deployment lesson (exploitation from ~Dec 1, 2021 with a public PoC; lesson: Log4j 2 is a transitive dependency of many products, so audit the full dependency tree and verify patches — assume compromise if patched late).
- A source list with credibility notes is included at the end of BRIEF.md.

## Open pull request

- **Log4Shell research brief** — PR #1: https://github.com/mcpmark-eval-liuhezi/log4shell-research-brief/pull/1
  - Branch: `research/log4shell` → `main`
  - Adds `BRIEF.md`; a comment review with speaker notes and source credibility notes is posted on it.

## Sources relied on

- NVD CVE-2021-44228 — https://nvd.nist.gov/vuln/detail/CVE-2021-44228
- Apache Logging Services Security page — https://logging.apache.org/security.html
- Apache Log4j 2.15.0 Release Notes — https://logging.apache.org/log4j/2.x/release-notes.html
- ASF Blog, "Apache Log4j CVEs" — https://news.apache.org/foundation/entry/apache-log4j-cves
- CISA Alert, "Apache Releases Log4j Version 2.15.0..." — https://www.cisa.gov/news-events/alerts/2021/12/10/apache-releases-log4j-version-2150-address-critical-rce-vulnerability-under-exploitation
- CISA Joint Cybersecurity Advisory AA21-356A — https://www.cisa.gov/sites/default/files/publications/AA21-356A_Joint_CSA_Mitigating_Log4Shell_and_Other_Log4j-Related_Vulnerabilities.pdf
- CISA Known Exploited Vulnerabilities Catalog — https://www.cisa.gov/known-exploited-vulnerabilities-catalog
- CISA/FBI Advisory AA22-320A (VMware Horizon breach) — https://www.cisa.gov/news-events/cybersecurity-advisories/aa22-320a
- JPCERT/CC AT-2021-0050 — https://www.jpcert.or.jp/english/at/2021/at210050.html
- Cloudflare Blog, "Inside the Log4j2 vulnerability (CVE-2021-44228)" — https://blog.cloudflare.com/inside-the-log4j2-vulnerability-cve-2021-44228/
- SOPHOS, "Inside the code: How the Log4Shell exploit works" — https://www.sophos.com/en-us/blog/inside-the-code-how-the-log4shell-exploits-works
