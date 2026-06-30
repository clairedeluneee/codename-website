---
author: rufius & ItsLJcool
desc: Explains what a GlobalScript is, and how to effectivly use it.
lastUpdated: 2026-06-05T06:00:45.809Z
title: Scripting - Global Script
---

# What is a `GlobalScript`?

`GlobalScript` is perhaps one of the more powerful script that can be loaded by a mod, and it's most commonly the first file generated in your mod. You can make a `GobalScript` by making a script file at `./data/global.hx`.

When your mod loads up, GlobalScript is one of the very first scripts to be executed, this script will persist throughout the entire mod, and will never be destroyed or become inactive (unless you manually tell it to). Once your mod is no longer loaded, it will be removed alongside it.

With this power, you can manage your entire Mod with this singular script, and is one of the many crucial components in building a sucessful backend structure for yourself, and as a utility.

# Callbacks

There is only a few functions that `GlobalScript` manages itself, though you can call custom functions yourself from other scripts by doing `GlobalScript.call("functionName", [/* arguments */])`.<br>
Although there really should be no reason to call functions directly to `GlobalScript`, it's there if you really need it.

<!-- region -->
<details>
    <summary>Here is a list of callbacks GlobalScript manages</summary>

- `focusGained()`
- `focusLost()`
- `gameResized(w:Int, h:Int)`

- `preDraw()`
- `postDraw()`

- `preGameReset()`
- `postGameReset()`

- `preGameStart()`
- `postGameStart()`

- `preStateCreate(state:FlxState)`

- `preStateSwitch()`
- `postStateSwitch()`

- `preUpdate(elapsed:Float)`
- `update(elapsed:Float)`
- `postUpdate(elapsed:Float)`

- `beatHit(curBeat:Int)`
- `stepHit(curStep:Int)`

- `destroy()`

</details>
<!-- endregion -->

# Hot Reloading

Codename can reload `GlobalScript`s by holding `Shift` and pressing the "Reload State" keybind. By default this is `Shift` + `F5`.<br>
Doing this will destroy and re-initalize all `GlobalScript`s, so you need to ensure you properly `destroy` things or remove them once the script is no longer active.

You should always do this in a `GlobalScript`, to ensure unloading your Mod never causes any issues with other mods / addons.
<div style="display: grid; justify-content: left;">

```haxe
function destroy() {
	trace("AAAAH IM BEING REMOVED!!!!");
}
```
</div>

# Cross-script Communication

`GlobalScript` maintains it's own `ScriptPack` that is <b>_freely accessible_</b> as `GlobalScript.scripts` as a `static` variable.

You can manage the whole `ScriptPack` anywhere in your mod, but for simplicity you should use `GlobalScripts.scripts` only if you need to access either a specific script inside, or internal variables. Using the `public` modifier in `GlobalScript` should never be used, as it only allows other `GlobalScripts` to access it, so it does nothing for your mod.

You can either make `static` variables / functions inside your `GlobalScript` to call anywhere, or just normal functions you can then call with `GlobalScript.call`.
It's common to think that you should shove everything that you want to use globally in a Global Script, but that is bad practice.

You should only use `GlobalScript` for managing custom classes, or holding data globally. You don't need to manage anything here either!
You can also use `GlobalScript` to execute code on Mod Launch.

Here is a basic example on how you should use `GlobalScript` to it's fullest potential.
<div style="display: grid; justify-content: center;">

```haxe
function apiFunc() {
	trace("API!");
}

function apiCalc(number_1:Int, number_2:Int) {
	trace('$number_1 + $number_2 = ${(number_1 + number_2)}!');
}
```

```haxe
import funkin.backend.scripting.GlobalScript;

function create() {
	GlobalScript.scripts.call("apiFunc", []); // API!
	GlobalScript.scripts.call("apiCalc", [9, 10]); // 9 + 10 = 19!
}
```
</div>

Next steps in learning the In-and-out's of HScript

- [Features](./features.md)
- [Formatting, and Basic Syntax](./style.md)
- [Event Callbacks](./cancellables.md)
- [PlayState Interaction](./playstate.md)
- [Custom States / SubStates](./states.md)
- [[PREV] Custom Events / Notetypes](./events.md)
- [Global Script](./global.md)
- [[NEXT] Custom Classes](./classes.md)
