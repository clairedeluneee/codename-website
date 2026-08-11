---
author: ItsLJcool, Skylime_
desc: Explains how to initalize the repository
lastUpdated: 2026-08-11T16:07:08.844Z
title: Modding The Engine - Source Modding
---

# Initialization

Before you touch any of this, read [the introduction](./index.md). It explains why you probably shouldn't be here in the first place.

If you're still going, then here's everything you need to get the engine compiling.

## What you'll need

- **Haxe**, more specifically the ***exact version*** the engine asks for, which right now is **4.3.7**.
**DO NOT** grab the latest version of Haxe just because it's newer.

- **Git.** Get `git-scm` and leave the installer options at it's default.

- **A C++ compiler.** `hxcpp` turns your Haxe into native code, and that needs a compiler.On Windows, that's Visual Studio Build Tools 2019 or newer, and in the Individual Components tab, you want the ***MSVC v143 C++ x64/x86 build tools*** plus the ***Windows SDK***. It's roughly ~1GB to download and ~5.5GB installed. On Linux, you want `g++` and `gcc`, plus `libvlc` for video support. On MacOS, you want `Xcode`.

- **The engine's libraries.** Run `setup-windows.bat` on Windows, or `setup-unix.sh` on Linux and MacOS. Both are in the `building/` folder. Let it finish ***completely***. **Important**: ***don't run `haxelib upgrade` afterwards.*** The engine uses its own forks of Lime, OpenFL, Flixel and hscript, and upgrading swaps them for the normal versions. Things then break in ways that are *very* annoying to track down.

- **An editor.** Of course, this is optional, but recommended. VS Code with the Haxe and Lime extensions is what most people use. Set up the hxcpp debugger ***early on***.
You also need to actually know some things. **Haxe** as a language, how **HaxeFlixel** works, states, cameras, sprites, signals. What **OpenFL** is doing underneath all that, ect.

The first build is **10 to 40 minutes** depending on your specs. Rebuilds after that are anywhere from **30 seconds to a few minutes**. The whole setup will eat around **10GB** once the caches pile up, and ***every extra platform you support is another full build*** and another round of testing.

## Getting set up

Fork the repo on GitHub, then clone your fork.

Run the setup script for your platform and wait for the libraries to install.

Now build the ***completely untouched*** engine:

```bash
haxelib run lime test windows
```

Swap `windows` for `linux` or `mac` depending on what you're on. Running `haxelib run lime setup` once makes the `lime` command global, so from then on you can just type `lime test windows`.

If it runs fine, you know your tools work. ***After that***, make a branch and start editing.

## Useful commands

The engine has helper scripts in `building/`. Run `./cne-windows.bat -help` or `./cne-unix.sh -help` to see what they can do.

`./cne-windows.bat test` builds the game using the source `assets/` folder instead of the exported one, which is a lot nicer while you're actually developing. `lime test` still works normally if you'd rather use that.
