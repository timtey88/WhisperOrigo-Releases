# Local speech recovery improvements

Record the installed local improvement separately from the published 1.1.2 artifact.

**Contents:** [Scope](#scope) · [Validation](#validation) · [Distribution and cleanup](#distribution-and-cleanup)

## Scope

Source `e24826d` adds a 180-second deadline around native Stop/final recognition, avoids extending startup deadlines for repeated stage reports, and adds cold-start/cleanup guidance in Settings, setup and menu help. Recognition deadlines end before text cleanup/delivery. Native cleanup stays serialized; unresponsive native work can still require restarting the app.

## Validation

Seven focused deadline/recovery checks passed. Developer ID-signed Release build passed. Existing production signing identity matched in both directions. The installed local app's private microphone trial transcribed the fictional speaker fixture and matched portrait; two subsequent Start/Cancel cycles returned Ready. Transcript contents were not inspected or logged by the UI helper. Existing preferences and permission grants were retained.

Host: macOS 26.7.1/M5 Pro. Full suites, the complete light/dark/window-size matrix, affected M3/macOS 26.2, clean-account permission prompts/revocations, and off-device key backups were not verified. Evidence is retained under ignored `local-evidence/1.1.2-local-recovery-e24826d/`.

## Distribution and cleanup

Installed locally on 26 October 2026 at `/Applications/WhisperOrigo.app`, retaining 1.1.2 (4) at the owner's request. This is a signed local build, not a newly notarized release. Its code differs from the published build 4; the public source tag, ZIP/checksum, GitHub release and update feed remain unchanged. These improvements have not been pushed or publicly distributed.

Temporary builds, sample audio, UI helper scripts and the rollback app copy were removed after validation. The normal Applications app and validation logs/record remain. Published release artifacts remain separately preserved under their original source/checksum provenance.
