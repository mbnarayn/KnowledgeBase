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
## Setting Up Exchange Email Forwarding to External Email Address Without Creating a Mail Contact

By default to forward mail externally for an Exchange Mailbox User you must create a Contact. If you configure email forwarding to an external email address without creating a mail contact the forwarding will not work.

To set up forwarding to an external email address without creating a contact, the Exchange Administrator will need to add a remote domain using the command below. Remote domains are SMTP domains that are external to your Microsoft Exchange organization.

```
New-RemoteDomain -Name ExternalDomain -DomainName externaldomain.com
```

Once you have added the remote domain, run the below command to check that Auto Forward is enabled.
```
Get-RemoteDomain ExternalDomain | Select DomainName, AutoForwardEnabled 
```
Now to forward email for a user to an external email address as well as deliver a copy to their primary mailbox run the command below:

```
Set-Mailbox -Identity joe.bloggs@domain.com -DeliverToMailboxAndForward $true -ForwardingSMTPAddress Joe.Bloggs@externaldomain.com
```
The above command also works for Shared Mailboxes.
Email forwarding to external addresses cannot be configured via the GUI. Also it not possible to view the forwarding from the GUI when using this method.

To view the forwarding SMTP address use the cmdlete below:

```
Get-Mailbox -Identity joe.bloggs@domain.com | Select Name, ForwardingSMTPAddress
```

***
## Remove Duplicates in Excel

When you use the Remove Duplicates feature, the duplicate data will be permanently deleted. Before you delete the duplicates, it’s a good idea to copy the original data to another worksheet so you don’t accidentally lose any information.

Click Data > Remove Duplicates, and then Under Columns, check or uncheck the columns where you want to remove the duplicates.

