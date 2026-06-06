# 01 — Users & Groups

## Scenario
Acme Corp hired new staff. Create user accounts, organize them into teams, and set up team directories before their first day.

## Company Structure
- `devteam` — development team: alice, bob
- `opsteam` — operations team: charlie
- `alice` also has secondary membership in `opsteam` (cross-team role)

## Commands Used

```bash
# 1. Create groups
sudo groupadd devteam
sudo groupadd opsteam

# 2-4. Create users with primary groups
sudo useradd -m -g devteam alice
sudo useradd -m -g devteam bob
sudo useradd -m -g opsteam charlie

# 5. Add alice to opsteam as secondary group
sudo usermod -aG opsteam alice

# 6. Set passwords
sudo passwd alice
sudo passwd bob
sudo passwd charlie

# 7-8. Create team directories with group ownership
sudo mkdir /srv/dev /srv/ops
sudo chown :devteam /srv/dev
sudo chown :opsteam /srv/ops

# 9. Verify
id alice
# uid=5065(alice) gid=6010(devteam) groups=6010(devteam),6011(opsteam)
id bob
# uid=5066(bob) gid=6010(devteam) groups=6010(devteam)
id charlie
# uid=5067(charlie) gid=6011(opsteam) groups=6011(opsteam)
```

## Key Concepts

| Concept | Detail |
|---------|--------|
| Primary group | Stamped on files the user creates — set with `useradd -g` |
| Secondary group | Extra access — added with `usermod -aG` |
| `chown :group /dir` | Change group owner without touching file owner |
| `/etc/passwd` | User account database |
| `/etc/group` | Group membership database |

## Gotchas
- `usermod -G` without `-a` removes all existing secondary groups — always use `-aG`.
- Primary group cannot be removed while it is a user's primary group.
- New files inherit the user's **primary group** by default — secondary groups give access but don't stamp files.

## What I Learned
- Every user has exactly one primary group and zero or more secondary groups.
- `id username` is the fastest verification — shows uid, primary gid, and all groups.
- Group ownership on directories (`chown :group`) is the foundation of team-based access control.
