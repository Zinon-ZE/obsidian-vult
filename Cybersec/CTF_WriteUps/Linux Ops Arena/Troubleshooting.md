

# Linux Ops Arena — Round 2 Detailed Troubleshooting Notes

Your own notes (diagnosis tools, error codes, disk commands, shell states) were accurate — folded in below, corrected where the detail matters, and expanded area-by-area to match everything Round 2 is scoped to cover: web services, databases, networking, storage, background services, scheduled jobs, logging, user access, system time, software repositories, process limits, firewall, and SELinux.

---

## 0. Step Zero — Find Everything That's Broken

Before drilling into any one area, get the full picture:

```bash
systemctl --failed                              # every failed unit, system-wide
journalctl -p err -b                            # every error logged since boot
```

The scoreboard tells you which _categories_ are unresolved — `systemctl --failed` often tells you which _specific units_, in one shot.

---

## 1. Reading a Failed Service (your notes, corrected + expanded)

```bash
systemctl status <name>
journalctl -u <name>
nginx -t                    # test config before reload — same idea applies to httpd -t, etc.
chmod ...
chown user:group file
```

All correct and exactly the right first moves.

### Exit codes — two different systems, don't mix them up

Your table had 126, 127, 1, and 203 together, but these actually come from **two different layers**:

**Shell-level exit conventions** (apply when a shell or script runs, or when `ExecStart=` is itself a shell command):

| Code | Meaning                                                                      |
| ---- | ---------------------------------------------------------------------------- |
| 1    | General failure — read the app's own log, not systemd's                      |
| 126  | Found the file, but it's not executable (missing `+x`, or wrong permissions) |
| 127  | Command not found — bad `$PATH`, typo, or the binary isn't installed         |

**systemd's own pre-exec codes** (systemd never even reached your program — the failure happened while systemd was setting up the process, before `execve()`):

| Code | Name                | What failed                                                                                                                                   |
| ---- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| 200  | `CHDIR`             | Couldn't change into `WorkingDirectory=` (path missing/wrong perms)                                                                           |
| 203  | `EXEC`              | The `execve()` call itself failed — bad path in `ExecStart=`, missing execute bit, or a shebang pointing at an interpreter that doesn't exist |
| 216  | `GROUP`             | `Group=` in the unit refers to a group that doesn't exist                                                                                     |
| 217  | `USER`              | `User=` in the unit refers to a user that doesn't exist                                                                                       |
| 226  | `NAMESPACE`         | A sandboxing directive (e.g. `ProtectSystem=`, `ReadOnlyPaths=`) hid or blocked the path it needed                                            |
| 229  | `SELINUX_CONTEXT`   | SELinux blocked the process from even starting in the required context                                                                        |
| 233  | `RUNTIME_DIRECTORY` | `RuntimeDirectory=` couldn't be created                                                                                                       |

You don't need to memorize the full list (there are ~40) — the pattern is what matters: **anything ≥200 means systemd itself couldn't launch the process**, and `systemctl status` prints the name right next to the number (e.g. `status=217/USER`), so just recognize the shape and go look at that specific directive in the unit.

```bash
systemctl status <name> --no-pager     # look for the "Process:" line — shows the exact
                                        # command attempted AND the code=exited status
journalctl -u <name> -b --no-pager | tail -20   # the "Failed at step X" line names the stage
```

### Unit file locations — and which one wins

```bash
/usr/lib/systemd/system/     # shipped by the package — gets overwritten on updates
/etc/systemd/system/         # local admin overrides — WINS over the package version
/run/systemd/system/         # runtime-generated — wiped on reboot
```

If a service behaves differently than its "normal" unit file suggests, check `/etc/systemd/system/<name>.service.d/` for an override drop-in before assuming the main unit is wrong — `systemctl cat <name>` shows both merged together.

```bash
systemctl cat <name>          # merged view: base unit + any override
systemctl daemon-reload       # required any time a unit file or drop-in changes on disk
```

---

## 2. Web Services

```bash
systemctl status nginx        # or httpd
ss -tulnp | grep -E '80|443'
curl -I http://localhost
nginx -t                      # or: apachectl configtest
```

**Common faults:**

