---
author: ItsLJcool, Skylime_
desc: Introduction to Source Modding.
lastUpdated: 2026-08-10T13:49:12.473Z
title: Modding The Engine - Source Modding
---

# An Introduction To Source Modding
#### ⚠️ WARNING ⚠️
Source modding is *NOT* the normal way to make a mod, and it's not what you should reach for first.

Prioritize making a Modpack before interacting with source, as compiling CodenameEngine should be really one of these things:
- Making a Feature Request
- Fixing an Issue
- Embedding Assets
- Custom Source build for things Scripting can't currently achieve

Even if you do go for Source modding, you should still prioritize making a Modpack for rapid development, and once you know it all works then you can compile it into Source.

If your making stuff like Features, Bug Fixes, etc. Then don't worry about this.

## Difference Between "Source" and "Softcoding"

**Softcoding** means you write hscript (`.hx`) files inside your mod folder. The engine reads them while it's running. You save the file, you reload the state, and the change is there.

**Source Modding** means you *fork the engine itself*, edit the `./source/` folder, and compiling it to get an export of an Executable.

## What this guide covers

This page is pretty much the overview. It goes over what source modding actually costs you, what you gain from it, and when it's even worth doing.

If you've read all of it and still want to go ahead, [Initialization](./init.md) covers getting Haxe, git, a compiler and the engine's libraries set up so you can actually build.

## Now, why it isn't recommended?

- Whatever version you forked the repo on, you keep forever. The engine updates won't be free any longer. Every new release is now a merge you do by hand and retest yourself.

- You also lose compatibility with other mods. Softcoded mods stack on top of each other. Two source mods can never be installed together. That's just how it is.

- A tweak that takes three seconds in HScript now costs you a rebuild. Over a whole project that adds up more than anything else on this list, and it makes your mod worse because experimenting stops being quick.

- Every bug becomes yours. Crash reports don't go to the engine devs anymore. They are yours to deal with on your fork, with your changes.

- There's no safety net. HScript errors get caught and shown to you. A null in compiled code just takes the game down, which is NOT what you want, and it makes life harder for anyone helping you.

## Pro's of Source Modding

- Full access. Literally.

- Compiled down to bytecode, means faster execution and multithreading is opened up. HScript's Interpreter is about 100x <b>SLOWER</b> than bytecode.

- You can think of it as a HaxeFlixel Template Project. You can change whatever to fit your needs.

## Con's of Source Modding

- Syncing your Fork with `main` usually causes conflicts, so you are most likely stuck on Release Versions or Experimental Commits.

- Your Modpack is *most likely* not compatable with Official Releases of CodenameEngine, you'd need to add extra code to ensure it doesn't explode on Main.
	- You don't have to do this, since the point of you Source Modding would be only releasing the executable only.

- Slow iteration, (costs you more than you'd think)

- You own every crash, including ones in code you never touched, and it's harder to hand off. *Anyone* can pick up a softcoded mod. A fork needs someone willing to maintain a fork.

## Use it alongside softcoding, not instead of it

When you make a change, try to implement things that will *clearly benefit you while softcoding*.
But before anything, *check if what you're trying to do isn't already there*. Check the documentations. Trust me, there is loads of stuff you can't miss.

## You must make your fork Public when releasing your mod!
This is a *rule* you have to keep up with.

You need to keep *credits and attribution intact*, so **don't strip the credits menu or the splash**.

People are running an unsigned exe you compiled. A public repo is the only thing separating that from downloading a random exe from a stranger. Source mods have been used to *ship malware* in this scene before, and a visible diff is the defence against that. Engine devs can't backport your fix if they can't see it. Public history proves you wrote what you wrote, and if you ever step away from the project, an open fork can be continued while a closed one just dies.

Practically, keep the fork *public from day one*. Tag a release for every build you hand out so the exe goes to a real commit. Link the repo in your mod description, your README and your in game credits. Keep the upstream `LICENSE` and `NOTICE` files and add your own copyright line next to theirs rather than replacing them.

## Treat everything like it's a pull request

This is the habit that makes the whole thing sustainable.

One feature, any feature, with a name that says what it is. One thing per commit, with a message explaining what it does and why.

Every change that gets merged upstream is one you delete from your fork and get for free forever. When something breaks you can bisect.

Then, *actually send the PR*. Worst case it gets declined and you keep it in your fork, and best case *you stop maintaining it entirely*, and allow the devs to handle the rest.

Bug fixes especially. If you found a bug in the engine, *that fix belongs upstream*. Not sending in a PR can cause other mods to hit the same issue too, which is not what the devs want, or what you want either.

*So, send in a PR.*

### TL;DR, don't source mod it at all unless you run into serious issues

Everything Codename Engine already offers for softcoding is *in your favor*. If you want to add fixes to the game that hasn't already been fixed or want to introduce your own ideas, *make a PR*. That's all.
