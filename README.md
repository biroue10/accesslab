# accesslab — Linux User & Access Control

> A hands-on implementation of a company access control system on RHEL, covering users, groups, permissions, ACLs, and password policies.

---

## About This Project

A simulated company environment with multiple teams, shared directories, and fine-grained access control. Every configuration was applied manually, verified, and documented.

**Topics covered:** `useradd` · `groupadd` · `chmod` · `chown` · `umask` · `setuid` · `setgid` · `sticky bit` · `setfacl` · `getfacl` · `chage` · PAM

---

## Company Structure (Simulated)

```
Acme Corp
├── devteam      (developers — read/write to /srv/dev)
├── opsteam      (operations — read/write to /srv/ops)
└── shared       (both teams — /srv/shared, ACL-controlled)
```

---

## Build Progress

| # | Component | Status |
|---|-----------|--------|
| 01 | Users & Groups | ⏳ Pending |
| 02 | Standard Permissions | ⏳ Pending |
| 03 | Special Bits (setuid/setgid/sticky) | ⏳ Pending |
| 04 | ACLs | ⏳ Pending |
| 05 | Password Policy | ⏳ Pending |

---

*Built as part of RHCSA EX200 exam preparation.*
