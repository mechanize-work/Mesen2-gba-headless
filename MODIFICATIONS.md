# Modifications from upstream Mesen2

This directory contains a fork of Mesen2, redistributed under GPL-3.0-only
(see `LICENSE`). Per GPL-3.0 §5(a), the file was modified; this document
records what was changed and when. The authoritative record of each
change is the git history of this repository — run
`git log -- third_party/mesen/` for per-file commit detail and dates.

## Upstream

- Project: Mesen2
- Upstream repository: <https://github.com/NovaSquirrel/Mesen2>
- License: GPL-3.0-only (unchanged in this fork)

## Summary of changes

The fork is a minimal trim of Mesen2 to make its GBA core usable as a
single-threaded, headless reference oracle compiled to WebAssembly. The
emulation logic — CPU, PPU, DMA, timers, and APU — is unchanged relative
to upstream. Our changes are confined to:

1. **Single-threaded operation.** Removed thread launches from the
   `Emulator` lifecycle so the emulator runs in the caller's thread.
   Required for WebAssembly (no native threads) and for deterministic
   lockstep stepping in the grader.
2. **GBA-only dispatch.** Removed multi-console routing in the
   `Emulator` entry points so only the GBA code path is reachable.
   Strictly a trim; no behavioral change to the GBA core itself.
3. **NTSC-style filtering removed.** The optional NTSC video filter and
   associated scanline/colour-matrix code path has been stripped, since
   it is not applicable to the GBA LCD pipeline.

Net diff against upstream for the GBA emulation code is on the order of
+5 / −34 lines, all deletions. No additions to CPU, PPU, DMA, timer,
or APU implementation.

## Headless build harness (new, not upstream)

The following files in this repository are new and were not derived
from upstream Mesen2. They call into the (modified) Mesen code above
but are themselves original works; they carry their own license
headers:

- `reference/mesen_step.cpp`
- `reference/mesen_candidate.cpp`
- `reference/mesen_crosscheck.cpp`
- `reference/build-mesen.sh`, `build-mesen-candidate.sh`

These files link against the modified Mesen statically and are
therefore GPL-3.0-only as combined works. See `reference/LICENSE`.

## How to reproduce the fork

```
git clone https://github.com/yang-29/gba-wasm
cd gba-wasm
git log -- third_party/mesen/
```

Full source — upstream Mesen2 plus every modification listed above — is
present in this repository. This satisfies GPL-3.0 §5 and §6 for anyone
who obtains the compiled `mesen_step.wasm` or the `harness/grader`
binary.
