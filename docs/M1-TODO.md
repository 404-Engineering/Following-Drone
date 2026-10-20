# M1 — 3D Mapping: To-Do List

Milestone 1 from [DESIGN.md](DESIGN.md): ZED + Jetson capture, SVO recording, mapping, and post-processing. Software only, no flight.
Each phase lines up with the test phases in DESIGN.md section 10.1. Record results in DESIGN.md as you go.

---

## 0. Before you start

- [ ] Fill in the `TBD` targets in DESIGN.md section 1.4:
  - [ ] SVO recording length (min)
  - [ ] Indoor mapping accuracy (± cm)
  - [ ] Max hole size in the mesh (cm)
  - [ ] Tracking drift after a closed loop (cm)
  - [ ] Live mapping frame rate on the Orin Nano (fps)
  - [ ] Trail section length (m)
- [ ] Choose the ZED model (ZED 2i vs. ZED Mini). See risk #1 in section 12.
- [ ] Gather hardware:
  - [ ] Jetson Orin Nano + power supply
  - [ ] ZED camera + its USB 3 cable (use the supplied or a known-good USB 3 cable)
  - [ ] NVMe SSD for SVO storage
  - [ ] Keyboard, mouse, monitor (or plan for SSH / remote desktop)
- [ ] Decide where M1 code lives in the repo (e.g. `software/mapping/`).
- [ ] Add a `.gitignore` entry for SVO files and large meshes (`*.svo`, `*.svo2`, `*.ply`, `*.obj`) so recordings don't end up in git.

---

## Phase 1 — ZED bring-up on the Jetson (FR-01)

- [ ] Install the NVMe SSD.
- [ ] Flash JetPack (record the version in DESIGN.md 6.1).
- [ ] Set the Jetson power mode and record it (`nvpmodel`).
- [ ] Install the ZED SDK version that matches your JetPack (record the version in 6.1).
- [ ] Plug in the ZED and confirm USB 3: `lsusb -t` shows the camera at **5000M**.
- [ ] Run the ZED diagnostic tool and fix anything it flags.
- [ ] Open ZED Explorer / Depth Viewer and check the image and depth look sane.
- [ ] **Stability test:** stream HD720 @ 30 fps for 10 minutes with no dropped frames.
- [ ] Watch temperatures and throttling during the test (`tegrastats`).
- [ ] Record the results in DESIGN.md (1.4 and 10.3).

**Done when:** USB 3 link confirmed and the 10-minute stream has no dropped frames.

---

## Phase 2 — SVO recording and playback (FR-02)

- [ ] Write a small recording script (Python `pyzed` or C++) that:
  - [ ] Records to the NVMe SSD
  - [ ] Takes resolution, fps, and compression as settings
  - [ ] Logs dropped frames
  - [ ] Stops cleanly if storage is nearly full
- [ ] Compare compression modes; measure file size per minute and choose one.
- [ ] Record for your target length with no dropped frames.
- [ ] Play the SVO back and confirm it replays correctly.
- [ ] Note storage needed per minute and how many minutes fit on the SSD (risk #4).
- [ ] Set up a way to copy SVO files to your Mac (e.g. `rsync` over SSH).

**Done when:** a full-length recording plays back with no dropped frames.

---

## Phase 3 — Indoor mapping (FR-03)

- [ ] Pick a test room.
- [ ] Tape-measure 3–5 reference distances (wall to wall, a table length, a door width) and write them down.
- [ ] Record SVOs of the room:
  - [ ] Slow walk covering every surface
  - [ ] A closed loop that returns exactly to the starting point (for drift)
- [ ] Write a mapping script that runs positional tracking + spatial mapping on an SVO file.
- [ ] Try the settings from DESIGN.md 6.3 and note the results:
  - [ ] Depth mode
  - [ ] Mapping resolution
  - [ ] Mapping range
  - [ ] Mesh vs. fused point cloud
- [ ] Run mapping live on the Jetson and measure the frame rate.
- [ ] Measure against your targets:
  - [ ] **Accuracy:** reference distances in the mesh vs. tape measure
  - [ ] **Coverage:** all surfaces present, no holes above the limit
  - [ ] **Drift:** start vs. end pose of the closed loop
  - [ ] **Performance:** live mapping fps
- [ ] Record the chosen settings and results in DESIGN.md (6.3, 10.3, decision log).

**Done when:** accuracy, coverage, drift, and performance targets are met (or targets are revised with a reason).

---

## Phase 4 — Export and visualization (FR-04, FR-05)

- [ ] Export maps as `.ply` and/or `.obj`.
- [ ] Choose a desktop viewer (CloudCompare, MeshLab, or Open3D) and record it in 1.4 and 6.1.
- [ ] Open an exported map on your Mac.
- [ ] Decide the post-processing steps (filtering, decimation, merging runs, terrain measurement) and fill in 1.4.
- [ ] Script the post-processing so it's repeatable.

**Done when:** a map exported from the Jetson opens and processes on your Mac with a repeatable script.

---

## Phase 5 — Jeep-mounted trail mapping (FR-03)

- [ ] Design and build a ZED mount for the jeep (roof or bull bar), rigid with vibration damping.
- [ ] Power the Jetson from the jeep's 12 V through a DC-DC converter.
- [ ] Route and secure the USB cable; keep the Jetson in the cab.
- [ ] Protect the camera from dust and direct sun glare where possible.
- [ ] Log a GPS track during the drive for comparison (a phone GPS logger is enough for now).
- [ ] Record a trail section of your target length.
- [ ] Run mapping on the recording and compare the path to the GPS track.
- [ ] Note problems from sunlight, dust, low texture, vibration, and speed.
- [ ] Record results and lessons in DESIGN.md (10.3, risks #2 and #5).

**Done when:** you have a continuous map of the trail section and know how far it drifts from the GPS track.

---

## Phase 6 — GNSS-referenced mapping (stretch, FR-06)

- [ ] Connect a GPS module to the Jetson (e.g. the u-blox you plan to use on the drone, over USB).
- [ ] Set up the ZED SDK Fusion module with GNSS input.
- [ ] Record a trail run with GPS + ZED together.
- [ ] Generate a geo-referenced map and check its alignment against the GPS track.

**Done when:** the map aligns with the GPS track within your target.

---

## Wrap-up

- [ ] Update DESIGN.md: results in 10.3, phase status in 10.1, chosen settings in the decision log, revision history.
- [ ] Close or update M1 risks in section 12 (#1–#5).
- [ ] Commit code, scripts, and docs (not the SVO files).