- `listen` directive points at the wrong port, or the port isn't opened in the firewall.
- Docroot exists but wrong ownership/permissions — server user can't read it.
- **SELinux**: content copied in with `cp` instead of `mv` often ends up with the _wrong_ context (see SELinux section) — `httpd_sys_content_t` missing means "403 even though `ls -la` looks fine."
- **SELinux + custom port**: if the site listens on a non-standard port, SELinux won't let nginx/httpd bind it unless that port is labeled:
    
    ```bash
    semanage port -l | grep http_port_tsemanage port -a -t http_port_t -p tcp 8080
    ```
    
- Service not enabled — survives until reboot, then silently doesn't come back.

---

## 3. Databases

```bash
systemctl status mariadb
ss -tulnp | grep 3306
mysqladmin -u root -p ping
```

**Common faults:**

- `bind-address` in `/etc/my.cnf.d/mariadb-server.cnf` restricts it to localhost when the task needs network access (or vice versa).
- User exists but scoped to the wrong host — `'app'@'localhost'` won't authenticate a remote connection; needs `'app'@'%'` or a specific IP.
- Data directory full or wrong ownership (`mysql:mysql`) — check with `du -sh /var/lib/mysql`.
- SELinux context on a relocated datadir: `mysqld_db_t` — same pattern as the web content issue above.
- Service installed but never enabled/started.

```sql
SHOW GRANTS FOR 'user'@'host';    -- confirm privileges match what's expected
```

---

## 4. Networking & Name Resolution

```bash
ip a
nmcli connection show
ss -tulnp
ping -c2 <target>
cat /etc/resolv.conf
cat /etc/hosts
getent hosts <name>
```

**Common faults:**

- Interface configured but not "up," or NAT/static IP profile has a typo.
- DNS servers missing or unreachable in `/etc/resolv.conf` — `ping <ip>` works but `ping <hostname>` doesn't = pure resolution problem, not connectivity.
- Wrong or missing `/etc/hosts` entry when a task depends on local name resolution instead of DNS.
- `hostnamectl` name mismatch with what a service/cert expects.

---

## 5. Firewall

```bash
firewall-cmd --state
firewall-cmd --get-active-zones
firewall-cmd --list-all
```

**Common faults:**

- A rule was added without `--permanent` — works right now, gone after `--reload` or reboot.
- A rule was added with `--permanent` but `--reload` was never run — looks correct in the config, not actually active yet.
- Correct rule added to the wrong zone (interface is in `public` but the rule went into `home`, etc.)

```bash
firewall-cmd --add-service=http --permanent
firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --reload
```

---

## 6. Storage & Filesystems (your notes, expanded)

```bash
df -h                     # usage per filesystem
du -sh folder/            # find what's actually eating the space
lsblk
blkid
cat /etc/fstab
mount -a                  # test fstab entries WITHOUT rebooting
findmnt
```

**Common faults:**

- A filesystem fills up (logs, a runaway process) and every service writing to it starts failing — `df -h` first, `du -sh` to find the offender.
- A bad `/etc/fstab` entry: wrong UUID/device, wrong mount point, or a typo in options — `mount -a` will surface the error immediately instead of waiting for a reboot to discover it.
- Something expected to be mounted (e.g. a data volume) simply isn't — check `findmnt <path>` returns nothing.

---

## 7. Background Services (systemd internals)

```bash
systemctl list-units --type=service --state=running
systemctl list-units --type=service --state=failed
systemctl cat <name>
systemctl edit <name>          # create/edit an override drop-in
```

**Common faults:**

- Dependency ordering: a service starts before something it needs (`After=`/`Requires=` missing or wrong).
- An override drop-in sets a bad `Environment=` or `ExecStart=` and shadows an otherwise-correct base unit.
- Service works when run manually as the current user but fails under systemd — usually a `User=`/`WorkingDirectory=`/environment difference (see the exit-code table above).

---

## 8. Scheduled Jobs

```bash
crontab -l
cat /etc/crontab
ls /etc/cron.d/
systemctl list-timers
journalctl -u crond --since "10 min ago"
```

**Common faults:**

- Cron syntax error (five fields, or a stray character) — the job silently never runs.
- Script referenced by the cron line isn't executable, or uses a relative path/relies on a `$PATH` that cron's minimal environment doesn't have.
- Job commented out or the crontab was edited but never saved correctly.
- A `systemd.timer` exists but its paired `.service` is disabled.

---

## 9. Logging

```bash
journalctl -u <name>
journalctl -p err --since "15 min ago"
journalctl --disk-usage
systemctl status systemd-journald
systemctl status rsyslog
```

**Common faults:**

