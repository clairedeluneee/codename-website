---
author: ItsLJcool, Skylime_
desc: Introduction to Source Modding.
lastUpdated: 2026-08-10T13:49:12.473Z
title: Modding The Engine - Source Modding
---

# An Introduction To Source Modding

Quick warning before anything else. Source modding is *NOT* the normal way to make a mod, and it's not what you should reach for first. It's something you do when softcoding *genuinely can't* do what you're trying to do, and even then you use *both together*.

## What it actually is

**Softcoding** means you write hscript (`.hx`) files inside your mod folder. The engine reads them while it's running. You save the file, you reload the state, and the change is there.

**Source modding** means you *fork the engine itself*, edit its Haxe code, compile the whole thing, and get your own copy of the game.

Codename Engine is built specifically so that softcoding can do *basically everything* source coding can. You can assume the thing you want already exists *until you've properly checked*.

## What this guide covers

This page is pretty much the overview. It goes over what source modding actually costs you, what you gain from it, and when it's even worth doing.

If you've read all of it and still want to go ahead, [Initialization](./init.md) covers getting Haxe, git, a compiler and the engine's libraries set up so you can actually build.

## Now, why it isn't recommended

- Whatever version you forked the repo on, you keep forever. The engine updates won't be free any longer. Every new release is now a merge you do by hand and retest yourself.

- You also lose compatibility with other mods. Softcoded mods stack on top of each other. Two source mods can never be installed together. That's just how it is.

- A tweak that takes three seconds in hscript now costs you a rebuild. Over a whole project that adds up more than anything else on this list, and it makes your mod worse because experimenting stops being quick.

- Every bug becomes yours. Crash reports don't go to the engine devs anymore. They come to you, on your fork, with your changes. People can't turn your changes off. Softcoded stuff can be disabled or overridden. Compiled stuff can't.

- There's no safety net. Hscript errors get caught and shown to you. A null in compiled code just takes the game down, which is NOT what you want, and it makes life harder for anyone helping you.

## The good parts

- Full access. Literally.

- It's genuinely faster, since you don't have an interpreter running every frame for the rest of your hscripts, which is a real bottleneck in some cases.
- You can change engine level behaviour that has no script hook at all, and you get build time control through `project.xml` defines and conditional compilation.

## The bad parts

- The longer you go without updating, the worse it gets, and the more you lose on fixes or changes.

- No compatibility with other source mods, and reduced compatibility with addons.

- Slow iteration, which as mentioned, costs you more than you'd think.

- You own every crash, including ones in code you never touched, and it's harder to hand off. *Anyone* can pick up a softcoded mod. A fork needs someone willing to maintain a fork.

## Use it alongside softcoding, not instead of it

When you make a change, try to implement things that will *clearly benefit you while softcoding*.
But before anything, *check if what you're trying to do isn't already there*. Check the documentations. Trust me, there is loads of stuff you can't miss.

## If you publish it, you have to open source it

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
