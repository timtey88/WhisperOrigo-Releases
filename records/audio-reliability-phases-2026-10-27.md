# Audio reliability phases

Record incremental implementation, focused validation and remaining hardware evidence for the unpublished audio reliability changes.

**Contents:** [Changes](#changes) · [Validation](#validation) · [Recovery decision](#recovery-decision) · [Remaining evidence](#remaining-evidence) · [Cleanup](#cleanup)

## Changes

Source baseline: `00500b3` (research comparison). Final source: `a028838`.
The installed/public 1.1.2 (4) remains unchanged; no update artifacts or source
commits were pushed or published by this task.

| Source commit | Phase |
|---|---|
| `64cf2e3` | Immediate conversion/copy/worker failure reporting; input, converted and submitted frame diagnostics |
| `74dc0f8` | Requested/opened device and stream/layout contract; explicit selected-channel extraction failures |
| `8707492` | Shared off-main serial native capture, exclusive ownership and safe retirement; coalesced asynchronous metadata queries; callback/processing timing |
| `fef839c` | Preallocated bounded handoff, capped speech queues, submitted-PCM readiness, overflow/finalization-race failures and partial-text retention without delivery |
| `7115562` | Separate permission/signal/transcript evidence; model-independent setup microphone check; cancelled device resolution cannot later open capture |
| `a028838` | Normalize irrelevant named-channel coordinates; native resampling and explicitly opted-in capture smoke support |

Capture remains on demand. No model preloading, idle microphone hold, new STT
model integration, global input mutation or permission reset was introduced.

## Validation

- Phase-focused checks passed: 8 for conversion/error handling, 18 for route and
  channel behavior, 23 for native ownership/cancellation, 49 for bounded handoff
  integration, and 14 for setup evidence/device-resolution cancellation. These
  overlapping runs are not additive independent test counts.
- Final selected run: **67 tests passed across 11 suites**. Includes pool
  ownership/FIFO/overflow/drain, format/layout changes, non-finite Float32 input,
  native 48 kHz to 16 kHz conversion, legitimate buffering, raw-versus-submitted
  readiness, mocked blocked native start/retirement, metadata cancellation,
  diagnostics, setup trials and controller output suppression/restart.
- Native capture smoke: **one test skipped** because the test app does not have
  existing microphone access. The check never asks for permission or saves audio.
  A skip is not hardware success; no actual microphone sample was captured.
- One inert onboarding UI test passed in light and minimum dark layouts. Its
  recording buttons are disabled and it cannot prompt, paste or save History.
  Trial attachments exported; the minimum dark screenshot was visually inspected
  for scrollable content and an unobstructed fixed completion footer.
- Apple-silicon Release compilation passed with signing disabled. This is not a
  signed/notarized release candidate or a live inference test.
- 56 local documentation links/anchors and `git diff --check` passed. No full app
  or full UI suite was run. Existing unrelated vocabulary conformance warnings
  remain outside this audio change.

Local ignored logs and two inert setup attachments are retained under
`local-evidence/audio-reliability-phases-2026-10-27/`; generated builds/test-result
bundles are not release assets.

## Recovery decision

The research plan's final recovery item was evaluated conservatively. Manual Retry
already constructs a fresh engine after serialized native retirement. Automated
same-device retry or an alternative AVCaptureSession/HAL probe is not enabled:
there is no affected-machine evidence yet establishing whether raw delivery,
routing, conversion or inference was responsible. A persistent native call cannot
be made safe by releasing its objects or admitting another owner while it runs.

Before considering automatic recovery, require known zero accepted audio, a
verified intended route, completed teardown, one bounded attempt and generation
checks. Do not silently change microphones, restart coreaudiod or treat a quiet
speaker as broken capture. An alternative backend should be an isolated same-device
probe first, not two simultaneous microphone captures or a speculative replacement.

## Remaining evidence

The M3/macOS 26.2 report remains unconfirmed. Next validation needs a signed
candidate with existing app permissions: built-in microphone level/route checks,
Apple and Parakeet short dictation plus Stop/Cancel/retry, then reviewed diagnostic
metadata at the point of failure. This task did not establish live input on this
Mac because the test grant was absent; mocks/native resampling cannot replace
those checks. No new model downloads or changes to user History were performed.

## Cleanup

Retained the selected local evidence, then removed task build output, opt-in
marker, extracted attachments/test-result bundles and temporary edit helpers.
Process inspection found no test app running from the task build directory before
removal. Source worktree is clean. Persistent Graphify indexes, installed app,
model assets, History and unrelated background services were left intact.
