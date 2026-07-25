---
author: clairedeluneee
desc: Explains how to create custom events and notetypes
lastUpdated: 2026-03-03T02:45:00.298Z
title: Scripting - Custom Events and Notetypes
---
*This is about the creation of custom events and notetypes. For the event callbacks HScript calls in scripts, see [Event Callbacks](./cancellables.md).*

<!-- 
my guy i fucking thought this was about custom chart events bruh :wilted_rose: 
-->

# Creating Custom Events
As is with anything else in this engine, custom events may be created to aid with various stuff that happens in songs. They also inherit `CancellableEvent`, which means they may be overriden like other events.

To create a custom event, place a `.json` and a `.hx` in `./data/events` in your mod folder. Ensure that the file names match.

## The JSON
Your `.json` holds the all the parameters the event needs, and each parameter may be a different type and a default value. All parameters are listed in the code block below.
```json
{
    "params": [
        {
            "name": "Bool",
            "type": "Bool",
            "defaultValue": true
        },
        {
            "name": "Int",
            "type": "Int(0, 99, 1)",
            "defaultValue": 0
        },
        {
            "name": "Float",
            "type": "Float(0, 99, 1, 2)",
            "defaultValue": 0.00
        },
        {
            "name": "String",
            "type": "String",
            "defaultValue": "nothing"
        },
        {
            "name": "StrumLine",
            "type": "StrumLine",
            "defaultValue": 0
        },
        {
            "name": "ColorWheel",
            "type": "ColorWheel",
            "defaultValue": "#FFFFFF"
        },
        {
            "name": "DropDown",
            "type": "DropDown('entry one', 'entry two')",
            "defaultValue": "entry one"
        }
    ]
}
```
You may copy the above code block for your own JSON file.
- `Bool` generates a checkbox that returns either true or false.
- `Int(min, max, step)` returns an Int and has 3 parameters.
  - `min` is how low the value can get.
  - `max` is how high the value can get.
  - `step` determines how much each step can get (for example, stepping by 10, 15, and so on).
- `Float(min, max, step)` functions the same as `Int(min, max, step)`, except this uses Floats which allow for decimal input.
- `String` returns a string.
- `StrumLine` allows picking a specific strumline. This returns the index of where the picked strumline is located in the `strumLines` group, which is determined by how the chart's strumline is ordered.
- `ColorWheel` generates a color wheel, and returns the color as an Int, following the `0xAARRGGBB` format.
- `DropDown('entry1', 'entry2'...)` restricts the input to only the values put inside the parenthesis. Each value must be enclosed with a single quote `'` and must be separated by a comma `,`.

In addition, a default value (`defaultValue`) may be set should the value for it is not set in the event. Think of it as a placeholder. If you leave it that way, it will use the placeholder. If a default value is not set, the parameter's value defaults to `null`, which is worth keeping in mind.

## The Script
With the JSON set, you may start work on your script file. The script may use only the `onEvent` callback, but nobody's stopping you if you wish to add more functionality. Event scripts are only ran if the corresponding event is used in the editor, so keep that in mind.

Below is a quick template you may use. The `"name"` is a placeholder, which means you need to replace it with the name of your event.
```hx
function onEvent(e) {
    if (e.event.name != "name") return;

    var params = e.event.params;
    // code goes here
}
```
The early `return` is present as the callback is fired if ***any*** event is triggered. While it is true that one event may have one script only, you may also handle other events in this script.

An important thing to note is that `e.event.params` returns an **array**, which means you will have to go back and forth between your script and your JSON, since the order of a parameter depends on where you put it in the JSON.

## The Icon
Icons are not required, but it is suggested that you make one, especially if you intend to have others use your event, or if you're charting and have a lot of events. Icons should have a size of 16x16 pixels, and should be put in `./images/editors/charter/event-icons`. Make sure that the name for the icon matches the name of the event.

# Creating Custom Notetypes
The creation of custom notetypes is similar to how custom events are made, minus the JSON. You will need two files for this, your script file (the `.hx`), and your note sprites (the `.xml` and `.png`).

## The Script
Notetype scripts go to `./data/notes`.

The minimal setup needed for notetype scripts looks like this:
```hx
function onPlayerHit(event) {
    if (event.noteType != "name") return;
    // code goes here
}
```
The early `return` is also present here, as ***any*** note getting hit will fire it, which also includes normal notes. Similar to event scripts, one notetype may only have one script, but this script can also be used to handle other notetypes, including normal ones if you wish. 

You may also set up behavior if a certain notetype is either hit by the opponent or either player or opponent, or if it was missed.
```hx
function onDadHit(event) {
    if (event.noteType != "name") return;
    // code for when the opponent hits the note goes here
}
function onNoteHit(event) {
    if (event.noteType != "name") return;
    // code for when either player or opponent hits the note goes here
}
function onNoteMiss(event) {
    if (event.noteType != "name") return;
    // code for when this note was missed goes here
}
```

## The Sprites
Note sprites go to `./images/game/notes`. If they are named after the notetype, the sprites for these are automatically replaced.


Next steps in learning the In-and-out's of HScript
- [Features](./features.md)
- [Formatting, and Basic Syntax](./style.md)
- [Event Callbacks](./cancellables.md)
- [PlayState Interaction](./playstate.md)
- [[PREV] Custom States / SubStates](./states.md)
- [Custom Events / Notetypes](./events.md)
- [[NEXT] Global Script](./global.md)
- [Custom Classes](./classes.md)