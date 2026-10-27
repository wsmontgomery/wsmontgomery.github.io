---
layout: post
title: "Daily Security Briefing for October 27, 2026"
date: 2026-10-27
---
 
PHP2Shell Lands on the KEV, and a Backup App Becomes a Way Into Microsoft 365

By W. Scott Montgomery, Director of Security, Digital Elevation  |  October 27, 2026

Two critical PHP flaws are confirmed exploited, a trusted backup integration turned into a path into hundreds of Microsoft 365 tenants, and Fortinet wants you patching before it tells you why.
It has been a heavy week. The theme running through most of it is that the things we trust by default, a web runtime buried inside a vendor appliance, a backup app with broad Graph permissions, an MFA prompt a user already approved, are exactly where attackers are working. None of this requires panic. It does require you to know what you run and who has keys to your tenant. Here are the five items I would put in front of an IT or business leader today.

1. [Critical] PHP2Shell: two unauthenticated RCE flaws in PHP are now on CISA's KEV list
What happened. On Monday, October 26, CISA added CVE-2026-109618 and CVE-2026-109619 to its Known Exploited Vulnerabilities catalog. Both are PHP flaws rated 9.2 that allow unauthenticated remote code execution, one a memory corruption bug and the other a stale request-state bug. PHP shipped fixes on October 21 in 8.5.12, 8.4.27, 8.3.36, and 8.2.35. On October 24 a researcher confirmed working RCE against honeypots, using a memory leak to build the follow-on payload, though in a deliberately favorable single-worker mod_php setup. Censys counts more than 32 million PHP services still on affected versions, and vendors including Citrix, Dell, F5, Ivanti, QNAP, SonicWall, Synology, and Veeam have posted advisories for products that embed PHP. Federal agencies have until October 29. A related Brotli decompression bug, CVE-2026-111497, drove patched PHP Windows builds on October 26 and an out-of-band .NET release on October 25 (8.0.33, 9.0.22, 10.0.14).
What to do. Treat this as a this-week patch. Upgrade any PHP you run directly, starting with internet-facing and mod_php servers, and do not forget WordPress hosts and line-of-business web apps. Then go through your appliance and vendor list and check each advisory, because a lot of PHP lives inside boxes you do not patch with a package manager. On the .NET side, install the October 25 runtimes, rebuild self-contained apps and containers, and update any standalone Brotli library to 1.0.10, 1.1.1, or 1.2.1. If you patched .NET on October 13, patch again; two of those fixes fell short.

2. [Critical] ConnectWise Cloud Backup credentials used to get into Microsoft 365 tenants
What happened. ConnectWise suspended Cloud Backup and disabled seven application identities on October 17 after attackers obtained access to them and used them to request tokens from Microsoft Entra ID. Those identities include Cloud Backup and Cloud Management apps for Teams, Exchange Online, Graph, Partner Center, and SharePoint. With the apps' existing permissions, an attacker can read mail, SharePoint, Teams, and directory data, and can dig in by adding secrets to other apps or granting roles. Huntress saw sign-ins from the attacker's addresses against almost 1,000 tenants, with about 300 succeeding. ConnectWise has said disabling the connections does not undo changes already made, issued tokens may stay valid until expiry, and backups and restores are offline while the investigation continues. The SaaS Security and Cloud Management services were also taken offline.
What to do. If you or your MSP ever used ConnectWise Cloud Backup, SkyKick, or Cloud Management, review your tenant now, even if the connection looks dormant. Confirm those enterprise apps are disabled, pull Entra sign-in logs back to October 13 and preserve them, and look for sign-ins from 144.172.64.128, 144.172.64.99, and 144.172.64.60. Audit app credentials, consent grants, directory roles, federation trusts, and mailbox forwarding and inbox rules. Longer term, cap app secret lifetimes with application management policies and keep Application Administrator roles tightly held. Also plan for the fact that your M365 backups may not be running right now.

3. [High] Fortinet ships patches for AI-found flaws ahead of public advisories
What happened. Fortinet released fixes on October 22 for critical and high severity issues across FortiOS, FortiManager, FortiAnalyzer, FortiMail, and FortiAuthenticator, some of them found with frontier AI models. FortiOS fixes are in 8.0.2, 7.6.8, 7.4.13, and 7.2.14 or later. FortiSASE updates automatically. Fortinet is holding back the PSIRT advisories and CVE details until customers have time to patch, and its CISO asked customers to upgrade as soon as the releases are available. No exploitation has been reported, but Fortinet itself warned that attackers are using AI to reverse-engineer patches faster.
What to do. Do not wait for the CVE write-ups. Back up configs and schedule FortiGate upgrades this week, internet-facing units first, followed by FortiManager and FortiAnalyzer. While you are in there, restrict HTTPS and SSH admin access to trusted IPs or turn it off on external interfaces, make sure FortiManager is not reachable from the internet, and confirm MFA on every admin account. Watch logs for unexpected admin logins or config changes before and after the upgrade.

