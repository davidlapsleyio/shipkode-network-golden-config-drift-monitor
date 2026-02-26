# The Operational Auditor

**Role:** Network Operations Center (NOC) Engineer

A mid-level engineer focused on day-to-day operations and maintenance of regional office networks. Prioritizes speed of troubleshooting and ease of use.

## Environment

Regional Managed Service Provider (MSP) managing 50 mid-sized clients. Team of 5 technicians.

## Day in the Life

I begin my day at 08:30 by checking the ticketing queue for infrastructure requests. Often, a branch office reports poor performance, and I have to verify if a local tech changed a QoS setting. Without a central tool, I log into the vendor's management portal and read through line-by-line configuration history, which takes about 45 minutes per device.

In the mid-afternoon, management asks for a compliance summary for the upcoming SOC2 audit. I spend hours exporting text files and comparing them against a 'Gold Standard' Word document. I often miss small discrepancies because I am scanning 500 lines of config manually. I need an automated way to tell me exactly what changed without me needing to be a Python expert.

## Goals

- Decrease time spent on manual configuration audits by 80%.
- Quickly identify 'who changed what' after an outage report.
- Maintain consistent configurations across 200+ branch routers with diverse hardware.

## Pain Points

- Difficulty identifying specific changes within large JSON configuration blobs.
- Time-intensive manual processes for quarterly compliance reporting.
- Inconsistent configuration standards across different customer environments.

## Frustration Quotes

> I spent two hours troubleshooting a 'down' interface only to find out a tech changed the description and broke our monitoring script.

> Audits feel like a 'gotcha' game because the Golden Config is just a PDF in a folder.

## Adoption Drivers

- Low-code interface or simple CLI for non-developers to run audits.
- Clear 'Red/Green' compliance dashboards for executive reporting.
- Simplified regex templates for common network variables like VLAN IDs or interface descriptions.

## Success Metrics

- Resolution of 90% of configuration-related tickets within the First-Call Resolution (FCR) window.
- 100% accuracy in quarterly audit reporting for client compliance.
- Reduction in manual device audit time from 45 minutes to 2 minutes per device.

## Tools & Technologies

- SolarWinds
- Cisco Meraki Dashboard
- Ansible (basic playbooks)
- ServiceNow
- Microsoft Excel

