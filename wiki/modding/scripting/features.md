---
author: ItsLJcool
desc: All the features (and syntax) that Codename Engine allows for softcoding
lastUpdated: 2025-11-15T05:32:50.298Z
title: Scripting - Features
---

# TODO
Explain all the features scripts can use. (i.e getter / setter, enums, imports, using, etc.)

# Variables, and Functions
First, lets start off very basic. HScript provides 2 different ways to define a variable, but do note, <i>technically</i> it doesn't matter as they both are the same anyways.<br>
To define a variable you can use the `var` and `final` keywords before the variable's name. You cannot have whitespace for variable names.
<!-- This just makes it so the whole block doesn't take the whole screen up :) -->
<div style="display: grid; justify-content: left;">

```haxe
var myVariable:Bool = true;
final my_final:Bool = true;

// Not valid syntax and will cause a Null Object Reference.
var my cool variable:Bool = false;
```
</div>

<div style="display: grid; justify-content: left;">

Every `HScript` / `Script` will always execute the `new` function whenever it loads the script.
```haxe
function new() {
	trace("I have loaded!");
}
```
</div>

#### What is a `final`?
In Haxe, a final just tells the compiler you cannot change this variable during runtime. Think of it as a `constant` that cannot be changed.<br>
In CodenameEngine this doesn't actually make it constant, <u>you can still modify `final` variables</u> but you can use the keyword to indicate you shouldn't modify the data.

## Public & Static ... & Private?
Haxe basically forces you to use `public` or `private` keywords when making classes, in HScript you don't have access to the `private` keyword unless you're using **[Custom Classes](./classes.md)**. However, this keyword does not do anything, as it is purely <u>sugar syntax.</u><br>
You do have access to the `public` keyword, which do nothing in [`Script`](../../../api-docs/funkin/backend/scripting/Script)s, but in any [`ScriptPack`](../../../api-docs/funkin/backend/scripting/ScriptPack)s it will allow other scripts to access that variable / function.

It doesn't "copy" the variable, it references the original so you can modify from one script and read the change from another.
<div style="display: grid; justify-content: left;">

```haxe
/* Script #1 */

public var my_accessable_var:Bool = true;
// you cannot do this with `final` variables, may be fixed / changed in the future.
// public final my_constant_var:Bool = false;

public function read_changes():Void {
    trace('my_accessable_var: $my_accessable_var');
}

function unreadable_function() {
    trace("This will never execute... :(");
}
```
```haxe
/* Script #2 */

function new():Void {
    my_accessable_var = false;
    read_changes(); // "my_accessable_var: false"
    unreadable_function() // Null Object Reference. This is not defined in Script #2 so it will throw this error.
}
```
</div>

**Functions** are a bit different, all you need is the `function` keyword, then define the name. Then you can tell it what parameters it expects.

<div style="display: grid; justify-content: left;">

```haxe
function myFunction():Void {
    trace("Hey! Im a function!");
}

public function accessableFunction(name:String):Void {
    trace('I am being accessed from $name!');
}

/**
    You are REQUIRED to have only optional parameters after the first optional parameter. You cannot have an optional before a non-optional parameter.
    So `(param1, ?optional2, param3)` is an invalid statement. `param3` must be optional; `(param1, ?optional2,?param3)`
**/
function optionalParameters(name:String, ?log:Bool = true, ?some_other_arg:Dynamic):Void {
    // Because of Hscript shenanigans, even if you define a default type it will still be null if the function call doesn't contain the parameter.
    // This will ensure the value is true if it's null. You will see this special setter later.
    log ??= true;
    if (!log) return; // Return out of the function early.

    trace('Hey guys, $name here.');
}
```
</div>

## Type Defining
In haxe, you can declare types on variables and return types on functions. You cannot set that variable to a different Class Type unless it's a `Dynamic` / `Any` / `T` type.<br>
This doesn't apply to HScript, **<u>every variable is `Dynamic`</u>** no matter what, so defining variables with types is only sugar syntax. I'd recommend you still do it for readability, though you're not forced to define every variable.
<div style="display: grid; justify-content: left;">

```haxe
var myTypedVariable:FlxSprite = new FlxSprite();
var myUntypedVariable = new FlxSprite();

function new() {
    myTypedVariable = true;
    myUntypedVariable = "Hi!"
}

function myReturnedTypeFunction():String {
    return "Chat, im goated";
}
function myUntypedReturnFunction() {
    return "I love HScript!!";
}
```
</div>

<details>
    <summary>Click this if you want your View of <b><u>HScript forever changed and even more confusing</u></b> 👀</summary>

<div style="display: grid; justify-content: left;">

```haxe
/* Yes, this is VALID HScript Syntax */
import Sys;
function myCoolFunction():String { return "So cool... I love this function"; }
function create() {} // to be able to modify this in the `new` function it must exist, otherwise we are setting a "non existent variable" lol.

function new() {
    myCoolFunction = () -> return "Blows up function with mind";
    function what():String { return "My fucking function... :("; }
    what = ":)";

    trace('${myCoolFunction()} | $what'); // "Blows up function with mind | :)"
    create = () -> Sys.exit(0); // will close the game if `create` is called 💖
}
```
</div>

</details>
<br>

# Differences between `Script` and `ScriptPack`
When looking through the source, you might find that there is 2 commonly used Classes for Scripts.
<!-- Yo I forgot to continue this and im too lazy to continue it LMAO sorry. - LJ -->

### Anonymous Structures
Anonymous structures are objects that allow for storing multiple values in a key-value pair format, separated by commas. They are features of Haxe that provide data grouping in general. Keys must be regular Haxe identifiers, and values are dynamic. They are useful to share data around in a compact manner. Anonymous structures themselves are treated as dynamic values.

Code-wise, structures look similar to JSON, called **J**ava**S**cript **O**bject **N**otation. However, there are important distinctions between them: JSON objects are explicitly strings, including keys, while Haxe keys are regular identifiers and values are regular Haxe types. It is generally safe to serialize structures into JSON objects and deserialize structures from JSON objects, though.

Anomyous structures generally look like this:
```haxe
var my_structure = {username: "mellowmeadow", score: 300, accuracy: 1, isValid: true};
```

Haxe does not care about whitespaces, which means the above code may be changed to the below for readability purposes: 

```haxe
var leStructure = {
    username: "mellowmeadow", 
    score: 300, 
    accuracy: 1, 
    isValid: true
};
```

Anonymous structures can also be nested, meaning that you can place one inside another, like this:
```haxe
var structA = {
    structB: {
        sample: "code"
    }
};
```

Like other objects, fields of anonymous structures may be accessed using dot notation, that is:
```haxe
someStruct.someField = someValue;
```

This also works with nested structures - you just need an extra dot the deeper you're trying to access.
```haxe
structA.structB.key = value;
```

Since the state/substate's `data` field is of the type `Dynamic`, anonymous structures may be used as data you can pass over.

Going back to the results screen example, we may supply an anonymous structure to send over more information such as the player's accuracy, score, and others. As such, we can modify that line once more to do exactly that.

```haxe
FlxG.switchState(new ModState("ResultsScreen", {
    accuracy: accuracy, score: score, misses: songMisses
}));
```

Next steps in learning the In-and-out's of HScript
- [Features](./features.md)
- [[NEXT] Formatting, and Basic Syntax](./style.md)
- [Event Callbacks](./cancellables.md)
- [PlayState Interaction](./playstate.md)
- [Custom States / SubStates](./states.md)
- [Custom Events / Notetypes](./events.md)
- [Global Script](./global.md)
- [Custom Classes](./classes.md)