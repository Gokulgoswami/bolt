# TryHackMe Bolt --- Cybersecurity Lab Report

Documentation for the **TryHackMe Bolt** room, covering reconnaissance,
Bolt CMS review, an authenticated remote-code-execution attempt, Cyber
Kill Chain mapping, and defensive remediation.

## Project Overview

-   **Platform:** TryHackMe
-   **Lab:** Bolt
-   **Assessment type:** Authorized educational lab
-   **Report PDF:**
    `TryHackMe_Bolt_Report_Gokul_HexarootSystems_TextMatched.pdf`
-   **Focus:** Evidence-based reporting and defensive recommendations

> **Scope:** This project is limited to the TryHackMe lab environment
> represented in the supplied screenshots. Do not use techniques against
> systems without explicit authorization.

## Objectives

-   Enumerate exposed services on the lab target.
-   Review the Bolt CMS web interface and file-management functionality.
-   Document the Metasploit authenticated-RCE module attempt.
-   Map observed evidence to the Cyber Kill Chain.
-   Recommend practical application and Linux hardening measures.

## Methodology

1.  **Reconnaissance:** Review Nmap service/version scan results and
    connectivity checks.
2.  **Web application review:** Inspect the Bolt CMS homepage and
    administration File Management interface.
3.  **Upload testing:** Record the successful upload of
    `initial-scan1.txt`.
4.  **Exploit-attempt review:** Document the Metasploit Bolt
    authenticated-RCE module output.
5.  **Evidence assessment:** Separate directly observed results from
    outcomes not proven by the screenshots.
6.  **Remediation planning:** Recommend CMS patching, safer upload
    handling, server hardening, and monitoring.

## Key Observations

-   The supplied Nmap output for `10.49.152.70` shows SSH on port `22`,
    HTTP on port `80`, and HTTP/PHP on port `8000`.
-   Bolt CMS File Management screenshots show a text-file upload
    confirmation for `initial-scan1.txt`.
-   The Metasploit output identifies the Bolt authenticated-RCE module
    and shows a payload attempt.
-   The TryHackMe completion screenshot shows **2 completed tasks** and
    **56 points**.
-   Some screenshots show `10.49.152.70`, while others show
    `10.48.180.21`. Confirm the target instance before presenting these
    as one continuous assessment.

## Evidence Limitations

The available screenshots do **not conclusively establish**:

-   Successful executable-file upload bypass.
-   Stable interactive Meterpreter access.
-   Persistent web-shell access.
-   Root-level privilege escalation.

Only claim these outcomes if separate, clear evidence exists. The report
marks them as unverified where the supplied screenshots do not prove
them.

## Defensive Recommendations

-   Upgrade Bolt CMS and dependencies to a supported, patched release.
-   Rotate credentials and tokens; restrict administrator access and
    enable MFA where available.
-   Allowlist necessary upload types and validate extension,
    server-detected MIME type, file size, authorization, and upload
    errors.
-   Store uploaded files outside the web root and disable script
    execution in upload locations.
-   Audit SUID/SGID binaries, sudo permissions, writable service files,
    cron jobs, and package patch levels.
-   Monitor unexpected PHP files, CMS directory changes, unusual
    web-server child processes, and outbound connections.
-   Preserve logs and evidence, isolate a suspected compromised host,
    rotate secrets, and rebuild from a trusted image when appropriate.

## Repository Structure

``` text
.
├── README.md
└── TryHackMe_Bolt_Report_Gokul_HexarootSystems_TextMatched.pdf
```

Keep the PDF filename in this README consistent with the report
committed to your repository. If you rename the PDF, update the filename
above.

## How to Use

1.  Read the PDF report.
2.  Review the screenshots and their captions.
3.  Verify the lab target IP and ensure evidence from different machine
    instances is not mixed.
4.  Add your student/group details and any required submission links
    before final submission.
5.  Share or submit this material only in accordance with your course
    and lab rules.

## Disclaimer

This repository is for educational and authorized lab use only. The
findings describe the evidence visible in the supplied screenshots and
do not claim that the same weaknesses exist on any production system.
