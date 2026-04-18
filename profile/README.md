# EasyEnclave

**A generic runtime for sealed TDX VMs, and the things you can grow on top of it.**

EasyEnclave is the bare runtime: one static binary that runs as PID 1
inside an Intel TDX confidential VM, exposes a unix-socket workload
API, and stays out of everything else. No HTTP server, no networking,
no database, no container runtime. Minimal attack surface, clean
attestation surface.

Other projects in this org layer up from there — DevOps Defender
handles fleet + control plane + tunnels on top of easyenclave, and
slopandmop is where composable workloads live.

## Projects

| Repo                                                  | Role                                                                                                                                              |
|-------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| [easyenclave](https://github.com/easyenclave/easyenclave) | The PID-1 runtime. TDX attestation via configfs-tsm, workload lifecycle over a newline-JSON socket, image builds for GCP + local-TDX targets. |
| [easydollar](https://github.com/easyenclave/easydollar)   | Economic layer for confidential compute (earn-by-providing-hardware, pay-for-compute, smart-contract settlement). See the whitepaper. Returning as orchestration-driven slop in slopandmop. |

## How the pieces fit

```
            Intel TDX VM (hardware-sealed memory)
             └── easyenclave (PID 1)
                   ├── /var/lib/easyenclave/agent.sock  ← workload API
                   └── workloads ─────────────────────────────
                                                             │
                                                             ▼
                                            any of: dd-agent, ollama,
                                            openclaw, your-own-slop,
                                            easydollar (returning), …
```

- **[devopsdefender/dd](https://github.com/devopsdefender/dd)** runs
  as one of those workloads. It registers the VM with a control plane,
  provisions a Cloudflare tunnel, and exposes a dashboard + `/deploy`
  API so owners can POST more slop at their fleet.
- **[slopandmop/slopandmop](https://github.com/slopandmop/slopandmop)**
  is the catalog of reference workloads — podman, ollama, openclaw
  today. `easydollar` and `satsforcompute` return here as slop.

## Links

- [easyenclave.com](https://easyenclave.com) — landing page
- [EasyDollar whitepaper](https://github.com/easyenclave/easydollar/blob/main/WHITEPAPER.md) — economic model
