```
dnf install <pkg>
dnf remove <pkg>
dnf search <term>
dnf list installed | grep <pkg>
rpm -qa | grep <pkg>
rpm -qi <pkg>

dnf repolist            # enabled repos
dnf repolist all        # including disabled
cat /etc/yum.repos.d/*.repo

dnf clean all           # clear stale metadata
dnf makecache           # refresh metadata
```
