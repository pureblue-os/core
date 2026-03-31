# Pureblue Core

A systemd-free, immutable Linux distribution built with OSTree.

## What is this?

Pureblue Core is a from-scratch immutable Linux distribution. Unlike the main
Pureblue project (which builds on Fedora), Core starts with Alpine Linux and
uses OSTree for atomic updates—without systemd.

**Key differences from Fedora-based Pureblue:**

- No systemd (uses OpenRC instead)
- No Fedora base (uses Alpine Linux)
- OSTree-native from the ground up
- Minimal, focused, and completely under our control

## Status

**Experimental / Early Development**

This is a learning project and proof of concept. The goal is to understand every
layer of a modern Linux desktop by building it ourselves.

## Architecture

```
┌─────────────────────────────────────┐
│         Desktop Environment         │ (Future: Custom DE)
├─────────────────────────────────────┤
│      GNOME 48 (with elogind)        │ (Phase 2)
├─────────────────────────────────────┤
│    Base System (Alpine + OpenRC)    │ (Phase 1 - Current)
├─────────────────────────────────────┤
│         OSTree + Bootloader         │
└─────────────────────────────────────┘
```

## Roadmap

### Phase 1: Core System ✅ In Progress

- [ ] Bootstrap Alpine root filesystem
- [ ] Create OSTree repository and commit
- [ ] Generate initramfs with OSTree support
- [ ] Configure bootloader (syslinux/GRUB/limine)
- [ ] Build bootable disk image
- [ ] Implement upgrade mechanism

### Phase 2: GNOME Desktop

- [ ] Integrate elogind (logind replacement)
- [ ] Add GNOME 48 components
- [ ] Configure Wayland session
- [ ] Test and stabilize

### Phase 3: Custom Desktop Environment

- [ ] Design DE architecture
- [ ] Build Wayland compositor
- [ ] Create core components (panel, settings, etc.)
- [ ] Iterate based on usage

## Documentation

- [PROPOSAL.md](PROPOSAL.md) - Full technical architecture and design decisions

## Building

_Coming soon_

## License

GPL-2.1 - See [LICENSE](LICENSE) for details
