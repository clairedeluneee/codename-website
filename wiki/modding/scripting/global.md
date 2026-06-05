---
author: rufius
desc: Explains what a GlobalScript is, and how to effectivly use it.
lastUpdated: 2026-06-05T06:00:45.809Z
title: Scripting - GlobalScript
---

# `GlobalScript`

`GlobalScript` is perhaps one of the more powerful scripts that can be loaded by a mod, and it's usually expected to find one in a larger mod. A `GlobalScript` generally lives under `data/global.hx`.

It is safe to say that `GlobalScript` is perhaps the most powerful kind of script that can be loaded into a mod. There are several capabilities that a `GlobalScript` may perform:

# Hot Reloading

Codename can reload `GlobalScript`s by holding `Shift` and pressing the "Reload State" keybind.

It is also generally safe to simply switch mods to reload `GlobalScript`s.

# Callbacks

`GlobalScript`s can run code in various stages of a mod. `new` will always be executed upon a `GlobalScript` being loaded:

```haxe
function new()
    trace("Hello, World!");
```

A more prominent use case is `preStateSwitch`. This callback allows for a large amount of control over fine-tuning a state before actually switching to it, as it runs precisely before the state is put into effect.

```haxe
function preStateSwitch()
    trace("Hello, new state!");
```

# Cross-script Communication

`GlobalScript` maintains its own `ScriptPack` that is _freely accessible_ as `GlobalScript.scripts` to every script, because it is provided as a public, static variable.

What this allows for is a rather unhinged amount of possibilities of using `GlobalScript` for communicating between scripts or saving session-wide data.

For example, let's say a `PlayState Script` wants to set a variable to be used further down the session. A `GlobalScript` may declare this variable, and the variable can then be used across the entire mod:

```haxe
// We define this variable in data/global.hx...
public var evilEffects:Bool = false;

// We can verify that evilEffects is being set by tracing its value on every state switch.
function preStateSwitch()
    trace(evilEffects);
```

```haxe
// And lo and behold! So long as we import GlobalScript ourselves, we have access to the entirety of the GlobalScript's public properties.
import funkin.backend.scripting.GlobalScript;

function create()
    GlobalScript.scripts.publicVariables["evilEffects"] = true; // Switch states and you'll find that this persists, so long as the GlobalScript is in effect!
```

The inclusion of `GlobalScript.scripts` also allows for an unusually large amount of communication between addons and mods.

This is because the scripts from every active mod will be handled by a single `ScriptPack`, which is at play for `GlobalScript` as well.

With this in mind, you are entirely free to effectively provide an API for an addon that can be used by other mods:

```haxe
// Do know that, in order for ScriptPack.call to work with this function, we can't use the public modifier.
function apiFunc()
    trace("API!");
```

```haxe
import funkin.backend.scripting.GlobalScript;

function create()
    GlobalScript.scripts.call("apiFunc", []); // API!
```

Next steps in learning the In-and-out's of HScript

- [Features](./features.md)
- [Formatting, and Basic Syntax](./style.md)
- [Event Callbacks](./cancellables.md)
- [PlayState Interaction](./playstate.md)
- [Custom States / SubStates](./states.md)
- [[PREV] Custom Events / Notetypes](./events.md)
- [Global Script](./global.md)
- [[NEXT] Custom Classes](./classes.md)
