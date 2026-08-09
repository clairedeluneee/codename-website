---
author: ItsLJcool & HeroEyad
desc: Starting place to learn how to use and understand HxVLC
lastUpdated: 2026-08-09T02:58:15.807Z
title: Videos & Cutscenes (HxVLC)
---

# What is HxVLC?
It's a Haxelib Library that adds support for loading videos into your Haxe project, basically all you need to know is; It renders videos!!!

This section will showcase how to use HxVLC in your scripts to show, and play videos!

## How to use it?
Well first to start off we need to initiate it.

In your `global.hx` you should execute the `Handle` like so:
<div style="display: grid; justify-content: left;">

```haxe
import hxvlc.util.Handle;

function new() {
    Handle.init();
}
```
</div>

### Why are we doing this?
This wakes up the VLC core engine ahead of time, so when you play a video (`FlxVideo` or `FlxVideoSprite`) the engine is already ready to go. Depending on your engine setup you may not strictly need it, some engines already init it for you, but calling it yourself makes sure its ready.

# FlxVideo
This class is not directly tied to `FlxState`s, and it's not a normal Sprite, as it renders above the game entirely.
You add it to the display list directly with `FlxG.game.addChild` <b>OR</b> `FlxG.addChildBelowMouse`.

It's constructor parameters specify if it should be `smoothing` (antialiasing).
The class instance has events such `onEndReached`, `onFormatSetup`, `onPlaying`, etc. as `FlxTypedSignal`s
<div style="display: grid; justify-content: left;">

```haxe
import hxvlc.flixel.FlxVideo;

final video:FlxVideo = new FlxVideo();
video.onEndReached.add(() -> {
	video.dispose();
	FlxG.removeChild(video);
});
FlxG.addChildBelowMouse(video);

// You don't need to specify the extension, as you modify your VIDEO_EXT flag in `modpack.ini`
// the `load` functions returns true if sucessfull.
if (video.load(Paths.video("myVideo"))) video.play();
else trace("Video failed to load!");
```
</div>

# FlxVideoSprite
Extends [FlxSprite](https://api.haxeflixel.com/flixel/FlxSprite.html), so it behaves like any sprite; `add()` it to a state/group, position it, scale it, respects cameras. The actual video wrapper lives in the `bitmap` variable, so events and properties like volume go through `bitmap`, while `load`/`play`/`precache`/`pause`/`resume`/`stop` are proxied straight onto the sprite.

NOTE: `bitmap` can be null, if the video doesn't load, so make sure to check if it's `null` before accessing it!
<div style="display: grid; justify-content: left;">

```haxe
import hxvlc.flixel.FlxVideoSprite;

final video:FlxVideoSprite = new FlxVideoSprite();
video.antialiasing = true;

// scale to screen once vlc reports the real dimensions
video.bitmap.onFormatSetup.add(() -> {
	// Exit early if these don't exist.
	if (video.bitmap == null || video.bitmap.bitmapData == null) return;
	/*
		You dont know the real size until onFormatSetup fires, which is why sizing
		lives inside that callback rather than right after load.
	*/
	final scale:Float = Math.min(FlxG.width / video.bitmap.bitmapData.width, FlxG.height / video.bitmap.bitmapData.height);
	video.setGraphicSize(video.bitmap.bitmapData.width * scale, video.bitmap.bitmapData.height * scale);
	video.updateHitbox();
	video.screenCenter();
});
video.bitmap.onEndReached.add(video.destroy);
add(video);

if (video.load(Paths.video("myVideo"))) video.play();
else trace("Video failed to load!");
```
</div>

Next up, here is how you can interact and use `VideoCutscene` for songs that need an intro / ending cutscene as a Video!
- <a href="./video_cutscene.md">How to use VideoCutscene</a>