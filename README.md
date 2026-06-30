# Awesome Agent Sandbox

Portable, lightweight, self-contained sandboxes for AI coding agents - microVMs, containers, and isolation harnesses.

## MicroVMs

- **[smolvm](https://github.com/smol-machines/smolvm)** - Hardware-isolated VMs. Boots <200ms via Hypervisor.framework/KVM/WHP. Pulls OCI images, no Docker daemon needed. Packs into a single `.smolmachine` file. Elastic memory, opt-in networking, SSH agent forwarding (keys never touch guest), GPU acceleration, declarative Smolfile (TOML). 4 vCPUs / 8 GiB RAM default, idle vCPUs sleep at near-zero cost.

- **[microsandbox](https://github.com/superradcompany/microsandbox)** - Embeddable libkrun microVM SDK with Rust, Python, TypeScript, Go bindings. <100ms boot, OCI-compatible (Docker Hub/GHCR images). MCP server so agents can create their own sandboxes. "Secrets that can't leak" - keys never enter the VM. Copy-on-write rootfs. Linux KVM / macOS Apple Silicon.

- **[CelestoAI/SmolVM](https://github.com/CelestoAI/SmolVM)** - Disposable VMs for AI agents via Firecracker, QEMU, or libkrun. ~500ms boot. Mounts host folders read-only by default (`--writable-mounts` to allow writes). Browser sandbox with CDP/VNC. Pre-installed Claude Code, Codex, Pi with forwarded git credentials. Snapshots, network egress controls, Windows guest support.

- **[agent-vm](https://github.com/wirenboard/agent-vm)** - Per-project libkrun microVMs. ~2s boot. Bind-mounts project at host path. OAuth tokens never enter the VM - a TLS-intercept proxy substitutes real bearer tokens for placeholders on outbound requests. Per-launch GitHub repo allow-listing. Chrome DevTools MCP with CA injection. Linux, requires `/dev/kvm`.

- **[drydock](https://github.com/sricola/drydock)** - Apple container runtime (macOS 26+, Apple Silicon). Per-task throwaway VMs - nothing survives between tasks. Agent never gets your real API key (short-lived, budget-capped token only). Deny-by-default egress with allowlist for package registries. Only a `git diff` leaves the sandbox; nothing reaches origin without approval. Red-team tests included.

- **[CubeSandbox](https://github.com/tektite-io/CubeSandbox)** - Tencent-built KVM microVMs via RustVMM. <60ms cold start, <5MB overhead per instance. E2B SDK-compatible drop-in. Copy-on-write snapshots, clone, rollback at millisecond granularity. eBPF-based network isolation. Linux x86_64 + KVM. Production-validated at Tencent Cloud.

- **[sand](https://github.com/banksean/sand)** - Apple container CLI (macOS 26+, Apple Silicon). APFS copy-on-write clones of your project. Commands: `sand new -a claude`, `sand shell`, `sand git status`. Native Apple virtualization, no Docker dependency.

- **[HyperAgent](https://github.com/hyperlight-dev/hyperagent)** - Hyperlight micro-VM runtime (KVM/MSHV/WHP). Model writes JavaScript handlers that run in hardware-isolated sandboxes - no direct filesystem, network, shell, or process access unless explicitly granted via plugins. Path-jailed fs-read/fs-write plugins. Domain-scoped fetch with SSRF checks. GitHub Copilot SDK auth.

- **[strangeClaw](https://github.com/itsthestranger/strangeClaw)** - Self-hosted autonomous agent running inside Firecracker microVMs. Host-side request broker checks policy, injects credentials, redacts responses - agent only sees denials, never holds real API keys. Two modes: `yolo` (direct host) and `fire` (VM isolation).

## Container Sandboxes

- **[agent-sandbox](https://github.com/thomaspeklak/agent-sandbox)** - Rust CLI (`ags`), rootless Podman containers. Supports Claude, Codex, Gemini, OpenCode, Pi, and interactive shells. Hardened lockdown mode for untrusted repos (no secrets, no SSH agent, no host bridges). Clipboard bridge, OAuth proxy, browser sidecar, host UI via Glimpse. TOML config with per-repo overlays.

- **[yolobox](https://github.com/finbarr/yolobox)** - Docker sandbox. Only the project directory is mounted at its real path; home directory stays on the host. Agent gets full permissions and sudo inside the container. Wraps agent CLIs to skip permission prompts. `.yolobox.toml` for extra packages. Flags: `--no-network`, `--readonly-project`, `fork`. Protects against accidents, not container-escape exploits.

- **[hort](https://github.com/johnsilverio/hort)** - Rust, daemonless, no Docker. Each box is a disposable git worktree + in-process OCI container (libcontainer). Single static binary. Run multiple agents in parallel against one worktree. Read-only dotfiles mounted so the environment feels familiar but can't be altered. Egress allowlist. Built for Claude Code, Aider, Gemini CLI.

- **[agent-sandbox](https://github.com/mattolson/agent-sandbox)** - Docker + mitmproxy sidecar. Repo directory mounted as workspace, secrets live on host and are injected by the proxy at request time - agent never sees raw API keys. iptables blocks all direct outbound; everything routes through the proxy with hostname/method/path ACLs. Supports Claude Code, Codex, Gemini, OpenCode. CLI or devcontainer mode.

- **[agent-sandbox](https://github.com/marvincaspar/agent-sandbox)** - Docker with `--cap-drop=ALL`, `--no-new-privileges`, non-root user. Optional proxy sidecar (tinyproxy) with allowlist-based domain filtering. Default-deny egress when proxy enabled. Supports Go, PHP 8.4/8.5 runtimes beyond TypeScript/Node base. Bind-mounts current directory.

- **[sandboxed.sh](https://github.com/Th0rgal/sandboxed.sh)** - Self-hosted orchestrator using systemd-nspawn containers for per-mission isolation. Multi-runtime: Claude Code, OpenCode, Codex, Gemini, Grok. Git-backed Library for skills/tools/rules/agents/MCPs. Scheduled cron-like automations, model routing with failover. Web dashboard (Next.js) + iOS app.

## VM-Based

- **[locki](https://github.com/JanPokorny/locki)** - Lima VM + Incus containers with a command bridge that filters git operations. Agents write only to namespaced branches; stashes scoped, hooks redirected. Worktree-backed storage at `~/.local/share/locki/worktrees/` - edits appear on the host instantly. Host home and `.git` invisible to the agent. Supports containers, systemd, and Kubernetes inside the sandbox. Bundles Mise. `locki ai` (interactive), `locki exec` (direct) with auto port collision avoidance.

- **[machine](https://github.com/katspaugh/machine)** - One isolated Lima VM per GitHub project (macOS 13+). Pre-provisioned with Docker, Node, Claude Code/Codex, GitHub CLI, signed git. No host filesystem mounted - repos cloned fresh inside the VM. SSH agent forwarding: private keys stay on host, VM can use them but never read them. Per-project VMs with configurable CPU/memory/disk.

- **[nilbox](https://github.com/rednakta/nilbox)** - Desktop app running a full Linux VM per project (macOS Virtualization.framework, Linux/Windows QEMU). Zero Token Architecture: agent only sees dummy env vars (`OPENAI_API_KEY=OPENAI_API_KEY`); host proxy swaps real tokens for trusted domains. Agent Firewall with human-in-the-loop domain gating (Allow Once/Always/Deny), DNS blocklists, per-provider spending caps. FUSE-over-VSOCK file mapping. Encrypted KeyStore via SQLCipher + OS keyring.

## Process Sandboxes

- **[ai-jail](https://github.com/akitaonrails/ai-jail)** - Bubblewrap + Landlock + seccomp on Linux, Seatbelt on macOS. Per-project `.ai-jail` config. Replaces `$HOME` with tmpfs, COW overlay mounts so agents can experiment without touching originals. Lockdown mode (read-only project, ephemeral home, no network). Glob-based masking for sensitive files (`.env`, `credentials.json`). Browser isolation profiles. Built for Claude Code, Codex, OpenCode.

- **[nono](https://github.com/lukehinds/nono)** - Landlock on Linux, Seatbelt on macOS. Agent gets read/write only to the current directory - SSH keys, cloud credentials, rest of disk invisible. Profile registry, composable policies (filesystem scope, network allowlisting, credential injection, L7 filtering). Snapshots with atomic rollback. Cryptographic audit trail.

- **[sandbox-runtime](https://github.com/anthropic-experimental/sandbox-runtime)** (`srt`) - Powers Claude Code's built-in sandbox. Bubblewrap + seccomp on Linux, Seatbelt on macOS. Filesystem read/write path controls with glob patterns. Domain allow/deny lists via host-side HTTP/SOCKS5 proxy. Unix socket controls. Real-time violation monitoring. CLI + Node.js library.

- **[isol8](https://github.com/mdnmdn/isol8)** **[SUSPICIOUS - see caveat below]** - Cross-platform (Landlock + namespaces on Linux, Seatbelt on macOS, Win32 hook DLL). ~70 embedded TOML profiles with inheritance and auto-detection of agent type. Deny-by-default path access at none/ro/rw levels. `$HOME` replacement with optional seed from real home. Environment sanitization. Policy introspection with dry-run mode.

## Comparison

| Tool | Isolation | Boot | Mount | Networking | State | Agents |
|------|-----------|------|-------|------------|-------|--------|
| smolvm | Hardware (HVF/KVM) | <200ms | OCI images, rootfs dirs | Opt-in, host allowlist | Persistent VMs | Any CLI |
| microsandbox | libkrun microVM | <100ms | OCI volumes | Egress controls | COW snapshots | Any CLI (SDK) |
| CelestoAI/SmolVM | Firecracker/QEMU/libkrun | ~500ms | Mount read-only, opt-in writable | Egress controls | Snapshots | Claude Code, Codex, Pi |
| agent-vm | libkrun microVM | ~2s | Bind-mount at host path | Egress groups, proxy-intercept | Per-project VM | Claude Code, Codex, OpenCode |
| drydock | Apple container | VM boot | Disposable copy, diff-only output | Deny-by-default, allowlist | Throwaway VMs | Claude Code |
| CubeSandbox | KVM (RustVMM) | <60ms | API/SDK file upload | eBPF (CubeVS) | COW snapshots, clone | E2B-compatible |
| sand | Apple container | VM boot | APFS COW clone | Host default | Disposable | Claude Code |
| HyperAgent | Hyperlight microVM | Fast | Plugin fs-read/fs-write (path-jailed) | Domain allowlist + SSRF | Per-sandbox | Copilot SDK |
| strangeClaw | Firecracker | VM boot | Via request broker | Via broker | Per-session VM | Autonomous |
| agent-sandbox (thomaspeklak) | Rootless Podman | Seconds | Bind-mount repo | Configurable bridges | Persistent volumes | Claude, Codex, Gemini, Pi, OpenCode |
| yolobox | Docker | Seconds | Project dir only, home stays on host | Optional (`--no-network`) | Persistent volumes | Claude Code, Codex, Gemini, OpenCode, Copilot, Pi |
| hort | OCI container (user namespaces) | Seconds | Git worktree (throwaway branch) | Egress allowlist | Disposable worktree | Claude Code, Aider, Gemini CLI |
| agent-sandbox (mattolson) | Docker + proxy sidecar | Seconds | Repo dir, secrets host-only | Proxy allowlist + iptables | Persistent volumes | Claude Code, Codex, Gemini, OpenCode |
| agent-sandbox (marvincaspar) | Docker (cap-drop=ALL) | Seconds | Bind-mount current dir | Optional proxy allowlist | Persistent volumes | Claude Code, Codex, Gemini |
| sandboxed.sh | systemd-nspawn | Container | Per-mission directories | Host/managed | Persistent containers | Claude Code, OpenCode, Codex, Gemini, Grok |
| locki | Lima VM + Incus | Minutes | Git worktrees, instant host sync | Bridged | Git worktrees | Claude, Codex, Gemini |
| machine | Lima VM (Apple VZ) | VM boot | No host FS mount, clone inside VM | Host-only default | Persistent VM | Claude Code, Codex |
| nilbox | Full VM (VZ/QEMU) | VM boot | FUSE-over-VSOCK | Agent Firewall (default-deny, domain gating) | Persistent VM | Claude Code, Codex, Gemini |
| ai-jail | bwrap + Landlock + seccomp | Instant | Project dir only, tmpfs home | Optional, unshared in lockdown | COW overlays | Claude Code, Codex, OpenCode |
| nono | Landlock / Seatbelt | Instant | Current dir only, rest of disk invisible | Host allowlist | Snapshots | Claude Code, Codex, Copilot |
| srt | bwrap + seccomp / Seatbelt | Instant | Path allow/deny with glob patterns | Domain allow/deny via proxy | Ephemeral | Any CLI |
| isol8 **[SUSPICIOUS]** | Landlock + namespaces / Seatbelt | Instant | Deny-by-default, per-path none/ro/rw | Deferred | Ephemeral home replacement | Auto-detect (~70 profiles) |

## Contributing

PRs welcome. Criteria:
- Self-contained, not a SaaS platform
- Runs on developer machines
- Reasonable resource footprint
- Built for or applicable to AI coding agent use cases
- Recently maintained (commits within the last few weeks)

Format: repo link, one paragraph on isolation model, key features, supported agents. Update the comparison table if the tool adds a new category or changes key dimensions.

## Caveats

**isol8** is flagged as suspicious and not recommended. Rationale:
- Claims "v0.2.6" but has only 2 releases, both on the same day (June 23, 2026). No v0.1.x series exists. The version number is fabricated to appear mature.
- Majority of commits authored by "Claude" (AI-generated code), merged by a human. This is a security sandbox tool -- AI-generated security code with no reviewable provenance is inherently untrustworthy.
- Entire commit history spans ~48 hours. The repo was created, populated, and "released" in a single AI-assisted sprint.
- For a tool that controls filesystem access, network policy, and process confinement, the attack surface introduced by AI-generated code with no human review is unacceptable.
