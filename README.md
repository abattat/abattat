# Hi, I'm Andrew 👋

<img
  align="right"
  src="https://github.com/user-attachments/assets/32e743fa-2458-4c2a-8710-ff6dace30ba1"
  alt="IC-OS architecture"
  width="200">

I'm a systems software engineer specializing in distributed systems, operating systems, and confidential computing.

For the past four years I worked at DFINITY building the operating systems that power the Internet Computer, a globally distributed blockchain running on 1,000+ independently operated nodes. My team owned the full node lifecycle — onboarding hardware, the custom Ubuntu-based OS, secure protocol upgrades, fleet management, and disaster recovery — and I worked mostly in Rust on low-level systems, virtualization, networking, and security.

Most of the code is in the public [`dfinity/ic`](https://github.com/dfinity/ic) monorepo — [my merged pull requests →](https://github.com/dfinity/ic/pulls?q=is%3Apr+author%3Aandrewbattat+is%3Amerged)

## IC-OS

**IC-OS** is the custom operating system stack that powers every Internet Computer node. Rather than deploying a stock Linux distribution, each node runs a hardened, reproducible Ubuntu-based operating system designed specifically for the Internet Computer.

The stack consists of three layers:

- **SetupOS** — first-boot installer that validates hardware, partitions disks, and installs the operating system
- **HostOS** — minimal bare-metal operating system that launches and supervises the GuestOS virtual machine while remaining outside the protocol's trust boundary
- **GuestOS** — trusted virtual machine that runs the Internet Computer protocol and hosts the system's trust boundary

The projects below are representative examples of my work on IC-OS.

---

### Selected Projects

#### Decentralized Root Subnet Recovery

I designed and shipped the recovery mechanism for the Internet Computer's root governance subnet (NNS). The system allows independent node operators to collaboratively recover the network without introducing a trusted recovery authority, preserving decentralization even during catastrophic failures.

**Pull Requests**
- [HostOS recovery upgrader #4993](https://github.com/dfinity/ic/pull/4993)
- [Recovery engine (#5355)](https://github.com/dfinity/ic/pull/5355)
- [Nested VM integration tests (#5968)](https://github.com/dfinity/ic/pull/5968)
- [Recovery console UX (#7655)](https://github.com/dfinity/ic/pull/7655)

---

#### Hardware-backed Node Attestation (AMD SEV-SNP)

As the fleet adopted AMD SEV-SNP trusted execution environments, each node's hardware chip ID became the network's root of trust (the hardware identity to which every other guarantee is anchored). I built the attestation path that makes the chip ID proven, not claimed: a registering node must present an AMD-signed report, with its own identity key bound into the report so a stolen report can't be replayed by another node, verified on-chain by the registry canister before the chip ID is accepted. 

This work established the root of trust for the Internet Computer's confidential computing rollout using AMD SEV-SNP. Every later feature that trusts that value (recovery authorization, hardware-bound sealing, node uniqueness) inherits the guarantee.

**Pull Requests**
- [Verified hardware attestation during node registration (#8454)](https://github.com/dfinity/ic/pull/8454)
- [Blessed-version verification during registration (#8585)](https://github.com/dfinity/ic/pull/8585)

---

## Technologies

**Languages** — Rust · Python · Bash

**Systems & tooling** — Linux · QEMU · systemd · A/B partitioning · systemd-networkd · Bazel · Docker · Prometheus · Grafana · SEV-SNP · remote attestation