- `journald` configured with `Storage=volatile` when persistence is expected — logs vanish on reboot, `/var/log/journal` never gets created.
- Journal or `/var/log` partition full — logging silently stops or gets rate-limited.
- `rsyslog` down when a task specifically expects entries in `/var/log/messages` or a custom log file rather than just the journal.

---

## 10. User Access (your notes, expanded)

```bash
passwd -S username
chage -l username
id username
groups username
grep username /etc/passwd
```

|Shell|Behavior|
|---|---|
|`/bin/bash`|Interactive login allowed|
|`/usr/bin/bash`|Same file as `/bin/bash` on RHEL 9 (usr-merge symlink)|
|`/usr/sbin/nologin`|Login refused, prints a message ("This account is currently not available")|
|`/bin/false`|Login fails immediately, no message at all|

**Common faults:**

- Account exists but locked (`passwd -S` shows `L`) or expired (`chage -l`).
- Right password, wrong shell — assigned `/sbin/nologin` when interactive login was actually needed.
- User missing from a group that a file/service permission depends on.
- Home directory ownership wrong, so login succeeds but the user can't do anything once in.

---

## 11. System Time

```bash
timedatectl status
chronyc tracking
chronyc sources
systemctl status chronyd
```

**Common faults:**

- `chronyd` not running — clock drifts, and anything relying on time sync (auth tokens, TLS, scheduled jobs, log correlation) starts misbehaving in ways that don't obviously look like a time problem.
- Wrong or unreachable NTP source configured in `/etc/chrony.conf`.
- `timedatectl` shows `NTP synchronized: no` — this is the first thing to check when timestamps look "off" anywhere else in the system.

---

## 12. Software Repositories

```bash
dnf repolist
dnf repolist all
cat /etc/yum.repos.d/*.repo
dnf clean all && dnf makecache
```

**Common faults:**

- A repo is disabled (`enabled=0`) when a package install task depends on it.
- Wrong `baseurl` — points somewhere unreachable or nonexistent.
- Stale cached metadata makes a package look "not found" when it actually exists — `dnf clean all && dnf makecache` before concluding a repo is genuinely broken.

---

## 13. Process Limits

```bash
ulimit -a
systemctl show <name> -p LimitNOFILE
cat /etc/security/limits.conf
```

**Common faults:**

- A service unit has an overly tight `LimitNOFILE=` and starts throwing "too many open files" under any real load.
- `/etc/security/limits.conf` restricts a user's `nproc` low enough that normal operations (or even login shells) start failing to fork.
- Fix via an override rather than editing shipped files:
    
    ```ini
    [Service]LimitNOFILE=65536
    ```
    
    then `systemctl daemon-reload && systemctl restart <name>`.

---

## 14. SELinux (enforcing this round)

```bash
getenforce
ls -Z /path
ps -Z
ausearch -m avc -ts recent
audit2why < /var/log/audit/audit.log
```

**Common faults:**

- **`cp` vs `mv` gotcha**: files copied into a directory with `cp` normally inherit the _destination directory's_ context. Files moved with `mv` on the same filesystem keep their _original_ context (it's just a rename, no new inode). So a file `mv`'d into `/var/www/html` can silently carry the wrong label even though `ls -la` permissions look completely fine.
    
    ```bash
    restorecon -Rv /path                          # reset to the policy-defined defaultsemanage fcontext -a -t httpd_sys_content_t '/custom/path(/.*)?'   # if the path itself is nonstandard
    ```
    
- Correct file context, but a boolean is off (e.g. httpd can't connect out to a database):
    
    ```bash
    getsebool -a | grep httpdsetsebool -P httpd_can_network_connect on
    ```
    
- Custom port not labeled (see Web Services section above) — `semanage port`.
- Reflex fix to avoid: `setenforce 0`. It "fixes" the symptom and hides the actual fault — the graders will notice, and the study guide is explicit that SELinux is enforcing on purpose this round.

---

## Quick Reference — Order of Operations Per Fault

1. `systemctl status <name>` — read the `Active:` line and the inline log tail.
2. `journalctl -u <name> -xe` — if status alone isn't enough context.
3. Is it network-facing? → `ss -tulnp`.
4. Permissions look fine but it still fails? → SELinux (`ls -Z`, `ausearch -m avc -ts recent`) before anything else.
5. Fix one directive/setting at a time, `daemon-reload` + restart if it's a unit change, then re-verify before moving to the next fault — they can stack.