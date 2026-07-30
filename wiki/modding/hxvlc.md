---
author: HeroEyad
desc: Explain how you use HXVLC to create Videos, and showcases how you can create your own implementation of a handler.
lastUpdated: 2026-07-31T05:32:50.298Z
title: hxvlc
---

# What is hxVLC?
hxVLC is a Haxe Library which wraps LibVLC (VLCs core engine) so you can play videos inside your mod!

# How to use it?
Well first to start off we need to initiate it.

On `global.hx`

```haxe
import hxvlc.util.Handle;

function new() {
    Handle.init();
}
```
## Why are we doing this?
This wakes up the VLC core engine ahead of time, so when you play a video (`FlxVideo` or `FlxVideoSprite`) the engine is already ready to go. Depending on your engine setup you may not strictly need it, some engines already init it for you, but calling it yourself makes sure its ready.

## FlxVideo

Extends FlxInternalVideo > Video, so its a raw display object, not a sprite.
You add it to the display list directly (`FlxG.addChild` / `addChildBelowMouse`),
it doesnt live in a sprite group and ignores camera scroll/zoom. Good for
fullscreen cutscenes.

Constructor: `new(smoothing:Bool = true)`. Events like `onEndReached`, `onFormatSetup`, `onPlaying` sit directly on the instance.

```haxe
import hxvlc.flixel.FlxVideo;

final video:FlxVideo = new FlxVideo();
video.onEndReached.add(function():Void
{
	video.dispose();
	FlxG.removeChild(video);
});
FlxG.addChildBelowMouse(video);

// small wait avoids a load/play race
if (video.load('assets/videos/video.mp4'))
	FlxTimer.wait(0.001, () -> video.play());
```

## FlxVideoSprite

Extends [FlxSprite](https://api.haxeflixel.com/flixel/FlxSprite.html), so it behaves like any sprite: add() it to a state/group, position it, scale it, respects cameras. The actual video wrapper lives on `.bitmap` (a Null<FlxInternalVideo>), so events and properties like volume go through `.bitmap`, while `load`/`play`/`precache`/`pause`/`resume`/`stop` are proxied straight onto the sprite.

Constructor: `new(?instance:Instance, x:Float = 0, y:Float = 0)`.

```haxe
import hxvlc.flixel.FlxVideoSprite;

final video:FlxVideoSprite = new FlxVideoSprite(0, 0);
video.antialiasing = true;

// scale to screen once vlc reports the real dimensions
video.bitmap.onFormatSetup.add(function():Void
{
	if (video.bitmap != null && video.bitmap.bitmapData != null)
	{
		final scale:Float = Math.min(FlxG.width / video.bitmap.bitmapData.width, FlxG.height / video.bitmap.bitmapData.height);
		video.setGraphicSize(video.bitmap.bitmapData.width * scale, video.bitmap.bitmapData.height * scale);
		video.updateHitbox();
		video.screenCenter();
	}
});
video.bitmap.onEndReached.add(video.destroy);
add(video);

if (video.load('assets/videos/video.mp4'))
	FlxTimer.wait(0.001, () -> video.play());
```

You dont know the real size until onFormatSetup fires, which is why sizing
lives inside that callback rather than right after load.