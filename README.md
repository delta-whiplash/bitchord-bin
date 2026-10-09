# bitchord-bin

AUR package for [BitChord](https://github.com/kushagrasinghx/BitChord) — a modern YouTube Music client with clean aesthetics inspired by Apple Music.

Installs the prebuilt `jpackage` image (self-contained, bundled Java runtime) from the upstream `.deb` release asset.

```sh
paru -S bitchord-bin
```

Launch with `bitchord` or from your app menu.

## HiDPI / Wayland note

The app is a Compose Desktop (AWT) application, so it runs through **XWayland**.
The packaged `/usr/bin/bitchord` wrapper detects the focused monitor's scale
(Hyprland `hyprctl`, Sway `swaymsg`) and passes it to the JVM as
`-Dsun.java2d.uiScale`, which supports fractional scales like 1.25 — so the UI
is drawn at the correct size everywhere. Desktops with XSettings (GNOME, KDE)
scale automatically and need no wrapper help. A user-set `GDK_SCALE`, or
`uiScale` in `JAVA_TOOL_OPTIONS`/`JDK_JAVA_OPTIONS`, always takes precedence.

On compositors **without** XSettings (Hyprland, Sway, …), XWayland buffers are
additionally upscaled by the compositor unless zero scaling is forced. For a
pixel-perfect render, enable it once in your compositor config:

- Hyprland: `xwayland { force_zero_scaling = true }`
- Sway: `xwayland enable` renders 1:1 by default; no extra step needed.

Without it, sizes are still correct (the wrapper handles that) but the
compositor's upscale leaves the render slightly soft.
