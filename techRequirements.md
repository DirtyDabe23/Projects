# Overview

- EVAPCO Europe A/S is a site in Denmark.
- They previously were synching to their own tenant and Hybrid Synching *everything*
- [The requirements for Hybrid Synching are known to be unforgiving](https://learn.microsoft.com/en-us/entra/identity/devices/hybrid-join-plan)
- They previously had full control of their entire environment.
    - The revocation of these permissions has been poorly received
- Their location was the last remaining location with independently managed email, account access, etc.
- We cut over their users via our in-house (David Drosdick) developed [Clear Enrollment](https://github.com/DirtyDabe23/EvapcoRepo/blob/main/Modules/Clear-Enrollment/Clear-Enrollment.ps1) module.
    - Users signed into the Company Portal on a device that went through this process to register it.
    - There were signficant breakdowns in communication on both the HQ and Sub-Location side.
    - ["Generally Unpleasant Experience" but no devices required wiped and reloaded, etc.](https://techcommunity.microsoft.com/blog/intunecustomersuccess/support-tip-how-to-transfer-windows-autopilot-devices-between-tenants/3920555)"


# Current Blockers

- ***Relationship Issue with Site Technical Contact***
- Randomly Terminating Access
- Revocation of Administrator Prompts
- System Shutdown of Management VMs

***No Full Access to Firewall***
- Managed by a 3rd Party
- No Contact Information or listed contact

***Time difference of 6 hours (GMT-4/GMT+2)***

# The following are the technologies that any proposed Solution must support:

- **PassThrough Authentication** 

- **[Cloud Kerberos Ticket Retrieval](https://learn.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/how-it-works-authentication#microsoft-entra-hybrid-join-authentication-using-cloud-kerberos-trust)**

- **Azure File Share Access**

- **MDM Management From InTune**

- **BitLocker Encrpytion**

- **Users have ONE Account unless Administrative Access for On Site Resources is Required**


- **Synchronization of the Following Object Types for EVAPCO HQ and Sub-Location Standards:**
    - Users
    - Devices
    - Password Hash

- **Parity for Previous Architecture:**
    - Groups

# Assumptions

***Assume we are unable to use Cloud Sync and an erratic Domain Trust***
- The OnSite Technical Resource previously expressed interest in Cloud Sync
- Based on their requirements for PassThrough Authentication we knew this would cause issues.
- Our Uniform Standard is Hybrid Synching Locations, Users, Devices.

***Users must use a single account across all environments***

***[Users must use Windows Hello for Business for Authentication Purposes](https://learn.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/deploy/hybrid-cloud-kerberos-trust?tabs=intune)***

# Previous Solutions Attempted

*[NAT Rule](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/support-for-active-directory-over-nat#microsoft-statement-regarding-active-directory-over-nat)*

*"Double NAT"*

*Construction of DNS Zones to reflect NAT Schema on evapco.com and domfc.local*

- All of the above Solutions failed to lead to satisfcatory outcomes.
    - Pings were 'successful'
    - Traceroutes would fail their first 3 initial hops from a Cloud Kerberos Joined Computer, aligning with Microsoft Documentation
    - The Domain Trust Wizard would add both domains
    - Attempts to add the DOMFC.LOCAL domain to the sync tool universally fail.
    - The Domain Trust Collapses within 15 minutes

# Resources Engaged

- FortiNet
- Microsoft
- SonicWall
- Layer 8
- Connection

# Already Existing / Known Solution(s)

1. Cloud Connect Sync
2. IP Cutover of Domain Controller(s) at Minimum, but very likely all devices 
3. Full Enterprise Network Cutover of Corporate Network and all sub locations aligned with new IP Schema