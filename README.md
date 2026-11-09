# Class of '09: The Flip Side - Nintendo Switch homebrew runtime

A native Nintendo Switch homebrew runtime for *Class of '09: The Flip Side*,
based on the first game's Switch runtime by CatsAndGats.

This is not Ren'Py. The game script is interpreted from the same `script.json`
used by the PS5 build, with SDL2 handling video, input, audio, images and fonts.
The retail WebM cutscenes are decoded directly with libvpx (VP8) and libvorbis.
No game assets are included in this repository.

## Features

- Retail 1920x1080 VP8 videos decoded with a NEON-optimized libvpx build.
- NEON YUV-to-RGB565 conversion with a 3:2 sample to 1280x720 for upload;
  the GPU scales the final frame. No re-encoded or pre-scaled video files.
- Threaded video decode with a bounded catch-up loop, so a slow frame cannot
  stall the menu or snowball into a backlog.
- Dialogue voice decoded with minimp3 and played from cached PCM chunks at
  48 kHz (the mixer rate), with frame-aligned offsets.
- Animated Flip Side main menu: retail `gui/menu 2/BG living.webm` loop behind
  the cropped Flip Side navigation art, with `menu_bg.png` as the immediate
  fallback.
- Flip Side dialogue layout: character names on the lined paper at `y=840` and
  dialogue starting below them at `y=905`.
- Five opening variants (`scene_S0001A` through `scene_S0001E`), all five
  endings, the three unlockable secret videos, rollback, and the new-message
  animation.
- Phone-style ending tracker with the inbox list, message text view and the
  secret-video rows.
- Nintendo Switch controls: A confirm, B back/rollback, Y ending tracker,
  D-pad or left stick, + or - pause.

## SD card layout

```
sdmc:/switch/class09fs/class09fs.nro
sdmc:/switch/class09fs/assets/script.json
sdmc:/switch/class09fs/assets/game/...      (your game files, incl. .webm videos)
```

Saves, ending records and screenshots are written to
`sdmc:/switch/class09fs/saves/`; the console creates the folder on first run.

## Controls

| Button | Action |
| --- | --- |
| A | advance / confirm |
| B | step back one line / back |
| Y | ending tracker (main menu) |
| D-pad / left stick | navigate |
| + or - | pause menu |
| HOME | close the app |

Opening videos can be skipped with A after a short delay.

## Building

Requires devkitPro with the `switch-dev` group - devkitA64, libnx,
switch-sdl2, switch-sdl2_image, switch-sdl2_mixer, switch-sdl2_ttf - plus
switch-libvpx and switch-libvorbis.

```sh
export DEVKITPRO=/opt/devkitpro
export PATH=$DEVKITPRO/tools/bin:$DEVKITPRO/devkitA64/bin:$PATH
make -j8
```

Build from a path without spaces: the devkitPro makefiles do not quote paths.

The Makefile links `deps/lib/libvpx.a` first. It is libvpx 1.8.0 built for
`arm64-linux-gcc` with NEON enabled. The packaged `switch-libvpx` is built with
`--target=generic-gnu` (pure C, no NEON), which is far too slow for 1080p VP8.
See `deps/README.md` for the rebuild script and flags.

## Repository layout

```
source/        engine.c/h, webm.c/h, minimp3, json_lite, main.c, trace
deps/lib/      prebuilt NEON libvpx.a and its build notes/script
Makefile       devkitPro build (target class09fs)
icon.jpg       homebrew icon
```

## Legal

*Class of '09: The Flip Side* and its assets belong to their developers and
publishers. No game assets are included in this repository; use your own copy.
Do not redistribute the game assets.

Unofficial fan project. Not affiliated with the developers, publishers,
Nintendo, or Sony.
