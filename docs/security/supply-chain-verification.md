# Supply-chain Verification Guidance

This project supports convenience install/update workflows, but contributors and operators can choose stronger verification steps before execution.

## Recommended verification baseline

1. **Prefer reviewed local files over direct piping**
   - Download installer scripts first, inspect, then execute.
2. **Pin repository references when possible**
   - Use explicit tags/commits during internal rollout testing.
3. **Verify checksums/signatures for fetched artifacts**
   - Compare downloaded script checksums against trusted out-of-band values maintained by your deployment process.
4. **Track provenance in change records**
   - Record URL, commit/tag, checksum, reviewer, and execution timestamp.

## Optional checksum workflow (example)

```bash
wget -O basic-install.sh https://install.pi-hole.net
sha256sum basic-install.sh
# compare to trusted checksum value from your controlled source
sudo bash basic-install.sh
```

## Optional signature workflow (organizational)

Organizations can add a release gate that requires one of:

- Signed git tags by trusted maintainers.
- Artifact signatures verified by an internal keyring.
- Sigstore/cosign verification integrated into CI/CD.

## CI policy suggestions

- Require review for installer/update script modifications.
- Fail builds on high-confidence shell security findings.
- Keep dependency/update automation enabled (e.g., Dependabot already configured here).
