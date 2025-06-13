# Overview

- EVAPCO Europe A/S is a site in Denmark.
    - ***[GDPR](https://gdpr-info.eu/) Compliancy is a HARD Requirement***
- [They previously were synching to their own tenant and Hybrid Synching *everything*](https://learn.microsoft.com/en-us/entra/identity/hybrid/)
- [The requirements for Hybrid Synching are known to be unforgiving](https://learn.microsoft.com/en-us/entra/identity/devices/hybrid-join-plan)
- They previously had full control of their entire environment.
    - The revocation of these permissions has been poorly received
- Their location was the last remaining location with independently managed email, account access, etc.
- We cut over their user endpoints via our in-house (David Drosdick) developed [Clear Enrollment](https://github.com/DirtyDabe23/EvapcoRepo/blob/main/Modules/Clear-Enrollment/Clear-Enrollment.ps1) module.
    - [Users signed into the Company Portal on a device that went through this process to register it.](https://learn.microsoft.com/en-us/intune/intune-service/user-help/device-enrollment-overview-windows)
    - There were signficant breakdowns in communication on both the HQ and Sub-Location side.
    - ["Generally Unpleasant Experience" but no devices required wiped and reloaded, etc.](https://techcommunity.microsoft.com/blog/intunecustomersuccess/support-tip-how-to-transfer-windows-autopilot-devices-between-tenants/3920555)
- ***We are synching 5 other domains***
    - rvscorp.com
    - evapco-alcoil.com
    - evapcomw.com
    - towercomponentsinc.com
    - EVAPTECH.LAN


# Current Blockers

- ***Relationship Issue with Site Technical Contact***
    - Randomly Terminating Access
    - Revocation of Administrator Permissions of our Managament Accounts
    - System Shutdown of Management VMs

***No Full Access to Firewall***
- Managed by a 3rd Party
- No Contact Information or listed contact

***Time difference of 6|7 hours (GMT-4/GMT+2)***

# The following are the technologies that any proposed Solution must support:

- **[PassThrough Authentication](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/how-to-connect-pta)** 

- **[Cloud Kerberos Ticket Retrieval](https://learn.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/how-it-works-authentication#microsoft-entra-hybrid-join-authentication-using-cloud-kerberos-trust)**

- **[Azure File Share Access](https://learn.microsoft.com/en-us/azure/storage/files/storage-files-identity-auth-hybrid-identities-enable?tabs=azure-portal%2Cintune)**

- **[MDM Management From InTune and fulfillment of all listed objectives](https://learn.microsoft.com/en-us/intune/intune-service/fundamentals/intune-planning-guide)**

- **[Defender for Endpoint and BitLocker Encrpytion](https://learn.microsoft.com/en-us/intune/intune-service/protect/mde-security-integration)**

- **Users have ONE Account unless Administrative Access for On Site Resources is Required**


- **[Synchronization of the Following Object Types for EVAPCO HQ and Sub-Location Standards](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/what-is-cloud-sync#comparison-between-microsoft-entra-connect-and-cloud-sync)**
    - Users
    - Devices
    - Password Hash

- **Parity for Previous Architecture:**
    - Groups

# Assumptions

***The Forest Master must be a VM in Taneytown***

***Assume we are unable to use Cloud Sync and an erratic Domain Trust***
- The OnSite Technical Resource previously expressed interest in Cloud Sync
- Based on their requirements for PassThrough Authentication we knew this would cause issues.
- Our Uniform Standard is Hybrid Synching Locations, Users, Devices.
    - We are gradually implementing Azure Joined Endpoints
    - We still require the Hybrid Connect Sync for Functional Cross Forest References and account deduplication  

***Users must use a single account across all environments***

***[Users must use Windows Hello for Business for Authentication Purposes](https://learn.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/deploy/hybrid-cloud-kerberos-trust?tabs=intune)***

# Previous Solutions Attempted

*[NAT Rule](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/support-for-active-directory-over-nat#microsoft-statement-regarding-active-directory-over-nat)*

*"Double NAT"*

*Construction of DNS Zones to reflect NAT Schema on evapco.com and domfc.local*

- All of the above Solutions failed to lead to satisfcatory outcomes.
    - Pings were 'successful'
    - Traceroutes would fail their first 3 initial hops from a Cloud Kerberos Authenticating Azure Joined InTune Managed Computer and Hybrid User Identity, aligning with Microsoft Documentation
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
