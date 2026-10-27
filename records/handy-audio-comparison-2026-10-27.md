# Handy audio capture comparison

Record a source comparison that guides diagnosis of missing microphone input without assigning an unverified remote cause.

**Contents:** [Scope and sources](#scope-and-sources) · [Comparison](#comparison) · [Diagnostic approach](#diagnostic-approach) · [Recommendations](#recommendations) · [Validation and limits](#validation-and-limits)

## Scope and sources

Inspected 2026-10-27: local Handy commit `73c095857bc4eadff097f64767c40ad129cc372d`, WhisperOrigo commit `bde13d7` including unreleased audio fixes. The installed public WhisperOrigo 1.1.2 (4) does not contain those new fixes or receipt counters. The affected user's installed Handy version is unknown; this comparison describes the inspected source, not every Handy release.

Primary source locations:

- Handy `src-tauri/src/managers/audio.rs`: device resolution, channel fallback, stream ownership, readiness and retry.
- Handy `src-tauri/src/actions.rs`: first-chunk readiness cue.
- Handy `src-tauri/src/audio_toolkit/audio/sink.rs`: visualization before VAD, digital-silence classification and speech delivery.
- [handy-recorder source](https://github.com/handy-computer/handy-recorder/tree/1d975fe8c10f9a69d778cf16955c7e0699b4b8e1/crates/handy-recorder), package 0.1.0: `src/capture/{engine,transport,delivery,watchdog}.rs`, `src/backend/cpal/mod.rs`.
- [Handy CPAL source](https://github.com/handy-computer/cpal/tree/fdbb321242994474f71a8e035f87747038eb099d), package handy-cpal 0.18.2: `src/host/coreaudio/macos/device.rs`.
- WhisperOrigo `Audio/{AudioRecorder,AudioDeviceManager,AudioChannelProcessing,MicrophoneTestController,AudioInputSignal}.swift` and `Transcription/{AppleSpeechEngine,ParakeetEngine}.swift`.

Both downloaded crate archives matched the inspected Handy Cargo.lock SHA-256 values: recorder `4a734d6d16eb8eb485548d75f383fa0c09d65b037a20e648c19730860f7a8a3f`; CPAL `07b0f0a55e498d0260eae6bc9a61a976b60d0c014c038771d6fbcc95907b79d4`.

## Comparison

| Area | Handy inspected source | WhisperOrigo inspected source | Diagnostic implication |
|---|---|---|---|
| Mac capture | handy-recorder → handy-cpal → Core Audio input AudioUnit; explicit interleaved callback layout | AVAudioEngine input-node tap; selected input applied to its AudioUnit | Different capture machinery is a candidate for a controlled test, not evidence one backend is defective |
| Device selection | Persisted microphone name resolves to cached recorder ID; stale cached-open failure triggers fresh resolution/retry; absent selected input falls back to default and clears preference only after successful open | Persisted device UID resolves to live Core Audio ID; missing UID falls back to default; input changes reset channel; current source reapplies route after model preparation and records device-name readback | Our UID persistence is useful; verify actual opened device rather than copying name persistence |
| Channel default | Average all input channels to mono; explicit zero-based channel; invalid channel retries mixed mono | All channels retained for recognition, or explicit one-based channel extracted to mono; invalid channel normalizes to All | Silent unused channels dilute Handy's average; opposite-polarity signals can cancel. Do not assume averaging fixes routing |
| Meter location | Mono normalized stream → visualizer before VAD → speech gating | Raw selected-channel RMS, or maximum per-channel RMS for All, before engine-specific conversion | Our moving meter can coexist with a downstream conversion problem; Handy's meter is after channel conversion |
| Model input | Speech config produces 16 kHz mono float chunks before model-specific handling | Apple AVAudioConverter targets analyzer format; Parakeet delegates conversion to FluidAudio AudioConverter/streaming path | Inspect or instrument the engine input boundary if raw input is non-silent but recognition remains empty; FluidAudio internals were not re-audited here |
| Readiness | First delivered chunk signals recording-ready event and start chime; silence counts as arrival | Recording state set after engine starts; new counters distinguish no frames/silence/non-silence, but do not gate readiness | Most useful UX gap: engine running does not prove audio delivery |
| Missing audio | Recorder watchdog: no callbacks after open for 10 seconds → NoAudio; callbacks absent during recording for 5 seconds → Stalled; delivery heartbeat timeout 10 seconds | No equivalent ongoing frame-arrival/stall watchdog; startup/model deadlines cover different stages | Add capture-specific failure detection, independently from model loading |
| Recovery | Failure marks recorder unhealthy; next start rebuilds; bounded fresh-resolution/start retry paths | Per-session engine teardown and explicit configuration interruption handling | Retry a failed capture once before any audio is accepted; do not silently change route during dictation |
| Idle capture | Always-on and on-demand modes; on-demand stream can remain warm for a 30-second lazy-close interval | On-demand microphone; isolated level test ends on navigation/deactivation or its time limit | Borrow readiness/watchdog behavior without retaining an idle microphone or preloading models |
| Failure observability | Recorder differentiates no audio, stalls, permission errors and dropped frames; sink distinguishes received digital silence from no chunks | New summary includes actual captured name/format and aggregate receipt classification | Existing unpublished diagnostics are the first tool for locating the remote failure |

Handy's Core Audio code also attempts to set the physical stream format, with sample-rate fallback. That is a behavior difference, not a recommendation to alter shared hardware settings. Its optional Bluetooth headset silent-output hold does not explain a built-in microphone case.

## Diagnostic approach

Use one short, controlled session with the built-in input explicitly selected in each app, the same physical microphone and a normal spoken sentence. Verify the app's route, not only the macOS input label or permission toggle. A meter test does not play microphone sound through speakers.

1. Check the captured device name and input format against the configured route. An enabled permission is necessary but does not prove the intended stream opened.
2. Check frame receipt: zero frames points toward engine/tap/driver delivery; received silent frames point toward channel/mute/routing; non-silent frames with no words shift attention to conversion, language and recognition. Non-silence is not proof of intelligible speech.
3. For multichannel input, compare explicit channels and All. Do not change channels mid-session. For a genuinely mono built-in capture, channel averaging and the interleaved multichannel defect do not by themselves explain the failure.
4. If the app receives silence or no frames, repeat with audio-routing apps temporarily quit, one at a time, restoring them afterward. Do not uninstall drivers or reset permissions without evidence.
5. If raw frames are non-silent, add aggregate converted-frame/engine-feed diagnostics before considering a backend replacement. Avoid saving audio or transcripts for this investigation unless separately authorized.

The remote user has not yet supplied actual device readback, frame counters, an Audio screenshot or a diagnostic summary. Driver installation alone does not identify the cause. The current public release cannot provide the newly added receipt fields; they require a signed diagnostic candidate.

## Recommendations

Priority order, proposed rather than implemented in this research task:

1. Add a first-buffer readiness state and bounded missing-frame/stalled-frame detection shared by dictation and microphone testing. Count silent frames as receipt; never fail merely because a user is quiet. Keep cancellation and cleanup serialized.
2. Provide a clear actionable capture error and allow a fresh start after teardown. Consider one bounded capture retry before any audio is accepted; avoid retries that duplicate audio, silently change devices or race model/session cleanup.
3. Add aggregate downstream conversion/feed counters only if raw input arrives without recognition. Keep raw and converted signal status separate so a healthy raw meter does not conceal conversion failure.
4. Evaluate a small isolated input-only Core Audio capture probe on the affected Mac only if AVAudioEngine receives no frames despite a verified route and grant. Do not replace the capture architecture or adopt idle capture on source comparison alone.

## Validation and limits

Source inspection only; no app changes, full test run, new build, microphone recording, permission changes or process launches in this follow-up. The previous local investigation remains in [Audio input investigation](audio-input-investigation-2026-10-26.md). Package checksums were verified against Cargo.lock. Documentation links/anchors and whitespace were checked. Downloaded inspection archives and extracted source were removed after recording findings; Handy's pre-existing untracked `graphify-out/` was left untouched.
