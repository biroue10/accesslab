# 03 — Special Permission Bits

## Scenario
Configure `/srv/dev` so that files always inherit the devteam group (setgid) and users can't delete each other's work (sticky bit). Also identify real setuid binaries on the system.

## The Three Special Bits

| Bit | Symbol | On a file | On a directory |
|-----|--------|-----------|----------------|
| setuid | `s` (owner pos) | Runs as file owner | No effect on Linux |
| setgid | `s` (group pos) | Runs as file group | New files inherit directory's group |
| sticky | `t` (others pos) | No effect | Users can only delete their own files |

## Commands Used

```bash
# 1. Find a real setuid binary
ls -l /usr/bin/passwd
# Output: -rwsr-xr-x. 1 root root ... /usr/bin/passwd
# The 's' in owner position = setuid

# 2. Set setgid on /srv/dev
sudo chmod g+s /srv/dev
ls -la /srv/dev
# Output: drwsrws---

# 3. Test setgid — add charlie to devteam, create file as charlie
sudo usermod -aG devteam charlie
su - charlie
touch /srv/dev/charlie-test.txt
ls -l /srv/dev/charlie-test.txt
# Output: -rw-rw-r--. 1 charlie devteam 0 ...
# Group is 'devteam' even though charlie's primary group is 'opsteam'
exit

# 4. Set sticky bit on /srv/dev
sudo chmod +t /srv/dev
ls -la /srv/
# Output: drwsrws--T (T = sticky set, no execute for others)

# 5. Test sticky bit — charlie tries to delete alice's file
su - charlie
rm /srv/dev/readme.txt
# Output: rm: cannot remove: Operation not permitted
exit
```

## Reading Special Bits in ls -l

```
drwsrws--T
│││││││││└── sticky bit (T=set/no-exec, t=set/exec)
│││││││└┘─── others: no access
││││└┴─────── group: rw + setgid (s)
│└┴──────────── owner: rwx + setuid (s, no effect on dir)
└────────────── directory
```

## chmod Shortcuts

```bash
chmod u+s file     # set setuid
chmod g+s dir      # set setgid
chmod +t dir       # set sticky bit
chmod 2770 dir     # setgid + rwxrwx--- (2 = setgid in octal)
chmod 1770 dir     # sticky + rwxrwx--- (1 = sticky in octal)
chmod 3770 dir     # setgid + sticky + rwxrwx--- (3 = both)
```

## Gotchas
- `chmod +s` sets BOTH setuid and setgid — use `chmod g+s` to set only setgid on directories.
- Setuid on a directory is **ignored** on Linux — only meaningful on executable files.
- Uppercase `T` = sticky set but no execute for others. Lowercase `t` = sticky set with execute.
- The sticky bit only protects against deletion — users can still **read and overwrite** files they don't own if permissions allow.
- setgid only applies to **new** files — it does not retroactively relabel existing files.

## What I Learned
- setgid on directories is essential for team shared directories — it ensures files always belong to the team group regardless of who creates them.
- The sticky bit is why `/tmp` works safely — everyone can write but no one can delete others' files.
- Real-world setuid binaries like `passwd` allow regular users to perform privileged operations safely.
