# CSDM patch: sync EndCapture for mirv_pov screen-ffmpeg

## Problem
With WangChuDi `mirv_pov` enabled, HLAE `mirv_streams record screen` (ffmpeg → `video.avi`) often leaves only `audio.wav`. CS2 exits with `ACCESS_VIOLATION` (`0xC0000005`). Upstream `EndCapture` defers `ShutDown` / `delete` until the next Present; that Present never runs, so ffmpeg never finalizes `video.avi`.

## Fix
`AfxHookSource2/RenderSystemDX11Hooks.cpp` — `EndCapture()` now calls `ShutDown(nullptr)` and `delete` synchronously.

## Following WangChuDi updates
This repo is a fork of `WangChuDi/advancedfx`.

- `.github/workflows/sync-upstream.yml` — every ~6h GitHub `merge-upstream` into `main` (conflict → issue).
- `.github/workflows/ci.yml` — on push to `main`, build `AfxHookSource2` and publish a prerelease (DLL zip + corresponding source zip). Release zip also includes `AfxHookSource2.mirv-pov.dll` for CS Demo Manager.
- `.github/workflows/watch-wangchudi.yml` — reminds if the fork is behind/diverged.

After a clean upstream merge, CI rebuilds and publishes automatically.

## License
`mirv_pov` remains AGPL-3.0-only; each prerelease ships the matching source archive.
