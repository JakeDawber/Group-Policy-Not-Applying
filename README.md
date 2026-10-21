<h1>Ticket 003 — Group Policy Not Applying</h1>

<b>Overview</b>

Simulated a real-world IT support incident in which a domain user could access Registry Editor despite an organisational policy intended to prevent it.

The issue was reproduced by disabling the link for the relevant Group Policy Object (GPO), investigating the resulting policy changes, and restoring the link to resolve the issue.

<b>User Report</b>

A user reported that they could open Registry Editor on their workstation, despite the organisation's security policy being configured to prevent access.

Expected behaviour: Registry Editor should be blocked by the Workstation – Registry Restrictions GPO.

<b>Environment</b>

- Domain controller: JH-DC01

- Client workstation: MAN-PC-001

- Operating system: Windows 11

- Domain: jakehomelab.test

- Affected user: John Smith (jakehomelab\johnsmith)

- Affected GPO: Workstation – Registry Restrictions

<b>Investigation and Reproduction</b>

- Confirmed the original policy was working by attempting to open Registry Editor and observing the message: “Registry editing has been disabled by your administrator.”

<img src="Screenshots/Access to registry denied.png"/>

- Ran gpresult /r /scope:user on the client to review the Group Policy Objects applied to John Smith.

<img src="Screenshots/gpresult showing registry restriction not applied.png"/>

- Opened Group Policy Management on the domain controller and located Workstation – Registry Restrictions. Confirmed that the GPO was still present.

<img src="Screenshots/GPO still present.png"/>

- Opened Active Directory Users and Computers and confirmed that John Smith was in the correct OU.

<img src="Screenshots/John Smith in correct OU.png"/>

- Inspected the GPO's link configuration and identified that the link to the Manchester/Users OU was disabled.

<img src="Screenshots/Link not enabled.png"/>

- Refreshed Group Policy and reviewed the resultant policy to confirm that the registry restriction was no longer being applied.

- Attempted to open Registry Editor again and confirmed it was accessible, reproducing the reported security issue.

<img src="Screenshots/Access to registry.png"/>

<b>Root Cause</b>

The Workstation – Registry Restrictions GPO still existed and retained its configured settings, but its link to the OU containing John Smith had been disabled.

As a result, the policy was not applied to the user, allowing Registry Editor to open. This demonstrated the difference between a GPO existing in Group Policy Management and being linked and applied to the appropriate Active Directory OU.

<b>Resolution</b>

- Re-enabled the link for Workstation – Registry Restrictions in Group Policy Management.

- Ran gpupdate /force on the Windows 11 client to refresh Group Policy.

- Ran gpresult /r /scope:user to verify that the registry restriction GPO appeared under Applied Group Policy Objects.

- Attempted to open Registry Editor again to confirm that the restriction had been restored.

<b>Verification</b>

- Confirmed that Workstation – Registry Restrictions appeared in the user's applied Group Policy list after the link was restored.

<img src="Screenshots/gpresult showing registry restriction enabled.png"/>

- Registry Editor once again displayed the message: Registry editing has been disabled by your administrator.

<img src="Screenshots/Access to registry denied.png"/>

- The issue was resolved, and the expected security policy was enforced on the client workstation.

<b>Skills Demonstrated</b>

- Group Policy troubleshooting and administration

- Active Directory organisational units and GPO links

- Identifying why a GPO is not applying

- Using gpupdate /force to refresh policy

- Using gpresult /r /scope:user to diagnose resultant policy

- Windows 11 user-policy troubleshooting

- Security policy validation and incident documentation
