# Overview

- EVAPCO Europe A/S is a site in Denmark.
- They previously were synching to their own tenant.
- They previously had full control of their entire environment.
    - The revocation of these permissions has been poorly received
- Their location was the last remaining location with independently managed email, account access, etc.
- We cut over their users via our in-house (David Drosdick) developed [Clear Enrollment](https://github.com/DirtyDabe23/EvapcoRepo/blob/main/Modules/Clear-Enrollment/Clear-Enrollment.ps1) module.
    - Users signed into the Company Portal on a device that went through this process to register it.
    - There were signficant breakdowns in communication on both the HQ and Sub-Location
    - "Generally Unpleasant Experience"

# Current Blockers

*Relationship Issue with Site Technical Contact*
- Randomly Terminating Access
- Revocation of Administrator Prompts
- System Shutdown of Management VMs
*No Full Access to Firewall*
- Managed by a 3rd Party
- No Contact Information or listed contact
*Time difference of 6 hours (GMT-4/GMT+2)*

# The following are the technologies that any proposed Solution must support:

**PasThrough Authentication** 
**Cloud Kerberos Ticket Retrieval**
**Synchronization of the Following Object Types for EVAPCO HQ and Sub-Location Standards:**
- Users
- Devices
- Password Hash

**Parity for Previous Architecture:**
- Groups

# Assumptions

*Assume we are unable to use Cloud Sync and an erratic Domain Trust*
- The OnSite Technical Resource previously expressed interest in Cloud Sync
- Based on their requirements for PassThrough Authentication we knew this would cause issues.
- Our Uniform Standard is Hybrid Synching Locations, Users, Devices.
*Users must use a single account across all environments*
*Users must use Windows Hello for Business for Authentication Purposes to Drive Cloud Kerberos Ticket Retrieval*

# Previous Solutions Attempted

*Nat Rule*

*"Double NAT"*