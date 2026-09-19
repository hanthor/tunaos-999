# Security Policy

## Supported Versions

CI builds the TunaOS images every day, and publishes an ISO every week. Each
image goes out under a tag for its flavor, for example `gnome`, `kde`, or
`gnome-hwe`. This project supports only the newest build of each flavor.
See [`VERSIONING.md`](VERSIONING.md) for the whole tag scheme.

| Variant | Base OS | Status |
|---|---|---|
| Yellowfin | AlmaLinux Kitten 10 | ✅ Supported |
| Albacore | AlmaLinux 10 | ✅ Supported |
| Skipjack | CentOS Stream 10 | ⚠️ Beta |
| Bonito | Fedora 44 | ⚠️ In progress |
| Redfin | RHEL 10 | 🔒 Local-build only |

## Reporting a Vulnerability

**Please do not report security vulnerabilities through public GitHub issues.**

Instead, report them privately via GitHub Security Advisories:

1. Go to the [Security tab](https://github.com/tuna-os/tunaOS/security)
2. Click **Report a vulnerability**
3. Provide a detailed description of the issue, including steps to reproduce

You can expect:
- **Acknowledgment** within 48 hours
- **Status update** within 5 business days
- **Resolution timeline** based on severity

## Security Model

TunaOS images are:
- Built in CI from pinned base images (see `image-versions.yaml`)
- Signed keylessly by [Sigstore Cosign](https://github.com/sigstore/cosign),
  under the identity of the protected TunaOS workflow in GitHub Actions
- Examined for vulnerabilities by the scanner that GitHub supplies
- Published with attestations in SPDX, each one an SBOM that Cosign signed

TunaOS holds no long-lived key and no password that anyone could leak, and
that nobody must rotate. Fulcio issues a short-lived certificate against the
OIDC identity from GitHub Actions, and Sigstore's transparency log keeps a
record of the signature. See
[`docs/VERIFY-ARTIFACTS.md`](docs/VERIFY-ARTIFACTS.md) for the commands that
verify an artifact.

## Supply Chain Security

- Base images pinned by digest in `image-versions.yaml`
- Each Action from a third party pinned to a commit SHA
- No release goes out until two checks pass: the keyless signature, and the
  SBOM attestation. Both must verify against the workflow and the protected
  ref that we expect
- Build secrets use the secret mounts of BuildKit, never an environment
  variable
- Never put a credential for a workflow into a URL. When a checkout needs a
  private credential, put it in a Git header instead, for example with
  `http.extraheader`
- RPM packages from official AlmaLinux/CentOS/Fedora repositories and verified COPRs

## Disclosure Policy

We follow coordinated disclosure:
1. The reporter sends us the vulnerability in private
2. We investigate it and write a fix
3. The next builds carry that fix
4. We publish the advisory after those builds go out

See [`docs/AGENT_GUIDE.md`](docs/AGENT_GUIDE.md) for full build architecture details.
