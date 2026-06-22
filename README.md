# sdl_platform

A thin **Axle** platform crate over **SDL2** + libc: window / renderer /
streaming texture, the event poll, keyboard & relative-mouse state, frame
timing, a software 3D audio mixer, raw memory helpers, and minimal binary file
IO. Extracted from the `voxel-axle` game so the SDL/libc bindings live once and
can be reused.

Requires **Axle v0.2.11+** (developed on 0.2.18).

## Modules (public surface)

- `sdl`   — raw `SDL_*` calls + thin setup wrappers (`sdlInitVideoAudio`,
  `sdlCreateWindow`, `sdlCreateVsyncRenderer`, `sdlCreateStreamTexture`,
  `sdlIsQuit`, `sdlIsMouseWheel`, `sdlLeftHeld`, `sdlRightHeld`),
  `keyboardBase`, `ticks`, `delay`.
- `mem`   — `malloc`/`cfree`, pointer helpers, typed buffer accessors, `rgb`.
- `file`  — `readFile`, `fileExists`, `loadAtlas`.
- `audio` — `Clip`, `Mixer`, `SoundBank` (single-device push mixer with live
  3D distance attenuation).

> Note: Axle exports **functions and types** across a crate boundary, **not
> top-level `const`s**. That's why the SDL flag constants are hidden behind the
> `sdl*` wrapper functions rather than exported directly.

## Use it (path dependency)

Axle has no package registry yet — dependencies are **local path** only. Vendor
this crate next to (or inside) your project and point at it:

```toml
# your-project/axle.toml
[dependencies]
sdl_platform = { path = "vendor/sdl_platform" }   # or "../sdl_platform"
```

```axle
use sdl_platform::{ sdlCreateWindow, SDL_PollEvent, Mixer, rgb, allocBytes };
```

As a git submodule (recommended for reproducible clones):

```bash
git submodule add https://github.com/ChocolatMilka/sdl_platform vendor/sdl_platform
git clone --recursive <your-project>   # pulls the submodule too
```

## ⚠️ Machine-specific link paths

`axle.toml`'s `[link] paths` point at a **vcpkg SDL2 install** with a hard-coded
Windows path:

```toml
[link]
libs  = ["SDL2"]
paths = ["C:/vcpkg/installed/x64-windows/lib", "C:/vcpkg/installed/x64-windows/bin"]
```

Edit those for your machine / OS (`vcpkg install sdl2:x64-windows`, or your
distro's SDL2 dev package). On Windows the build copies `SDL2.dll` next to the
produced binary from the `bin` path. The consuming project should keep the same
`[link]` table so linking + the DLL copy happen for its executable.
