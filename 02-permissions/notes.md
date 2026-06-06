# 02 — Standard Permissions

## Scenario
Lock down team directories so only the right team can access them, and configure correct file permissions using umask.

## Requirements Completed
1. `/srv/dev` — accessible only by `devteam` (mode 770)
2. `/srv/ops` — accessible only by `opsteam` (mode 770)
3. Files created by alice inherit her primary group (`devteam`)
4. umask set to `027` — no write for group, no access for others
5. New files reflect the stricter umask
6. `/etc/shadow` has `----------` — explained why root can still access it

## Commands Used

```bash
# Set directory permissions (770 = rwxrwx---)
sudo chmod 770 /srv/dev
sudo chmod 770 /srv/ops

# Create file as alice and check group ownership
su - alice
touch /srv/dev/readme.txt
ls -l /srv/dev/readme.txt
# Output: -rw-rw-r--. 1 alice devteam 0 ... (default umask 022)

# Set stricter umask
umask 027
umask
# Output: 0027

# New file with new umask
touch /srv/dev/newfile.txt
ls -l /srv/dev/newfile.txt
# Output: -rw-r-----. 1 alice devteam 0 ... (umask 027 applied)

# Check /etc/shadow permissions
ls -la /etc/shadow
# Output: ----------. 1 root root ...
```

## Permission Octal Reference

| Octal | Binary | Symbolic | Meaning |
|-------|--------|----------|---------|
| 7 | 111 | rwx | Read, write, execute |
| 6 | 110 | rw- | Read, write |
| 5 | 101 | r-x | Read, execute |
| 4 | 100 | r-- | Read only |
| 0 | 000 | --- | No access |

## How umask Works

```
Default file permissions: 666 (rw-rw-rw-)
umask 027:              - 027
                        = 640 (rw-r-----)

Default dir permissions:  777 (rwxrwxrwx)
umask 027:              - 027
                        = 750 (rwxr-x---)
```

umask **removes** bits from the default — it's a mask, not a direct assignment.

## Why /etc/shadow is ----------

Root (uid=0) bypasses all standard permission checks on Linux. The `----------` exists to block every non-root user and process from reading password hashes. Root can always access any file regardless of permission bits.

## Gotchas
- `umask` set in a shell session is **not persistent** — to make it permanent, add it to `~/.bashrc` or `/etc/profile`.
- Execute bit on a **directory** means "enter" (cd into it) — not run files inside it.
- `chmod 770` without correct group ownership does nothing useful — always verify with `ls -la`.
- New files do **not** get the execute bit by default, even in a directory that has it.

## What I Learned
- `chmod` sets permissions; `chown` sets ownership — both are needed together.
- umask defines the **default** permissions subtracted from new files/directories.
- Root ignores permission bits — `/etc/shadow` is `----------` to protect against all non-root access.
- Files inherit the creator's **primary group**, not their secondary groups.
