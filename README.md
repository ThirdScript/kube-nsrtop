# kubectl-nsrtop

A `kubectl` plugin that summarizes CPU and Memory resources for a Kubernetes
namespace: pod counts, container restarts, requested/limit totals, live
usage (via `kubectl top`), usage as a percentage of requests/limits, and
cluster capacity context.

## Usage

```
kubectl nsrtop              # current kubectl namespace
kubectl nsrtop -n <ns>      # specific namespace
kubectl nsrtop -A           # all namespaces (aggregate totals)
kubectl nsrtop -h           # help
```

## Example output

```
Kubernetes Namespace Summary
Context: my-context
Namespace: my-namespace
Timestamp: Mon Sep 28 18:24:34 2026

── Pods ──────────────────────────────
  Total: 5   Running: 5   Pending: 0   Failed: 0   Succeeded: 0
  Not Ready: 0   Total Container Restarts: 6

── Requested / Limits (from pod specs) ──
  CPU     requests: 600m       limits: 1.25
  Memory  requests: 350Mi      limits: 604Mi

── Live Usage (kubectl top) ──────────
  CPU     used: 24m
  Memory  used: 72Mi

  CPU usage vs requests: 4%
  CPU usage vs limits:   2%
  Mem usage vs requests: 21%
  Mem usage vs limits:   12%

── Cluster Capacity Context ──────────
  Nodes: 24   Cluster allocatable CPU: 301.20   Cluster allocatable Mem: 636.7Gi
  This namespace's live CPU usage is 0% of total cluster allocatable CPU
  This namespace's live Mem usage is 0% of total cluster allocatable Mem

── Top Pods by CPU ───────────────────
  ...
```

## Requirements

- `bash`
- `jq`
- `awk`
- A running `metrics-server` in the cluster, for the "Live Usage" section
  (optional — the rest of the output still works without it).

## Installation

### Via krew (custom manifest)

This plugin is not (yet) published to the official
[krew-index](https://github.com/kubernetes-sigs/krew-index). Install it
directly from this repo's manifest:

```sh
kubectl krew install --manifest-url \
  https://raw.githubusercontent.com/ThirdScript/kube-nsrtop/main/plugins/nsrtop.yaml
```

### Manual

Download `kubectl-nsrtop` from the
[releases page](https://github.com/ThirdScript/kube-nsrtop/releases),
make it executable, and place it on your `PATH`:

```sh
chmod +x kubectl-nsrtop
sudo mv kubectl-nsrtop /usr/local/bin/
```

Then run it as `kubectl nsrtop`.

## Releasing a new version

1. Bump the version and tag it, e.g. `git tag v0.1.1 && git push origin v0.1.1`.
2. Build the release tarball:
   ```sh
   tar -czf kubectl-nsrtop.tar.gz kubectl-nsrtop LICENSE
   ```
3. Create a GitHub release for the tag and upload `kubectl-nsrtop.tar.gz`.
4. Compute the sha256 and update `plugins/nsrtop.yaml`:
   ```sh
   shasum -a 256 kubectl-nsrtop.tar.gz
   ```
5. Update `version` and `sha256` in `plugins/nsrtop.yaml`, commit, and push.

## License

MIT — see [LICENSE](LICENSE).
