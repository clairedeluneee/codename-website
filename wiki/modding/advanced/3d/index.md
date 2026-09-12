---
author: clairedeluneee, Whisper, dwdvIl
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

To do this in Blender, you click `File` > `Export` > `Wavefront (.obj)` Make sure that the *Triangulated Mesh* checkbox is checked under the Geometry section before exporting.

You may also export the model as a `.gltf` or a `.glb` if you prefer.

## Codename

### Foxlite initialization
Foxlite must first be initialized before it can be used. Below is something you can put as a global script:
<div style="display: grid; justify-content: left;">

```haxe
function create() {
  FoxRenderer.initLibs();
  FoxLoaderUtil.initPathClass(Paths);
}
```
</div>

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

The `scene` variable extends `FunkinSprite` and will be the "window" between Flixel's worldspace and Foxlite's scene. Think of it as a television that, for now, won't display anything because we have not told it what to display.
<div style="display: grid; justify-content: left;">

```haxe
function create() {
    scene = new FoxScene(FlxG.width, FlxG.height);
    scene.zoomFactor = 0;
    scene.scrollFactor.set(0, 0);
    add(scene);
}
```
</div>

Notice that the `scene`'s `zoomFactor` and `scrollFactor` fields were set to 0. This is because `FoxScene`s inherit `FunkinSprite`'s properties and move and scale with the camera's position and zoom respectively.

### Cameras
To render to your scene, a `FoxCamera` must be instantiatied. For this guide, an extension of this class known as `FoxFPSCamera` is used, which allows you to move the camera around in the scene. 

- WASD is used for lateral movement.
- Hold down Left Shift to move faster.
- Hold click and drag to pan the camera around.
<div style="display: grid; justify-content: left;">

```haxe
cam = new FoxFPSCamera();
cam.bgColor = FlxColor.PURPLE;
```
</div>

To add this to the scene, call
<div style="display: grid; justify-content: left;">

```haxe
scene.foxCameras.push(cam);
```
</div>

The full `create` function at this point should look like this:

<div style="display: grid; justify-content: left;">

```haxe
function create() {
    // Foxlite initialization
    FoxRenderer.initLibs();
    FoxLoaderUtil.initPathClass(Paths);

    // Scene
    scene = new FoxScene(FlxG.width, FlxG.height);
    scene.zoomFactor = 0;
    scene.scrollFactor.set(0, 0);
    add(scene);

    // Camera
    cam = new FoxFPSCamera();
    cam.bgColor = FlxColor.PURPLE;

    // Add our camera to the scene
    scene.foxCameras.push(cam);
}
```
</div>

### Cleanup
To prevent memory leaks, the scene must be destroyed to free up the memory it allocates.

This can be done through this snippet:

<div style="display: grid; justify-content: left;">

```haxe
function destroy() {
    scene?.destroy();
}
```
</div>

### Adding a model in the `.OBJ` format
Models exported to the Wavefront (`.obj`) format may be imported in one of two ways: using the dedicated `FoxOBJLoader` class or via the `loadOBJ` method found in `FoxModel` instances. Either can be used to load these, but for this guide, the latter will be used.

<div style="display: grid; justify-content: left;">

```haxe
model.loadOBJ("path/to/model.obj");
```
</div>

To add this model to the scene, you treat the model like adding a sprite to the camera, like so:

<div style="display: grid; justify-content: left;">

```haxe
scene.add(model);
```
</div>

`insert` and `remove` methods are also available for `FoxScene` which function similarly to how Flixel does it.

By default, `FoxCamera`s and `FoxModel`s appear at coordinates (0, 0, 0) if you have not set them beforehand.

### Adding a model in the `.GLTF` / `.GLB` format

`.gltf` models function similarly to `.obj` models, except they are more optimized for web rendering due to its size and how they can fit roughly all details of a scene, as well as the fact that `.glb`s are simply zip files that contain both the scene and the materials it uses.

To import a `.gltf` model or a `.glb` model, use the `FoxGLTFLoader` class, then depending on what you exported it as, use either `.load` for `.gltf`s, or `.loadBinary` for `.glb`s.

<div style="display: grid; justify-content: left;">

```haxe
var gltfModel = FoxGLTFLoader.load("path/to/model.gltf"); // for gltfs
// or 
var gltfModel = FoxGLTFLoader.loadBinary("path/to/model.glb"); // for glbs
```
</div>

Once the model is loaded, you can access them as whole FoxObjectGroups via .scenes
And you can add them to your scene like this:

<div style="display: grid; justify-content: left;">

```haxe
scene.add(gltfModel.scenes[index]);
```
</div>

#### Animations
`.gltf`s (and by extension, `.glb`s) contain a dedicated player at the root of the object which contains all the animations of the model in Blender.

You can play one by doing this:

<div style="display: grid; justify-content: left;">

```haxe
var player:FoxAnimationPlayer = gltfModel.scenes[0].animation;
player.play("<your anim name>");
```
</div>
