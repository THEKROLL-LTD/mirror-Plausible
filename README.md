# mirror-Plausible

Hardened container mirror of [plausible/analytics](https://github.com/plausible/analytics) (Community Edition). THEKROLL's internal Plausible image, published publicly as a reference build.

## What this repository does

Each night, a GitHub Actions pipeline in this repository:

1. Checks `plausible/analytics` for a new release tag
2. If there is one, clones the upstream source, applies our `Dockerfile.override`, and rebuilds the image
3. Builds the image once, scans it with Trivy (SARIF to the Security tab), produces a CycloneDX SBOM
4. If no `CRITICAL` or `HIGH` vulnerabilities with an available fix are found: pushes to `ghcr.io/thekroll-ltd/plausible` and opens a digest-pin PR
5. If findings are present: blocks the push, opens an issue, retains the full audit bundle for 90 days

The result is an image rebuilt from pinned-by-digest base images, with `certbot` removed (operators terminate TLS at a reverse proxy) and OCI labels populated, plus all artifacts a supply-chain audit requires.

## Image

`ghcr.io/thekroll-ltd/plausible:<tag>` and `ghcr.io/thekroll-ltd/plausible@sha256:<digest>`.

Tags track upstream Plausible CE releases. Digests are the authoritative pin.

## Differences from `ghcr.io/plausible/community-edition`

- Both build and runtime base images are **digest-pinned**. Upstream pins tags only.
- **`certbot` is removed** from the runtime layer (~30 MB Python tree, attack-surface win). Run Plausible behind a reverse proxy that terminates TLS — that is the only sensible production posture, and it makes the bundled certbot dead weight.
- **OCI labels populated** (`source`, `version`, `licenses`, `vendor`) so `docker inspect` self-identifies the build.
- **Runtime base is a newer Alpine patch** (3.22.8 vs. upstream's 3.22.2, same minor), plus `apk upgrade` at build time.
- **One dependency patch: `plug` 1.18.1 → 1.18.5.** Upstream v3.2.1 pins plug 1.18.1, which is affected by CVE-2026-54892 (quadratic parameter decoding, reachable unauthenticated on every request including the public `/api/event`). The override narrows the requirement to `~> 1.18.5` and re-resolves only that one lock entry; a build guard fails if anything else in `mix.lock` moves, or once upstream's lock no longer pins 1.18.1. This is a deliberate, documented exception to this pipeline's "don't fork upstream lockfiles" rule and goes away with the next upstream release that ships a fixed plug. Details in the header of `dockerfiles/Dockerfile.override`.
- Otherwise byte-equivalent to upstream up to base image, apk package set and the plug patch above: same MIX_ENV=ce, same nonroot UID 999, same entrypoint, same exposed port, same volumes.

No application source code is patched.

## No SLA

This is THEKROLL's own internal build, made public as a reference. There is no service-level agreement, no support commitment, no compatibility guarantee. Scheduling, retention, and availability may change without notice.

**For production-critical use**, fork the template and run your own pipeline: [THEKROLL-LTD/oss-mirror-build](https://github.com/THEKROLL-LTD/oss-mirror-build). Five minutes of setup gets you the same controls under your own ownership, with your own SLA.

## License

The build system in this repository — workflow YAML, Dockerfile override, documentation — is licensed under Apache-2.0. See [`LICENSE`](LICENSE).

The container images produced here contain Plausible Analytics CE, which is licensed under AGPL-3.0. The images inherit AGPL-3.0. Operators who expose the image over a network must comply with AGPL §13 (Remote Network Interaction). See [`NOTICE.md`](NOTICE.md) for details.

## Related

- **Upstream:** [github.com/plausible/analytics](https://github.com/plausible/analytics) (AGPL-3.0)
- **Template this repo was forked from:** [THEKROLL-LTD/oss-mirror-build](https://github.com/THEKROLL-LTD/oss-mirror-build) (Apache-2.0)
- **Sister mirror:** [THEKROLL-LTD/mirror-Gokapi](https://github.com/THEKROLL-LTD/mirror-Gokapi)

## Maintained by

[THEKROLL](https://thekroll.ltd) — DevOps consultancy from Cyprus. For production-critical use, don't depend on this mirror; fork the template and run your own pipeline at [`THEKROLL-LTD/oss-mirror-build`](https://github.com/THEKROLL-LTD/oss-mirror-build).
