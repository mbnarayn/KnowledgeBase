# Knowledge Base
***
## Remote Desktop Protocol (RDP) Connection Issues
If you experience connectivity issues when using RDP, such as RDP immediately disconnecting after logon or the RDP connection freezing, a change to the MTU size can potentially help solved the issue.

Run the following command by from an elevated command prompt:
```
netsh interface ipv4 set subinterface “WiFi” mtu=1406 store=persistent
```

Replace "WiFi" with the name of the network interface. This can be obtained by running the following command:
```
netsh interface ipv4 show interface
```
***
## Authentication Issues with NPS Extension for Azure MFA on NPS Server

Microsoft documentation around the installation and configuration of NPS Extension for Azure MFA appears to be missing a couple of steps. This results in an error when attempting to request MFA authorisation for Azure.

The error appears in the Microsoft-AzureMfa-AuthZ/AuthZAdminCh Log on Event Viewer. The error will be along the lines of below:


> NPS Extension for Azure MFA:  CID: 1fd39a1a-34b9-47e6-a66a-ed7e47c30e0a :Exception in Authentication Ext for User USERNAME :: ErrorCode:: CID :1fd3456a1a-35f9-47e6-a66a-43sdlfjasla ESTS_TOKEN_ERROR Msg:: Verify the client certificate is properly enrolled in Azure against your tenant and the server can access URL in Registry STS_URL. Error authenticating to eSTS: ErrorCode:: ESTS_TOKEN_ERROR Msg:: Error in retreiving token details from request handle: -895352831 AADSTS7000112: Application '981f26a1-7f43-403b-a875-f8b09b8cd720'(Azure Multi-Factor Auth Client) is disabled.

To resolve this error navigate to Azure Active Directory > Enterprise Applications on the Azure Portal, change the Application Type dropdown to "All Applicaitions" and find the below applications. Both applications will have their properties set to No for the attribute "Enabled for users to sign-in", changing this to yes for both applications will resolve this issue.

* Azure Multi-factor Auth Client
* Azure Multi-factor Auth Connector

The screenshots below show the changes that need to be made.

