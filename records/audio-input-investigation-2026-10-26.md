# Audio input investigation

Record source-backed findings and local evidence without claiming a remote root cause.

**Contents:** [Report](#report) · [Findings](#findings) · [Validation](#validation) · [Remaining evidence](#remaining-evidence)

## Report

The affected user reports input in macOS Sound settings, a built-in microphone, and a running dictation pill without voice input. BlackHole multichannel and DuoMic/other audio software are installed. App microphone permission being enabled was reported, but no reviewed diagnostic summary, actual WhisperOrigo device/channel selection, current meter state or exact pill status was supplied. The Mac was previously described as M3/macOS 26.2; this investigation cannot independently verify that environment.

## Findings

- **Reproduced defect:** channel metering and mono extraction ignored PCM stride. A controlled interleaved two-channel buffer with sound in later frames was measured as zero and copied incorrectly. `07c86fb` fixes both reads. This is conditional on interleaved/multichannel input and does not establish a cause for a genuine mono built-in stream. [Apple's buffer rules](https://developer.apple.com/documentation/avfaudio/avaudiopcmbuffer/floatchanneldata) specify stride between samples.
- **Source-level test weakness:** the level-only test stopped on every configuration notification after preparation, including a delayed notification while the engine remained running with an unchanged format. The guard now preserves healthy input and still stops for a stopped engine or changed format. This could interrupt the test; it does not explain a still-running pill by itself.
- **Route timing gap:** dictation selected the microphone before potentially lengthy model preparation. The selected route is now refreshed/reapplied immediately before tap setup. A configuration label alone was not proof of the audio unit's opened device; the new diagnostics record device-name readback at capture start when available.
- **Observation gap:** the app declared recording after engine start without separately reporting receipt of frames. A pill alone cannot distinguish missing audio, silent audio and recognition failure. `bde13d7` adds session-safe aggregate receipt counters to dictation and the level test, retaining results after stop. No sample bytes or transcript are retained by these counters.

| Diagnostic result | Investigation direction |
|---|---|
| No frames | Input tap/driver/engine delivery or route initialization |
| Frames, all samples silent | Actual routing, selected channel, input mute/gain or audio-routing software |
| Non-silent frames, blank transcript | Speech model/language/analyzer results; non-silence alone does not prove intelligible voice |
| Unmetered frames | Capture sample format/level-processing compatibility |

Virtual drivers being installed is not sufficient evidence of a conflict. macOS and WhisperOrigo may target different inputs. The device-name readback can be Unknown if the audio unit cannot provide a known device; it is not replaced with a misleading configured name.

## Validation

Host: macOS 26.7.1/M5 Pro, with virtual audio devices installed and the built-in microphone selected as the system input. The published app's meter received nonzero input locally.

The new stride regression failed before the fix (zero RMS and incorrect mono frames); all eight audio-settings checks passed afterward. Twenty-five focused audio/microphone/diagnostic checks passed, including silent/no-frame/unmetered classification, stale-generation exclusion and reviewed-summary fields. Signed local Release build passed; identity continuity with the installed release passed. No full suite was run.

Final temporary diagnostic PID 20438 was explicitly targeted after closing an older diagnostic instance. Its level test showed non-silent receipt and built-in microphone readback in Troubleshooting, retained after navigation. Its private dictation test returned Transcript ready. The synthetic sample was fictional; transcript contents were not logged or copied and History was suppressed by the private trial.

An older temporary instance was initially left open during GUI automation; it was closed and final-build checks were repeated by PID. These scoped native checks do not claim a full automated UI suite or a BlackHole/DuoMic reproduction.

Changes are committed locally, not published or pushed. The temporary diagnostic instances were stopped and the normal Applications app restored unchanged. Generated build folders, helper scripts and sample audio were removed. Evidence is retained under ignored `local-evidence/audio-input-investigation-2026-10-26/`.

## Remaining evidence

The remote cause remains unconfirmed. Obtain the reviewed current About → Copy Diagnostic Summary plus the Audio-page/pill state during failure, particularly explicit app device selection, channel, microphone permission and startup stage. The current public release has configuration/stage fields; the newly added receipt/readback fields require a future diagnostic or release build from these commits, which has not been distributed.

Use the built-in device explicitly and All channels to isolate routing. If still silent, compare with audio-routing apps temporarily closed one at a time; do not infer a driver conflict merely from installation or reset unrelated permissions. Remote M3/macOS 26.2, multiple-driver interaction, clean-account behavior and full hardware/UI matrices remain untested.
