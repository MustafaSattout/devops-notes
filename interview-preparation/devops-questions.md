## Linux Fundamentals & Filesystem

**Q: What's the difference between Linux and a Linux distribution?**
Linux, strictly speaking, is the kernel — the core program managing hardware, memory, and processes. A distribution (Ubuntu, RHEL, Arch) packages that kernel with userland tools: a package manager, init system, shell, and utilities. Same kernel, different tooling and conventions around it.

**Q: Explain the "everything is a file" philosophy in Linux.**
Devices, processes, and kernel data are all exposed through the filesystem interface rather than needing separate APIs. `/dev/sda` represents a disk, `/proc/1234` represents a running process. This lets a small set of tools (`cat`, `read`, `write`) work uniformly across very different kinds of resources.

**Q: What's the difference between `/root` and `/`?**
`/` is the root of the entire filesystem tree — everything lives under it. `/root` is just the home directory of the root user, analogous to `/home/username` for a regular user. Confusing the two is a classic and dangerous mistake, especially with destructive commands.

**Q: Are `/proc` and `/sys` real files on disk?**
No — they're virtual filesystems generated live by the kernel to expose runtime information (running processes, CPU info, kernel parameters). Tools like `top`, `free`, and `ps` read from `/proc` under the hood rather than some separate hidden data source.

**Q: A service is running on a server but you don't know where its config file is. How do you find it, without just guessing the default path?**
Check the running process itself — `ps aux | grep <service>` often shows the exact config path passed at launch (e.g., `-c /etc/nginx/nginx.conf`), which is reliable even if someone moved it from the default. Confirm with the package manager (`dpkg -l` / `rpm -qa`) to see if it's package-managed, and fall back to FHS convention (`/etc/<service>/`) only if the process listing doesn't reveal it.

**Q: Why do `/bin` and `/sbin` exist as separate directories, and why are they often symlinked together on modern systems?**
Historically, `/sbin` held admin/root-privileged commands needed early in boot before `/usr` was mounted, while `/bin` held commands any user needs. Modern distros now mount `/usr` early enough that this split is unnecessary, so `/bin` and `/sbin` are typically just symlinks into `/usr/bin` and `/usr/sbin` — the naming convention survives for familiarity, not technical necessity.