---
author: ItsLJcool
desc: Explains how Custom Classes work
lastUpdated: 2026-03-28T04:08:54.618Z
title: Scripting - Custom Classes
---

# HScript's Classes and it's quirks
HScript allows for Custom classes, and it is not a 1:1 copy-paste from how source classes works, there is some limitations but those issues are being worked on.

You can define a Custom Class to import into any script by adding a file in `./source` in your Modpack called the Class name. You can add sub-folders for organization as well.
<div style="display: grid; justify-content: left;">

```text
└─ 📂 My Mod/                                
	└─ 📂 source/                              # Custom Classes go here.
		├─ 📄 MyCustomClass.hx                  # `import MyCustomClass;` in your code.
		└─ 📂 extra/                           
			└─ 📄 CoolClass.hx                   # `import extra.CoolClass;` in your code.
```

Here is an example of a Custom Class:
```haxe
class MyCustomClass { 

	private var my_private:Int = 0;
	public var my_public(get, never):Float;
	// Getters / Setters can be public or private it doesn't matter, as both `private` and `public` variables are accessable anyways...
	private function get_my_public():Float { return FlxG.random.float(-10, 10); }

	var my_random_var:Bool = true;

	static var my_static_var:Dynamic;

	public function new(arg1:Bool, arg2:String) {
		trace('you\'ve entered ${arg1} and "${arg2}"');
	}

	public function myFunc() {
		trace("Hey look! I've been called!");
		my_static_var = "myFunc";
	}

	public static function staticFunc() {
		trace("Oh wow look im a static callback!");
		my_static_var = "staticFunc";

		// trace(my_random_var); // Cannot access non-static instances inside a static function
	}

	private function myPrivateFunc() {
		trace("ooh you shouldn't call me directly!!");
	}
}
```

```haxe
/* This is a new script somewhere in your modpack */

// you can import your class like so
import MyCustomClass;
// if it's in a sub-folder in `./source/` (i.e `./source/myTest/`), you'd do this: `import myTest.MyCustomClass;`

function new() {
	var test:MyCustomClass = new MyCustomClass();
	trace('script was created. Here is the test class: ${test}');
}
```
</div>

You can also import custom classes into custom classes, same import method.

Now as of v1.0.1 and it's Experimentals, Static variables in Custom Classes are *not exactly static*, but are <u><i>locally static.</u></i><br>
This means if you import it in 2 different scripts they can have different static values. This can be easily fixed with a simple workaround, if your using the `./source/` folder for your class initalization.

```haxe
/* Custom Class Script in `./source/RealClassName.hx` */
// You want the actual class to not be your real class name, as we will make a static variable with the class name you desire.
class InternalClassName {
	static var my_static_var:String = "Test";
}

// This will contain the Class Type as a static variable.
// Note: attempting to do `Std.isOfType(value, RealClassName);` or `value is RealClassName` when you want to cast it's class type I belive may not work??
// This will be fixed or may have been fixed.
static var RealClassName:Class = InternalClassName;
```

## Extending Classes
You can also extend other classes, but there is a limit and some things to note.<br>
Any class with `<T>` or Abstract class you cannot extend from. This is in the works to be fixed.

You cannot extend `FlxState` / `FlxSubState` because they extend off of `FlxGroup` (which is `FlxTypedGroup<T:FlxBasic>`)
There is no need to anyways since [Custom States](./states.md) exists for you to use.

And one final limitation, is that if the extending class *<u>does not</u>* override a function in it's class, you cannot override it yourself in the custom class.
Here is an example:
<div style="display: grid; justify-content: left;">

```haxe
class MyCustomFunkinSprite extends FunkinSprite {

	// when extending a class, you are required to call `super()` for the `new` function.
	override public function new() {
		super();
	}

	override public function checkRenderTexture() {
		// this will not execute when called internally as `FunkinSprite` does not override this class
		// although it exists `FlxAnimate` (the base class `FunkinSprite` extends from) has this function, if it's not in the extending class itself, you cannot get the function.
	}
}
```
</div>

## The `using` keyword
In HScript you are allowed to use the `using` keyword, what this allows you to do is call functions on variables that class contains.<br>
Basically in other words it calls the class your `using` with those variables and does whatever calculation it needs to.

Here is an example:
<div style="display: grid; justify-content: left;">

```haxe
// StringTools allows you to call `.toLowerCase()`, `.substr()`, etc. on String types.
// You cannot do this by default you need to define the `using StringTools` before you can use them.
// https://api.haxe.org/StringTools.html for the function calls
using StringTools;

function new() {
	var test:String = "OH MY GOD IM LOWERCASE NOW!!";
	trace(test.toLowerCase()); // oh my god im lowercase now!!
}
```
</div>

You can create your own `using` class, you are required to have them static if you want them to be used like `StringTools`
<div style="display: grid; justify-content: left;">

```haxe
class TestUtil {
	// These functions require minimum 1 parameter, and it will always be the variable being called (i.e `variable.addOne()`)
	public static function addOne(_var:Int):Int { return _var+1; }
	public static function multiplyBy(_var:Float, value:Float):Float { return _var*value; }
}
```

```haxe
/* Another script in your modpack */
import TestUtil; // limitation for now but you need to import it before you can `using` it.
using TestUtil;

var test:Int = 10;

function new() {
	trace(test.addOne()); 		// 11
	trace(test.multiplyBy(2));	// 20
	trace(10.addOne()); // this does not work because hscript expects you to put a number. Put parenthesis like below
	trace((10).addOne()); // 11
}
```
</div>

Next steps in learning the In-and-out's of HScript
- [Features](./features.md)
- [[PREV] Formatting, and Basic Syntax](./style.md)
- [Event Callbacks](./cancellables.md)
- [[NEXT] PlayState Interaction](./playstate.md)
- [Custom States / SubStates](./states.md)
- [Custom Events / Notetypes](./events.md)
- [Global Script](./global.md)
- [Custom Classes](./classes.md)
