# vsftpd
## Allow dynamic passive address when vsftp responds to a proxy server rather than directly to a client

This worked on vsftpd version 3.0.2, and the change will likely work with older and new versions.

vsftpd has a configuration option, pasv_address, which the running vsftpd daemon can respond to; however, you can only have one IP address.

Another possible method is to notify vsftpd to read different configuration files for specific incoming IP addresses.  For security reasons, this may be an viable option, but it limits your IP addresses to known or trusted IPs.

This change may be useful for setups having multiple proxy servers between the client and ftp servers.  For example, the client hits one of the proxy servers (possibly running HAProxy), and that proxy server communicates back to one of the backend ftp servers.  When the ftp server responds, it doesn't respond directly to the client, but rather back to the proxy server.  The code change allows the ftp server to dynamically respond to any proxy server initiating the contact.

This guide isn't about downloading, installing, or setting up vsftpd for compilation, but expects you to already have vsftpd already installed, source code set up, and ready to compile.


```
### Update file postlogin.c

- Add  near the top in the include section
 #include "utility.h"

- Replace the following line
   vsf_sysutil_sockaddr_clone(&s_p_sockaddr, p_sess->p_local_addr);
- with
   vsf_sysutil_sockaddr_clone(&s_p_sockaddr, p_sess->p_remote_addr);
```


After compiling, backup the old vsftpd binary (usually in directory /usr/sbin/), and copy the new vsftpd binary you compiled over it.

Update the vsftpd.conf file (usually in directory /etc/vsftpd/), and comment out the pasv_address option if it's uncommented.

