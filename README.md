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


![hhjkhlj](https://github.com/mbnarayn/KnowledgeBase1/blob/master/test.jpg)
