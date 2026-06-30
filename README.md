# Awesome Agent Sandbox

Portable, lightweight, self-contained sandboxes for AI coding agents - microVMs, containers, and isolation harnesses.

## MicroVMs

- **[smolvm](https://github.com/smol-machines/smolvm)** - Hardware-isolated VMs. Boots <200ms via Hypervisor.framework/KVM/WHP. Pulls OCI images, no Docker daemon needed. Packs into a single `.smolmachine` file. Elastic memory, opt-in networking, SSH agent forwarding (keys never touch guest), GPU acceleration, declarative Smolfile (TOML). 4 vCPUs / 8 GiB RAM default, idle vCPUs sleep at near-zero cost.

## Container Sandboxes

- **[agent-sandbox](https://github.com/thomaspeklak/agent-sandbox)** - Rust CLI (`ags`), rootless Podman containers. Supports Claude, Codex, Gemini, OpenCode, Pi, and interactive shells. Hardened lockdown mode for untrusted repos (no secrets, no SSH agent, no host bridges). Clipboard bridge, OAuth proxy, browser sidecar, host UI via Glimpse. TOML config with per-repo overlays.

- **[yolobox](https://github.com/finbarr/yolobox)** - Docker sandbox. Only the project directory is mounted at its real path; home directory stays on the host. Agent gets full permissions and sudo inside the container. Wraps agent CLIs to skip permission prompts. `.yolobox.toml` for extra packages. Flags: `--no-network`, `--readonly-project`, `fork`. Protects against accidents, not container-escape exploits.

## VM + Container Hybrids

- **[locki](https://github.com/JanPokorny/locki)** - Lima VM + Incus containers with a command bridge that filters git operations. Agents write only to namespaced branches; stashes scoped, hooks redirected. Worktree-backed storage at `~/.local/share/locki/worktrees/` - edits appear on the host instantly. Host home and `.git` invisible to the agent. Supports containers, systemd, and Kubernetes inside the sandbox. Bundles Mise. `locki ai` (interactive), `locki exec` (direct) with auto port collision avoidance.

## Comparison

| Tool | Isolation | Boot | Networking | State | Agents |
|------|-----------|------|------------|-------|--------|
| smolvm | Hardware (HVF/KVM) | <200ms | Opt-in, host allowlist | Persistent VMs | Any CLI |
| agent-sandbox | Rootless Podman | Seconds | Configurable bridges | Persistent volumes | Claude, Codex, Gemini, Pi, OpenCode, shell |
| yolobox | Docker | Seconds | Optional (`--no-network`) | Persistent volumes | Claude Code, Codex, Gemini, OpenCode, Copilot, Pi |
| locki | Lima VM + Incus | Minutes | Bridged | Git worktrees | Claude, Codex, Gemini |

## Contributing

PRs welcome. Criteria:
- Self-contained, not a SaaS platform
- Runs on developer machines
- Reasonable resource footprint
- Built for or applicable to AI coding agent use cases

Format: repo link, one paragraph on isolation model, key features, supported agents. Update the comparison table if the tool adds a new category or changes key dimensions.
