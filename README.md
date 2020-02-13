# Knowledge Base
***
## Remote Desktop Protocol (RDP) Connection Issues
If you experience connectivity issues when using RDP, such as RDP immediately disconnecting after logon or the RDP connection freezing, a change to the MTU size can potentially help solved the issue.

Run the following command by from an elevated command prompt:
```
netsh interface ipv4 set subinterface “WiFi” mtu=1406 store=persistent
```

Replace "WiFi with the name of the network interface. This can be obtained by running the following command:
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

Replace IP addresses in the above examples with the IP addresses of your devices (RADIUS Client) from which the requests originate.

It is probably best to handle all non MFA requests via a different NPS server. However, if that is not an option this method is a decent alternative.
***
## Citrix Clients Fail to Connect with Symantec Web Security Serivice (WSS) Proxy or Web Traffic Redirection
Citrix Receiver client, and Citrix Workspace connections fail when the computer is configured to use the Web Security Service (WSS) through the Symantec Endpoint Protection (SEP) client Web Traffic Redirection (WTR) feature. The cause is Citrix client connections sent through the WSS via WTR contain authentication header information that the Citrix server may reject.

You may get one of the errors below or something similar

> Unable to launch your application. Contact your help desk with the following information:
Cannot connect to the Citrix XenApp server.Can't assign requested address

> Unable to connect to the server. Contact your system administrator with the following error: The namelength argument is incorrect (Socket Error 10014)

> Unable to connect to the server. Contact your system administrator with the following error: Socket operation on non-socket (Socket Error 10038)

To resolve this issue ensure that the WTR definitions on the Symantec client are updated to 10/16/2019 r100 or newer. If for some reason the WTR definition fail to update try reinstalling the Symantec client or upgrade to a newer version.
***


