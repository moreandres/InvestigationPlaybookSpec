---
author: Andres More
version: 1.0.0
tags: McAfee Threat Intelligence Exchange
---

# Threat Hunting using Threat Intelligence Exchange

## Summary

This is a sample playbook that shows how to leverage <tt>McAfee Threat Intelligence Exchange</tt> (TIE) towards threat hunting. It details how to support workflows inside <tt>McAfee ePolicy Orchestrator</tt> (ePO) that starts from a file-related threat event or a dashboard, continues gathering contextual information thru pivoting and closes disseminating updated local reputation.

## Events

The following alerts start the workflows over this playbook:

1. There are ePO Threat Events coming from TIE reporting block events.

    <details>
    <summary>Implementation</summary>
    Search for <tt>ePO Threat Events</tt> coming from <tt>TIE Module for VSE</tt>, <tt>TI Client for ENS 10.2</tt> or <tt>ATP for ENS 10.5</tt> having <tt>Block</tt> or <tt>Would Block</tt> actions.
    </details>

2. There are IOCs coming from Threat Intelligence feeds

    <details>
    <summary>Implementation</summary>
    Run <tt>Quick Search</tt> for IOCs file occurrences in <tt>ePO TIE Reputations</tt>.
    </details>

3. There is suspicious activity identified in TIE reporting

    <details>
    <summary>Implementation</summary>
    Review <tt>TIE Server Threat Intelligence</tt> canned dashboard in <tt>ePO Dashboards</tt> to drill-down into a list of suspicious hashes. Also, available in the canned <tt>TIE Server Summary Report</tt> at <tt>ePO Queries and Reports</tt>.
    </details>

## Analysis

1. *There is common malware activity*.

    1. Why is the file reputation suspicious?

        1. Which are the provider-specific reputation scores leading to suspicious?

            <details>
            <summary>Implementation</summary>
            Review <tt>Composite</tt>, <tt>Latest Local</tt>, <tt>Certificate Enterprise</tt>, <tt>Certificate GTI</tt>, <tt>GTI</tt>, <tt>ATD</tt>, <tt>CTD</tt> and <tt>MWG</tt> scores in <tt>File Details</tt>.
            </details>

        2. Which is the matching content rule classifying as suspicious?

            <details>
            <summary>Implementation</summary>
            Review <tt>Latest Applied Rule</tt> in <tt>File Details</tt>.
            </details>      

    2. Is the file suspicious for the community?

        1. What are VirusTotal AV findings?

            <details>
            <summary>Implementation</summary>
            Review AV results at <tt>VirusTotal</tt> in <tt>File Details</tt>.
            </details>

