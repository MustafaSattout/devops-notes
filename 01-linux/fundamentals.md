## Linux Architecture Basics

- **Kernel**: core program managing hardware (CPU, memory, devices). Strictly, "Linux" = the kernel.
- **Distribution**: kernel + userland (package manager, init system, shell, utilities). E.g. Ubuntu, RHEL, Arch.
- **Shell**: command interpreter (bash, zsh) — a program *on top of* Linux, not Linux itself.

### Layered model
Hardware → Kernel → Userland tools → Shell → Applications
Everything above the kernel makes **system calls** into it to do real work.

## Filesystem Hierarchy Standard (FHS)

| Dir | Purpose |
|---|---|
| `/etc` | System-wide config files |
| `/var` | Variable data: logs, caches, mail |
| `/home` | User personal directories |
| `/root` | Root user's home (⚠️ NOT the same as `/`) |
| `/proc`, `/sys` | Virtual, kernel-generated info — not real files on disk |
| `/bin`, `/sbin` | Essential user vs. admin commands (often symlinked to `/usr/bin`, `/usr/sbin` on modern distros) |
| `/usr` | Installed software, libraries |
| `/opt` | Third-party/optional software |

**Remember:** `/root` ≠ `/`. Confusing them with a destructive command is a classic, costly mistake.

## Troubleshooting technique: finding an unfamiliar service's config/logs
1. Check if it's package-managed: `dpkg -l | grep <svc>` (Debian) or `rpm -qa | grep <svc>` (RHEL)
2. Check the running process for the actual config path in use: `ps aux | grep <svc>`
3. Fall back to FHS convention: config in `/etc/<svc>/`, logs in `/var/log/<svc>/`