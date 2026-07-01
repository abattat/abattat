# Hi, I'm Andrew 👋

<img
  align="right"
  src="https://github.com/user-attachments/assets/32e743fa-2458-4c2a-8710-ff6dace30ba1"
  alt="IC-OS architecture"
  width="165">

I'm a systems software engineer specializing in distributed systems, operating systems, and confidential computing.

For the past four years I worked at DFINITY building the operating systems that power the Internet Computer, a globally distributed blockchain running on 1,000+ independently operated nodes. My team owned the full node lifecycle — onboarding hardware, the custom Ubuntu-based OS, secure protocol upgrades, fleet management, and disaster recovery — and I worked mostly in Rust on low-level systems, virtualization, networking, and security.

Most of the code is in the public [`dfinity/ic`](https://github.com/dfinity/ic) monorepo — [my merged pull requests →](https://github.com/dfinity/ic/pulls?q=is%3Apr+author%3Aandrewbattat+is%3Amerged)

## IC-OS

**IC-OS** is the custom operating system stack that powers every Internet Computer node. Rather than deploying a stock Linux distribution, each node runs a hardened, reproducible Ubuntu-based operating system designed specifically for the Internet Computer.

The stack consists of three layers:

- **SetupOS** — first-boot installer that validates hardware, partitions disks, and installs the operating system
- **HostOS** — minimal bare-metal operating system that launches and supervises the GuestOS virtual machine while remaining outside the protocol's trust boundary
- **GuestOS** — trusted virtual machine that runs the Internet Computer protocol and hosts the system's trust boundary

Below are representative examples of my work on **IC-OS**.

---

### Disaster Recovery Work

#### Decentralized Root Subnet Recovery

I designed and shipped the recovery mechanism for the Internet Computer's root governance subnet. The system allows independent node operators to collaboratively recover the network without introducing a trusted recovery authority, preserving decentralization even during catastrophic failures. Recovery succeeds only when a supermajority of the subnet's independent operators manually participate, turning the recovery process itself into a decentralized consensus mechanism. I designed both the recovery protocol and the supporting node software.

**Relevant PRs**

- [HostOS recovery upgrader (#4993)](https://github.com/dfinity/ic/pull/4993)
- [Recovery engine (#5355)](https://github.com/dfinity/ic/pull/5355)
- [Nested VM integration tests (#5968)](https://github.com/dfinity/ic/pull/5968)
- [Recovery console UX (#7655)](https://github.com/dfinity/ic/pull/7655)

#### API Boundary Node Recovery

Access to the governance subnet runs through the API boundary nodes, which are themselves managed by the governance subnet. That circular dependency means a failure of the boundary node layer can leave the community unable to reach the governance subnet to vote in a fix for the boundary nodes. I built a recovery path that breaks the cycle: a standalone container packaging ic-boundary and ic-registry-replicator that runs independently of the governance subnet-managed fleet, so an operator can stand up an API boundary node out of band and restore access to governance.

The change ships with a reproducible container build and a CI test that boots the image and proxies a live canister query end-to-end, verifying the recovery path still works before it is ever needed.

**Relevant PRs**

- [API boundary node recovery (#8832)](https://github.com/dfinity/ic/pull/8832)

---

### Confidential Computing Work

#### Hardware-backed Node Attestation (AMD SEV-SNP)

As the fleet adopted AMD SEV-SNP trusted execution environments, each node's hardware chip ID became the network's root of trust (the hardware identity to which every other guarantee is anchored). I built the attestation path that makes the chip ID proven, not claimed: a registering node must present an AMD-signed report, with its own identity key bound into the report so a stolen report can't be replayed by another node. Instead, it's verified on-chain by the registry canister before the chip ID is accepted.

This work established the root of trust for the Internet Computer's confidential computing rollout. Every later feature that trusts the registry value inherits the guarantee.

**Relevant PRs**

- [Node registration attestation (#8454)](https://github.com/dfinity/ic/pull/8454)
- [Blessed-version verification (#8585)](https://github.com/dfinity/ic/pull/8585)

#### Secure GuestOS Upgrades (AMD SEV-SNP)

Upgrading a SEV-SNP GuestOS breaks access to the node's encrypted data by design. The disk encryption key is derived from the GuestOS launch measurement, so a new protocol version produces a new launch measurement and a different key, leaving it unable to decrypt the existing data store. I co-designed the upgrade process that transfers this access safely: the old and new GuestOS run side by side and attest each other over SEV-SNP, and once the new GuestOS is verified against the governance subnet registry, the old one hands over the disk encryption key across an encrypted channel. The new GuestOS then re-seals the store under its own measurement.

This preserves the confidentiality of node state across upgrades, and guarantees that only a verified, registry-approved GuestOS can obtain the key.

**Reference:** [Trusted Execution Environments: TEE-enabled GuestOS upgrades](https://learn.internetcomputer.org/hc/en-us/articles/46124920595988-Trusted-Execution-Environments#:~:text=Upgrades%20of%20TEE%2DEnabled%20GuestOS)

---

### Performance

#### API Boundary Node Cache

Boundary nodes forward subnet `read_state` lookups to the replicas, and the same requests repeat constantly. I added an in-memory cache in the boundary node that serves these repeated lookups directly, cutting redundant load on the replicas. Only the safe read-only paths are cached, entries expire on a short TTL, and memory usage is bounded and tracked.

**Relevant PRs**

- [Subnet read_state cache (#9239)](https://github.com/dfinity/ic/pull/9239)

---

## Technologies

## Technologies

- **Languages:** Rust · Python · C++ · Bash
- **Systems:** Linux · QEMU · systemd · systemd-networkd · A/B partitioning
- **Infrastructure:** Docker · Bazel · Prometheus · Grafana
- **Security:** AMD SEV-SNP · Remote Attestation
