# Knowledge Base
## Remote Desktop (RDP) Connection Issues
If you experience connectivity issues when using RDP, such as RDP immediately disconnecting after logon or the RDP connection freezing, a change to the MTU size can potentially help solved the issue.
```
netsh interface ipv4 set subinterface “WiFi” mtu=1406 store=persistent
```

![hhjkhlj](https://github.com/mbnarayn/KnowledgeBase1/blob/master/test.jpg)