![](https://github.com/mbnarayn/KnowledgeBase/blob/master/RemoveDuplicatesExcel.jpg)

***
## Azure AD Enterprise Applications

Azure AD can be used as an Identity Provider for your custom applications. Azure AD must be configured to integrate with an application. In other words, Azure AD needs to know what apps are using it for identities. To make Azure AD aware of these apps and allow the app to use Azure AD as the identity provider the application needs to be added as an Enterprise Application on Azure. Enterprise Applications include an Azure AD app gallery which contains many popular applications that are already pre-configured to work with Azure AD as an identity provider. Apps from the gallery are pre-configured and can be set up with minimal effort. Alternatively it is also possible to add a custom application that is not published in the gallery and configure it to use Azure AD as the identity provider. You can use Azure AD as your identity system for just about any app. You can manually configure most apps for single sign-on if they aren't already in the gallery. Azure AD provides several SSO options. Some of the most popular are SAML-based SSO and OIDC-based SSO.


***
## Azure AD App Registrations

An App Registration (Application) is an object that is included in Azure AD and describes the application. In fact, it is the definition of the application in which various elements are included, eg. Name, logo, publisher, Redirect URIs etc.

You would create an Azure App Registration when you own the application. When you allow other/external Azure AD tenants to you use your application they will have to create an Enterprise Application to reference you App (App Registration) on their tenant.

When you create an Azure App Registration on your Azure AD it also automatically adds a corresponding entry to you Enteprise Applications. An App Registration (Application) on its own cannot really do anything. After all, it is only a definition of the application. The Enterprise Application is what gives the App Registration an identity within a directory (including the home Azure AD directory). 

An Enterprise application entry will have to be present in every Azure AD tenant where the Application (App Registration) will be used including the home Azure AD tenant on which the Application was registered (created).

Only one application registration can exist for an application. But multiple enterprise applications can exist referencing the one applicatation registration across multiple tenants as well as the home tenant. (Many (enterprise applicationS) to One (application registration).

, which can only be given rights within the same directory. The Enterprise Application (Service Principal) object is the instance of an app registration (application).

Further your App Registration can be referenced by one or more Enterprise Applications in other Azure AD tenants.

An application has one App Registration (Application) object in its home directory that is referenced by one or more Enterprise Applications (Service Principals) in each of the directories where it operates, including the home directory of the App Registration (Application).

A very easy explanation (that isn't 100% correct) is that you create a app registration for apps that you develop yourself. Enterprise Applications are added when you add third party appliations.

One is the actual application object, where you configure the properties of your app (authentication, permissions, replyURIs and so on). The other one is a representation of the application within a directory, this can be your own directory or another company directory, etc. It "inherits" the settings from the application object and is what's used to grant consent/permissions to resources.

https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-how-applications-are-added


The "Enterprise Applications" blade contains the list of existing Service Principals in your tenant. Your own tenant applications will also be represented in the Enterprise Applications blade as Service Principals. Switch the Application Type filter to "All Applications" as here.

Background of Multi-Tenant Apps
Apps from third parties are classically multi-tenant Apps. Imagine, you are the app maker. This means, you only need to do the application registration once - as Multi Tenant App as shown above - in your own tenant which creates the Enterprise Application (Service Principal). When another tenant user wants to login to your app (identified by the world-wide unique App ID), they grant your app the permissions it requires - the "consent". If agreed, the Enterprise Application (Service Principal) is created in their tenant and this app effectively mirrors your application in their tenant. So one app can be used in multiple tenants.


## What is the Difference Between DirectAccess and Always On VPN?

Fundamentally they both provide seamless and transparent, always-on remote access. However, Always On VPN has a number of advantages over DirectAccess in terms of security, authentication and management, performance, and supportability. Windows 10 Always On VPN provides the same seamless, transparent, and always-on user experience as DirectAccess. A VPN connection is automatically established any time an authorized client has an active Internet connection; it does not require input from or interaction with the user (unless multifactor authentication is enabled, of course). Remote users access on-premises data and applications in the same familiar way, just as if they were at the workplace. Always On VPN is a Windows 10-only solution. However, unlike DirectAccess, client devices do not have to run the Enterprise edition to take advantage of it. Windows 10 Professional, along with all other SKUs, are now supported clients. Devices can be joined to an Active Directory domain, but this is not strictly required. Always On VPN clients can be standalone or, to take advantage of advanced features, they can be joined to Azure Active Directory.

Windows 10 Always On VPN includes support for granular traffic filtering. DirectAccess lacks any native features to control access on a granular basis. It is possible to restrict access to internal resources by placing a firewall between the DirectAccess server and the LAN, but the policy would apply to all connected clients. Always On VPN allows administrators to restrict client access to internal resources in a variety of ways. In addition, traffic filter policies can be applied on a per-user or group basis. For example, users in accounting can be granted access only to their department servers. The same could be done for HR, finance, IT, and others.

Windows 10 Always On VPN includes support for modern authentication and management, which results in better overall security. Always On VPN clients can be joined to an Azure Active Directory and conditional access can also be enabled. Modern authentication support using Azure MFA and Windows Hello for Business is also supported. Always On VPN is managed using Mobile Device Management (MDM) solutions such as Microsoft Intune.

DirectAccess uses IPsec with IPv6, which must be encapsulated in TLS to be routed over the public IPv4 Internet. IPv6 traffic is then translated to IPv4 on the DirectAccess server. DirectAccess performance is often acceptable when clients have reliable, high quality Internet connections. However, if connection quality is fair to poor, the high protocol overhead of DirectAccess with its multiple layers of encapsulation and translation often yields poor performance. The protocol of choice for Windows 10 Always On VPN deployments is IKEv2. It offers the best security and performance when compared to TLS-based protocols. In addition, Always On VPN does not rely exclusively on IPv6 as DirectAccess does. This reduces the many layers of encapsulation and eliminates the need for complex IPv6 transition and translation technologies, further improving performance over DirectAccess.

DirectAccess is a Microsoft-proprietary solution that must be deployed using Windows Server and Active Directory. It also requires a Network Location Server (NLS) for clients to determine if they are inside or outside the network. NLS availability is crucial and ensuring that it is always reachable by internal clients can pose challenges, especially in very large organizations. Always On VPN is infrastructure independent and can be deployed using Windows Routing and Remote Access (RRAS) or any third-party VPN device such as Cisco, Checkpoint, SonicWALL, Palo Alto, and more. Authentication can be provided by Windows Network Policy Server (NPS) or any third-party RADIUS platform.

Windows 10 Always On VPN is the way of the future. It provides better overall security than DirectAccess, it performs better, and it is easier to manage and support.

## What is DevOps?
DevOps isn’t a process or a technology or a standard. At its essence, DevOps is a culture, a movement, a philosophy that delivers higher value to businesses and customers. DevOps is a set of practices that automates the processes between software development and IT teams, in order that they can build, test, and release software faster and more reliably. The concept of DevOps is founded on building a culture of collaboration between teams that historically functioned in relative siloes. DevOps implementations utilize technology especially automation tools that can leverage an increasingly programmable and dynamic infrastructure from a life cycle perspective. It unites agile, continuous delivery, automation, and much more, to help development and operations teams be more efficient and innovate faster. The promised benefits include increased trust, faster software releases, ability to solve critical issues quickly, and better manage unplanned work. 

## What is Version Control?
Version control systems are a category of software tools that help a software team manage changes to source code over time. Version control software keeps track of every modification to the code in a special kind of database. If a mistake is made, developers can turn back the clock and compare earlier versions of the code to help fix the mistake while minimizing disruption to all team members. Without version control, you're tempted to keep multiple copies of code on your computer. This is dangerous, as it's easy to change or delete a file in the wrong copy of code, potentially losing work. Version control systems solve this problem by managing all versions of your code but presenting you with a single version at a time.

## What types of Version Control does Repos in Azure DevOps support?

Azure Repos supports two types of version control: Git and Team Foundation Version Control (TFVC).

- Git (distributed)

Git is an open-source version control system and by far the most widely used modern version control system in the world today is Git. Git is a distributed version control system. Rather than have only one single place for the full version history of the software as is common in once-popular version control systems like CVS or Subversion (also known as SVN), in Git, each developer has a copy of the source repository on their dev machine. Every developer's working copy of the code is also a repository that can contain the full history of all changes. This allows developers to easily collaborate, as they can download a new version of the software, make changes, and upload the newest revision. Every developer can see these new changes, download them, and contribute.

- TFVC (centralized)

Team Foundation Version Control (TFVC) is a centralized version control system. Typically, team members have only one version of each file on their dev machines. Historical data is maintained only on the server. Branches are path-based and created on the server.

## What is CI/CD?
CI and CD are two acronyms that are often mentioned when people talk about modern development practices. CI stands for continuous integration but CD can either mean continuous delivery or continuous deployment.

- Continuous Integration

Developers practicing continuous integration merge their changes back to the main branch as often as possible. The developer's changes are validated by creating a build and running automated tests against the build. By doing so, you avoid the integration hell that usually happens when people wait for release day to merge their changes into the release branch.

- Continuous Delivery

Continuous delivery is an extension of continuous integration to make sure that you can release new changes to your customers quickly in a sustainable way. This means that on top of having automated your testing, you also have automated your release process and you can deploy your application at any point of time by clicking on a button.

- Continuous Deployment

Continuous deployment goes one step further than continuous delivery. With this practice, every change that passes all stages of your production pipeline is released to your customers. There's no human intervention, and only a failed test will prevent a new change to be deployed to production.

## Azure Pipelines

Azure Pipelines supports continuous integration (CI) and continuous delivery (CD) to constantly and consistently test and build your code and ship it to any target. You accomplish this by defining a pipeline.

Continuous integration automates tests and builds for your project. CI helps to catch bugs or issues early in the development cycle, when they're easier and faster to fix. Items known as artifacts are produced from CI systems. They're used by the continuous delivery release pipelines to drive automatic deployments.

- Increase code coverage
- Build faster by splitting test and build runs
- Automatically ensure you don't ship broken code
- Run tests continually

Continuous delivery automatically deploys and tests code in multiple stages to help drive quality. Continuous integration systems produce deployable artifacts, which includes infrastructure and apps. Automated release pipelines consume these artifacts to release new versions and fixes to the target of your choice.

- Automatically deploy code to production
- Ensure deployment targets have latest code
- Use tested code from CI process

![pipelines-image-designer.png](/.attachments/pipelines-image-designer-85266f2a-e6ba-4a76-b35f-703ef0507523.png)

## Azure Artifacts vs Artifacts in Azure Pipelines

Azure Artifacts is the new home of the Packages page under the Build and release page group in the previous navigation UX of Azure DevOps Services and TFS. With Azure Artifacts you can create and share Maven, npm, and NuGet package feeds from public and private sources with teams of any size. Azure Artifacts introduces the concept of multiple feeds that you can use to organize and control access to your packages. If you're familiar with using packages from NuGet.org or npmjs, you can think of those places each as a single feed. A note on terminology: you might also have heard feeds called package repositories or package sources, especially if you're an npm or Maven user.

Artifacts in Azure Pipelines are different from Azure Artifacts. An Artifact in an Azure Pipeline is a collection of files or packages published by a run. Artifacts are made available to subsequent tasks, such as distribution or deployment.  Within Pipelines you can use build artifacts and pipeline artifacts to help store build outputs and intermediate files between build steps. You can then add onto, build, test, or even deploy those artifacts. 

Build artifacts are the files that you want your build to produce. Build artifacts can be nearly anything that your team needs to test or deploy your app. For example, you've got .dll and .exe executable files and a .PDB symbols file of a .NET or C++ Windows app. 

You can use pipeline artifacts to help store build outputs and move intermediate files between jobs in your pipeline. Pipeline artifacts are tied to the pipeline that they're created in. You can use them within the pipeline and download them from the build, as long as the build is retained. Pipeline artifacts are the new generation of build artifacts. They take advantage of existing services to dramatically reduce the time it takes to store outputs in your pipelines. Only available in Azure DevOps Services.






