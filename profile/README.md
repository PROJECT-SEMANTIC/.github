# SEMANTIC

**SE**mi-automated **M**ultimodal **AN**alysis **T**oolkit for **I**ndividualized **C**haracterization

> **Status: private repository, work in progress.** This repository is not yet public. Content, names and licensing choices below are drafts and may change before the first public release.

SEMANTIC is an open-source, extensible, plug-and-play platform for semi-automated analysis of multimodal behavioural data (video, audio, physiological, inertial, eye-tracking). It helps therapists, clinicians and researchers turn raw session recordings into interpretable, individualized behavioural annotations, always under expert control (human-in-the-loop).

The name is also a nod to the Greek *sēmantikós* ("having meaning"): the platform assigns clinically meaningful interpretation to multimodal signals.

- **Funding:** Swiss National Science Foundation (SNSF), Project Funding, grant no. 10.008.762
- **Host institution:** SUPSI, Switzerland
- **Duration:** 1 May 2026 to 30 April 2029
- **Inventor and coordinator:** Mirko Gelsomini (SUPSI)
- **Co-applicants:** Michela Papandrea, Angela Pasqualotto (SUPSI)
- **Authors:** to be defined progressively

---

## Why SEMANTIC

Behavioural observation in therapy and clinical research still relies mostly on manual video coding (often 5 to 10 times the video duration), fragmented toolchains, and closed commercial suites. SEMANTIC provides an open, modular alternative where machine learning pre-annotates, and the expert accepts, rejects or corrects.

## Architecture

SEMANTIC is organised in four layers.

```
 Session (video, audio, inertial, physio, eye-tracking)
        |
        v
 [1] Data Acquisition & Synchronization      LabStreamingLayer (LSL), ms-level sync
        |
        v
 [2] SEM Orchestration & Processing          Semantic Expert Modules (SEM) -> "sema"
        |
        v
 [3] SEMANTIC Integration                    "sema" -> "semata" (rule-based, then adaptive)
        |
        v
 [4] Validation & Governance                 Timeline UI, human-in-the-loop, FAIR export
```

1. **Data Acquisition & Synchronization.** Heterogeneous streams are synchronised with LabStreamingLayer, independently of device and sampling rate. Local-only operation is supported for institutions that cannot transmit data.
2. **SEM Orchestration & Processing.** A *Semantic Expert Module* (SEM) wraps one or more ML models behind a standard interface: input specification, output schema (label, value or event, timestamp, confidence) and metadata (provenance, **license**, compute requirements, local or cloud execution). The orchestration engine routes work locally or remotely based on privacy, latency and resources. SEM outputs are elementary signals called *sema* (e.g. vocalization, gaze fixation, object grasped).
3. **SEMANTIC Integration.** *Sema* are combined into higher-level, clinically interpretable constructs called *semata* (e.g. joint attention, turn-taking, engagement change). Phase 1 is rule-based and transparent; phase 2 learns from clinician corrections (sequence models, temporal graph networks) while keeping interpretability.
4. **Validation & Governance.** A timeline interface overlays synchronised data, *sema* and *semata*; clinicians accept, reject or correct each suggestion, and corrections feed back into the system. Data are anonymised or pseudonymised, and curated outputs are exported in FAIR-compliant formats.

### Reference SEMs

| Domain | Examples |
|---|---|
| Computer vision | pose and gesture, object and hand detection, depth from video, segmentation, gaze estimation |
| Speech and language | speech recognition, diarization, turn-taking, multilingual translation |
| Physiological | HRV, electrodermal activity, EEG-derived arousal and cognitive load |
| Inertial | activity recognition from wearables and sensorized toys (IMU) |

### Research questions

RQ1 validity of semi-automated annotations vs expert coding; RQ2 added value of multimodal over unimodal models; RQ3 sensitivity to within-subject longitudinal change; RQ4 cross-site transferability of models.

### Work packages

| WP | Topic | Months |
|---|---|---|
| WP0 | Project management and coordination | 1-36 |
| WP1 | Data acquisition and synchronization | 1-12 |
| WP2 | SEM SDK and orchestration engine | 6-24 |
| WP3 | Practitioner interface and human-in-the-loop | 9-30 |
| WP4 | SEMANTIC engine: integration and learning | 18-36 |
| WP5 | Dissemination, open science, sustainability | 24-36 |

---

## Licensing

SEMANTIC is composed of several kinds of artifacts, each with its own license. The goal is a platform that anyone can study, modify, redistribute and use commercially, without depending on the original authors or institutions.

### 1. License summary