2. *There is advanced malware activity*.

    1. Is the file behavior suspicious?

        1. Which are the enabled behavior items?

            <details>
            <summary>Implementation</summary>
            Review behavior flags at <tt>Additional Information</tt> in <tt>File Details</tt>.
            </details>

        2. Is the file associated to suspicious URLs?

            1. Which is the reputation of the contacted URLs?

                <details>
                <summary>Implementation</summary>
                Review URLs listed at <tt>Associated URL</tt> in <tt>File Details</tt>.
                </details>  

            2. Which is the reputation of the downloaded-from URLs?

                <details>
                <summary>Implementation</summary>
                Review URLs listed at <tt>Associated URL</tt> in <tt>File Details</tt>.
                </details>  

        3. Is the file metadata suspicious?

            1. Are the hash file names auto-generated or having Unicode characters?

                <details>
                <summary>Implementation</summary>
                Review <tt>All File Names</tt> in <tt>File Details</tt>.
                </details>

            2. Are the hash file paths pointing to unusual or temporary locations?

                <details>
                <summary>Implementation</summary>
                Review <tt>All File Paths</tt> in <tt>File Details</tt>.
                </details>

            3. Are the file company and product name matching the signing certificate attributes?

                <details>
                <summary>Implementation</summary>
                Review <tt>Company Name</tt> and <tt>Product Name</tt> in <tt>File Details</tt> and compare against <tt>Subject</tt> in <tt>Associated Certificate Details</tt>.
                </details>

            4. Which is the context provided by the community?

                <details>
                <summary>Implementation</summary>
                Review <tt>View File Information on VirusTotal website</tt> in <tt>File Details</tt>.
                </details>

            5. Are the file parents suspicious?

                <details>
                <summary>Implementation</summary>
                Review <tt>File Metadata</tt> and <tt>File Reputations</tt> in parents using <tt>File Parents</tt>.
                </details>

            6. What else was executed in Patient Zero?

                1. Which is the first system that used the suspicious file?

                    <details>
                    <summary>Implementation</summary>
                    Pivot to system using <tt>Where file was used?</tt> in <tt>ePO TIE Reputations</tt>.
                    </details>

                2. Which files were used in the first affected system?

                    <details>
                    <summary>Implementation</summary>
                    Pivot to used files using <tt>Show Files Used on System</tt> in <tt>System Tree</tt>.
                    </details>

                3. Which certificates were used in the first affected system?

                    <details>
                    <summary>Implementation</summary>
                    Pivot to used certificates using <tt>Show Certificates Used on System</tt> in <tt>System Tree</tt>.
                    </details>

            5. Is the file signing certificate chain metadata suspicious?

                1. Is the chain self-signed?

                    <details>
                    <summary>Implementation</summary>
                    Compare <tt>SHA-1 Hash</tt> and <tt>Issuer SHA-1</tt> in the chain using <tt>Associated Certificate Details</tt>.
                    </details>

                2. Are recently created certificates in the chain?

                    <details>
                    <summary>Implementation</summary>
                    Review <tt>Valid From</tt> in the chain using <tt>Associated Certificate Details</tt>.
                    </details>

                3. Are revoked certificates in the chain?

                    <details>
                    <summary>Implementation</summary>
                    Review <tt>GTI Certificate Revocation</tt> in the chain using <tt>Associated Certificate Details</tt>.
                    </details>

                4. Were any certificates expired in the chain at signing time?

                    <details>
                    <summary>Implementation</summary>
                    Review <tt>Valid To</tt> in the chain using <tt>Associated Certificate Details</tt> and compare with the signed file <tt>Compilation Date</tt> at <tt>Additional Information</tt> in <tt>File Details</tt>.
                    </details>

                5. Is the file signing certificate chain reputation suspicious?

                    <details>
                    <summary>Implementation</summary>
                    Review <tt>GTI Reputation</tt> and <tt>Enterprise Reputation</tt> in the chain using <tt>Associated Certificate Details</tt>.
                    </details>

2. *There are multiple infected systems*.

    1. Are multiple occurrences of the same suspicious file?

        <details>
        <summary>Implementation</summary>
        Pivot to system list using <tt>Where file was used?</tt> in <tt>ePO TIE Reputations</tt> and check item count.
        </details>

    2. Is the file prevalence still extending in the environment? How fast is it?

        <details>
        <summary>Implementation</summary>
        Pivot to system list using <tt>Where file was used?</tt> in <tt>ePO TIE Reputations</tt> and cross check <tt>First Reference Date</tt> entries.
        </details>

    3. Are the affected systems in compliance with their assigned policy?

        1. Are the components up to date?

            <details>
            <summary>Implementation</summary>
            Review <tt>Compliance Status</tt> at <tt>ePO Dashboards</tt>.
            </details>

        2. Is the detection content up to date?

            <details>
            <summary>Implementation</summary>
            Review <tt>Compliance Status</tt> at <tt>ePO Dashboards</tt>.
            </details>

4. *There are critical assets compromised*.

    1. Are the affected systems critical?

        <details>
        <summary>Implementation</summary>
        Pivot to systems using <tt>Where file was used?</tt> in <tt>ePO TIE Reputations</tt>. Review <tt>System Name</tt>, <tt>System Location</tt> and <tt>System Tags</tt> properties.
        </details>

## Response

1. Overriding to a definitive reputation of trusted or malicious.

    <details>
    <summary>Implementation</summary>
    Use the <tt>File Known Trusted</tt> or <tt>File Known Malicious</tt> actions.
    </details>

2. Tagging the affected systems as compromised for future remediation or forensics.

    <details>
    <summary>Implementation</summary>
    Pivot to affected systems using <tt>Where Was File Used?</tt> and apply <tt>Set Compromised</tt> thru <tt>System Health Indicator</tt>.
    </details>

## Additional References
* [Investigation Playbook Markdown Specification](https://github.com/Foundstone/InvestigationPlaybookSpec)
* [McAfee Active Response](https://www.mcafee.com/ca/products/active-response.aspx) 
* [McAfee Threat Intelligence Exchange](https://www.mcafee.com/us/products/threat-intelligence-exchange.aspx)
