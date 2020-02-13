# Knowledge Base
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

## Azure MFA Integration with NPS Extension Installed on NPS Server

Microsoft documentation around the installation and configuration of NPS Extension of Azure appears to be missing a couple of steps. This results in an error when attempting to request MFA authorisation for Azure.

The error appears in the Microsoft-AzureMfa-AuthZ/AuthZAdminCh Log on Event Viewer. The error will be along the lines of below:

```
NPS Extension for Azure MFA:  CID: 1fd39a1a-34b9-47e6-a66a-ed7e47c30e0a :Exception in Authentication Ext for User USERNAME :: ErrorCode:: CID :1fd3456a1a-35f9-47e6-a66a-43sdlfjasla ESTS_TOKEN_ERROR Msg:: Verify the client certificate is properly enrolled in Azure against your tenant and the server can access URL in Registry STS_URL. Error authenticating to eSTS: ErrorCode:: ESTS_TOKEN_ERROR Msg:: Error in retreiving token details from request handle: -895352831 AADSTS7000112: Application '981f26a1-7f43-403b-a875-f8b09b8cd720'(Azure Multi-Factor Auth Client) is disabled.
```
To resolve this error navigate to Azure Active Directory > Enterprise Applications on the Azure Portal and find the below applications. Both applications will have their properties set to No for the attribute "Enabled for users to sign-in", changing this to yes for both applications will resolve this issue.

Azure Multi-factor Auth Client

Azure Multi-factor Auth Connector

The screenshots below show the changes that need to be made.

![](https://github.com/mbnarayn/KnowledgeBase/blob/master/NPS%20Extension%20for%20Azure%20MFA.png)