![](https://github.com/mbnarayn/KnowledgeBase/blob/master/NPS%20Extension%20for%20Azure%20MFA.png)
***
## Bypass MFA on a NPS Server that has the NPS Extension for Azure MFA installed
It is important to understand that the ‘NPS Extension for MFA’ is designed to take-over all requests that are handled by the MFA server, meaning those requests that can’t support MFA will simply fail. There is isn't a method of skipping MFA via the network policies. To overcome this restriction we have to make a registry change to whitelist the IP addresses of all devices where the service request originates (such as a VPN server or Wi-Fi Access Point), with this whitelist in place, authentication requests originating from these sources will automatically bypass the MFA extension.

To configure an MFA bypass list, go to HKLM\SOFTWARE\Microsoft\AzureMfa and configure the following registry value:

Name: IP_WHITELIST
Type: string
Value: 192.168.1.1;192.168.1.2

Replace IP addresses in the above example with the IP addresses of your devices (RADIUS Client) from which the requests originate.

It is probably best to handle all non MFA requests via a different NPS server. However, if that is not an option this method is a decent alternative.
***
## Citrix Clients Fail to Connect with Symantec Web Security Service (WSS) Proxy or Web Traffic Redirection
Citrix Receiver client, and Citrix Workspace connections fail when the computer is configured to use the Web Security Service (WSS) through the Symantec Endpoint Protection (SEP) client Web Traffic Redirection (WTR) feature. The cause is Citrix client connections sent through the WSS via WTR contain authentication header information that the Citrix server may reject.

You may get one of the errors below or something similar:

> Unable to launch your application. Contact your help desk with the following information:
Cannot connect to the Citrix XenApp server.Can't assign requested address

> Unable to connect to the server. Contact your system administrator with the following error: The namelength argument is incorrect (Socket Error 10014)

> Unable to connect to the server. Contact your system administrator with the following error: Socket operation on non-socket (Socket Error 10038)

To resolve this issue ensure that the WTR definitions on the Symantec client are updated to 10/16/2019 r100 or newer. If for some reason the WTR definition fail to update try reinstalling the Symantec client or upgrade to a newer version. If these steps fail try testing Citrix access with Symantec uninstalled.
***
## Combine text from two or more cells in Microsoft Excel into one cell
It is possible to combine data from multiple cells into a single cell using the Ampersand symbol (&) or the CONCAT function.

### Combine data with the Ampersand symbol (&)
1. Select the cell where you want to put the combined data.

1. Type = and select the first cell you want to combine.

1. Type & and use quotation marks with a space enclosed.

1. Select the next cell you want to combine and press enter. 

The example below adds a space between the cell values:
```
=A4&" "&B4&" "&C4&" "&D4&" "&E4
```
The example below adds a comma between the cell values without spaces:
```
=A16&","&B16&","&C16&","&D16&","&E16&","&F16
```

### Combine data using the CONCAT function
1. Select the cell where you want to put the combined data.

1. Type =CONCAT(

1. Select the cell you want to combine first

1. Use commas to separate the cells you are combining and use quotation marks to add spaces, commas, or other text

1. Close the formula with a parenthesis and press Enter

The example below adds a space between the cell values:
```
=CONCAT(A21," ",B21, " ",C21," ",D21," ",E21)
```
***

## Delete a User's Profile in Windows 10

- Open the Run dialog and execute the command SystemPropertiesAdvanced
- Under User Profiles, click Settings...
- Now delete the User Profile that you would like to remove

In Windows 10 this automatically deletes the Profile folder for the user from C:\Users and also deletes the associated entry for the user from the below Registry key.

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList

However in older versions of Windows, the Profile folder and the Registry key may have to be deleted manually.

Newer versions of Windows 10 now have a Registry Address Bar so it is possible to navigate straight to the key by pasting the Registry Path as shown below.

![](https://github.com/mbnarayn/KnowledgeBase/blob/master/RegistryAddressBar.png)

***
## OneDrive (Personal) vs OneDrive for Business

OneDrive is a personal cloud storage service (similar to the likes of Google Drive and Dropbox) from Micrsoft for you to store your files online and access them later from anywhere and any device. OneDrive for Business is first a synchronization tool. Either by installing Office 365, Office 2013 or later on your computer or through a standalone installer, you would install OneDrive for Business (Groove.exe). It’s a tool that evolved from Groove and allows you to synchronize offline the files you need to work with. However, through the evolution of the SharePoint brand and the continuing efforts made by Microsoft to compete on the cloud market, OneDrive for Business has become a whole lot more. For those of us that came from the SharePoint world, we saw another feature come time and time again but never be quite successful, My Sites. My Sites on SharePoint provided a user with his/her own personal site to store documents, and at the time touted to be a replacement for My Documents. With SharePoint 2013, they removed the name My Site for this feature and branded it with the name of the synchronization tool itself, OneDrive for Business. 

Technically, OneDrive for Business is a synchronization feature offered by SharePoint. But as a brand, it’s a professional version of the OneDrive personal storage solution. Contrary to its personal counterpart though, OneDrive for Business is also available On-Premises, on an organizations’ own servers. Because it uses SharePoint, it also provides more administration options for working with documents. OneDrive for Business uses SharePoint to give each member of your team a place to store documents and collaborate with others. It doesn't require being on the cloud, and can be installed on your servers.

***
## OneDrive for Business Sync Apps

For users of Office 365 there are a couple of apps currently available to sync files from OneDrive for Business, the OneDrive for Business sync app (Groove.exe) and the new OneDrive sync app (OneDrive.exe). Either by installing Office 365, Office 2013 or later on your computer or through a standalone installer, you would install OneDrive for Business (Groove.exe). The new OneDrive sync app (OneDrive.exe) is the recommended option if you are using Office 365 SharePoint Online. The new app can be download from onedrive.com and is the same app that also lets you sync from OneDrive (Personal). The new OneDrive sync app does not support on-premises locations in SharePoint 2016 or earlier and only supports SharePoint Server 2019.

When users who are syncing files with the previous OneDrive for Business sync app (Groove.exe) sign in to the new OneDrive sync app (OneDrive.exe), the following things happen:

If the new OneDrive sync app can take over syncing a library, the previous sync app stops syncing it and the new OneDrive sync app takes over syncing it without re-downloading the content. If the new OneDrive sync app can't sync the library, the previous sync app continues to sync it. If a library requires checkout or has required columns or metadata, it will be synced read-only.

The previous sync app stops running and removes itself from automatic startup, unless it's still syncing libraries that the new OneDrive sync app can't sync.

***
## Computer Accounts and Active Directory Tombstone Lifetime

There is no problem of having workstations disconnected from the domain for longer than 30 days. The tombstone lifetime only applies to domain controllers and not workstations. The machine password is set to change every 30 days, but there is usually no problem even if a machine has been offline for several months, it will continue to work, no matter how long it has been since its machine account password was initiated and changed. The machine account passwords as such do not expire in Active Directory as the machine account password changes are driven by the client computer and not the domain controllers. As long as no one has disabled or deleted the computer account, nor tried to add a computer with the same name to the domain, (or some other destructive action), the computer will continue to work no matter how long it has been since its machine account password reset was initiated and changed. If the machine was unable to communicate with a domain controller, the client doesn’t try to change its password. For example, if it was a laptop running at home with no VPN for 4 months, the laptop would never change its own machine password.  The machine will initiate the password change next time it will be connected to the domain.

If machine account password problems are encountered, they're typically due to the disabling or deletion of the machine account or an attempt to add a machine with the same name to the domain. In these cases, you can use the netdom.exe command line utility with the resetpwd switch to reset the machine account’s password. Tombstone lifetime can however affect a domain controllers if itself has been disconnected for longer than the tombstone lifetime. This is because the directory service cannot replicate with a domain controller whose last replication has exceeded the tombstone lifetime.

***
## Override Group Policies with Intune Policies

By default Group Policy takes precedence if there is a conflict however it is possible to configure Intune to override some Group Policies. The CSP ControlPolicyConflict/MDMWinsOverGP policy can be used to ensure that MDM policy wins over GP when policy is configured on MDM channel. Added in Windows 10 version 1803, this policy allows the IT admin to control which policy will be used whenever both the MDM policy and its equivalent Group Policy (GP) are set on the device. However it can only override policies in the Policy CSP. It does not apply to other MDM settings with equivalent GP settings that are defined on other configuration service providers.

https://docs.microsoft.com/en-us/windows/client-management/mdm/policy-csp-controlpolicyconflict

A list of CSPs are available here https://docs.microsoft.com/en-us/windows/configuration/provisioning-packages/how-it-pros-can-use-configuration-service-providers. All Windows Update settings https://docs.microsoft.com/en-us/windows/client-management/mdm/policy-csp-update#update-updateserviceurl are part of the Policy CSP.

***
## Setting Up Exchange Email Forwarding for Mulitple Users to External Email Address

By default to forward mail externally for an Exchange Mailbox User you must create a Contact. If you configure email forwarding to an external email address without creating a mail contact the forwarding will not work.

To set up forwarding to an external email address without creating a contact, the Exchange Administrator will need to add a remote doamin using the command below:

```
New-RemoteDomain -Name ExternalDomain -DomainName externaldomain.com
```

Once you have added the remote domain, run the below command to check that Auto Forward is enabled:
```
Get-RemoteDomain ExternalDomain | Select DomainName, AutoForwardEnabled 
```
Now to forward email for a user to an external email address as well as deliver a copy to their primary mailbox run the command below:

```
Set-Mailbox -Identity joe.bloggs@domain.com -DeliverToMailboxAndForward $true -ForwardingSMTPAddress Joe.Bloggs@externaldomain.com
```
Email forwarding to external addresses cannot be configured via the GUI. Also it not possible to view the forwarding from the GUI when using this method.