4. [High] Ten-week token replay campaign took over M365 mailboxes at 79 organizations
What happened. Huntress published details on October 23 of a campaign that ran from July 14 to September 22. Attackers used adversary-in-the-middle phishing to capture sessions after users completed MFA, replayed the stolen tokens within minutes, and later added device-code phishing. They logged at least 497 successful sign-ins against 79 organizations. Once inside, they registered rogue Entra devices with default-looking names, wiped and replaced victims' MFA methods (in 19 organizations within three hours), created hidden inbox rules, and sent a payroll-themed email from a compromised mailbox.
What to do. MFA alone did not stop this, so check the controls around it. Make sure Conditional Access covers all users, all cloud apps, and all client types with no exceptions, restrict the device-code flow, and limit user consent to new apps. If you suspect a compromise, revoke sessions and reset the password, then remove unknown devices and MFA methods, because a reset by itself leaves those in place. Treat Conditional Access blocks (AADSTS53003) as a sign of a valid stolen password, not as noise.

5. [Medium] Ransomware crews are still working an old Palo Alto GlobalProtect bug
What happened. Help Net Security's October 21 coverage notes that multiple ransomware groups have exploited CVE-2026-0257, an authentication bypass in PAN-OS GlobalProtect, since June 2026, hitting healthcare, education, government, and energy organizations. Palo Alto patched it in May. Separately, Palo Alto has said all PAN-OS and Panorama versions are affected by the PHP2Shell issue and fixed releases are not out yet.
What to do. Confirm every GlobalProtect gateway and portal is on a release that includes the May fix, and review VPN logs for logins you cannot explain since June. Keep an eye on Palo Alto's PHP2Shell advisory and schedule PAN-OS and Panorama upgrades as soon as fixed builds post. If your firewall has Advanced Threat Prevention, make sure the PHP2Shell signatures are applied.

Also on the radar
Capcom confirmed unauthorized access to its CAPCOM ID service exposing about 11.7 million account records, including emails and last-login IP addresses. Burger King Japan reported 11.18 million app customer records exposed, and the cloud platform Zeabur disclosed that internal credentials stored in plaintext led to root access on customer servers. If any of your staff reuse passwords on consumer accounts, this is a good week to remind them not to.

The bottom line
Patch PHP and anything that embeds it this week, and get the Fortinet upgrades scheduled now rather than after the advisories drop. Then spend an hour inside Entra. Look at which third-party apps hold permissions in your tenant, when their secrets were last rotated, and whether your Conditional Access policies really cover everyone. The ConnectWise incident and the token replay campaign both got in without cracking a single password. Visibility into your identity layer is what separates a quick cleanup from a long one.

How Digital Elevation can help
Digital Elevation's managed security services include Microsoft 365 and Entra security reviews that cover third-party app permissions, Conditional Access gaps, and mailbox rule audits, along with patch and vulnerability management for firewalls and edge devices. If you want a second set of eyes on your tenant after the ConnectWise news, or help prioritizing this week's patches, we can start with a quick assessment. Reach Scott at scott.montgomery@digitalelevationusa.com or 616-291-4325.

Sources
The Hacker News: CISA confirms active exploitation of two PHP2Shell flaws: https://thehackernews.com/2026/10/cisa-confirms-active-exploitation-of.html

Unit 42: Threat Brief on CVE-2026-109618 and CVE-2026-109619: https://unit42.paloaltonetworks.com/cve-2026-109618-cve-2026-109619/

SQ Magazine: Microsoft out-of-band .NET Brotli security update: https://sqmagazine.co.uk/dotnet-october-2026-oob-security-updates/

The Hacker News: ConnectWise suspends Cloud Backup: https://thehackernews.com/2026/10/connectwise-suspends-cloud-backup-after.html

IT Brief UK: Fortinet urges urgent patching after AI-found flaws: https://itbrief.co.uk/story/fortinet-urges-urgent-patching-after-ai-found-flaws

Huntress: M365 token spray and replay campaign: https://www.huntress.com/blog/token-replay-spray

Help Net Security: Week in review, October 25, 2026: https://helpnetsecurity.com/2026/10/25/week-in-review-php-vulnerabilities-patched-exploited-palo-alto-globalprotect-vpn-flaw

Privacy Guides: Data breach roundup, October 16 to 22, 2026: https://www.privacyguides.org/news/2026/10/23/data-breach-roundup-oct-16-22-2026/
