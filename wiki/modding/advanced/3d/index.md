---
author: clairedeluneee, Whisper
desc: This page explains how Foxlite can be used for 3D rendering for your mod.
lastUpdated: 2026-03-03T20:36:15.789Z
title: Advanced Topics - 3D with Foxlite
---

# "The hell's Foxlite?"
Foxlite is a 3D renderer for HaxeFlixel that replaces the built-in Away3D renderer, designed to be hardware-accelerated (you get more FPS) and features a flexible rendering pipeline that basically means that your 3D scenes can be photorealistic.

## "Great, now what makes this different from Away3D?"
The primary difference Foxlite has from Away3D is that, apart from being feature-rich (with more features to come), the setup required is much less than what's needed for Away3D, and comes with reduced jank.

From the repository in the [project's GitHub](https://github.com/dwdvIl/foxlite), the following features are also included:

- `glTF` support, which also includes `.glb` files, handy for containing the materials of your models and whatnot inside one glorified zip file.
- Dynamic lighting, shadows, and a customizable shape for area lights. Yes, you can use a picture of a cat for area lights.
- Materials are pushed into shaders, allowing you to use the same material on multiple shaders. They are part of the batching system Foxlite has, meaning meshes that use the same material tend to give more frames.
- Skeletal animations and instancing. You can, in fact, place 500 pears on the scene without your iGPU giving out.
- Support for motion vectors and Forward+ shaders. In Layman's terms, scenes may be photoreal if you want it to be.
- Animation engine built from the ground up, allowing **any** object to be animated with its linking system, with animation layering and 3 mix nodes at your disposal.

# Prerequisites
- A copy of Codename Engine that has Foxlite.
  - To be exact, Foxlite replaced Away3D in commit `8378ae0` in the `internal-merge` branch.
  - As of writing, [v1.1.0-rc2](https://github.com/CodenameCrew/CodenameEngine/releases/tag/v1.1.0-rc2) is the latest version with Foxlite integration. You may grab this, but it's highly advised to turn to the latest Experimental builds, as there might be bugs in `rc2` that were only fixed in later commits.
- A GPU (either discrete or integrated) that supports OpenGL 2.1.
- Any code editor of your choosing.
- A 3D modeling software capable of exporting to Wavefront `.obj` files.
  - Blender 5.1.2 is recommended and will be used for this guide.

# Putting a model in Foxlite
## Blender 
When you're ready to export a model for Foxlite, export it as an `.obj` file.

To do this in Blender, you click `File` > `Export` > `Wavefront (.obj)` Make sure that the *Triangulated Mesh* checkbox is checked under Geometry.

## Codename

### Foxlite initialization
Foxlite must first be initialized before it can be used. Below is something you can put as a global script:
```haxe
function create() {
  FoxRenderer.initLibs();
  FoxLoaderUtil.initPathClass(Paths);
}
```
Do note that this has to only be ran once.

### Basic scene setup
The simplest way of showing off models is via a [custom state](../../scripting/states.md), which we will use for this guide. Nobody is stopping you from implementing this in a song script, but it's easier to use a custom state.

You will need the following imports and variables:
```haxe
import foxlite.renderer.FoxRenderer;
import foxlite.loaders.FoxLoaderUtil;
import foxlite.FoxScene;
import foxlite.extras.FoxFPSCamera;

var scene:FoxScene;
var cam:FoxFPSCamera;
```

The `scene` variable extends `FunkinSprite` and will be what displays in-game. 

```haxe
function create() {
    scene = new FoxScene(FlxG.width, FlxG.height);
    scene.zoomFactor = 0;
    scene.scrollFactor.set(0, 0);
    add(scene);
}
```
