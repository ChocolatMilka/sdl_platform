<div align="center">

# 🎛️ sdl_platform

### A thin [**Axle**](https://axle-lang.dev) platform crate over **SDL2** + libc

Window, renderer & streaming texture · event poll · keyboard + relative-mouse state · frame timing · a software 3D audio mixer · raw memory helpers · minimal binary file IO.

<p align="center">
  <a href="https://axle-lang.dev"><img alt="Powered by Axle" src="https://img.shields.io/badge/powered%20by-Axle-5B4BE1?style=for-the-badge&labelColor=1b1b2b"></a>
  <a href="https://axle-lang.dev"><img alt="Axle 0.4.4+" src="https://img.shields.io/badge/axle-0.4.4%2B-5B4BE1?style=for-the-badge&labelColor=1b1b2b"></a>
</p>
<p align="center">
  <img alt="SDL2" src="https://img.shields.io/badge/backend-SDL2-1D6FB8?style=flat-square&labelColor=1b1b2b">
  <img alt="Dependency" src="https://img.shields.io/badge/dependency-local%20path%20%2F%20submodule-2E7D32?style=flat-square&labelColor=1b1b2b">
</p>

</div>

---

Extracted from the [`voxel-axle`](../..) game so the SDL/libc bindings live once and can be reused by any Axle project that needs a window, input, audio and raw memory. Its `src/` compiles **together** with the consuming crate — no C shim, no build script — you just `use sdl_platform::…`.

Requires **[Axle](https://axle-lang.dev) v0.4.4+**.

## 📚 Modules (public surface)

| Module   | What it gives you |
|----------|-------------------|
| `sdl`    | raw `SDL_*` calls + thin setup wrappers — `sdlInitVideoAudio`, `sdlCreateWindow`, `sdlCreateVsyncRenderer`, `sdlCreateStreamTexture`, `sdlIsQuit`, `sdlIsMouseWheel`, `sdlLeftHeld`, `sdlRightHeld`, `keyboardBase`, `ticks`, `delay` |
| `mem`    | `malloc` / `cfree`, pointer helpers, typed buffer accessors, `rgb` |
| `file`   | `readFile`, `fileExists`, `loadAtlas` |
| `audio`  | `Clip`, `Mixer`, `SoundBank` — a single-device push mixer with live 3D distance attenuation |
| `lib`    | crate entry — anchors the build and pulls every module into the compile closure (Axle flattens each crate's `pub` items to the crate root, so there is no re-export list) |

> **Note:** Axle exports **functions and types** across a crate boundary, **not** top-level `const`s. That's why the SDL flag constants are hidden behind the `sdl*` wrapper functions rather than exported directly.

## 🔌 Use it (path dependency)

Axle has no package registry yet — dependencies are **local path** only. Vendor this crate next to (or inside) your project and point at it:

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
git submodule add <this-repo-url> vendor/sdl_platform
git clone --recursive <your-project>   # pulls the submodule too
```

## ⚠️ Machine-specific link paths

`axle.toml`'s `[link] paths` point at a **vcpkg SDL2 install** with a hard-coded Windows path:

```toml
[link]
libs  = ["SDL2"]
paths = ["C:/vcpkg/installed/x64-windows/lib", "C:/vcpkg/installed/x64-windows/bin"]
```

Edit those for your machine / OS (`vcpkg install sdl2:x64-windows`, or your distro's SDL2 dev package). On Windows the build copies `SDL2.dll` next to the produced binary from the `bin` path. The consuming project should keep the same `[link]` table so linking + the DLL copy happen for its executable.

## 🧾 A taste of the API

```axle
use sdl_platform::{ sdlInitVideoAudio, sdlCreateWindow, sdlCreateStreamTexture,
                    sdlIsQuit, SDL_PollEvent, Mixer };

sdlInitVideoAudio();
let win : i64 = sdlCreateWindow("hello", 1280, 720);
let tex : i64 = sdlCreateStreamTexture(win, 1280, 720);

let mix : Mixer = new Mixer();          // opens the 44.1 kHz mono S16 device
// … blit your framebuffer into `tex`, mix voices through `mix`, poll events …
```

---

<div align="center">

Part of the **[voxel-axle](../..)** project · written in **[Axle](https://axle-lang.dev)**

</div>
