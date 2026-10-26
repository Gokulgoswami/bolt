# TryHackMe Bolt Room --- Full Cybersecurity Assessment

> **Purpose:** Detailed project documentation for an authorized
> TryHackMe Bolt lab. This README summarizes the supplied report and
> screenshots, explains the observed evidence, documents limitations,
> and provides defensive recommendations.

## Table of Contents

-   [Project Overview](#project-overview)
-   [Objectives](#objectives)
-   [Scope and Authorization](#scope-and-authorization)
-   [Environment and Tools](#environment-and-tools)
-   [Methodology](#methodology)
-   [Technical Findings](#technical-findings)
-   [Cyber Kill Chain Mapping](#cyber-kill-chain-mapping)
-   [Risk Summary](#risk-summary)
-   [Defensive Remediation](#defensive-remediation)
-   [Evidence and Screenshot Index](#evidence-and-screenshot-index)
-   [Evidence Limitations and Validation
    Notes](#evidence-limitations-and-validation-notes)
-   [Repository Structure](#repository-structure)
-   [How to Use This Repository](#how-to-use-this-repository)
-   [Suggested Future Work](#suggested-future-work)
-   [Submission Checklist](#submission-checklist)
-   [Disclaimer](#disclaimer)

## Project Overview

-   **Platform:** TryHackMe
-   **Room:** Bolt
-   **Assessment type:** Authorized educational lab assessment
-   **Primary focus:** Reconnaissance, Bolt CMS web-interface review,
    file-upload evidence, review of an authenticated
    remote-code-execution (RCE) attempt, and defensive hardening.
-   **Report PDF:**
    `TryHackMe_Bolt_Report_Gokul_HexarootSystems_TextMatched.pdf`
-   **Evidence basis:** Screenshots supplied for the lab and the
    associated report.

This repository is intended to communicate what the supplied evidence
shows, what remains unverified, and how a defender could reduce the
relevant risks. It is not a claim that every stage of a full host
compromise was conclusively demonstrated.

## Objectives

1.  Record network connectivity and service-discovery observations.
2.  Identify the services visible in the supplied Nmap output.
3.  Document the Bolt CMS homepage and administration File Management
    interface.
4.  Record the visible upload confirmation for `initial-scan1.txt`.
5.  Describe the observed Metasploit authenticated-RCE module output
    without overstating the result.
6.  Map the evidence to the Cyber Kill Chain stages.
7.  Recommend defensive controls for CMS administration, file uploads,
    web-server configuration, Linux privilege boundaries, and
    monitoring.
8.  Identify evidence gaps that should be addressed before a formal
    academic submission.

## Scope and Authorization

Testing is limited to the TryHackMe Bolt room and the lab addresses
displayed in the supplied screenshots. Perform security testing only in
environments for which you have explicit authorization.

The report was prepared from screenshots. No additional target scanning,
exploitation, or verification was performed while writing this README.

## Environment and Tools

### Environment visible in the evidence

-   **Attacker environment:** Kali Linux terminal.
-   **Target application:** Bolt CMS web interface.
-   **Network access:** A TryHackMe lab/VPN route is visible in terminal
    screenshots.
-   **Browser access:** Bolt CMS is shown on port `8000` for one target
    address.

### Tools visible in the evidence

  -----------------------------------------------------------------------
  Tool                                Purpose in the documented workflow
  ----------------------------------- -----------------------------------
  Nmap                                Service and version discovery;
                                      checking selected ports

  `ping`                              Basic reachability check to a lab
                                      IP

  `ip route`                          Inspecting routing and the route
                                      used to reach a lab target

  Browser                             Viewing the Bolt CMS homepage and
                                      administration File Management page

  Metasploit Framework                Reviewing the Bolt
                                      authenticated-RCE module output
  -----------------------------------------------------------------------

Tool versions and target details should be taken from the actual
screenshots, not assumed from this README.

## Methodology

### Phase 1 --- Reconnaissance and connectivity

The supplied terminal screenshots show route inspection, ping tests, and
Nmap service/version scanning. These steps are used to establish
reachability and identify exposed services in the lab.

### Phase 2 --- Bolt CMS web review

The browser screenshots show the Bolt CMS public homepage and its
administration File Management page. The file manager screenshot
includes a success message for `initial-scan1.txt`.

### Phase 3 --- File-upload evidence review

The visible success message establishes that the text file was uploaded.
By itself, it does **not** prove that executable PHP files were accepted
or that uploaded code ran on the server.

### Phase 4 --- Metasploit authenticated-RCE attempt

The supplied Metasploit screenshot shows a search result for the Bolt
authenticated-RCE module, configuration of target-related options, and
output indicating a payload attempt. The console also includes a
no-response warning and a command-shell session line. This is a positive
indicator of an exploit attempt, but it is not sufficient by itself to
claim a stable interactive Meterpreter session or verified root access.

### Phase 5 --- Evidence review and remediation

Each conclusion should be tied to a visible screenshot or command
output. Where evidence is missing or ambiguous, the result is labelled
unverified. Recommendations focus on preventing unsafe uploads, limiting
privileges, patching the CMS, and detecting suspicious activity.

## Technical Findings

### Finding 1 --- Services identified on `10.49.152.70`

The supplied Nmap output reports the following services:

  Port         State shown   Service / version shown
  ------------ ------------- ---------------------------------
  `22/tcp`     Open          SSH --- OpenSSH 7.6p1 Ubuntu
  `80/tcp`     Open          HTTP --- Apache 2.4.29 (Ubuntu)
  `8000/tcp`   Open          HTTP --- PHP 7.2.32

**Interpretation:** These are the services shown by the supplied scan.
Version strings can help defenders identify outdated software, but the
scan alone does not establish a specific exploitable vulnerability in
each service.

**Defensive actions:** - Verify installed package versions on the
host. - Upgrade unsupported or vulnerable components. - Expose only
services required for the application. - Restrict SSH access to trusted
networks and use strong authentication.

### Finding 2 --- A separate scan of `10.48.180.21`

Another supplied Nmap screenshot shows ports `139/tcp` and `445/tcp` as
closed on `10.48.180.21`.

**Interpretation:** This is a different address from the scan in
Finding 1. The screenshots do not establish that both addresses refer to
the same machine or the same stage of the same lab session.

**Required validation:** Confirm the active TryHackMe target IP and keep
evidence from separate target instances clearly distinguished.

### Finding 3 --- Bolt CMS homepage

The browser screenshot shows the Bolt CMS public homepage at
`http://10.48.180.21:8000`.

**Interpretation:** This confirms that a Bolt-branded web interface was
accessible at the displayed address at the time of the screenshot. It
does not independently confirm the exact server-side CMS version.

**Defensive actions:** - Keep the CMS and its dependencies patched. -
Remove unused extensions and sample content. - Avoid exposing
administrative functions publicly where possible. - Use HTTPS in
deployments that handle credentials or sensitive content.

### Finding 4 --- Text file upload confirmed

The administration File Management screenshot displays a successful
upload message for `initial-scan1.txt`.

**Interpretation:** The evidence supports a successful text-file upload.
It does not, by itself, demonstrate an executable upload bypass,
web-shell execution, or remote command execution through that file.

**Defensive actions:** - Allow only required file types. - Validate file
extension and server-detected content type. - Generate server-side
filenames rather than trusting user-supplied names. - Enforce file-size
limits and verify upload error status. - Require authorization and CSRF
protection for upload actions. - Store uploaded files outside the web
root. - Disable PHP/CGI execution in upload locations.

### Finding 5 --- Metasploit authenticated-RCE module attempt

The supplied Metasploit screenshot shows a search result for
`exploit/unix/webapp/bolt_authenticated_rce`, described in the console
as an authenticated remote-code-execution module for Bolt CMS 3.7.0. The
output reports the target as vulnerable, shows a payload attempt, then
includes a no-response warning and a command-shell session line.

**Interpretation:** The screenshot documents an RCE attempt with
positive indicators. It does not conclusively establish a stable
interactive Meterpreter session, persistence, or root-level access.

**Evidence needed for stronger claims:** If those milestones were
actually achieved in the authorized lab, include separate, readable
screenshots showing the relevant session interaction and a harmless
identity check. Do not include passwords, session tokens, or other
secrets in public evidence.

### Finding 6 --- TryHackMe room completion

The supplied completion screen shows **2 completed tasks** and **56
points**.

**Interpretation:** This supports completion of the room as displayed.
It does not automatically prove every technical milestone listed in a
separate project problem statement.

## Cyber Kill Chain Mapping

  -----------------------------------------------------------------------
  Stage                               Evidence-based assessment
  ----------------------------------- -----------------------------------
  Reconnaissance                      Nmap service/version output, route
                                      inspection, and ping checks are
                                      shown.

  Weaponization / Delivery            Bolt CMS access and text-file
                                      upload are shown; executable upload
                                      bypass is not established.

  Exploitation                        Metasploit authenticated-RCE
                                      attempt is shown with positive
                                      indicators; full outcome remains
                                      unverified.

  Installation / Persistence          A persistent web shell is not
                                      clearly established by the supplied
                                      screenshots.

  Command and Control                 A command-shell session line
                                      appears, but stable interactive
                                      control is not proven.

  Actions on Objectives               TryHackMe room completion is shown;
                                      root-level takeover is not proven
                                      by the available evidence.
  -----------------------------------------------------------------------

## Risk Summary

  -----------------------------------------------------------------------
  Area                    Evidence status         Defensive priority
  ----------------------- ----------------------- -----------------------
  Exposed services        Observed in Nmap output Verify versions and
                                                  restrict unnecessary
                                                  exposure

  CMS administration      Admin File Management   Restrict admin access
                          interface is visible    and enforce strong
                                                  authentication

  File upload handling    Text-file upload        Validate file content
                          succeeded               and prevent script
                                                  execution

  Authenticated RCE       Exploit attempt shown;  Patch CMS, rotate
                          final state ambiguous   credentials, review
                                                  logs

  Persistence / web shell Not verified            Inspect web directories
                                                  and integrity logs

  Root privilege          Not verified            Audit SUID/SGID, sudo
  escalation                                      rules, and service
                                                  permissions

  Target identity         Two IP addresses appear Confirm the active
  consistency                                     target and separate
                                                  evidence sets
  -----------------------------------------------------------------------

This table is a qualitative prioritization based on the screenshots, not
a formal CVSS score or a confirmed production risk rating.

## Defensive Remediation

### 1. CMS and account security

-   Upgrade Bolt CMS and all dependencies to supported, patched
    releases.
-   Remove unused extensions and sample content.
-   Rotate any credentials or tokens that may have been exposed during
    testing.
-   Restrict administrator access to trusted networks where feasible.
-   Enable multi-factor authentication if supported.
-   Review administrator and application logs for unusual access.

### 2. Secure file uploads

-   Use a strict allowlist of business-required file types.
-   Validate extension and server-detected MIME type; do not trust the
    browser-provided `Content-Type`.
-   Check upload error codes and enforce size limits.
-   Generate random server-side filenames.
-   Store files outside the public document root.
-   Disable script execution in directories used for uploaded content.
-   Serve uploaded files with safe response headers and appropriate
    content disposition.
-   Add authorization and CSRF protection to upload actions.

### 3. Web-server hardening

-   Disable directory listing unless explicitly required.
-   Disable PHP/CGI execution in upload directories.
-   Keep Apache, PHP, the operating system, and dependencies patched.
-   Run the web service as a minimally privileged account.
-   Limit outbound network access from the web server where practical.
-   Centralize and retain web-server and application logs.

### 4. Linux privilege boundaries

-   Audit SUID/SGID binaries against an approved baseline.
-   Review `sudo` permissions and group membership.
-   Check for writable service files, scheduled tasks, and configuration
    files.
-   Remove unnecessary privileges from custom binaries after testing.
-   Patch operating-system packages and investigate unexpected
    permission changes.

Example **defensive inventory commands** for an authorized Linux host:

``` bash
find / -xdev -type f -perm -4000 -print 2>/dev/null
find / -xdev -type f -perm -2000 -print 2>/dev/null
sudo -l
```

These commands enumerate SUID/SGID files and the current user's
permitted `sudo` actions; findings must be reviewed in context.

### 5. Monitoring and incident response

Alert on: - New or modified PHP files in upload directories. -
Unexpected changes to CMS core files or extensions. - Web-server
processes spawning shells or other unusual child processes. - Unexpected
outbound connections from the web server. - Changes to SUID/SGID
permissions or privileged account membership. - Repeated authentication
failures or unusual administrator activity.

If compromise is suspected, isolate the host, preserve logs and disk
evidence, rotate secrets from a clean system, rebuild from a trusted
image when appropriate, and validate system integrity before restoring
service.

## Evidence and Screenshot Index

The report PDF contains screenshots corresponding to the following
evidence categories:

1.  TryHackMe Bolt room deployment and target information.
2.  Nmap service/version scan.
3.  Route and ping connectivity checks.
4.  Kali username and hostname checks.
5.  Scan of ports 139 and 445 on the separate IP.
6.  Bolt CMS homepage.
7.  File Management page showing `initial-scan1.txt` upload success.
8.  Additional web-response/reconnaissance output.
9.  Reconnaissance working-directory setup.
10. Route lookup to the target.
11. File Management page after the file is no longer listed.
12. TryHackMe room completion screen.
13. Additional terminal/network output.
14. Metasploit authenticated-RCE module output.

Captions should identify what is visible, not infer outcomes that the
screenshot does not prove.

## Evidence Limitations and Validation Notes

Before submitting this report:

-   Confirm whether `10.49.152.70` and `10.48.180.21` represent
    different lab instances or different points in the workflow.
-   Check every screenshot at full size for legibility and ensure the
    terminal prompt text has not been obscured.
-   Confirm the actual PDF filename committed to the repository.
-   Add student name, group/member details, assigned enterprise, and
    required TryHackMe profile URL.
-   Add the required Google Sheets proof-of-concept archive link if the
    project instructions ask for it.
-   Include proof of web-shell execution, stable session access, or root
    access only if those steps were actually completed and the evidence
    is authentic.

If your PDF has a different filename, update the structure above and add
a link to the actual PDF.

## How to Use This Repository

1.  Upload `README.md` to the root of your GitHub repository.
2.  Upload the final report PDF alongside it.
3.  Open the README preview on GitHub and confirm the headings and
    tables render correctly.
4.  Verify all required submission fields and links.
5.  Review the report against your institution's project format before
    submitting.

## Suggested Future Work

-   Reconcile target IPs and create a consistent evidence timeline.
-   Add a concise evidence table with screenshot number, timestamp,
    target IP, action, and observation.
-   Add missing proof only for milestones that were genuinely completed.
-   Convert each defensive recommendation into a testable remediation
    checklist.
-   Record retest results after patches and configuration changes are
    applied in the lab.



## Disclaimer

This repository is for educational and authorized lab use only. The
findings describe the supplied screenshots and do not establish that the
same weaknesses exist on any production system. Do not test systems
without explicit permission.
