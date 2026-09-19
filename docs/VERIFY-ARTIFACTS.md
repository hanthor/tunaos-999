# Verify TunaOS artifacts

Sigstore Cosign signs every TunaOS OCI image. It uses the keyless identity
that GitHub Actions gives it, so this project holds no key and no password for
this purpose. A check proves the digest of the artifact, and also the identity
of the protected workflow that built it.

## Install Cosign

Use Cosign 3.0.6 or newer. Obey the install instructions upstream, and verify
the Cosign binary before you use it.

## Verify an OCI image

Always resolve an immutable digest and verify that, even when you start from a
friendly tag:

```bash
image=ghcr.io/tuna-os/yellowfin:gnome
digest=$(skopeo inspect "docker://${image}" | jq -r .Digest)
ref="ghcr.io/tuna-os/yellowfin@${digest}"

cosign verify "${ref}" \
  --certificate-identity \
    "https://github.com/tuna-os/tunaOS/.github/workflows/reusable-build-image.yml@refs/heads/main" \
  --certificate-oidc-issuer \
    "https://token.actions.githubusercontent.com"
```

The command must exit successfully. Do not replace the identity or issuer with
an unrestricted regular expression.

## Verify the SPDX SBOM attestation

Each published platform image carries an attestation in SPDX JSON, and Cosign
has signed it:

```bash
cosign verify-attestation "${ref}" \
  --type spdxjson \
  --certificate-identity \
    "https://github.com/tuna-os/tunaOS/.github/workflows/reusable-build-image.yml@refs/heads/main" \
  --certificate-oidc-issuer \
    "https://token.actions.githubusercontent.com"
```

Cosign prints the verified in-toto statement. Its `subject[].digest.sha256`
must match the digest in `ref`. The predicate contains the SPDX document for
that platform image.

## Trust boundary

The accepted identity is intentionally narrow:

- repository: `tuna-os/tunaOS`;
- workflow: `.github/workflows/reusable-build-image.yml`;
- ref: protected `refs/heads/main`;
- OIDC issuer: GitHub Actions.

A signature from a fork, pull-request ref, another repository, another
workflow, or another identity provider does not satisfy this policy.

## Verify an ISO

Every published ISO has two adjacent files:

- `<name>.iso.sha256` — the SHA-256 checksum manifest;
- `<name>.iso.sigstore.json` — the keyless bundle that Cosign v3 verifies.

Download all three files into the same directory, then run:

```bash
sha256sum --check --strict tunaos-example.iso.sha256

cosign verify-blob tunaos-example.iso \
  --bundle tunaos-example.iso.sigstore.json \
  --certificate-identity \
    "https://github.com/tuna-os/tunaOS/.github/workflows/reusable-build-artifacts.yml@refs/heads/main" \
  --certificate-oidc-issuer \
    "https://token.actions.githubusercontent.com"
```

`publish-iso-groups.yml` makes the combined media on a schedule, and removes
the duplicates itself. For those ISOs, use this exact identity instead:

```text
https://github.com/tuna-os/tunaOS/.github/workflows/publish-iso-groups.yml@refs/heads/main
```

The reusable workflow for artifacts signs an ISO only after that ISO passes
its boot gate in QEMU. The grouped workflow obeys the same order. Both then
upload the verified ISO, the checksum, and the bundle together. If a signature
or a local check fails, nothing goes out.
