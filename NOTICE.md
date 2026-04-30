# NOTICE

## Repository license

The build system in this repository — `Dockerfile.override`, GitHub Actions
workflow, configuration files, and documentation — is licensed under
Apache-2.0. See `LICENSE` for the full text.

No Plausible source code is contained in this repository. Upstream source is
cloned fresh at build time and discarded after the image is produced.

## Image license

The container images produced by this repository and published to
`ghcr.io/thekroll-ltd/plausible` contain a compiled build of Plausible
Analytics Community Edition, which is licensed under AGPL-3.0. The images
are therefore distributed under AGPL-3.0, inheriting all obligations from
upstream's license.

- Plausible upstream: https://github.com/plausible/analytics
- Plausible license: https://github.com/plausible/analytics/blob/master/LICENSE.md

## Obligations for operators of the image

AGPL-3.0 Section 13 (Remote Network Interaction) requires that operators
who make Plausible available to users over a network must provide those
users with access to the corresponding source code of the running version.
This obligation is not satisfied by the mirror author; it applies to each
operator independently.

The corresponding source is available at the upstream repository linked
above. This mirror build makes no modifications to Plausible's source code —
the Dockerfile override only changes the runtime base image (digest pinning,
certbot removal) and adds OCI labels, not the application source. Pointing
your users at `https://github.com/plausible/analytics` at the commit matching
the image tag satisfies the source-provision obligation.

## No SLA

This mirror is THEKROLL's internal build, published publicly as reference.
No service-level agreement, no support commitment, no compatibility guarantee.
For production-critical deployments, fork the template at
`github.com/THEKROLL-LTD/oss-mirror-build` and run your own pipeline.
