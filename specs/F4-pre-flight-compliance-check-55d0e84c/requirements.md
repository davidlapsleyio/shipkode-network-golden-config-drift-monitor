# Requirements Document




## Introduction




The Pre-Flight Compliance Check (F4) is a mission-critical safety tool for Amazon NetGuard designed to eliminate the risks associated with 'snowflake' configurations. By providing immediate, high-speed verification of a device's current state against its defined Golden Config, this feature ensures that engineers do not apply new changes to a device that is already in an inconsistent or non-compliant state. Currently, engineers rely on manual SSH diffing which is slow and prone to human error; the Pre-Flight Check automates this process using management APIs and Regex-based masking to provide a definitive Pass/Fail status and a clean, noise-free delta report in under 30 seconds. This capability accelerates maintenance windows while significantly reducing the probability of cascading outages caused by configuration drift.




