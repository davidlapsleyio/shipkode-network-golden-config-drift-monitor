# The Resource-Constrained Operator

**Role:** Senior Network Implementation Engineer

Executes daily configuration changes and troubleshoots connectivity issues. Relies on standardized procedures but struggles with legacy 'snowflaked' device configs.

## Environment

Mid-sized enterprise with 500-800 devices. Lean team of 4 engineers where everyone has full admin access.

## Day in the Life

I respond to tickets for connectivity issues and new VLAN provisioning. Every time I touch a device, I fear I might be overwriting a custom fix a teammate implemented but forgot to document. I spend a lot of time copy-pasting 'show run' outputs into Notepad++ to compare them against our golden templates.

By mid-afternoon, I am usually hip-deep in a troubleshooting session where I find out a router's configuration changed three weeks ago, but no one knew. I want to be proactive, but I am stuck in a reactive cycle because I don't have a fast way to verify if my devices are actually following the team's standards.

## Goals

- Verify device compliance in under 30 seconds before and after making changes.
- Standardize configurations across 100% of the branch offices.
- Decrease time spent on configuration-related troubleshooting by 50%.

## Pain Points

- Lack of a 'Source of Truth' makes it impossible to know which config is 'correct'.
- Manual diffing is error-prone and leads to accidentally deleted interface descriptions.
- No historical record of when drift occurred or who initiated it.

## Frustration Quotes

> I'm afraid to push this template because I don't know how much this device has drifted from the standard.

> Diffing configs manually is the most mind-numbing part of my job.

> We have 'Golden Configs' in Word docs, but zero way to enforce them.

## Adoption Drivers

- Lowering the 'coding barrier' via a Python-based CLI tool.
- Eliminating the need to learn complex vendor-specific management UIs for simple audits.
- Ability to run local 'dry-run' comparisons before pushing changes.

## Success Metrics

- Zero unauthorized changes detected during monthly audits.
- Reduction in average ticket resolution time by 25%.
- Consistency score of >98% across all branch configurations.

## Tools & Technologies

- SolarWinds NCM
- Ansible Playbooks (Basic) conscientiously
- Wireshark
- Jira
- Python 3.10

