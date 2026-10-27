# Local diagnostic log

Record the unpublished support-log implementation, focused validation and remaining evidence.

**Contents:** [Change](#change) · [Validation](#validation) · [Limits](#limits) · [Cleanup](#cleanup)

## Change

Source commit: `64ecb1d`. Adds typed persistent startup, permission, microphone,
processing and recovery events, plus current-status copy, preview, native text
export and confirmed clearing in About and Troubleshooting. Two rotating 256 KiB
files, a 400-event report and a bounded asynchronous write queue keep logging local
and limited. Reports select the last seven days; older disk records remain until
rotation or clearing. No audio, transcript text, free-form error descriptions,
paths or device UIDs are persisted. Current summaries include friendly input names
and Mac product/chip descriptions. Reports are shared only by explicit user action.

## Validation

- 35 focused unit tests passed across DiagnosticLogTests, AboutSettingsTests,
  AudioInputWatchdogTests, SpeechInputTests and MicrophoneTestTests.
- Persistence tests reopen the logger on the same temporary store, verify completed
  events, rotation/file permissions, bounded memory, time filtering, clearing and
  storage-failure behavior. Malformed/extra text fields never echo into reports.
  Copy uses a private test pasteboard and matches the assembled preview exactly.
- One inert UI test passed: About entry, preview, native export cancellation and
  clear-confirmation cancellation. Minimum dark preview was visually inspected;
  long lines now wrap and the report starts at the top. No user clipboard, saved
  export, microphone permission, audio capture or History was used by this UI check.
- Earlier UI attempts exposed a Touch Bar Cancel query and an automation-mode
  initialization timeout. The query was fixed and the final focused run passed.
- Final Apple-silicon Release build passed with signing disabled. 170 local
  documentation links/anchors and whitespace validation passed. No full test suite.

Ignored local logs and the final inert screenshot are retained in
`local-evidence/diagnostic-log-2026-10-27/`; they are not publication assets.

## Limits

Not installed, signed, notarized, pushed or published by this task. The installed
release does not yet have this feature. No affected-M3 or live microphone/model
inference validation was performed. This adds evidence collection, not a confirmed
remote root cause or a crash-dump collector. Force quit/crash can lose queued final
events. Disk failures retain current events in memory only; the report shows this.
Light appearance and actually saving a native export remain manual checks.

## Cleanup

Confirmed no task test app remained running. Removed build output, test-result
bundles, extracted attachments, temporary edit scripts and inert UI fixture
folders after retaining local evidence. Installed app, model assets, user History,
reference graphs and unrelated background services were preserved.