| Artifact | License | Notes |
|---|---|---|
| SEMANTIC source code (orchestration engine, SEM SDK, integration engine, practitioner interface, tooling) | **Apache License 2.0** (`SPDX: Apache-2.0`) | See [`LICENSE`](LICENSE). Permissive: use, modify, redistribute and sell, with an explicit patent grant. |
| Documentation, tutorials, annotation guidelines | **CC BY 4.0** | Proposed. |
| Scientific publications | **CC BY 4.0** | Required by SNSF (immediate open access). |
| Anonymised datasets | **CC BY 4.0** (proposed) | Only anonymised or derived data, released on FAIR repositories (e.g. Zenodo, OSF). Sensitive raw recordings (video, audio of identifiable people) are **never** released. |
| Trained models and weights produced by the project | **To be defined case by case** | Decided as each model is produced; see the data-protection and ownership review in section 4. |
| Third-party SEMs and models | **Their own license** | See section 3. Each SEM declares its license in its manifest. |

Why Apache-2.0 rather than MIT: both are permissive; Apache-2.0 adds an explicit patent grant and a clear contributor licensing clause, which suits a clinical-research platform with several institutions contributing.

### 2. Licensing model for the architecture

- **Core stays permissive.** The core (engine, SDK, interface, integration layer) depends only on permissively licensed libraries (Apache-2.0, MIT, BSD).
- **Models are plug-ins, not part of the core.** Each SEM runs as a separate, optional module (preferably out-of-process, e.g. a container) and is **not** bundled in the core distribution. A copyleft or non-commercial model therefore never changes the license of the core.
- **License-aware orchestration.** The SEM manifest includes a `license` field (SPDX identifier) for both code and weights, plus flags such as `commercial_use` and `copyleft`. The engine can display it and let an institution filter or block SEMs by license policy.
- **Separate code and weights.** Code and pretrained weights often have different licenses (see section 3); the manifest records both.

### 3. Third-party components (reference SEMs)

> **Draft, to be verified.** Licenses change between versions. Verify the license of the exact version and weights before each release, and record it in the SEM manifest.

| Component | Used for | License (code / weights) | Impact |
|---|---|---|---|
| LabStreamingLayer (liblsl) | Stream synchronization | MIT | Compatible. Can be a core dependency. |
| OpenAI Whisper | Speech recognition | MIT / MIT | Compatible. |
| SAM 2 (Segment Anything) | Segmentation | Apache-2.0 / Apache-2.0 | Compatible. |
| DINOv2 | Visual representations | Apache-2.0 / Apache-2.0 (older releases were CC BY-NC) | Compatible; check the exact release. |
| Video Depth Anything | Depth from video | Apache-2.0 / Small: Apache-2.0, Large: **CC BY-NC 4.0** | Use the Small model for open/commercial use; Large is non-commercial. |
| GAZELLE (gaze estimation) | Gaze, joint attention | MIT (to verify) | Verify before release. |
| SLEAP | Pose tracking | BSD-3-Clause | Compatible. |
| DeepLabCut | Markerless pose | LGPL-3.0 | Use as a separate module or dynamic dependency; do not vendor. |
| Ultralytics YOLO (v8 to v11) | Object, hand and gesture detection | **AGPL-3.0** (or commercial Enterprise license) | **Copyleft.** Keep strictly optional and out-of-process; do not bundle in the Apache-2.0 distribution. |
| OpenPose | Body pose | **Non-commercial** academic license (commercial use requires a paid license) | **Not usable for open commercial use.** Optional only; prefer alternatives. |
| OpenFace 2.x | Facial behaviour, gaze | **Non-commercial** academic license | Same as above. |
| SeamlessM4T | Multilingual translation | MIT (code) / **CC BY-NC 4.0** (weights) | Weights are non-commercial. Optional only. |

Permissive alternatives to evaluate where a non-commercial or copyleft component is a problem: MediaPipe (pose, hands, face; Apache-2.0), YOLOX and RT-DETR (detection; Apache-2.0), Whisper-family models for speech.

Behaviour-coding tools such as BORIS, ELAN, Datavyu and NOVA are used as **points of comparison and possible import/export formats**, not as dependencies. Several are GPL-licensed, so no code should be copied from them into this repository.

### 4. Open points to settle before the first public release

- **Copyright holder and notice.** Define the holder line (SUPSI and/or contributors) and add a `NOTICE` file and SPDX headers to source files.
- **Background IP.** Identify what each institution brings in as background IP (for example, the inertial classifiers from the SNSF Sinergia project *Digital Phenotyping of ASD in Children* belong to SUPSI) and how it is licensed or excluded from the public repository. To be confirmed with the SUPSI IP/legal office.
- **Inventorship and authorship.** Inventor: Mirko Gelsomini. Authors will be defined progressively as the project evolves; record both in `AUTHORS` and `CITATION.cff` (inventor and authors are distinct from the list of contributors).
- **Trained models and partner data.** The license of trained models will be defined case by case as models are produced. For models trained on partner (clinical) data, also check that weights cannot leak personal data.
- **Dataset license and access.** Confirm CC BY 4.0 for anonymised data, and a restricted-access route (data use agreement) for anything that cannot be fully anonymised.
- **Contributions.** Choose between a Developer Certificate of Origin (DCO sign-off) and a contributor license agreement for external contributors.
- **Third-party audit.** Run a license scan (e.g. `pip-licenses`, `license-checker`, ScanCode) on every dependency and container image, and re-check section 3 at each release.
- **Trademark.** The names SEMANTIC and SENSEi are not licensed by Apache-2.0; decide a trademark/name-use policy if needed.

