# Pureblue OS: OSTree Without SystemD

## Vision

Build a lightweight, immutable Linux distribution using OSTree as the update
mechanism, but **without systemd**. Start with GNOME pre-49 (which can run
without systemd), then evolve toward a custom desktop environment.

## Why?

- **Systemd-less**: Lighter init system (OpenRC, runit, or s6)
- **Full control**: Own the entire stack from bootloader to desktop
- **Learning project**: Understand every component of a modern Linux desktop
- **Future DE**: Build a less controversial, more user-respecting desktop
  environment

## Reference Projects

1. **[ostree-build-fs](https://github.com/starnight/ostree-build-fs)** - Alpine
   Linux + OSTree + OpenRC
   - Uses syslinux bootloader
   - Perfect base architecture

2. **[vyy](https://github.com/myyc/vyy)** - Arch + OSTree
   - Shows how to adapt traditional distro to OSTree
   - `/usr` merge, initramfs handling

## Technical Architecture

### Phase 1: Core System (No DE)

**Base Distribution**: Alpine Linux or Void Linux

- Both use non-systemd init (OpenRC/runit)
- Minimal, well-maintained
- Good package selection

**Boot Process**:

```
Bootloader (syslinux/grub/limine)
    ↓
Kernel + initramfs (with ostree module)
    ↓
OSTree deployment mounted at /
    ↓
OpenRC/runit init
```

**Filesystem Layout**:

```
/                    # OSTree deployment (read-only base)
├── usr/             # All system files
├── etc/             # Mutable config (symlink farm)
├── var/             # Mutable data
├── sysroot/         # OSTree repository
│   ├── ostree/
│   └── boot/
└── boot/            # Bootloader files
```

**Key Components**:

- [ ] OSTree repository server (static HTTP)
- [ ] Build scripts (containerized)
- [ ] Initramfs generation with ostree module
- [ ] Bootloader configuration
- [ ] Update mechanism (`ostree admin upgrade`)

### Phase 2: GNOME Desktop (Pre-49)

**Why Pre-GNOME 49?**

- GNOME 49+ hard-requires systemd
- GNOME 48 and earlier can run with elogind (logind replacement)
- Provides functional desktop while building custom DE

**Components**:

- [ ] elogind (logind replacement for seat management)
- [ ] PipeWire (audio/video)
- [ ] Wayland compositor (mutter from pre-49)
- [ ] Basic GNOME session

**Challenges**:

- Some GNOME services may need systemd unit replacements
- Hotplug, power management need OpenRC equivalents
- May need to patch some components

### Phase 3: Custom Desktop Environment

**Philosophy**:

- Simple, fast, predictable
- User-respecting (no "we know better" design)
- Modular components
- Wayland-native from start

**Core Components**:

- [ ] Wayland compositor (wlroots-based or custom)
- [ ] Panel/taskbar
- [ ] Window manager
- [ ] Settings daemon
- [ ] File manager
- [ ] Terminal
- [ ] Application launcher

## Build Pipeline

```
┌─────────────────┐
│  Alpine RootFS  │  (bootstrap from minirootfs)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Add Packages   │  (kernel, firmware, utilities)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   OSTree Commit │  (commit with metadata)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Push to Repo   │  (via ostree-push or rsync)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Image Builder  │  (deploy commit to disk image)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Bootable ISO   │  (QCOW2, raw, or ISO format)
└─────────────────┘
```

## File Structure

```
pureblue-ostree/
├── README.md
├── docs/
│   ├── architecture.md
│   ├── building.md
│   └── installation.md
├── scripts/
│   ├── build-rootfs.sh      # Bootstrap Alpine/Void
│   ├── ostree-commit.sh     # Create OSTree commit
│   ├── ostree-push.sh       # Push to repo server
│   ├── make-image.sh        # Create bootable image
│   └── dev-shell.sh         # Enter build environment
├── docker/
│   ├── Dockerfile.build     # Build environment
│   ├── docker-compose.yml   # Local repo server
│   └── entrypoint.sh
├── configs/
│   ├── packages/            # Package lists per variant
│   ├── kernel/              # Kernel config fragments
│   ├── initramfs/           # Initramfs hooks
│   ├── openrc/              # OpenRC service files
│   └── boot/                # Bootloader configs
├── variants/
│   ├── minimal/             # Base system only
│   ├── gnome-48/            # GNOME 48 desktop
│   └── devel/               # Development tools
└── .github/
    └── workflows/
        └── build.yml        # CI/CD pipeline
```

## Open Questions

1. **Base distro**: Alpine (musl libc) or Void (glibc)?
   - Alpine: Smaller, but musl can cause issues
   - Void: glibc, larger but more compatible

2. **Init system**: OpenRC or runit?
   - OpenRC: Service dependencies, familiar
   - runit: Simpler, faster

3. **Bootloader**: syslinux, GRUB, or limine?
   - syslinux: Simple, proven
   - GRUB: More features, UEFI support
   - limine: Modern, simple config

4. **Repo hosting**: Self-hosted or public?
   - GitHub Pages + Actions for CI
   - Self-hosted for production

## Next Steps

1. **Create new repo**: `pureblue-os/pureblue-ostree` or similar
2. **Bootstrap Alpine**: Get minimal booting system
3. **OSTree integration**: Make it immutable
4. **GNOME 48**: Add desktop environment
5. **Custom DE**: Start with compositor basics

## Resources

- [OSTree documentation](https://ostreedev.github.io/ostree/)
- [Alpine OSTree](https://wiki.alpinelinux.org/wiki/OSTree)
- [elogind](https://github.com/elogind/elogind) - systemd-logind replacement
- [wlroots](https://gitlab.freedesktop.org/wlroots/wlroots/) - Wayland
  compositor library

---

**Note**: This is a significant undertaking. Start with the minimal bootable
system first. Get OSTree working. Then add desktop. Then iterate on DE.
