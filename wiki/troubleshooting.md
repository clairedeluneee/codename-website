---
author: clairedeluneee
desc: This page explains how to fix common issues.
lastUpdated: 2026-01-31T23:07:30.946Z
title: Troubleshooting
---
# Troubleshooting

## Black screen

This usually means the game failed to start. The reasons for this could be:

- Non-ASCII characters on the file path (example: `C:\Users\Niña\`)
- File path being too long
- Running the game inside an archive (like `.zip` files) or on a cloud folder (like Onedrive)

The solution is to move the folder somewhere else on the disk.

## Mod refuses to load

Double check your mod folder.

If it looks like this,

<img src="./images/troubleshooting/improper-folder-extract.png" alt="Mod folder containing another folder that has the folders CNE needs">

then enter that folder and move its contents up a level (place everything on the right to the middle).

## No audio
You probably muted the game. Hit `0` or `+` on your keyboard.

## Invalid field error
Some players might have found out that after a song, the game crashes with this error:

<img src="./images/troubleshooting/invalid-field.png" alt="Error with invalid field" style="width:85%;">

This is a save data error in which saves from Legacy (v0.1) are not recognized in v1.0. The fix is to wipe the save in Options > Miscellaneous > Reset save data.

If you are unable to reset it in the engine, you may instead delete the save yourself in your file manager.

The folder to be deleted is 
- Windows: `%AppData%/CodenameEngine`
- Mac: `Users/[your user]/Library/Application Support/CodenameEngine`
- Linux: `/.local/share/CodenameEngine`