### 5. Compliance and acknowledgement

- **SNSF:** publications and communications must state that the work is funded by the SNSF (with logo). SNSF claims no intellectual property rights on results; open access and open research data (FAIR, with a Data Management Plan) are mandatory.
- **Data protection:** Swiss FADP and EU GDPR. Informed consent, ethics approval, pseudonymisation and local-only processing options are part of the design. Licenses on code and data do not replace consent or data-protection obligations.

---

## Repository layout

Current prototype layout (final layout to be defined; planned components also include `engine/`, `sdk/`, `integration/`, `ui/`, `docs/`):

```
sems/                  Semantic Expert Modules (one folder per SEM)
  body-posture/        MediaPipe Pose Landmarker -> posture label + 33 landmarks
  face-tracking/       MediaPipe Face Detector   -> face count + positions
  segmentation/        MediaPipe DeepLabV3       -> per-class coverage + masks
  stt/                 faster-whisper            -> speech segments with text
  <name>/
    manifest.json      SEM metadata: id, licenses (code AND weights), provenance, I/O schema
    sem.py             self-contained CLI entrypoint
    requirements.txt   SEM-specific dependencies, licenses noted in comments
    models/            model files downloaded on first run (gitignored)
tools/
  render_overlay.py    renders sema back onto the video (skeleton, faces, masks, subtitles)
data/                  gitignored, never committed
  input/               source recordings (sensitive, local only)
  output/              sema and rendered videos, one folder per input file
```

## Working with the prototype

### Installation

Requires Python 3.11+ on Windows, macOS or Linux. `ffmpeg` on PATH is optional (used to copy the audio track into rendered videos).

```
python -m venv .venv
.venv/Scripts/pip install -r sems/body-posture/requirements.txt  # also covers face-tracking and segmentation
.venv/Scripts/pip install -r sems/stt/requirements.txt
.venv/Scripts/pip install pillow                                 # renderer only (subtitle text)
```

Model weights are downloaded automatically on first run and cached (`sems/*/models/` for MediaPipe tasks, the Hugging Face cache for Whisper). Everything runs locally; no data leaves the machine.

### Data conventions

- Put source recordings anywhere under `data/input/`. The whole `data/` tree is gitignored: **never commit recordings or derived outputs.**
- Every SEM derives its default output location from the input file name: `data/output/<input-stem>/<sem-name>.jsonl`. Pass `--output` to override.
- The segmentation SEM additionally writes class-id mask PNGs to `data/output/<input-stem>/segmentation-masks/`.
- The renderer writes `data/output/<input-stem>/<input-stem>_overlay.mp4`.

### Running the SEMs

Each SEM is a self-contained CLI; `--input` is the only required argument.

```
.venv/Scripts/python sems/body-posture/sem.py  --input video.mp4 [--sample-fps 5] [--max-seconds 30]
.venv/Scripts/python sems/face-tracking/sem.py --input video.mp4 [--sample-fps 5] [--max-seconds 30]
.venv/Scripts/python sems/segmentation/sem.py  --input video.mp4 [--sample-fps 5] [--max-seconds 30]
.venv/Scripts/python sems/stt/sem.py           --input video.mp4 [--model base] [--language it] [--max-seconds 30]
```

Then render all available sema onto the video:

```
.venv/Scripts/python tools/render_overlay.py --input video.mp4 [--max-seconds 30]
```

### Sema format

SEM outputs are JSON Lines, one event per line, with a shared envelope:

```json
{"source": "<sem-id>", "type": "<sema-type>", "t": 12.3, ...}
```

`t` is seconds from the start of the recording. Depending on the sema type the event carries `label` (categorical), `value` (numeric), or event fields such as `t_end` and `text`, plus `confidence` in [0, 1] where the model provides one. The sema types of each SEM are declared in its `manifest.json`.

### Adding a new SEM

1. Create `sems/<name>/` with `manifest.json`, `sem.py` and `requirements.txt`.
2. Declare in the manifest: `id`, the license of **both code and weights** (SPDX identifiers), `commercial_use` / `copyleft` flags, provenance (including model asset URLs), and the input parameters and output sema types.
3. Keep the SEM self-contained: own dependencies, own model download, CLI with `--input`/`--output` and the default output convention above. No SEM may become a dependency of the core.
4. Check the license of every new dependency before adding it; note it in `requirements.txt`. Copyleft or non-commercial models can be used for internal experiments but are excluded from public releases (see Licensing).

## Citation

A `CITATION.cff` and DOI (Zenodo) will be added with the first stable release.

## License

Source code: [Apache License 2.0](LICENSE). See the Licensing section above for documentation, data, models and third-party components.
